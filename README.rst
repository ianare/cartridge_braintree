cartridge_braintree
===================

Braintree Payments processing for Mezzanine/Cartridge

Instructions for use
--------------------

1. Install cartridge_braintree::

      pip install cartridge_braintree
    
   If you need the correct sorting of not ASCII country names use::
    
      pip install cartridge_braintree[countries_utf8_sorting]
        
   This will add 'pyuca' to the requirements.

2. Add 'cartridge_braintree' to your ``INSTALLED_APPS``. List it higher than
   'cartridge.shop', otherwise the cartridge_braintree template will
   not be selected.

3. Set up the following settings in your ``settings.py`` or ``local_settings.py``::

      BRAINTREE_MERCHANT = <your merchant ID>
      BRAINTREE_PUBLIC_KEY = <your public key>
      BRAINTREE_PRIVATE_KEY = <your private key>

   .. Note:: 
      When ``DEBUG`` is ``True`` the *Braintree Sandbox environment* will be
      used, otherwise the *Braintree Production environment* is used.

   See also
   https://articles.braintreepayments.com/control-panel/important-gateway-credentials

4. Set the order form to be the modified Braintree form (in ``settings.py``)::

      SHOP_CHECKOUT_FORM_CLASS = 'cartridge_braintree.forms.BraintreeOrderForm'

   This form does the following:

   - Changes the shipping and billing country fields to a Select
     widget. This ensures that the country selected can be converted to
     a valid code for Braintree's payment processing.
     The supported countries can be set in ``settings.SHOP_SUPPORTED_COUNTRIES``,
     which is a list of alpha2 country codes and/or tuples in the form
     (alpha2, country_name).

     For example if you want to select the countries in the EU area use::

        SHOP_SUPPORTED_COUNTRIES = [
            'AL', 'AT', 'BA', 'BE', 'BG', 'CH', 'CY', 'DE', 'DK', 'EE', 'ES',
            'FI', 'FR', 'GB', 'GR', 'HR', 'HU', 'IE', 'IS', 'IT', 'LT', 'LV',
            'MK', 'MT', 'NL', 'NO', 'PL', 'PT', 'RO', 'RS', 'SE', 'SI'
         ]

     The country/countries (as alpha2 codes) listed in
     ``settings.SHOP_PRIMARY_COUNTRIES`` will show up at the top of the
     country selection fields.

   - Credit Card number and CCV fields are rendered using the
     ``NoNameTextInput`` widget so that the data is not submitted to the
     server. Javascript processes these fields to create a
     ``payment_method_nonce``, which is then stored in a hidden form
     element.

   - Uses `payform <https://github.com/jondavidjohn/payform>`_ for client-side
     validation of the payment form and submits the errors in the hidden
     ``braintree_errors`` field to the server to handle them as Django errors.

   See
   https://developers.braintreepayments.com/guides/transactions/python

5. Set the payment handler to be the Braintree payment handler::

      SHOP_HANDLER_PAYMENT = 'cartridge_braintree.braintree_payment.payment_handler'

   If you are overriding the templates in your own apps, then be sure to
   include the relevant ``braintreejs.html`` template.

   See https://www.braintreepayments.com/docs/python/guide/getting_paid
   for Braintree's tutorial.

6. If you want to use PayPal payments with Braintree activate them in
   the Admin Site Settings and set the currency to use with PayPal.

   Alternatively you can set them in ``settings.py`` in the form::

      BRAINTREE_PAYPAL_ACTIVATE = True
      BRAINTREE_PAYPAL_CURRENCY = "EUR"

   In this case the settings will not be shown in the Admin.