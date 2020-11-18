---
title: Řešení potíží s vlastními zásadami v Azure Active Directory B2C
description: Přečtěte si o přístupech k řešení chyb při práci s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840249"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Řešení potíží s Azure AD B2C vlastní zásady a architektura prostředí identit

Pokud používáte vlastní zásady Azure Active Directory B2C (Azure AD B2C), může docházet k problémům s nastavením architektury identity pro prostředí identity ve formátu XML v jazyku zásad. Seznámení s vytvářením vlastních zásad může být jako učení nového jazyka. V tomto článku popisujeme některé nástroje a tipy, které vám pomůžou zjistit a vyřešit problémy.

Tento článek se zaměřuje na řešení potíží s konfigurací vlastní zásady Azure AD B2C. Neřeší aplikaci předávající strany ani její knihovnu identit.

## <a name="xml-editing"></a>Úpravy XML

Nejběžnější Chyba při nastavování vlastních zásad je nesprávně naformátované XML. Dobrý editor XML je skoro nezbytný. Zobrazuje XML nativně, obsah barevných kódů, předem vyplní běžné výrazy, uchovává elementy XML indexované a může ověřit proti schématu XML.

Dva z oblíbených editorů jsou [Visual Studio Code](https://code.visualstudio.com/) a [Poznámkový blok + +](https://notepad-plus-plus.org/).

Ověřování schématu XML identifikuje chyby před nahráním souboru XML. V kořenové složce [Úvodní sady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)Získejte soubor definice schématu XML *TrustFrameworkPolicy_0.3.0.0. xsd*. Chcete-li zjistit, jak použít soubor schématu XSD k ověření v editoru, hledejte *nástroje XML* a *ověřování XML* nebo podobné v dokumentaci editoru.

Můžete najít užitečné přezkoumání pravidel XML. Azure AD B2C odmítne všechny chyby formátování XML, které detekuje. Nesprávně formátovaný kód XML může občas způsobit chybové zprávy, které jsou zavádějící.

## <a name="upload-policies-and-policy-validation"></a>Nahrávání zásad a ověřování zásad

Ověření souboru zásad XML se provádí automaticky při nahrání. Většina chyb způsobí selhání nahrávání. Ověření zahrnuje soubor zásad, který nahráváte. Zahrnuje také řetězec souborů, na které se nahrát soubor odkazuje (soubor zásad předávající strany, soubor rozšíření a základní soubor).

K běžným chybám ověření patří následující:

> Fragment chyby: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Hodnota ClaimType může být nesprávně napsaná nebo ve schématu neexistuje.
* Hodnoty ClaimType musí být definovány alespoň v jednom ze souborů v zásadě.
    Příklad: `<ClaimType Id="issuerUserId">`
* Pokud je v souboru rozšíření definována deklarace ClaimType, ale používá se také v hodnotě TechnicalProfile základního souboru, výsledkem odeslání základního souboru je chyba.

> Fragment chyby: `...makes a reference to a ClaimsTransformation with id...`

* Příčiny této chyby mohou být stejné jako u chyby ClaimType.

> Fragment chyby: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Ověřte, že hodnota TenantId v `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementech a odpovídá cílovému tenantovi Azure AD B2C.

## <a name="troubleshoot-the-runtime"></a>Řešení potíží s modulem runtime

* Pomocí rutiny **Spustit nyní** a `https://jwt.ms` otestujte zásady nezávisle na vaší webové nebo mobilní aplikaci. Tento web funguje jako aplikace předávající strany. Zobrazuje obsah tokenu JWT (JSON web token), který vygenerovala vaše zásada Azure AD B2C.

    Vytvoření testovací aplikace, která se může přesměrovat na `https://jwt.ms` pro kontrolu tokenu:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Chcete-li trasovat výměnu zpráv mezi prohlížečem klienta a Azure AD B2C, použijte [Fiddler](https://www.telerik.com/fiddler). Může vám získat informace o tom, kde se v krocích orchestrace vaše uživatelská cesta nedaří.

* V **režimu vývoje** použijte [Application Insights](troubleshoot-with-application-insights.md) ke sledování aktivity cesty uživatele architektury identity Experience Framework. V **režimu vývoje** můžete sledovat výměnu deklarací identity mezi architekturou prostředí identit a různými poskytovateli deklarací identity, které jsou definovány technickými profily, jako jsou poskytovatelé identity, služby založené na rozhraní API, Azure AD B2C adresář uživatelů a další služby, jako je Azure AD Multi-Factor Authentication.

## <a name="recommended-practices"></a>Doporučené postupy

**Udržujte si více verzí vašich scénářů. Seskupte je v projektu s vaší aplikací.** Základní, rozšíření a soubory předávající strany jsou přímo závislé na sobě. Uložte je jako skupinu. Při přidání nových funkcí do zásad mějte samostatné pracovní verze. Pracovní verze připravené ve vlastním systému souborů s kódem aplikace, se kterou pracují. Vaše aplikace mohou v tenantovi vyvolat mnoho různých zásad předávající strany. Můžou být závislé na deklaracích, které očekávají od vašich zásad Azure AD B2C.

**Vývoj a testování technických profilů se známými cestami uživatelů.** K nastavení vašich technických profilů použijte testované zásady počátečního packu. Otestujte je samostatně, než je zahrňte do vlastní cesty uživatelů.

**Vývoj a testování cest uživatelů pomocí testovaných technických profilů.** Přírůstkové změny kroků orchestrace cesty uživatele Postupně Sestavte zamýšlené scénáře.

## <a name="next-steps"></a>Další kroky

K dispozici na GitHubu, Stáhněte si archiv [Active-Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip. Můžete také klonovat úložiště:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
