---
title: MFA software development kit vlastních aplikací | Microsoft Docs
description: Tento článek ukazuje, jak stáhnout a použít Azure MFA SDK k povolení dvoustupňové ověřování pro vaše vlastní aplikace.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: joflore
ms.openlocfilehash: 28efd5a5e9e757f3e2eae64fc770393608c9c028
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Vytváření služby Multi-Factor Authentication do vlastní aplikace (SDK)

> [!IMPORTANT]
> Proběhlo oznámení o zastarání sady Azure Multi-Factor Authentication Software Development Kit (SDK). Tato funkce bude už podporována pro nové zákazníky. Aktuální zákazníci mohou tuto sadu SDK používat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří. 

Azure Multi-Factor Authentication Software Development Kit (SDK) umožňují vytvářet dvoustupňové ověření přímo do procesy přihlášení nebo transakci aplikací v klientovi služby Azure AD.

Sada SDK služby Multi-Factor Authentication je k dispozici pro C#, Visual Basic (.NET), Java, Perl, PHP a Ruby. Sada SDK poskytuje dynamické obálku kolem dvoustupňové ověřování. Obsahuje všechno, co potřebujete k zápisu kódu, včetně soubory komentáři zdrojového kódu, například soubory a podrobné souboru ReadMe. Každý SDK zahrnuje také certifikát a soukromý klíč pro šifrování transakce, které jsou jedinečné pro vaše zprostředkovatel vícefaktorového ověřování. Tak dlouho, dokud máte poskytovatele, si můžete stáhnout sadu SDK v jako v mnoha jazycích a formátů podle potřeby.

Struktura rozhraní API v službu Multi-Factor Authentication SDK je jednoduché. Ujistěte se, jedné funkce volání do rozhraní API s parametry Multi-Factor možnost (jako jsou ověřování režimu) a dat uživatele (například telefonní číslo pro volání nebo číslo PIN kódu k ověření). Rozhraní API převede volání funkce do webových požadavků služby založené na cloudu Azure Multi-Factor Authentication Service. Všechna volání musí obsahovat odkaz na privátní certifikát, který je zahrnuta v každé sadě SDK.

Protože rozhraní API nemají přístup k uživatelům zaregistrovat ve službě Azure Active Directory, je třeba zadat informace o uživateli v souboru nebo databáze. Rozhraní API také neposkytují funkce správy zápisu nebo uživatele, proto musíte vytvořit tyto procesy do vaší aplikace.

> [!IMPORTANT]
> Pokud si chcete stáhnout sadu SDK, je třeba vytvořit poskytovatele Azure Multi-Factor Auth i v případě, že máte licence Azure MFA, AAD Premium nebo EMS. Pokud pro tento účel vytvořit poskytovatele Azure Multi-Factor Auth a už máte licence, nezapomeňte si vytvořit zprostředkovatele s **za povoleného uživatele** modelu. Potom propojte poskytovatele s adresářem, ve kterém jsou uložené licence Azure MFA, Azure AD Premium nebo EMS. Tato konfigurace zajistí, že se pouze účtují Pokud máte více jedinečných uživatelů, kteří pomocí sady SDK než počet licencí, které vlastníte.


## <a name="download-the-sdk"></a>Stažení sady SDK
Stažení sady SDK Azure Multi-Factor vyžaduje [zprostředkovatel vícefaktorového ověřování Azure](concept-mfa-authprovider.md).  To vyžaduje úplné předplatné, i když jsou ve vlastnictví licence Azure MFA, Azure AD Premium nebo Enterprise Mobility Suite. Veřejné metody stahování sady SDK mít byla vyřazena, protože sadu SDK je zastaralá. Pokud budete muset stáhnout sady SDK, by měl otevřete případu podpory se společností Microsoft. Sada SDK je k dispozici pouze pro zákazníky, kteří jsou již pomocí sady SDK. Nové zákazníky nebudou zařazený nemá.

## <a name="whats-in-the-sdk"></a>Co je v sadě SDK
Sada SDK zahrnuje následující položky:

