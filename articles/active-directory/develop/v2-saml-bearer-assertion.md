---
title: Platforma identity společnosti Microsoft & tok kontrolního výrazu nosiče SAML | Azure
description: Zjistěte, jak načíst data z microsoft graphu bez zobrazení výzvy uživateli k použití toku kontrolního výrazu nosiče SAML.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886173"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Platforma identity Microsoft a OAuth 2.0 SAML tok kontrolního výrazu nosiče
Tok kontrolního výrazu nosiče OAuth 2.0 SAML umožňuje požádat o přístupový token OAuth pomocí kontrolního výrazu SAML, když klient potřebuje použít existující vztah důvěryhodnosti. Podpis použitý na kontrolní výraz SAML poskytuje ověřování autorizované aplikace. Kontrolní výraz SAML je token zabezpečení XML vydaný poskytovatelem identity a spotřebovaný poskytovatelem služeb. Poskytovatel služeb spoléhá na svůj obsah při identifikaci předmětu kontrolního výrazu pro účely související se zabezpečením.

Kontrolní výraz SAML je zaúčtován do koncového bodu tokenu OAuth.  Koncový bod zpracuje kontrolní výraz a vydá přístupový token na základě předchozího schválení aplikace. Klient není nutné mít nebo ukládat obnovovací token, ani je tajný klíč klienta musí být předány koncovému bodu tokenu.

Tok kontrolního výrazu saml je užitečný při načítání dat z rozhraní API aplikace Microsoft Graph (která podporují pouze delegovaná oprávnění) bez zobrazení výzvy uživateli k získání pověření. V tomto scénáři udělení pověření klienta, který je upřednostňován pro procesy na pozadí, nefunguje.

Pro aplikace, které interaktivní přihlášení založené na prohlížeči získat kontrolní výraz SAML a pak chcete přidat přístup k rozhraní API chráněné OAuth (například Microsoft Graph), můžete provést žádost OAuth získat přístupový token pro rozhraní API. Když je prohlížeč přesměrován na Azure AD k ověření uživatele, prohlížeč převezme relaci z přihlášení SAML a uživatel nemusí zadávat své přihlašovací údaje.

Tok kontrolního výrazu OAuth SAML je také podporován pro uživatele, kteří ověřují s poskytovateli identit, jako je služba ADFS (Active Directory) federovaná do služby Azure Active Directory.  Kontrolní výraz SAML získaný z adfs lze použít v toku OAuth k ověření uživatele.

![OAuth tok](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Volání grafu pomocí kontrolního výrazu nositele SAML
Nyní pojďme pochopit, jak můžeme skutečně přinést SAML tvrzení programově. Tento přístup je testován pomocí adfs. To však funguje s libovolným zprostředkovatelem identity, který podporuje vrácení výrazu SAML programově. Základní proces je: získat kontrolní výraz SAML, získat přístupový token a přístup k microsoft graphu.

### <a name="prerequisites"></a>Požadavky

Vytvořte vztah důvěryhodnosti mezi autorizačním serverem/prostředím (Microsoft 365) a poskytovatelem identity nebo vystavitelem kontrolního výrazu nosiče SAML 2.0 (ADFS). Chcete-li nakonfigurovat službu ADFS pro jednotné přihlašování a jako zprostředkovatele identity, můžete odkazovat na [tento článek](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Zaregistrujte aplikaci na [portálu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Přihlaste se k [portálu pro registraci aplikace](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Upozorňujeme, že pro rozhraní Graph API používáme koncové body v2.0 a proto je potřeba aplikaci zaregistrovat na tomto portálu. V opačném případě bychom mohli použít registrace ve službě Azure active directory). 
1. Vyberte **možnost Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace: 
    1. **Název** – Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace.
    1. **Podporované typy účtu** – Vyberte účty, které chcete, aby vaše aplikace podporovala.
    1. **Přesměrování identifikátoru URI (volitelné)** – vyberte typ aplikace, kterou vytváříte, web nebo veřejného klienta (mobilní & plochy) a zadejte identifikátor URI přesměrování (nebo adresu URL odpovědi) pro vaši aplikaci.
    1. Až budete hotovi, vyberte **Zaregistrovat**.
1. Poznamenejte si ID aplikace (klienta).
1. V levém podokně vyberte **certifikáty & tajných kódů**. V části Tajné **klíče klienta** klepněte na **položku Nový tajný klíč klienta.** Zkopírujte nový tajný klíč klienta, nebudete moci načíst při opuštění okna.
1. V levém podokně vyberte **oprávnění rozhraní API** a potom **přidejte oprávnění**. Vyberte **Microsoft Graph**, pak **delegovaná oprávnění**a potom vyberte **Tasks.read,** protože máme v úmyslu použít rozhraní API aplikace Outlook Graph. 

Nainstalujte [Pošťáka](https://www.getpostman.com/), nástroj potřebný k testování požadavků na vzorky.  Později můžete převést požadavky na kód.

### <a name="get-the-saml-assertion-from-adfs"></a>Získání kontrolního výrazu SAML z adfs
Vytvořte požadavek POST do koncového bodu Služby ADFS pomocí obálky SOAP k načtení kontrolního výrazu SAML:

![Získat kontrolní výraz SAML](./media/v2-saml-bearer-assertion/2.png)

Hodnoty záhlaví:

![Hodnoty záhlaví](./media/v2-saml-bearer-assertion/3.png)

Tělo požadavku služby ADFS:

![Tělo požadavku služby ADFS](./media/v2-saml-bearer-assertion/4.png)

Jakmile je tento požadavek úspěšně zaúčtován, měli byste obdržet kontrolní výraz SAML ze služby ADFS. Je vyžadována pouze data značky **SAML:Assertion,** převeďte je na kódování base64 pro použití v dalších požadavcích.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Získání tokenu OAuth2 pomocí kontrolního výrazu SAML 
V tomto kroku načíst token OAuth2 pomocí kontrolní odpověď Služby ADFS.

1. Vytvořte požadavek POST, jak je znázorněno níže s hodnotami záhlaví:

    ![Požadavek POST](./media/v2-saml-bearer-assertion/5.png)
1. V textu požadavku nahraďte **client_id**, **client_secret**a **kontrolní výraz** (kontrolní výraz saml kódovaný na základě základny 64 získaný v předchozím kroku):

    ![Text požadavku](./media/v2-saml-bearer-assertion/6.png)
1. Na základě úspěšné žádosti obdržíte přístupový token ze služby Azure active directory.

### <a name="get-the-data-with-the-oauth-token"></a>Získání dat pomocí tokenu Oauth

Po obdržení přístupového tokenu volejte grafová api (v tomto příkladu úkoly aplikace Outlook). 

1. Vytvořte požadavek GET s přístupovým tokenem načteným v předchozím kroku:

    ![Požadavek GET](./media/v2-saml-bearer-assertion/7.png)

1. Na základě úspěšné žádosti obdržíte odpověď JSON.

## <a name="next-steps"></a>Další kroky

Informace o různých [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md).