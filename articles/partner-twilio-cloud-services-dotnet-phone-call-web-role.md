---
title: Jak telefonovat z Twilio (.NET) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v rozhraní .NET.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636138"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak volat pomocí Twilio ve webové roli v Azure
Tato příručka ukazuje, jak pomocí Twilio volat z webové stránky hostované v Azure. Výsledná aplikace vyzve uživatele k volání s daným číslem a zprávou, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Požadavky
Budete muset provést následující postup, abyste použili kód v tomto tématu:

1. Získejte účet Twilio a ověřovací token z [konzoly Twilio][twilio_console]. Chcete-li začít s Twilio, zaregistrujte se na . [https://www.twilio.com/try-twilio][try_twilio] Ceny můžete vyhodnotit na adrese [https://www.twilio.com/pricing][twilio_pricing]. Informace o rozhraní API poskytované Twilio [https://www.twilio.com/voice/api][twilio_api]naleznete v tématu .
2. Přidejte *knihovnu Twilio .NET* do webové role. Další **informace naleznete v tématu Přidání knihoven Twilio do projektu webových rolí**dále v tomto tématu.

Měli byste být obeznámeni s vytvořením základní [webové role v Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Postup: Vytvoření webového formuláře pro volání
<a id="use_nuget"></a>Přidání knihoven Twilio do projektu webových rolí:

1. Otevřete řešení v sadě Visual Studio.
2. Klepněte pravým tlačítkem myši na **položku Odkazy**.
3. Klepněte na tlačítko **Spravovat balíčky NuGet**.
4. Klepněte na **tlačítko Online**.
5. Do pole hledat online zadejte *příkaz twilio*.
6. Klikněte na **nainstalovat** na balíček Twilio.

Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro volání. V tomto příkladu je vytvořena ASP.NET webová role s názvem **TwilioCloud.**

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Postup: Vytvoření kódu pro volání
Následující kód, který je volán po dokončení formuláře uživatelem, vytvoří zprávu o volání a vygeneruje volání. V tomto příkladu je kód spuštěn v obslužné rutině události onclick tlačítka ve formuláři. (Použijte svůj účet Twilio a ověřovací token namísto zástupných hodnot přiřazených k níže uvedenému `accountSID` kódu a `authToken` v něm.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Volání je provedeno a zobrazí se koncový bod Twilio, verze rozhraní API a stav volání. Následující snímek obrazovky ukazuje výstup z ukázkového spuštění.

![Odpověď na volání Azure pomocí Twilio a ASP.NET][twilio_dotnet_basic_form_output]

Více informací o TwiML [https://www.twilio.com/docs/api/twiml][twiml]naleznete na adrese . Více informací &lt;&gt; o Say a dalších slovestcích Twilio naleznete na adrese [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky
Tento kód byl k dispozici ukázat základní funkce pomocí Twilio v ASP.NET webové role v Azure. Před nasazením do Azure v produkčním prostředí můžete přidat další zpracování chyb nebo jiné funkce. Například:

* Místo použití webového formuláře můžete k ukládání telefonních čísel a textu volání použít azure blob storage nebo instanci Azure SQL Database. Informace o používání objektů BLOB v Azure najdete v [tématu Jak používat službu úložiště objektů Blob Azure v rozhraní .NET][howto_blob_storage_dotnet]. Informace o použití databáze SQL najdete [v tématu Použití azure sql database v aplikacích .NET][howto_sql_azure_dotnet].
* Můžete použít `RoleEnvironment.getConfigurationSettings` k načtení ID účtu Twilio a ověřovací ho tokenu z nastavení konfigurace nasazení namísto pevného kódování hodnot ve formuláři. Informace o `RoleEnvironment` třídě naleznete v [tématu Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Přečtěte si bezpečnostní pokyny [https://www.twilio.com/docs/security][twilio_docs_security]Twilio na adrese .
* Další informace o Twilio na . [https://www.twilio.com/docs][twilio_docs]

## <a name="see-also"></a><a name="seealso"></a>Viz také
* [Jak používat Twilio pro hlasové a SMS funkce z Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
