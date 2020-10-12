---
title: Microsoft Identity Platform & tok kontrolního výrazu SAML nosiče | Azure
description: Naučte se, jak načíst data z Microsoft Graph bez vyzvání uživatele k zadání přihlašovacích údajů pomocí toku kontrolního výrazu SAML.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 6e7e4dd6383b1f264ff2da7893d9f86a3708217d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89227912"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity Platform a OAuth 2,0 – tok kontrolního výrazu SAML
Tok kontrolního výrazu SAML 2,0 OAuth vám umožní požádat o přístupový token OAuth pomocí kontrolního výrazu SAML, když klient potřebuje použít stávající vztah důvěryhodnosti. Signatura použitá pro kontrolní výraz SAML zajišťuje ověřování autorizované aplikace. Kontrolní výraz SAML je token zabezpečení XML vydaný zprostředkovatelem identity a spotřebovaný poskytovatelem služeb. Poskytovatel služeb spoléhá na jeho obsah, aby identifikoval Předmět kontrolního výrazu pro účely související se zabezpečením.

Kontrolní výraz SAML je publikovaný do koncového bodu tokenu OAuth.  Koncový bod zpracuje kontrolní výraz a vydá přístupový token na základě předchozího schválení aplikace. Klient nevyžaduje nebo neuloží obnovovací token, ani není nutné předat tajný klíč klienta k koncovému bodu tokenu.

Tok kontrolního výrazu SAML je užitečný při načítání dat z rozhraní API Microsoft Graph (která podporují jenom delegovaná oprávnění) bez vyzvání uživatele k zadání přihlašovacích údajů. V tomto scénáři nefunguje udělení přihlašovacích údajů klienta, které je upřednostňováno pro procesy na pozadí.

Pro aplikace, které umožňují interaktivní přihlášení založené na prohlížeči pro získání kontrolního výrazu SAML a pak chcete přidat přístup k rozhraní API Protected OAuth (například Microsoft Graph), můžete vytvořit žádost OAuth pro získání přístupového tokenu pro rozhraní API. Když je prohlížeč přesměrován do služby Azure AD za účelem ověření uživatele, prohlížeč zahájí relaci z přihlášení SAML a uživatel nebude muset zadávat své přihlašovací údaje.

Tok kontrolního výrazu protokolu SAML protokolu OAuth je podporován pouze pro uživatele, kteří se ověřují pomocí zprostředkovatelů identity, jako je například Active Directory Federation Services (AD FS) (ADFS) Azure Active Directory.  Kontrolní výraz SAML získaný ze služby AD FS se dá použít v toku OAuth k ověření uživatele.

![Tok OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Graf volání pomocí kontrolního výrazu SAML nosiče
Teď nám dejte vědět, jak můžeme skutečně načíst kontrolní výraz SAML programově. Tento přístup se testuje pomocí služby AD FS. To ale funguje u libovolného poskytovatele identity, který podporuje návrat programově kontrolního výrazu SAML. Základní proces je: Získání kontrolního výrazu SAML, získání přístupového tokenu a přístup k Microsoft Graph.

### <a name="prerequisites"></a>Požadavky

Navažte vztah důvěryhodnosti mezi autorizačním serverem/prostředím (Microsoft 365) a poskytovatelem identity nebo vystavitelem kontrolního výrazu SAML 2,0 (ADFS). Pokud chcete nakonfigurovat službu AD FS pro jednotné přihlašování a jako poskytovatele identity, můžete se na [Tento článek](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365)podívat.

Zaregistrovat aplikaci na [portálu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Přihlaste se do okna [Registrace aplikace portálu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Všimněte si, že používáme koncové body v 2.0 pro Graph API, a proto je potřeba zaregistrovat aplikaci na tomto portálu. V opačném případě jsme mohli použít registrace ve službě Azure Active Directory). 
1. Vyberte **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace: 
    1. **Název** – Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace.
    1. **Podporované typy účtu** – Vyberte účty, které chcete, aby vaše aplikace podporovala.
    1. **Identifikátor URI pro přesměrování (volitelné)** – vyberte typ aplikace, kterou vytváříte, web nebo veřejný klient (Mobile & Desktop), a pak zadejte identifikátor URI přesměrování (nebo adresu URL odpovědi) pro vaši aplikaci.
    1. Až budete hotovi, vyberte **Zaregistrovat**.
1. Poznamenejte si ID aplikace (klienta).
1. V levém podokně vyberte **certifikáty & tajných**kódů. V části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** . Zkopírování nového tajného klíče klienta nebudete moct načíst, když necháte okno opustit.
1. V levém podokně vyberte **oprávnění rozhraní API** a pak **přidejte oprávnění**. Vyberte **Microsoft Graph**, pak **delegovaná oprávnění**a pak vyberte **úkoly. číst** , protože hodláte používat Graph API Outlooku. 

Nainstalovat [post](https://www.getpostman.com/), nástroj potřebný k otestování ukázkových požadavků.  Později můžete převést požadavky na kód.

### <a name="get-the-saml-assertion-from-adfs"></a>Získání kontrolního výrazu SAML ze služby ADFS
Vytvoření požadavku POST na koncový bod služby ADFS pomocí obálky SOAP pro načtení kontrolního výrazu SAML:

![Získat kontrolní výraz SAML](./media/v2-saml-bearer-assertion/2.png)

Hodnoty hlaviček:

![Hodnoty hlaviček](./media/v2-saml-bearer-assertion/3.png)

Tělo žádosti ADFS:

![Tělo žádosti ADFS](./media/v2-saml-bearer-assertion/4.png)

Po úspěšném odeslání této žádosti byste měli obdržet kontrolní výraz SAML ze služby AD FS. Je požadována pouze data značky **SAML: assertion** , převeďte je na kódování Base64, aby bylo možné je použít v dalších požadavcích.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Získání tokenu OAuth2 pomocí kontrolního výrazu SAML 
V tomto kroku načtete token OAuth2 pomocí odpovědi kontrolního výrazu ADFS.

1. Vytvořte požadavek POST, jak je znázorněno níže s hodnotami hlaviček:

    ![POST – požadavek](./media/v2-saml-bearer-assertion/5.png)
1. V těle žádosti nahraďte **client_id**, **client_secret**a **kontrolní výraz** (kontrolní výraz SAML kódovaný jako base64 získal předchozí krok):

    ![Text požadavku](./media/v2-saml-bearer-assertion/6.png)
1. Po úspěšné žádosti obdržíte přístupový token z Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Získání dat pomocí tokenu OAuth

Po přijetí přístupového tokenu volejte rozhraní API grafu (v tomto příkladu úkoly Outlooku). 

1. Vytvořte žádost o získání pomocí přístupového tokenu načteného v předchozím kroku:

    ![Požadavek GET](./media/v2-saml-bearer-assertion/7.png)

1. Po úspěšné žádosti se zobrazí odpověď JSON.

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md).
