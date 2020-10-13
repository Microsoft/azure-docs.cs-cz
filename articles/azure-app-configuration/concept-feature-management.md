---
title: Principy správy funkcí pomocí Azure App Configuration
description: Zapnutí a vypnutí funkcí pomocí konfigurace aplikace Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b77f0063b37adbfaecaff68387e858d0077561b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212656"
---
# <a name="feature-management-overview"></a>Přehled správy funkcí

Při obnově nové funkce aplikace se obvykle vyžaduje úplné opětovné nasazení aplikace. Testování funkce často vyžaduje více nasazení aplikace.  Každé nasazení může změnit funkci nebo zveřejnit tuto funkci různým zákazníkům pro účely testování.  

Správa funkcí je moderní postup vývoje softwaru, který odděluje vydání funkce od nasazení kódu a umožňuje rychlé změny dostupnosti funkcí na vyžádání. Používá techniku nazvanou *příznaky funkcí* (označované také jako *přepínání funkcí*, *přepínače funkcí*atd.) k dynamické správě životního cyklu funkce.

Správa funkcí pomáhá vývojářům řešit následující problémy:

* **Správa větví kódu**: použijte příznaky funkcí k zabalení nových funkcí aplikace, které jsou aktuálně ve vývoji. Tato funkce je ve výchozím nastavení skrytá. Tuto funkci můžete bezpečně dodávat, i když je nedokončená a zůstane neaktivní v produkčním prostředí. Pomocí tohoto přístupu s názvem *tmavě nasazené*můžete uvolnit veškerý kód na konci každého vývojového cyklu. Již nemusíte spravovat větve kódu napříč několika vývojovými cykly, protože daná funkce vyžaduje více než jeden cyklus.
* **Testování v produkčním**prostředí: pomocí příznaků funkcí udělíte včas přístup k novým funkcím v produkčním prostředí. Můžete například omezit přístup k členům týmu nebo interním testerům beta verzí. Tito uživatelé budou mít v testovacím prostředí místo simulovaného nebo částečného prostředí prostředí s možností celé přesnosti.
* **Let**: použití příznaků funkcí k přírůstkové zavedení nových funkcí koncovým uživatelům. Nejprve můžete cílit na malé procento naplnění uživatelského jména a zvýšit toto procento postupně v průběhu času.
* **Přepínač okamžitého**ukončení: příznaky funkcí poskytují vlastní síť pro bezpečnost pro vydávání nových funkcí. Funkce aplikace můžete zapnout a vypnout bez nutnosti opětovného nasazení jakéhokoli kódu. V případě potřeby můžete funkci rychle zakázat bez nutnosti opětovného sestavování a opětovného nasazení aplikace.
* **Selektivní aktivace**: pomocí příznaků funkcí můžete segmentovat uživatele a dodat konkrétní sadu funkcí pro každou skupinu. Můžete mít funkci, která funguje pouze v určitém webovém prohlížeči. Můžete definovat příznak funkce, aby se funkce mohla zobrazit a používat jenom uživatelé tohoto prohlížeče. S tímto přístupem můžete seznam podporovaných prohlížečů snadno rozšířit později, aniž byste museli dělat změny kódu.

## <a name="basic-concepts"></a>Základní koncepty

Tady je několik nových podmínek souvisejících se správou funkcí:

* **Příznak funkce**: příznak funkce je proměnná s binárním stavem *zapnuto* nebo *vypnuto*. Příznak funkce má také přidružený blok kódu. Stav příznaku funkce se aktivuje bez ohledu na to, jestli se blok kódu spouští.
* **Správce funkcí**: správce funkcí je balíček aplikace, který zpracovává životní cyklus všech příznaků funkcí v aplikaci. Správce funkcí také poskytuje další funkce, včetně příznaků funkcí pro ukládání do mezipaměti a aktualizace jejich stavů.
* **Filter**: filtr je pravidlo pro vyhodnocení stavu příznaku funkce. K potenciálním filtrům patří skupiny uživatelů, typy zařízení a prohlížečů, geografická umístění a časová okna.

Účinná implementace správy funkcí se skládá z nejméně dvou součástí pracujících v součinnosti:

* Aplikace, která využívá příznaky funkcí.
* Samostatné úložiště, ve kterém jsou uloženy příznaky funkcí a jejich aktuální stavy.

## <a name="using-feature-flags-in-your-code"></a>Používání příznaků funkcí v kódu

Základní vzor pro implementaci příznaků funkcí v aplikaci je jednoduchý. Příznak funkce je logická stavová proměnná řídící podmíněný příkaz ve vašem kódu:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Můžete nastavit hodnotu `featureFlag` staticky.

```csharp
bool featureFlag = true;
```

Stav příznaku můžete vyhodnotit na základě určitých pravidel:

```csharp
bool featureFlag = isBetaUser();
```

Můžete zvětšit podmíněný stav pro nastavení chování aplikace pro kterýkoli stav:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Úložiště příznaků funkcí

Chcete-li používat příznaky funkcí efektivně, je nutné Externalize všechny příznaky funkcí používané v aplikaci. To vám umožní měnit stavy příznaků funkcí beze změny a opětovného nasazení samotné aplikace.

Azure App Configuration poskytuje centralizované úložiště pro příznaky funkcí. Můžete ji použít k definování různých druhů příznaků funkcí a k rychlému a spolehlivému manipulaci s jejich stavy. Pak můžete použít knihovny konfigurace aplikace pro různé programovací jazykové architektury pro snadný přístup k těmto příznakům funkcí z vaší aplikace.

[Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje, jak se společně používají knihovny pro poskytovatele konfigurace aplikace .NET Core a knihovny správy funkcí k implementaci příznaků funkcí pro webovou aplikaci ASP.NET.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání příznaků funkcí do webové aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
