---
title: Správa funkcí konfigurace aplikací Azure
description: Přehled toho, jak se dá konfigurace aplikace Azure použít k zapnutí a vypnutí funkcí aplikace na vyžádání.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898731"
---
# <a name="feature-management-overview"></a>Přehled správy funkcí

Při obnově nové funkce aplikace se obvykle vyžaduje úplné opětovné nasazení aplikace. Chcete-li otestovat funkci, pravděpodobně budete muset aplikaci nasadit několikrát, abyste mohli řídit, kdy se tato funkce zobrazí nebo kdo se k ní dostane.

Správa funkcí je moderní postup vývoje softwaru, který odděluje vydání funkce od nasazení kódu a umožňuje rychlé změny dostupnosti funkcí na vyžádání. Používá techniku nazvanou *příznaky funkcí* (označované také jako *přepínání funkcí*, *přepínače funkcí*atd.) k dynamické správě životního cyklu funkce.

Správa funkcí pomáhá vývojářům řešit následující problémy:

* **Správa větví kódu**: příznaky funkcí se často používají k zabalení nových funkcí aplikace, které jsou ve vývoji. Tato funkce je ve výchozím nastavení skrytá. Tuto funkci můžete bezpečně dodávat, i když je nedokončená a zůstane neaktivní v produkčním prostředí. Pomocí tohoto přístupu s názvem *tmavě nasazené*můžete uvolnit veškerý kód na konci každého vývojového cyklu. Už nemusíte spravovat žádnou větev kódu napříč několika cykly, protože je potřeba dokončit víc než jeden cyklus.
* **Testování v produkčním**prostředí: příznaky funkcí můžete použít k udělení včasného přístupu k novým funkcím v produkčním prostředí. Tento přístup můžete například omezit pouze na členy týmu nebo interní beta testery. Tito uživatelé budou mít v testovacím prostředí místo simulovaného nebo částečného prostředí vyplněné provozní prostředí.
* **Lety**: můžete také použít příznaky funkcí k přírůstkové zavedení nových funkcí koncovým uživatelům. Můžete nejdřív cílit na malé procento naplnění uživatelů a po dosažení větší důvěry v implementaci zvýšit toto procento postupně v průběhu času.
* **Přepínač okamžitého**ukončení: příznaky funkcí poskytují vlastní síť pro bezpečnost pro vydávání nových funkcí. Díky nim můžete funkce aplikací snadno zapnout nebo vypnout. V případě potřeby můžete funkci rychle zakázat bez nutnosti opětovného sestavování a opětovného nasazení aplikace.
* **Selektivní aktivace**: i když většina příznaků funkcí existuje jenom v případě, že se jejich přidružené funkce úspěšně vystavily, některé můžou trvat dlouhou dobu. Pomocí příznaků funkcí můžete segmentovat uživatele a dodat konkrétní sadu funkcí pro každou skupinu. Můžete mít například funkci, která funguje pouze v určitém webovém prohlížeči. Můžete definovat příznak funkce, aby se funkce mohla zobrazit a používat jenom uživatelé tohoto prohlížeče. S tímto přístupem můžete seznam podporovaných prohlížečů snadno rozšířit později, aniž byste museli dělat změny kódu.

## <a name="basic-concepts"></a>Základní koncepty

Tady je několik nových podmínek souvisejících se správou funkcí:

* **Příznak funkce**: příznak funkce je proměnná s binárním stavem *zapnuto* nebo *vypnuto*. Příznak funkce má také přidružený blok kódu. Stav příznaku funkce se aktivuje bez ohledu na to, jestli je blok kódu spuštěný.
* **Správce funkcí**: správce funkcí je balíček aplikace, který zpracovává životní cyklus všech příznaků funkcí v aplikaci. Správce funkcí obvykle poskytuje další funkce, jako jsou například příznaky funkcí pro ukládání do mezipaměti a aktualizace jejich stavů.
* **Filter**: filtr je pravidlo pro vyhodnocení stavu příznaku funkce. V případě skupiny uživatelů, typu zařízení nebo prohlížeče, zeměpisného umístění a časového intervalu jsou uvedeny příklady toho, co může filtr představovat.

Účinná implementace správy funkcí se skládá z nejméně dvou součástí pracujících v součinnosti:

* Aplikace, která využívá příznaky funkcí.
* Samostatné úložiště, ve kterém jsou uloženy příznaky funkcí a jejich aktuální stavy.

Způsob interakce těchto komponent je znázorněn v následujících příkladech.

## <a name="feature-flag-usage-in-code"></a>Použití příznaků funkcí v kódu

Základní vzor pro implementaci příznaků funkcí v aplikaci je jednoduchý. Příznak funkce si můžete představit jako logickou hodnotu proměnné, která se používá s `if` podmíněným příkazem v kódu:

```csharp
if (featureFlag) {
    // Run the following code
}
```

V tomto případě, pokud je `featureFlag` nastaveno na `True`, je spuštěn blok kódu uzavřen; v opačném případě se přeskočí. Hodnotu `featureFlag` můžete nastavit staticky, jako v následujícím příkladu kódu:

```csharp
bool featureFlag = true;
```

Můžete také vyhodnotit stav příznaku na základě určitých pravidel:

```csharp
bool featureFlag = isBetaUser();
```

Trochu složitější vzor příznaků funkcí zahrnuje i příkaz `else`:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Toto chování však můžete přepsat v základním vzoru. [Používání příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje výhody standardizace pro jednoduchý vzor kódu. Příklad:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Úložiště příznaků funkcí

Chcete-li používat příznaky funkcí efektivně, je nutné Externalize všechny příznaky funkcí používané v aplikaci. Tento přístup umožňuje změnit stavy příznaků funkcí beze změny a opětovného nasazení samotné aplikace.

Konfigurace aplikace Azure je navržená jako centralizované úložiště pro příznaky funkcí. Můžete ji použít k definování různých druhů příznaků funkcí a k rychlému a spolehlivému manipulaci s jejich stavy. Pak můžete použít knihovny konfigurace aplikace pro různé programovací jazykové architektury pro snadný přístup k těmto příznakům funkcí z vaší aplikace.

[Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje, jak se společně používají knihovny pro poskytovatele konfigurace aplikace .NET Core a knihovny správy funkcí k implementaci příznaků funkcí pro webovou aplikaci ASP.NET.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání příznaků funkcí do webové aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
