---
title: Konfigurace aplikace pro přístup k webovým rozhraním API (Preview) | Azure
description: Zjistěte, jak nakonfigurovat aplikaci zaregistrovanou na platformě Microsoft Identity Platform tak, aby zahrnovala identifikátory URI pro přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08ad61070fd3bfb8472cffb51fd0b1bb4bcf169b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169373"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Rychlý start: Konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API (Preview)

Aby se webová nebo důvěrná klientská aplikace mohla účastnit toku udělení autorizace, který vyžaduje ověření (a získání přístupového tokenu), musí mít zabezpečené přihlašovací údaje. Výchozí metodou ověřování podporovanou webem Azure Portal je ID klienta a tajný klíč.

Kromě toho, než klient získá přístup k webovému rozhraní API zveřejněnému aplikací prostředků (jako je rozhraní Microsoft Graph API), rozhraní pro udělování souhlasu zajišťuje, že klient musí nejprve na základě vyžádání oprávnění získat udělení oprávnění. Ve výchozím nastavení si můžou všechny aplikace zvolit oprávnění z rozhraní Microsoft Graph API. Ve výchozím nastavení je vybrané [oprávnění rozhraní Graph API pro „přihlášení a čtení uživatelského profilu“](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions). U každého požadovaného webového rozhraní API si můžete vybrat ze [dvou typů oprávnění](developer-glossary.md#permissions):

* **Oprávnění aplikace** – Klientská aplikace vyžaduje přímý přístup k webovému rozhraní API sama za sebe (bez kontextu uživatele). Tento typ oprávnění vyžaduje souhlas správce a navíc není k dispozici pro veřejné (desktopové a mobilní) klientské aplikace.
* **Delegovaná oprávnění** – Klientská aplikace vyžaduje přístup k webovému rozhraní API jako přihlášený uživatel, ale s přístupem omezeným vybraným oprávněním. Pokud tento typ oprávnění nevyžaduje souhlas správce, může ho udělit uživatel.

  > [!NOTE]
  > Přidání delegovaného oprávnění do aplikace neuděluje uživatelům v tenantovi souhlas automaticky. Pokud správce neudělí souhlas jménem všech uživatelů, musí uživatelé stále souhlas pro delegovaná oprávnění přidaná za běhu udělit ručně.

V tomto rychlém startu si ukážeme, jak nakonfigurovat aplikaci následujícím způsobem:

* [Přidání identifikátorů URI pro přesměrování do aplikace](#add-redirect-uris-to-your-application)
* [Přidání přihlašovacích údajů do webové aplikace](#add-credentials-to-your-web-application)
* [Přidání oprávnění pro přístup k webovým rozhraním API](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Přečtěte si o podporovaných [oprávněních a souhlasu](v2-permissions-and-consent.md), kterým je důležité rozumět při vytváření aplikací, které budou používat jiní uživatelé či jiné aplikace.
* Máte tenanta, ke kterému jsou zaregistrované aplikace.
  * Pokud nemáte žádné zaregistrované aplikace, [přečtěte si o registraci aplikací na platformě Microsoft Identity Platform](quickstart-register-app.md).
* Na webu Azure Portal jste se přihlásili k prostředí pro registrace aplikací ve verzi Preview. Kroky v tomto rychlém startu odpovídají novému uživatelskému rozhraní a fungují pouze v případě, že jste se přihlásili k prostředí ve verzi Preview.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

Než budete moct nakonfigurovat aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview)**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** neboli hlavní stránka registrace dané aplikace.
1. Podle následujícího postupu nakonfigurujte aplikaci pro přístup k webovým rozhraním API: 
    * [Přidání identifikátorů URI pro přesměrování do aplikace](#add-redirect-uris-to-your-application)
    * [Přidání přihlašovacích údajů do webové aplikace](#add-credentials-to-your-web-application)
    * [Přidání oprávnění pro přístup k webovým rozhraním API](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Přidání identifikátorů URI pro přesměrování do aplikace

[![Přidání vlastních identifikátorů URI pro přesměrování pro webové a veřejné klientské aplikace](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Přidání identifikátorů URI pro přesměrování do aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Ověřování**.

1. Pokud chcete přidat vlastní identifikátor URI pro přesměrování pro webové a veřejné klientské aplikace, postupujte následovně:

    1. Vyhledejte část **Identifikátor URI pro přesměrování**.
    1. Vyberte typ aplikace, kterou vytváříte: **Web** nebo **Veřejný klient (mobilní a desktopová zařízení)**.
    1. Zadejte URI pro přesměrování pro vaši aplikaci.
        * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například http://localhost:31544 může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
        * V případě veřejných aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikaci, například https://MyFirstApp.

1. Pokud si chcete vybrat z navrhovaných identifikátorů URI pro veřejné klienty (mobilní, desktopové), postupujte následovně:

    1. Vyhledejte část **Navrhované identifikátory URI pro přesměrování pro veřejné klienty (mobilní, desktopové)**.
    1. Pomocí zaškrtávacích políček vyberte odpovídající identifikátory URI pro přesměrování pro vaši aplikaci.

## <a name="add-credentials-to-your-web-application"></a>Přidání přihlašovacích údajů do webové aplikace

[![Přidání certifikátů a tajných klíčů klienta](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Přidání přihlašovacích údajů do webové aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Certifikáty a tajné klíče**.

1. Pokud chcete přidat certifikát, postupujte následovně:

    1. Vyberte **Nahrát certifikát**.
    1. Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
    1. Vyberte **Přidat**.

1. Pokud chcete přidat tajný klíč klienta, postupujte následovně:

    1. Vyberte **Nový tajný klíč klienta**.
    1. Přidejte popis tajného klíče klienta.
    1. Vyberte dobu trvání.
    1. Vyberte **Přidat**.

> [!NOTE]
> Po uložení změn konfigurace bude krajní pravý sloupec obsahovat hodnotu tajného klíče klienta. **Nezapomeňte si tuto hodnotu zkopírovat**, abyste ji mohli použít v kódu klientské aplikace, protože jinak se k ní už nedostanete, když tuto stránku opustíte.

## <a name="add-permissions-to-access-web-apis"></a>Přidání oprávnění pro přístup k webovým rozhraním API

[![Přidání oprávnění rozhraní API](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Přidání oprávnění pro přístup k rozhraní API prostředků z klienta:

1. Na stránce **Přehled** aplikace vyberte **Oprávnění rozhraní API**.
1. Vyberte tlačítko **Přidat oprávnění**.
1. Ve výchozím nastavení si v tomto zobrazení můžete vybrat z **rozhraní API Microsoftu**. Vyberte sekci rozhraní API, která vás zajímají:
    * **Rozhraní API Microsoftu** – Umožňuje vybrat oprávnění pro rozhraní API Microsoftu, jako je Microsoft Graph.
    * **Rozhraní API, která používá moje organizace** – Umožňuje vybrat oprávnění pro rozhraní API zveřejněná vaší organizací nebo rozhraní API, která vaše organizace integrovala.
    * **Moje rozhraní API** – Umožňuje vybrat oprávnění pro rozhraní API, která jste zveřejnili.
1. Jakmile vyberete rozhraní API, zobrazí se stránka **Požádat o oprávnění rozhraní API**. Pokud rozhraní API zveřejňuje delegovaná oprávnění i oprávnění aplikace, vyberte, který typ oprávnění vaše aplikace potřebuje.
1. Až budete hotovi, vyberte **Přidat oprávnění**. Vrátíte se na stránku **Oprávnění rozhraní API**, kde se v tabulce zobrazí uložená a přidaná oprávnění.

## <a name="next-steps"></a>Další postup

Další informace najdete v těchto rychlých startech souvisejících se správou aplikací:

* [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
* [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
* [Odebrání aplikace zaregistrované na platformě Microsoft Identity Platform](quickstart-remove-app.md)

Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
