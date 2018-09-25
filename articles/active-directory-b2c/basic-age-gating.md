---
title: Používat ověřování věku v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak identifikovat nezletilým vaši aplikaci používají.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056518"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Používat ověřování věku v Azure AD B2C

>[!IMPORTANT]
>Tato funkce je ve verzi private preview.  Podrobnosti najdete na naší [blog služby](https://blogs.msdn.microsoft.com/azureadb2c/) podrobnosti, jako to bude k dispozici, nebo se obraťte AADB2CPreview@microsoft.com.  Nepoužívejte to o adresářích produkčního prostředí, pomocí těchto nových funkcích může vést ke ztrátě dat a může mít neočekávaným změnám v chování, až přejdeme do všeobecné dostupnosti.  
>

##<a name="age-gating"></a>Věku
Věku umožňuje používat k identifikaci nezletilé osoby ve vaší aplikaci Azure AD B2C.  Můžete nastavit blokování uživatele z přihlášení k aplikaci nebo zajistí, aby přejděte zpět do aplikace s další deklarace identity, které identifikují uživatele kategorie age group a jejich stav svolení rodičů.  

>[!NOTE]
>Svolení rodičů je sledována v atributu uživatele volá `consentProvidedForMinor`.  Můžete aktualizovat tuto vlastnost prostřednictvím rozhraní Graph API a použije toto pole při aktualizaci `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Nastavení v adresáři věku
Chcete-li použít věku v toku uživatele, budete muset nastavení konfigurace adresáře mít další vlastnosti. Tuto operaci lze provést prostřednictvím `Properties` v nabídce (což bude k dispozici pouze v případě, že jste součástí privátní verze preview).  
1. V rozšíření Azure AD B2C, klikněte na **vlastnosti** pro vašeho tenanta v nabídce na levé straně.
2. V části **věku** části, klikněte na **konfigurovat** tlačítko.
3. Počkejte na dokončení operace a nastaví adresář se věku.

##<a name="enabling-age-gating-in-your-user-flow"></a>Povolení věku ve svém toku uživatele
Po použití věku nastavení adresáře, se pak můžete tuto funkci v tocích uživatelů verze preview.  Tato funkce vyžaduje změny, které nemusel být kompatibilní s existující typy toky uživatelů.  Povolíte věku pomocí následujících kroků:
1. Vytvořte tok uživatele ve verzi preview.
2. Po jeho vytvoření, přejděte na **vlastnosti** v nabídce.
3. V **věku** části, stiskněte klávesu tímto přepínačem můžete povolit funkci.
4. Můžete pak, jak chcete spravovat uživatele, které identifikují jako nezletilé osoby.

##<a name="what-does-enabling-age-gating-do"></a>Povolení věku k čemu slouží?
Po věku povolení ve svém toku uživatele uživatelské prostředí změny.  V registraci zobrazí se uživatelům dotaz nyní pro svoje datum narození a zemi, kde bydlíte spolu s atributy uživatele, který je nakonfigurovaný pro tok uživatele.  Přihlašování vyzve uživatele, kteří ještě dříve zadané datum narození a zemi, kde bydlíte pro tyto informace při příštím přihlášení.  Z těchto dvou hodnot, bude Azure AD B2C zjistit, jestli je uživatel za a aktualizovat `ageGroup` pole, hodnota může být `null`, `Undefined`, `Minor`, `Adult`, a `NotAdult`.  `ageGroup` a `consentProvidedForMinor` pole se následně použijí k výpočtu `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Možnosti ověřování věku.
Můžete mít Azure AD B2C zablokovat nezletilým bez svolení rodičů nebo jim povolit a máte aplikaci rozhodování o tom, jak s nimi dělat.  

###<a name="allowing-minors-without-parental-consent"></a>Povolení nezletilé osoby bez svolení rodičů
Toky uživatelů, které povolit buď sign up, přihlaste se nebo obojí můžete zvolit, aby nezletilé osoby bez svolení do vaší aplikace.  Pro nezletilé osoby bez svolení rodičů, jim byl povolen se přihlaste nebo zaregistrujte normální a službou Azure AD B2C vydá token ID s `legalAgeGroupClassification` deklarací identity.  Pomocí této deklarace identity můžete použít prostředí, které mají tito uživatelé například přejdete prostřednictvím prostředí pro shromažďování svolení rodičů (a aktualizovat `consentProvidedForMinor` pole).

###<a name="blocking-minors-without-parental-consent"></a>Zablokování nezletilé osoby bez svolení rodičů
Toky uživatelů, které povolit buď sign up, přihlaste se nebo obojí můžete zablokovat nezletilým bez souhlasu z aplikace.  Existují dvě možnosti pro zpracování blokovaným uživatelům v Azure AD B2C:
* Poslat JSON zpět do aplikace – tato možnost pošle odpověď zpět do aplikace, za byl zablokován.
* Zobrazit chybovou stránku - uživateli se zobrazí stránka s informací, že nemají přístup k aplikaci