* **SOUBOR README**. Vysvětluje, jak používat rozhraní API služby Multi-Factor Authentication v nové nebo existující aplikaci.
* **Zdrojové soubory** pro službu Multi-Factor Authentication
* **Klientský certifikát** používaný ke komunikaci se službou Multi-Factor Authentication
* **Privátní klíč** certifikátu
* **Výsledky volání.** Seznam kódy výsledků volání. K otevření tohoto souboru, použijte aplikaci s formátování textu, například WordPad. Kódy výsledků volání používejte pro testování a řešení potíží s implementací vícefaktorového ověřování ve vaší aplikaci. Nejsou ověřování stavové kódy.
* **Příklady.** Ukázkový kód pro základní pracovní implementaci služby Multi-Factor Authentication.

> [!WARNING]
> Klientský certifikát je jedinečný privátní certifikát, který byl vygenerován speciálně pro vás. Sdílené složky nebo ztratit tento soubor. Je váš klíč k zajištění zabezpečení komunikace se službou Multi-Factor Authentication.

## <a name="code-sample"></a>Ukázka kódu
Tento příklad ukazuje, jak přidat standardní režim hlasového hovoru ověření do vaší aplikace pomocí rozhraní API v sadě SDK Azure Multi-Factor Authentication. Je standardní režim telefonního hovoru, který uživatel odpoví na stisknutím klávesy #.

Tento příklad používá C# .NET 2.0 Multi-Factor Authentication SDK v základní aplikace ASP.NET pomocí jazyka C# logiku na straně serveru, ale proces je podobný v dalších jazycích. Vzhledem k tomu, že sada SDK obsahuje zdrojové soubory, není spustitelné soubory, lze vytvořit soubory a odkazujte na ně nebo je přímo do aplikace zahrnout.

> [!NOTE]
> Při implementaci vícefaktorového ověřování, použijte další metody (telefonního hovoru nebo textové zprávy) jako sekundární nebo terciární ověření pro doplnění vaší primární metoda ověřování (uživatelské jméno a heslo). Tyto metody se nedají jako primární metody ověřování.

### <a name="code-sample-overview"></a>Přehled ukázka kódu
Tento ukázkový kód pro jednoduchou webovou aplikaci ukázku používá telefonního hovoru s klíče odpověď # ověřit uživatele. Tento faktor telefonní hovor je ověřování službou Multi-Factor Authentication označuje jako standardní režim.

Kód klienta neobsahuje žádné elementy specifické pro službu Multi-Factor Authentication. Protože dodatečných ověřovacích faktorů jsou nezávislé na primární ověřování, můžete je přidat beze změny existujícího rozhraní přihlášení. Rozhraní API v sadě SDK Multi-Factor umožňují přizpůsobit činnost koncového uživatele, ale možná nebudete muset nic nezmění vůbec.

Serverový kód přidá standardní režim ověřování v kroku 2. Vytvoří objekt PfAuthParams s parametry, které jsou požadovány pro standardní režim ověřování: uživatelské jméno, telefonní číslo a režim a cestu k certifikátu klienta (CertFilePath), který je požadován při každém volání. Ukázka všech parametrů v PfAuthParams naleznete v souboru příklad v sadě SDK.

V dalším kroku kód předá objekt PfAuthParams pf_authenticate() funkce. Návratová hodnota označuje úspěch nebo selhání ověřování. Parametry, callStatus a ID chyby, obsahují další volání výsledek informace. Kódy výsledků volání jsou popsané v souboru výsledků volání v sadě SDK.

Tato minimální implementace může být napsán v pár řádků. V produkčním kódu, bude však zahrnovat sofistikovanější zpracování chyb, kód další databáze a vylepšené uživatelské prostředí.

### <a name="web-client-code"></a>Kódu klienta webové
Zde je kódu klienta webové stránky ukázku.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
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
V následujícím kódu na straně serveru Multi-Factor Authentication nakonfigurován a spusťte v kroku 2. Standardní režim (MODE_STANDARD) je telefonní hovor, na kterou uživatel odpovídá stisknutím klávesy #.

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
