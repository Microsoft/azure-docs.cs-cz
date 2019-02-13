---
title: Azure MFA sada SDK pro aplikace
description: V tomto článku se dozvíte, jak si stáhnout a zapnout dvoustupňové ověřování pro vaše vlastní aplikace pomocí Azure MFA SDK.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46287e75a4f4717722c398cf4133b6409c75aa8e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178919"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Vytváření služby Multi-Factor Authentication do vlastních aplikací (SDK)

> [!IMPORTANT]
> Proběhlo oznámení o zastarání sady Azure Multi-Factor Authentication Software Development Kit (SDK). Tato funkce se již nejsou podporovány pro nové zákazníky. Aktuální zákazníci mohou tuto sadu SDK používat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří. 

Azure Multi-Factor Authentication Software Development Kit (SDK) umožňuje vytvářet dvoustupňové ověřování aplikací ve vašem tenantovi Azure AD přímo do procesů přihlašování a transakcí.

Multi-Factor Authentication SDK je k dispozici pro C#, Visual Basic (.NET), Java, Perl, PHP a Ruby. SDK podporuje prostřednictvím dynamického zajišťování obálku kolem dvoustupňové ověřování. Obsahuje všechno, co potřebujete k psaní kódu, včetně komentářem zdrojové soubory, například soubory a podrobné souboru ReadMe. Každá sada SDK obsahuje také certifikátu a privátního klíče pro šifrování transakcí, které jsou jedinečné pro poskytovatele služby Multi-Factor Authentication. Za předpokladu, budete mít zprostředkovatele, si můžete stáhnout sadu SDK v libovolný počet jazyků a formátů podle potřeby.

Struktura rozhraní API v sadě SDK pro ověřování službou Multi-Factor Authentication je jednoduché. Ujistěte se, jedinou funkci volání rozhraní API s parametry možností služby Multi-Factor Authentication (např. režim ověřování) a data uživatele (např. telefonní číslo nebo číslo PIN kód pro ověření). Rozhraní API pro překlad volání funkce do webové služby požadavky cloudové služby Azure Multi-Factor Authentication. Všechna volání musí obsahovat odkaz na privátní certifikát, který je zahrnutý v každé sadě SDK.

Rozhraní API nemají přístup k uživatelům v Azure Active Directory, je nutné zadat informace o uživateli v souboru nebo v databázi. Rozhraní API také neposkytují registrace nebo uživatelské funkce správy, proto budete muset sestavit tyto procesy do vaší aplikace.

> [!IMPORTANT]
> Pokud si chcete stáhnout sadu SDK, je třeba vytvořit poskytovatele Azure Multi-Factor Auth i v případě, že máte licence Azure MFA, AAD Premium nebo EMS. Pokud vytvoříte poskytovatele Azure Multi-Factor Auth pro tento účel a již máte licence, nezapomeňte poskytovatele vytvořit podle **za povoleného uživatele** modelu. Potom propojte poskytovatele s adresářem, ve kterém jsou uložené licence Azure MFA, Azure AD Premium nebo EMS. Tato konfigurace zajistí, že nebudete dostávat faktury, pokud máte více jedinečných uživatelů využívajících sadu SDK než počet vlastněných licencí.


## <a name="download-the-sdk"></a>Stáhnout sadu SDK
Stažení Azure Multi-Factor Authentication SDK vyžaduje [poskytovatele Azure Multi-Factor Auth](concept-mfa-authprovider.md).  To vyžaduje úplné předplatné, i když jsou ve vlastnictví licence Azure MFA, Azure AD Premium nebo Enterprise Mobility Suite. Veřejné metody stažení sady SDK mají byla vyřazena, protože sada SDK se už nepoužívá. Pokud potřebujete stáhnout sadu SDK, měli byste otevřít případ podpory s Microsoftem. Sada SDK je k dispozici jenom pro zákazníky, kteří již používají sadu SDK. Noví zákazníci nebudou připojili.

## <a name="whats-in-the-sdk"></a>Novinky v sadě SDK
Sada SDK zahrnuje následující položky:

