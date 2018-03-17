Tato ukázka zahrnuje použití [Twilio](https://www.twilio.com/) službu pro odeslání zpráv serveru SMS na mobilní telefon. Azure Functions již podporu pro Twilio prostřednictvím [Twilio vazby](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), a ukázku pomocí této funkce.

První věcí, kterou je třeba je účet Twilio. Můžete jej vytvořit zdarma v https://www.twilio.com/try-twilio. Jakmile máte účet, přidejte následující tři **nastavení aplikace** do funkce aplikace.

| Název nastavení aplikace | Hodnota Popis |
| - | - |
| **TwilioAccountSid**  | Identifikátor SID účtu Twilio |
| **TwilioAuthToken**   | Token ověření účtu Twilio |
| **TwilioPhoneNumber** | Telefonní číslo přidružené k účtu Twilio. To se používá k odesílání zpráv serveru SMS. |