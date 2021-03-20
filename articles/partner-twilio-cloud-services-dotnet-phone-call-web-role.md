---
title: Jak uskutečnit telefonní hovor z Twilio (.NET) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor pomocí služby Twilio API v Azure. Ukázky kódu napsané v .NET
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521117"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak uskutečnit telefonní hovor pomocí Twilio ve webové roli v Azure
Tato příručka ukazuje, jak pomocí Twilio uskutečnit volání z webové stránky hostované v Azure. Výsledná aplikace vyzve uživatele, aby provedl volání s daným číslem a zprávou, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Předpoklady
K použití kódu v tomto tématu budete muset provést následující:

1. Získejte účet Twilio a ověřovací token z [konzoly Twilio][twilio_console]. Pokud chcete začít s Twilio, zaregistrujte se [https://www.twilio.com/try-twilio][try_twilio] . Ceny můžete vyhodnotit na adrese [https://www.twilio.com/pricing][twilio_pricing] . Informace o rozhraní API, které poskytuje Twilio, najdete v tématu [https://www.twilio.com/voice/api][twilio_api] .
2. Přidejte *knihovnu .NET Twilio* do webové role. Viz téma **Přidání knihoven Twilio do projektu webové role** dále v tomto tématu.

Měli byste být obeznámeni s vytvořením základní [webové role v Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Postupy: Vytvoření webového formuláře pro volání
<a id="use_nuget"></a>Přidání knihoven Twilio do projektu webové role:

1. Otevřete své řešení v prostředí Visual Studio.
2. Klikněte pravým tlačítkem na **odkazy**.
3. Klikněte na **Spravovat balíčky NuGet**.
4. Klikněte na možnost **online**.
5. Do pole Hledat online zadejte *Twilio*.
6. V balíčku Twilio klikněte na **nainstalovat** .

Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro volání. V tomto příkladu je vytvořena webová role ASP.NET s názvem **TwilioCloud** .

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

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Postupy: vytvoření kódu pro volání
Následující kód, který je volána, když uživatel dokončí formulář, vytvoří zprávu volání a vygeneruje volání. V tomto příkladu se kód spouští v obslužné rutině události Click tlačítka na formuláři. (Použijte účet Twilio a ověřovací token místo zástupných hodnot přiřazených k `accountSID` a `authToken` v následujícím kódu.)

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

Volání se provede a zobrazí se koncový bod Twilio, verze rozhraní API a stav volání. Na následujícím snímku obrazovky vidíte výstup z ukázkového běhu.

![Odpověď volání Azure pomocí Twilio a ASP.NET][twilio_dotnet_basic_form_output]

Další informace o TwiML najdete na adrese [https://www.twilio.com/docs/api/twiml][twiml] . Další informace o &lt; slovnících &gt; a dalších příkazech Twilio najdete na adrese [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky
Tento kód vám poskytne základní funkce využívající Twilio ve webové roli ASP.NET v Azure. Před nasazením do Azure v produkčním prostředí budete možná chtít přidat další zpracování chyb nebo jiné funkce. Například:

* Místo používání webového formuláře můžete použít službu Azure Blob Storage nebo instanci Azure SQL Database k ukládání telefonních čísel a textů volání. Informace o používání objektů BLOB v Azure najdete v tématu [Jak používat službu Azure Blob Storage v .NET][howto_blob_storage_dotnet]. Informace o použití SQL Database najdete v tématu [použití Azure SQL Database v aplikacích .NET][howto_sql_azure_dotnet].
* Můžete použít `RoleEnvironment.getConfigurationSettings` k načtení ID účtu Twilio a ověřovacího tokenu z nastavení konfigurace nasazení místo hardwarového kódování hodnot ve formuláři. Informace o `RoleEnvironment` třídě naleznete v tématu [obor názvů Microsoft. windowsazure. ServiceRuntime][azure_runtime_ref_dotnet].
* Přečtěte si pokyny pro zabezpečení Twilio na adrese [https://www.twilio.com/docs/security][twilio_docs_security] .
* Další informace o Twilio najdete na adrese [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Viz také
* [Jak používat Twilio pro hlasové funkce a SMS z Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

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


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md