* **SOUBOR README**. Vysvětluje, jak používat rozhraní API ověřování službou Multi-Factor Authentication v nové nebo existující aplikace.
* **Zdrojové soubory** ověřování službou Multi-Factor Authentication
* **Klientský certifikát** , který používáte ke komunikaci se službou Multi-Factor Authentication
* **Privátní klíč** certifikátu
* **Výsledky volání.** Seznam kódy výsledků hovorů. Tento soubor otevřít pomocí aplikace formátování textu, jako je WordPad. Kódy výsledků hovorů používejte pro testování a řešení potíží s implementací ověřování službou Multi-Factor Authentication ve vaší aplikaci. Nejsou stavové kódy ověřování.
* **Příklady.** Ukázkový kód pro základní implementaci pracovních služby Multi-Factor Authentication.

> [!WARNING]
> Klientský certifikát je jedinečný privátní certifikát, který byl vygenerován speciálně pro vás. Sdílet nebo ztrátě tohoto souboru. To je váš klíč se zajištěním zabezpečení komunikace se službou Multi-Factor Authentication.

## <a name="code-sample"></a>Ukázka kódu
Tento vzorový kód ukazuje, jak přidat standardní režim hlasového volání ověření do vaší aplikace pomocí rozhraní API v sadě SDK Azure Multi-Factor Authentication. Je možnost Standardní režim telefonického hovoru, který uživatel odpoví stisknutím klávesy #.

Tento příklad používá C# .NET 2.0 Multi-Factor Authentication SDK v základní aplikace v ASP.NET s C# logiku na straně serveru, ale proces se podobá v jiných jazycích. Vzhledem k tomu, že sada SDK obsahuje zdrojové soubory, nikoli spustitelné soubory, lze vytvořit soubory a odkazovat na ně nebo jejich přímo do aplikace zahrnout.

> [!NOTE]
> Při implementaci ověřování službou Multi-Factor Authentication, použijte další metody (telefonní hovor nebo textovou zprávu) jako sekundární nebo terciární ověření k doplnění vaše primární metoda ověřování (uživatelské jméno a heslo). Tyto metody nejsou určeny jako primárních metod ověřování.

### <a name="code-sample-overview"></a>Přehled ukázky kódu
Tento ukázkový kód pro jednoduchou webovou aplikaci Ukázka používá telefonní hovor klíče odpovědí # ověřit uživatele. Tento faktor telefonní hovor je ověřování službou Multi-Factor Authentication označuje jako standardní režim.

Kód na straně klienta neobsahuje žádné prvky specifické pro multi-Factor Authentication. Protože dodatečných ověřovacích faktorů platí bez ohledu na primární ověřování, můžete je přidat beze změny stávajících rozhraní přihlašování. Rozhraní API v sadě SDK služby Multi-Factor Authentication umožňují přizpůsobit uživatelské prostředí, ale možná nebudete muset nic vůbec změnit.

Kód na straně serveru přidá ověřování v režimu standard v kroku 2. Vytvoří objekt PfAuthParams s parametry, které jsou požadovány pro standardní režim ověřování: uživatelské jméno, telefonní číslo a režim a cesta k certifikátu klienta (CertFilePath), která je vyžadována v každé volání. Ukázku všech parametrů v PfAuthParams naleznete v příkladu souboru v sadě SDK.

V dalším kroku kód předá objekt PfAuthParams pf_authenticate() funkce. Návratová hodnota označuje úspěšné nebo neúspěšné ověřování. Parametry, callStatus a ID chyby, obsahují informace o výsledcích další volání. Kódy výsledků volání jsou zdokumentované v souboru výsledků volání v sadě SDK.

Tato minimální implementace může být napsán v několika řádků. V produkčním kódu by ale obsahovat složitější zpracování chyb, kód další databáze a vylepšené uživatelské prostředí.

### <a name="web-client-code"></a>Kódu klienta webové
Následuje kódu klienta webové stránky ukázku.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="https://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Kód na straně serveru
V následujícím kódu na straně serveru je ověřování službou Multi-Factor Authentication konfiguraci a spuštění v kroku 2. Telefonní hovor, ke kterému uživatel odpoví stisknutím klávesy # je možnost Standardní režim (MODE_STANDARD).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
