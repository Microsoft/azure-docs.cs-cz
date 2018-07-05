---
title: Řešení potíží s vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o přístup k řešení chyb při práci s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 61f2dfcada0f7e03d8043d9b8f1b6dadf7027c8b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441161"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Řešení potíží s vlastní zásady Azure AD B2C a architekturu rozhraní identit

Pokud používáte Azure Active Directory B2C (Azure AD B2C) vlastní zásady, se mohou vyskytnout problémy při nastavení prostředí architekturu identit v jeho formát jazyka XML zásad.  Učení psaní vlastních zásad může být jako učení nový jazyk. V tomto článku popisujeme nástrojů a tipů, které vám můžou pomoct rychle zjišťovat a řešit problémy. 

> [!NOTE]
> Tento článek se zaměřuje na řešení potíží s vlastních zásad pro konfiguraci Azure AD B2C. Proto se nezabývá aplikaci předávající strany nebo jeho identita knihovny.

## <a name="xml-editing"></a>Úpravy XML

Nejčastěji používané při nastavení vlastních zásad je nesprávně ve formátu XML. Dobré editoru XML je téměř nezbytné. Dobré editoru XML zobrazí XML nativně, barevně označí obsah, prefills běžných termínů, udržuje elementů XML indexovat a ověřit se schématem. Tady jsou dvě z našich oblíbených editory XML:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Poznámkový blok ++](https://notepad-plus-plus.org/)

Ověření schématu XML identifikuje chyby před nahráním souboru XML. V kořenové složce sady starter získáte definici schématu XML TrustFrameworkPolicy_0.3.0.0.xsd. Další informace najdete v dokumentaci k editoru XML, vyhledejte *nástroje XML* a *ověření XML*.

Mohou být užitečné kontroly pravidel XML. Azure AD B2C odmítne všechny zjištěné chyby formátování jazyka XML. V některých případech nesprávný formát XML může způsobit, že chybové zprávy, které jsou zavádějící.

## <a name="upload-policies-and-policy-validation"></a>Nahrát zásady a zásady ověřování

 Ověření nahrání souboru XML je automatické. Většina chyb způsobit, že se nezdaří. Ověření zahrnuje soubor zásad, která nahráváte. Zahrnuje také řetězci souborů, na který odkazuje nahrání souboru (souboru zásad předávající strany, přípony souborů a základního souboru). 
 
 Běžné chyby ověření jsou následující.

Chyba fragment kódu: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* Hodnota typu deklarace identity může být překlep nebo neexistuje ve schématu.
* Hodnoty typu deklarace identity musí být definován v alespoň jeden ze souborů v zásadách. 
    Příklad: ` <ClaimType Id="socialIdpUserId">`
* Pokud typu deklarace identity je definována v souboru rozšíření, ale používá se také hodnotu základního souboru do technický profil, nahrávání základního souboru způsobí chybu.

Chyba fragment kódu: `...makes a reference to a ClaimsTransformation with id...`
* Příčiny chyby může být stejná jako chyba typu deklarace identity.

Chyba fragment kódu: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Zkontrolujte, že hodnota ID Tenanta v **\<TrustFrameworkPolicy\>** a **\<BasePolicy\>** prvky odpovídají vašeho tenanta Azure AD B2C cíl.  

## <a name="troubleshoot-the-runtime"></a>Řešení potíží s modulu runtime

* Použití `Run Now` a `https://jwt.io` k otestování vašich zásad nezávisle na svou webovou nebo mobilní aplikaci. Tento web se chová jako aplikaci předávající strany. Zobrazí obsah z JSON Web Token (JWT), který je generován zásady Azure AD B2C. Vytvoření aplikace testů v rozhraní prostředí pro Identity, použijte následující hodnoty:
    * Název: TestApp
    * Webovou aplikaci/webové rozhraní API: Ne
    * Nativní klient systému: Ne

* Pro sledování výměny zpráv mezi prohlížeče klienta a Azure AD B2C, použijte [Fiddler](http://www.telerik.com/fiddler). Pomůže vám zajistit indikaci, kde se nedaří vaší cesty uživatele v postupu k orchestraci.

* V **vývojový režim**, použijte **Application Insights** trasování aktivity vaší cesty uživatele architekturu rozhraní identit. V **vývojový režim**, můžete sledovat výměny deklarací identity mezi rozhraní prostředí pro Identity a různých zprostředkovatelů deklarací identity, které jsou definovány technické profily, jako je například zprostředkovatelů identity, služby založené na rozhraní API Adresář uživatele Azure AD B2C a dalším službám, jako je Azure více ověřování Multi-Factor-Authentication.  

## <a name="recommended-practices"></a>Doporučené postupy

**Uchovat více verzí scénáře. Seskupte v projektu s vaší aplikací.** Základ, rozšíření a předávající strany soubory jsou přímo na sobě navzájem závislé. Uložte jako skupinu. Při přidání nových funkcí do zásad, zachovejte samostatné pracovní verze. Fáze pracovní verze ve svém vlastním systému s kódem aplikace, které interagují s souborů.  Vaše aplikace může vyvolat mnoho různých předávající strana zásad v tenantovi. Že jsou závislé na deklaracích identity, které očekávají na základě zásad Azure AD B2C.

**Vyvíjejte a testujte technické profily s jízdy známé uživatele.** Pomocí zásad otestované starter pack nastavit technické profily. Testování je samostatně před začlenit do vlastní cesty uživatele.

**Vyvíjejte a testujte cesty uživatele s otestované technické profily.** Změňte kroky Orchestrace cesty uživatele postupně. Postupnému vytváření zamýšlený scénáře.

## <a name="next-steps"></a>Další postup

* V Githubu, stáhněte si [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) soubor .zip.
