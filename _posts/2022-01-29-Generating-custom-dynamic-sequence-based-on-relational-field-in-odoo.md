---
published: true
---
The standard approach of the odoo framework when it comes to custom sequences is creating them inside `ir.sequence` and calling the `next_by_code()` method or setting the record to use that sequence in the xml/view part of the model.

However recently i was working on a module that required an out-of-the-box kind of sequence and the standard approach couldn’t easily lead to the intended sequence.

As you know most of the time the sequence in odoo looks like this:


- QUO0005
- REF0002
- INV/2021/0001



Where only the digits or date part of the sequence is dynamic but the abbreviation in front of the sequence is static.

On the other hand, the feature i needed was to have both the sequence code and numbers dynamic hence a new approach was necessary.

For example


- RCK0001
- RCK0002
- MRY0001
- SMR0001
- JRY0001
- RCK0003


NOTE 🚨  Though in this case i wanted to generate sales order sequences based on customer code, the same process can be applied on any related models whether built-in or custom.

## The code

### Creating the field on which the sequences will be based on.

     import random

     class Partner(models.Model):
        _inherit = 'res.partner'
        partner_code = fields.Char(
            string='Code',
            compute='_compute_code',
            unique=True,
            store=True
        )
        @api.depends('create_date')
        def _compute_code(self):
            for record in self:
                code = ''.join(random.choices(
                    str(record.name).replace(' ', '').upper(), k=3))
                if len(self.env['res.partner'].search(
                        [('partner_code', '=', code)])) >= 1:
                    record.partner_code = self._compute_code()
                else:
                    record.partner_code = code
 
I started by inheriting the existing partner model, added the new field and made sure the sequence is generated from the partner name and is unique. You might need to check if the code does not exist in `ir.sequence` as well in case you are using other parts of odoo.

Note that the compute depends on `create_date` the reason is to avoid the partner code being recomputed on name change which might lead to inconsistent partner code on orders created after the name is changed.
 
### Making the sale.order model use the new sequence
 
     import re
     
     class SalesOrder(models.Model):
        _inherit = 'sale.order'
        def next_by_code(self, partner_id):
            previous_order = self.env['sale.order'].search(
                [('partner_id', '=', partner_id)], order="id desc", limit=1)
            if previous_order:
                return previous_order.partner_id.partner_code + str(
                    int(re.search(r'\d+', previous_order.name).group()) + 1).zfill(5)
            else:
                return self.env['res.partner'].search(
                    [('id', '=', partner_id)], order="id desc", limit=1).partner_code + '00001'

        @api.model_create_multi
        def create(self, vals_list):
            for vals in vals_list:
                if 'company_id' in vals:
                    self = self.with_company(vals['company_id'])
                if vals.get('name', _('New')) == _('New'):
                    vals['name'] = self.next_by_code(vals['partner_id']) or _('New')

            return super().create(vals_list)

For the sales order to use the custom sequence, i had to inherit the original model and change its `create` method to use the my `next_by_code()` instead of the default one from `ir.sequence`. In case your string/digit part is computed differently you can adjust the `next_by_code` or the `_compute_code` methods to return the intended format or maybe odoo's sequence model will be able to handle this kind of feature in the near future and we wont have to code it.


## Environment:

- Odoo 15.0 CE
- Docker-compose


If you have a better/cleaner approach feel free to send me a message on twitter.
Untill next time, wear a mask.
