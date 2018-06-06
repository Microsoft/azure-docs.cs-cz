---
title: Řešení potíží s vlastní zásady v Azure Active Directory B2C | Microsoft Docs
description: Další informace o přístupy k řešení chyb při práci s vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 22e7bc7bd275769f78ac2e482cb1af11e9404222
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709610"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Řešení potíží s Azure AD B2C vlastní zásady a Identity rozhraní Framework

Pokud používáte Azure Active Directory B2C (Azure AD B2C) vlastní zásady, můžete zaznamenat problémy nastavení Identity Framework prostředí v jeho formátu jazyka XML zásad.  Učení psaní vlastních zásad může být třeba učení o nový jazyk. V tomto článku jsme popisují nástrojů a tipů, které vám pomohou rychle zjistit a řešit problémy. 

> [!NOTE]
> Tento článek se zaměřuje na řešení potíží s vlastních zásad pro konfiguraci Azure AD B2C. Není adres, aplikace předávající strany nebo jeho identity knihovny.

## <a name="xml-editing"></a>Úpravy XML

Nejběžnější Chyba v nastavení vlastních zásad je nesprávně formátovaný XML. Dobrý editoru XML je téměř nezbytné. Dobrý editoru XML zobrazí XML nativně, barevně označí obsah, předvyplníte běžné podmínky, udržuje elementů XML indexované a můžete ověřit se schématem. Zde jsou uvedeny dvě naše Oblíbené editory XML:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Poznámkový blok ++](https://notepad-plus-plus.org/)

Ověření schématu XML označuje chyby před nahráním souboru XML. V kořenové složce sady starter získáte definice schématu XML TrustFrameworkPolicy_0.3.0.0.xsd. Další informace najdete v dokumentaci XML editor, vyhledejte *nástroje XML* a *ověření XML*.

Mohou být užitečné kontrolu pravidla XML. Azure AD B2C odmítne všechny XML formátování chyb, které zjistí. V některých případech nesprávný formát XML může způsobit chybové zprávy, které jsou zavádějící.

## <a name="upload-policies-and-policy-validation"></a>Nahrát zásady a ověření zásad

 Ověření nahrávání souboru XML je automaticky. Nejčastější chyby způsobit nahrávání selhání. Ověření zahrnuje soubor zásad, který odesíláte. Zahrnuje také řetězu soubory, které odkazuje souboru k odeslání (soubor zásad předávající strany, soubor rozšíření a základního souboru). 
 
 Běžné chyby ověření, patří.

Fragment kódu chyby: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* Typ ClaimType hodnota může být zadáno chybně nebo neexistuje ve schématu.
* Typ ClaimType hodnoty musí být definován v alespoň jeden ze souborů v zásadách. 
    Příklad: ` <ClaimType Id="socialIdpUserId">`
* Pokud je definován typ ClaimType v souboru rozšíření, ale používá se také hodnotu TechnicalProfile do základního souboru, odesílání základního souboru výsledkem chyba.

Fragment kódu chyby: `...makes a reference to a ClaimsTransformation with id...`
* Příčiny chyby může být stejné jako typ ClaimType chyby.

Fragment kódu chyby: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Zkontrolujte, zda hodnoty TenantId hodnotu **\<TrustFrameworkPolicy\>** a **\<BasePolicy\>** elementy odpovídat tenanta cíl Azure AD B2C.  

## <a name="troubleshoot-the-runtime"></a>Řešení potíží s modulem runtime

* Použití `Run Now` a `https://jwt.io` k otestování vašich zásad nezávisle na váš web nebo mobilní aplikaci. Tento web se chová jako aplikace předávající strany. Zobrazuje obsah z JSON Web Token (JWT) generovaných zásad služby Azure AD B2C. Pokud chcete vytvořit testovací aplikaci v rozhraní Framework Identity, použijte následující hodnoty:
    * Název: TestApp
    * Webovou aplikaci nebo webové rozhraní API: Ne
    * Nativní klient: Ne

* Pokud chcete trasovat výměny zpráv mezi prohlížeče klienta a Azure AD B2C, použijte [Fiddler](http://www.telerik.com/fiddler). Může pomoct vám to znamenat kde nedaří vám dobře slouží uživatele ve vaší kroků Orchestrace.

* V **režimu pro vývoj**, použijte **Application Insights** pro trasování aktivity vám dobře slouží Identity Framework činnost koncového uživatele. V **režimu pro vývoj**, můžete sledovat výměny deklarací identity mezi Framework prostředí Identity a různých zprostředkovatelů deklarací identity, které jsou definovány technické profilů, jako je například poskytovatelů identit, služeb založených na rozhraní API Adresáře uživatele Azure AD B2C a dalším službám, jako například Azure více-Factor-ověřování.  

## <a name="recommended-practices"></a>Doporučené postupy

**Zachovat více verzí vaše scénáře. Seskupte je do projektu s vaší aplikací.** Základní, rozšíření a předávající strany soubory jsou na sobě navzájem přímo závislé. Uložte jako skupinu. Jako nové funkce jsou přidány do zásad, zachovat samostatné pracovní verze. Fáze pracovní verze ve vašem vlastním systému s kódem aplikace, které komunikují s souborů.  Vaše aplikace může vyvolat mnoho různých předávající strany zásad v klientovi. Že jsou závislé na deklaracích identity, které se na základě zásad Azure AD B2C.

**Vývoj a testování technické profily pomocí cesty známé uživatele.** Otestované starter pack zásady slouží k nastavení vaše technické profilů. Test je samostatně předtím, než můžete začlenit do vlastní uživatelské cesty.

**Vývoj a testování cesty uživatele pomocí otestované technické profilů.** Změňte kroků Orchestrace cesty uživatele postupně. Progresivně sestavení určený scénářů.

## <a name="next-steps"></a>Další postup

* V Githubu, stáhněte si [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) soubor .zip.
