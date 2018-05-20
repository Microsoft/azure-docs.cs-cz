---
title: Pomocí stáří prostřednictvím brány v Azure AD B2C | Microsoft Docs
description: Další informace o tom, jak identifikovat nezletilých pomocí vaší aplikace.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 9186579126525cc269f7e3f9e778e06902b30eb4
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Pomocí stáří prostřednictvím brány v Azure AD B2C

>[!IMPORTANT]
>Tato funkce je v privátní Preview verzi.  Najdete v tématu naše [služby blog](https://blogs.msdn.microsoft.com/azureadb2c/) podrobnosti jako to bude k dispozici, nebo požádejte AADB2CFeedback@microsoft.com.  Nepoužívejte to na produkční adresářů, pomocí tyto nové funkce může způsobit ztrátu dat a může mít neočekávané změny v chování dokud jsme přejděte do obecné dostupnosti.  
>

##<a name="age-gating"></a>Stáří prostřednictvím brány
Stáří prostřednictvím brány, můžete použít k identifikaci nezletilých v aplikaci Azure AD B2C.  Můžete blokovat uživateli přihlášení k aplikaci nebo mohly přejděte zpět do aplikace s další deklarace identity, které identifikují skupiny stáří uživatele a jejich stav rodičovský souhlas.  

>[!NOTE]
>Rodičovský souhlas sledován v uživatelský atribut názvem `consentProvidedForMinor`.  Tato vlastnost prostřednictvím rozhraní Graph API můžete aktualizovat a při aktualizaci v tomto poli použije `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Nastavení adresáře pro stáří prostřednictvím brány
Chcete-li použít stáří prostřednictvím brány v toku uživatele, musíte nakonfigurovat adresáře mít další vlastnosti. Tuto operaci lze provést prostřednictvím `Properties` v nabídce (která bude k dispozici pouze v případě, že máte součástí soukromém náhledu).  
1. V rozšíření Azure AD B2C, klikněte na **vlastnosti** pro vašeho klienta v nabídce na levé straně.
2. V části **prostřednictvím brány stáří** části, klikněte na **konfigurace** tlačítko.
3. Počkejte na dokončení operace a adresáře se nastavit pro stáří prostřednictvím brány.

##<a name="enabling-age-gating-in-your-user-flow"></a>Povolení stáří prostřednictvím brány v uživatelskému toku
Po adresáře jsou nastaveny na použití stáří prostřednictvím brány, potom můžete tuto funkci toky uživatele verze preview.  Tato funkce vyžaduje změny, které není kompatibilní s existující typy toků uživatele.  Povolíte stáří prostřednictvím brány pomocí následujících kroků:
1. Vytvořte uživatelskému toku preview.
2. Po vytvoření, přejděte na **vlastnosti** v nabídce.
3. V **prostřednictvím brány stáří** tématu, stiskněte přepínač k povolení této funkce.
4. Můžete pak, jak chcete spravovat uživatele, které identifikují jako nezletilých.

##<a name="what-does-enabling-age-gating-do"></a>Povolení stáří prostřednictvím brány k čemu slouží?
Jakmile stáří prostřednictvím brány je v uživatelskému toku povolená, uživatele změny.  Na registrace zobrazí se uživatelům dotaz teď pro svoje datum narození a země bydliště společně s atributy uživatele, který je nakonfigurovaný pro tok uživatele.  Na přihlášení uživatelé, kteří nezadali dříve, datum narození a zemi, kde bydlíte vyzváni k tato informace při příštím přihlášení.  Z těchto dvou hodnot, bude Azure AD B2C zjistit, jestli je uživatel dílčím a aktualizovat `ageGroup` v poli hodnota může být `null`, `Undefined`, `Minor`, `Adult`, a `NotAdult`.  `ageGroup` a `consentProvidedForMinor` pole se pak použije k výpočtu `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Stáří, možnosti prostřednictvím brány
Můžete mít Azure AD B2C bloku nezletilých bez rodičovský souhlas nebo jim povolit a mít aplikace rozhodnutí v co dělat s nimi.  

###<a name="allowing-minors-without-parental-consent"></a>Povolení nezletilých bez rodičovský souhlas
Toky uživatele, které povolit buď přihlašovací nahoru, přihlaste se nebo obojí můžete zvolit, aby nezletilých bez souhlasu do vaší aplikace.  Pro nezletilých bez rodičovský souhlas, se můžou přihlásit nebo zaregistrovat jako normální a Azure AD B2C vydá token ID s `legalAgeGroupClassification` deklarací identity.  Pomocí této deklarace identity, můžete zvolit prostředí, které tito uživatelé mají například průchodu přes prostředí shromažďovat rodičovský souhlas (a aktualizovat `consentProvidedForMinor` pole).

###<a name="blocking-minors-without-parental-consent"></a>Blokování nezletilých bez rodičovský souhlas
Toky uživatele, které povolit buď přihlašovací nahoru, přihlaste se nebo obojí můžete blokovat nezletilých bez souhlasu z aplikace.  Existují dvě možnosti pro zpracování blokovaným uživatelům v Azure AD B2C:
* Zpět do aplikace odeslat JSON - tato možnost odešle odpověď zpět do aplikace, že byl zablokován podverzí.
* Zobrazit chybovou stránku - uživateli se zobrazí stránka s informací, že nemají přístup k aplikaci

##<a name="known-issues"></a>Známé problémy
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Formát pro odpověď při blokování podverzí.
Odpověď aktuálně není správně vytvořen, tato chyba bude vyřešen v nadcházejících aktualizace.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Odstranění konkrétní atributy, které byly přidány během instalace můžete nastavit adresáře nemůže použít stáří prostřednictvím brány.
V instalačním programu pro stáří prostřednictvím brány, jste nakonfigurovali adresáře prostřednictvím možnost ve vaší `Properties`.  Pokud odstraníte buď `legalCountry` nebo `dateOfBirth` prostřednictvím grafu, adresáře již nelze používat stáří prostřednictvím brány a tyto vlastnosti nemůže být znovu vytvořen.

###<a name="list-of-countries-is-incomplete"></a>Seznam zemí, je nekompletní
Aktuálně je neúplný seznam zemí, pro legalCountry, přidáme ostatních zemí v příští aktualizaci.