---
title: Principy správy funkcí pomocí konfigurace aplikací Azure
description: Zapnutí a vypnutí funkcí pomocí konfigurace aplikací Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523726"
---
# <a name="feature-management-overview"></a>Přehled správy funkcí

Odeslání nové funkce aplikace tradičně vyžaduje úplné přeřazení samotné aplikace. Testování funkce často vyžaduje více nasazení aplikace.  Každé nasazení může změnit funkci nebo vystavit funkci různým zákazníkům pro testování.  

Správa funkcí je moderní postup vývoje softwaru, který odděluje uvolnění funkcí od nasazení kódu a umožňuje rychlé změny dostupnosti funkcí na vyžádání. Používá techniku nazývanou *příznaky funkcí* (označované také jako *přepínače funkcí*, *přepínače funkcí*a tak dále) k dynamické správě životního cyklu funkce.

Správa funkcí pomáhá vývojářům řešit následující problémy:

* **Správa větví kódu**: Pomocí příznaků funkcí můžete zabalit nové funkce aplikace, které jsou aktuálně ve vývoji. Tato funkce je ve výchozím nastavení "skrytá". Můžete bezpečně loď funkce, i když je to nedokončené, a to zůstane spící ve výrobě. Pomocí tohoto přístupu, *nazývaného tmavé nasazení*, můžete uvolnit veškerý kód na konci každého vývojového cyklu. Již není nutné udržovat větve kódu ve více vývojových cyklech, protože daná funkce vyžaduje více než jeden cyklus k dokončení.
* **Test v produkčním prostředí**: Pomocí příznaků funkcí udělte včasný přístup k novým funkcím v produkčním prostředí. Můžete například omezit přístup na členy týmu nebo interní beta testery. Tito uživatelé budou mít možnost i namísto simulovaného nebo částečného prostředí v testovacím prostředí.
* **Flighting**: Pomocí příznaků funkcí můžete postupně zavádět nové funkce pro koncové uživatele. Můžete nejprve cílit na malé procento vaší populace uživatelů a postupně toto procento zvyšovat v průběhu času.
* **Okamžitý vypínač**: Funkce příznaky poskytují vlastní bezpečnostní síť pro uvolnění nové funkce. Funkce aplikace můžete zapínat a vypínat bez opětovného nasazení kódu. V případě potřeby můžete funkci rychle zakázat bez opětovného sestavení a opětovného nasazení aplikace.
* **Selektivní aktivace**: Pomocí příznaků funkcí můžete segmentovat uživatele a poskytovat konkrétní sadu funkcí každé skupině. Můžete mít funkci, která funguje pouze v určitém webovém prohlížeči. Příznak prvku můžete definovat tak, aby tuto funkci mohli zobrazit a používat pouze uživatelé tohoto prohlížeče. S tímto přístupem můžete později snadno rozbalit seznam podporovaných prohlížečů, aniž byste museli provádět změny kódu.

## <a name="basic-concepts"></a>Základní koncepty

Zde je několik nových termínů souvisejících se správou funkcí:

* **Příznak funkce**: Příznak prvku je proměnná s binárním stavem *zapnuto* nebo *vypnuto*. Příznak funkce má také přidružený blok kódu. Stav příznaku funkce aktivuje, zda je spuštěn blok kódu.
* **Správce funkcí**: Správce funkcí je balíček aplikace, který zpracovává životní cyklus všech příznaků funkcí v aplikaci. Správce funkcí také poskytuje další funkce, včetně příznaků funkcí ukládání do mezipaměti a aktualizace jejich stavů.
* **Filtr**: Filtr je pravidlo pro vyhodnocení stavu příznaku prvku. Potenciální filtry zahrnují skupiny uživatelů, typy zařízení nebo prohlížečů, zeměpisná umístění a časová okna.

Efektivní implementace správy funkcí se skládá z nejméně dvou složek pracujících ve shodě:

* Aplikace, která využívá příznaky funkce.
* Samostatné úložiště, které ukládá příznaky funkce a jejich aktuální stavy.

## <a name="using-feature-flags-in-your-code"></a>Použití příznaků funkcí v kódu

Základní vzor pro implementaci příznaků funkcí v aplikaci je jednoduchý. Příznak prvku je logická proměnná stavu ovládající podmíněný příkaz v kódu:

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

Podmíněnou možnost můžete rozšířit na nastavení chování aplikace pro oba stavy:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Úložiště příznaku funkce

Chcete-li účinně používat příznaky funkcí, je třeba externalizovat všechny příznaky funkcí použité v aplikaci. To umožňuje změnit stavy příznaku funkce bez úpravy a opětovného nasazení samotné aplikace.

Azure App Configuration poskytuje centralizované úložiště pro příznaky funkcí. Můžete ji použít k definování různých druhů příznaků prvků a rychléa sebevědomé manipulaci s jejich stavy. Potom můžete použít knihovny Konfigurace aplikace pro různé rozhraní programovacího jazyka pro snadný přístup k těmto příznakům funkcí z vaší aplikace.

[Příznaky funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazují, jak se společně používají knihovny konfigurace aplikace .NET Core a knihovny Správy funkcí k implementaci příznaků funkcí pro ASP.NET webové aplikace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání příznaků funkcí do webové aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
