---
title: Jak volat z Twilia (.NET) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v rozhraní .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: timlt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: jeconnoc
ms.openlocfilehash: 83f648b67ae4f8e6fb964d82c245f1c7ba475542
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568535"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak telefonování pomocí Twilio ve webové roli v Azure
Tato příručka ukazuje, jak se k volání z webové stránky hostované v Azure použít Twilio. Výsledné aplikace vyzve uživatele k volání daného čísla, zpráva, ujistěte se, jak je znázorněno na následujícím snímku obrazovky.

![Azure volání formulář s využitím platformy Twilio a ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Požadavky
Budete muset následujícím postupem použít kód v tomto tématu:

1. Získání účtu Twilio a ověřování tokenů z [Twilio konzoly][twilio_console]. Abyste mohli začít s platformou Twilio, zaregistrujte se na [ https://www.twilio.com/try-twilio ] [ try_twilio]. Můžete si vyzkoušet ceny na [ http://www.twilio.com/pricing ] [ twilio_pricing]. Informace o rozhraní API poskytovaných Twilio, naleznete v tématu [ http://www.twilio.com/voice/api ] [ twilio_api].
2. Přidat *knihovny Twilio .NET* pro vaši webovou roli. Zobrazit **Twilio knihovny přidat do projektu webové role**dále v tomto tématu.

Měli byste se seznámit s vytvářením základní [webová Role na Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Postupy: vytvoření webového formuláře pro volání
<a id="use_nuget"></a>Chcete-li přidat Twilio knihovny do projektu webové role:

1. Otevřete řešení v sadě Visual Studio.
2. Klikněte pravým tlačítkem na **odkazy**.
3. Klikněte na tlačítko **spravovat balíčky NuGet**.
4. Klikněte na tlačítko **Online**.
5. Zadejte do pole Hledat online *twilio*.
6. Klikněte na tlačítko **nainstalovat** na balíček platformy Twilio.

Následující kód ukazuje, jak vytvořit webový formulář data uživatele pro volání. V tomto příkladu webovou roli ASP.NET s názvem **TwilioCloud** se vytvoří.

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

## <a id="howtocreatecode"></a>Postupy: vytvoření kódu pro volání
Následující kód, který se volá, když uživatel vykoná formulář, vytvoří zprávu volání a vygeneruje volání. V tomto příkladu je kód spuštěn v obslužné rutině události onclick tlačítka na formuláři. (Použití vašeho účtu Twilio a ověřování tokenem místo zástupné hodnoty přiřazené `accountSID` a `authToken` v následujícím kódu.)

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
            // Call porcessing happens here.

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
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
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

Při volání a koncový bod Twilio, verze rozhraní API a stav volání se zobrazí. Následující snímek obrazovky ukazuje výstup z ukázkové spuštění.

![Azure volání odpovědi HTTP pomocí platformy Twilio a ASP.NET][twilio_dotnet_basic_form_output]

Další informace o TwiML najdete [ http://www.twilio.com/docs/api/twiml ] [ twiml]. Další informace o &lt;Say&gt; a ostatní operace Twilio lze nalézt v [ http://www.twilio.com/docs/api/twiml/say ] [ twilio_say].

## <a id="nextsteps"></a>Další kroky
Tento kód byl poskytnut zobrazit základní funkce s použitím Twilio webovou roli ASP.NET v Azure. Před nasazením v produkčním prostředí do Azure, můžete přidat další zpracování chyb nebo jiné funkce. Příklad:

* Místo použití webového formuláře, můžete použít úložiště objektů Blob v Azure nebo instance Azure SQL Database k ukládání telefonní čísla a volání text. Informace o používání objektů BLOB v Azure najdete v tématu [použití služby Azure Blob storage v rozhraní .NET][howto_blob_storage_dotnet]. Informace o použití SQL Database najdete v tématu [jak používat Azure SQL Database v aplikacích .NET][howto_sql_azure_dotnet].
* Můžete použít `RoleEnvironment.getConfigurationSettings` načíst Twilio ID účtu a ověřovací token z nastavení konfigurace vašeho nasazení, namísto pevně kódováno pomocí hodnoty ve formuláři. Informace o tom, `RoleEnvironment` najdete v tématu [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Přečtěte si pokyny pro zabezpečení platformy Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].
* Další informace o Twilio na [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="seealso"></a>Viz také
* [Postup použití Twilia pro hlasové hovory a SMS možnosti z Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
