---
title: Poradce při potížích s vlastními zásadami ve službě Azure Active Directory B2C
description: Přečtěte si o přístupech k řešení chyb při práci s vlastními zásadami ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186352"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Poradce při potížích s vlastními zásadami Azure AD B2C a architekturou prostředí identit

Pokud používáte vlastní zásady Azure Active Directory B2C (Azure AD B2C), může docházet k problémům s nastavením rozhraní Identity Experience Framework ve formátu XML jazyka zásad. Naučit se psát vlastní zásady může být jako naučit se nový jazyk. V tomto článku popisujeme některé nástroje a tipy, které vám pomohou zjistit a vyřešit problémy.

Tento článek se zaměřuje na řešení potíží s konfigurací vlastních zásad Azure AD B2C. Neřeší aplikaci předávající strany nebo její knihovnu identit.

## <a name="xml-editing"></a>Úpravy XML

Nejčastější chybou při nastavování vlastních zásad je nesprávně formátovaný kód XML. Dobrý editor XML je téměř zásadní. Zobrazuje XML nativně, obsah barevných kódů, předvyplnění běžných termínů, udržuje indexované elementy XML a může být ověřen proti schématu XML.

Dva z našich oblíbených editorů jsou [Visual Studio Code](https://code.visualstudio.com/) a [Notepad++](https://notepad-plus-plus.org/).

Ověření schématu XML identifikuje chyby před odesláním souboru XML. V kořenové složce [počátečního balíčku](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)získáte soubor definice schématu XML *TrustFrameworkPolicy_0.3.0.0.xsd*. Chcete-li zjistit, jak používat soubor schématu XSD pro ověření v editoru, vyhledejte *nástroje XML* a *ověření XML* nebo podobně v dokumentaci editoru.

Může vám být užitečná recenze pravidel XML. Azure AD B2C odmítne všechny chyby formátování XML, které zjistí. V některých případě nesprávně formátovaný kód XML může způsobit chybové zprávy, které jsou zavádějící.

## <a name="upload-policies-and-policy-validation"></a>Nahrát zásady a ověření zásad

Ověření souboru zásad XML se provádí automaticky při nahrávání. Většina chyb způsobí selhání nahrávání. Ověření zahrnuje soubor zásad, který nahráváte. Obsahuje také řetězec souborů, na které soubor pro nahrávání odkazuje (soubor zásad předávající strany, soubor přípony a základní soubor).

Mezi běžné chyby ověření patří následující:

> Fragment chyby:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Hodnota ClaimType může být chybně napsaná nebo ve schématu neexistuje.
* ClaimType hodnoty musí být definovány alespoň v jednom ze souborů v zásadě.
    Příklad: `<ClaimType Id="issuerUserId">`
* Pokud ClaimType je definována v souboru přípony, ale používá se také v TechnicalProfile hodnotu v základním souboru, nahrávání základní soubor má za následek chybu.

> Fragment chyby:`...makes a reference to a ClaimsTransformation with id...`

* Příčiny této chyby mohou být stejné jako u chyby ClaimType.

> Fragment chyby:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Zkontrolujte, zda hodnota TenantId v `<TrustFrameworkPolicy\>` `<BasePolicy\>` a prvky odpovídají cílovému tenantovi Azure AD B2C.

## <a name="troubleshoot-the-runtime"></a>Poradce při potížích s časem běhu

* Použijte **spustit** `https://jwt.ms` nyní a otestujte své zásady nezávisle na webové nebo mobilní aplikaci. Tato webová stránka se chová jako aplikace předávající strany. Zobrazí obsah webového tokenu JSON (JWT), který je generován zásadami Azure AD B2C.

    Chcete-li vytvořit testovací aplikaci, která může přesměrovat na `https://jwt.ms` pro kontrolu tokenu:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Chcete-li sledovat výměnu zpráv mezi prohlížečem klienta a Azure AD B2C, použijte [Šumař](https://www.telerik.com/fiddler). Může vám pomoci získat informace o tom, kde vaše cesta uživatele selhává v krocích orchestrace.

* V **režimu vývoje**použijte [Application Insights](troubleshoot-with-application-insights.md) ke sledování aktivity cesty uživatele rozhraní Identity Experience Framework. V **režimu vývoje**můžete sledovat výměnu deklarací mezi architekturou prostředí identit a různými zprostředkovateli deklarací identity, které jsou definovány technickými profily, jako jsou poskytovatelé identit, služby založené na rozhraní API, adresář uživatelů Azure AD B2C a další služby, jako je Azure Multi-Factor Authentication.

## <a name="recommended-practices"></a>Doporučené postupy

**Udržujte více verzí scénářů. Seskupte je do projektu s vaší aplikací.** Základní, rozšíření a přepínající strany soubory jsou přímo závislé na sobě navzájem. Uložte je jako skupinu. Při přidávání nových funkcí do zásad udržujte samostatné pracovní verze. Fáze pracovní verze ve vašem vlastním systému souborů s kódem aplikace, které pracují s. Vaše aplikace může vyvolat mnoho různých zásad předávající strany v tenantovi. Mohou být závislé na deklaracích, které očekávají od zásad Azure AD B2C.

**Vyvíjejte a testujte technické profily se známými cestami uživatelů.** K nastavení technických profilů použijte otestované zásady počátečního balíčku. Vyzkoušejte je samostatně, než je začleníte do svých vlastních uživatelských cest.

**Vyvíjejte a testujte cesty uživatelů s testovanými technickými profily.** Postupně změňte kroky orchestrace cesty uživatele. Postupně vytvářejte zamýšlené scénáře.

## <a name="next-steps"></a>Další kroky

K dispozici na GitHubu, stáhněte si [archiv active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip. Můžete také klonovat úložiště:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
