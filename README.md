# Creando Tarea Planificada en Odoo
## ¿Qué es una acción planificada?

En Odoo, podemos ejecutar tareas planificadas que se ejecuten cada cierto tiempo. Por ejemplo, podemos enviar un email cada mes con el resumen de las ventas o podemos crear un conjunto de registros cada semana.

Para este ejemplo validaremos el vencimiento de las facturas aquellas que estén vencidas serán notificadas mediante un correo al administrador.
## Desarrollando nuestra acción planificada

1. Heredamos del módulo de Facturas
```
	class AccountInvoice(models.Model):
    _name = 'account.invoice'
    _inherit ='account.invoice'
```
2. Creamos nuestro Método
```
class AccountInvoice(models.Model):
    _name = 'account.invoice'
    _inherit ='account.invoice'

    @api.model
    def revision_due_invoices(self, id=None):
        console.log( Revisando las Facturas Vencidas);
```

3. Creamos el Registro de Automatización (Objeto ir.con)
```
<data>

    <record forcecreate="True" id="revision_due_invoices_v1" model="ir.cron">
           <field name="name">Revision de Facturas Vencidas</field>
           <field eval="True" name="active" />
           <field name="user_id" ref="base.user_root" />
           <field name="interval_number">24</field>
           <field name="interval_type">hours</field>
           <field name="numbercall">-1</field>
           <field ref="model_account_invoice" name="model_id" />
            <field name="state">code</field>
           <field name="code">model.revision_due_invoices()</field>
           <field eval="False" name="doall"/>
           <field name="function">True</field>

        </record>

</data>
```

4. Para la revisión de las Facturas vencidas usaremos el campo **due_invoice**, quedándonos el siguiente método.
```
 @api.model
    def revision_due_invoices(self, id=None):
        print "### Revisando las Facturas Vencidas"
        date_act = fields.Datetime.now()
        invoice_due_ids = self.search([('due_invoice','<=',date_act),
        ('state','=','open')])
        if invoice_due_ids:
            # Folios de Facturas Vencidas
            ref_list_due = [x.number for x in invoice_due_ids]
            from odoo import SUPERUSER_ID
            user_admin = self.env.['res.user'].browse(SUPERUSER_ID)
            my_user = self.env.user
            mail_from = my_user.partner_id.email
            mail_to = user_admin.partner_id.email

            mail_vals = {
                    'subject': 'Notificacion de Facturas Vencidas %s' % date_act,
                    'author_id': my_user.id,
                    'email_from': mail_from,
                    'email_to': mail_to,
                    'message_type':'email',
                    'body_html': 'En la Fecha %s se encontraron las \
                    siguientes Facturas Vencidas %s' % (date_act, str(ref_list_due)) ,
                        }
            mail_id = self.env.['mail.mail'].create(mail_vals)
            mail_id.send()
```
5. Nuestro Resultado:
### Vista
**![](https://lh4.googleusercontent.com/FjwKVRwWO5a4DHUMALmDURJRO-f-m8g8jpJN6VHyT1eECMEqx6_d0F_zHnDylr_l_XMPi_ruBNxXRpN-b9UUyqEZhyAwsE0QdS6fSlAZnfA-IeSErtu6qVUYLV82a2m4lgCPJ10c)**
### Models
**![](https://lh3.googleusercontent.com/B6Mxzeku2kLenSBVWygdbQrhZjO7y3C41O-M0LfCGBy_qgp-nHYhqXigfbF1GiXDJcXJIn2Sa-NCwQybIX7Ydc8cymlvk8rGDpUgp7Kz_KXIlLcUdIqJD0Ho4wrrXUcjLpjqI0wl)**

### Xml
**![](https://lh4.googleusercontent.com/txZuwTZyzrd-oEla3me-VTFBISKVBJEznZOit6XSGRx1nEefbis0K723fQTdvQKZr3GDaDyCAhITibaptqAccHO_kKKyUfLReVlMxWZkoWr3hLzJaKhS79lD4stkYnccP-yI0keD)**

