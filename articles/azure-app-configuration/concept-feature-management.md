---
title: Správa funkce Azure konfigurace aplikace | Dokumentace Microsoftu
description: Přehled použití konfigurace aplikace pro Azure zapnout a vypnout aplikaci funkcí na vyžádání.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393364"
---
# <a name="feature-management-overview"></a>Přehled funkcí správy

Tradičně přesouvání novou aplikaci funkcí vyžaduje dokončení opakované nasazení vlastní aplikace. Chcete-li otestovat funkci, pravděpodobně musíte nasadit vaše aplikace v mnoha případech ovládacího prvku, když se stane viditelnou funkci nebo příjemců a prohlédněte si ho.

Funkce správy je zvykem moderní vývoj softwaru, která odděluje vydání funkcí od nasazení kódu a umožňuje rychlé změny na dostupnost funkcí na vyžádání. Pomocí techniky označované jako *příznaků funkcí* (označované také jako *přepínání funkcí*, *přepínače funkcí*, a tak dále) k dynamické správě životního cyklu funkce.

Funkce správy pomáhá vývojářům řešit tyto problémy:

* **Kód správu větví**: Příznaky funkcí jsou často slouží k zabalení novou funkčnost aplikace, která je ve vývoji. Tato funkce je "skrytá" ve výchozím nastavení. Jste bezpečně dodávat funkce, i když má nedokončené, a zůstane neaktivní v produkčním prostředí. Volá se, když použijete tento přístup, *tmavě nasazení*, uvolníte váš kód na konci každé vývojového cyklu. Už nemusíte udržovat všechny větve kódu napříč více cykly z důvodu trvá víc než jeden cyklus k dokončení funkce.
* **Testování v produkčním prostředí**: Příznaky funkcí můžete použít k udělení dřívější přístup k nové funkce v produkčním prostředí. Například můžete omezit přístup jenom členové týmu nebo interní beta testerům. Tito uživatelé budou dostávat Plnohodnotná produkční prostředí, místo Simulovaná nebo jeho část prostředí v testovacím prostředí.
* **Publikování testovacích verzí**: Příznaky funkcí můžete použít také k postupně zavádět nové funkce pro koncové uživatele. Můžete cílit na malé procento populace uživatel nejprve a postupem doby, po získali větší jistotou v implementaci postupně zvyšuje toto procento.
* **Přepínač kill rychlé**: Vlastní bezpečnostní nám poskytují příznaky funkcí pro uvolnění nové funkce. S nimi můžete snadno zapnout funkce aplikací, zapnout a vypnout. V případě potřeby můžete rychle zakázat funkci bez znovu sestavovat a nasazovat aplikace.
* **Selektivní aktivace**: Zatímco většina příznaků funkcí existovat, pouze dokud jejich přidružené funkce byly úspěšně vydány, některé může trvat dlouhou dobu. Příznaky funkcí můžete segmentovat uživatele a poskytovat konkrétní sadu funkcí pro každou skupinu. Například může mít funkce, která funguje pouze na určité webový prohlížeč. Příznak funkce můžete definovat tak, aby se může zobrazit a používat funkci jenom uživatelé z prohlížeče. S tímto přístupem můžete snadno rozšířit seznam podporovaných prohlížečů později bez nutnosti provádět změny kódu.

## <a name="basic-concepts"></a>Základní koncepty

Tady je několik nové podmínky týkající se správy funkce:

* **Příznak funkce**: Příznak funkce je proměnná se binární stavem *na* nebo *vypnout*. Příznak funkce má také z přidružený kód bloku. Stav příznaku funkce aktivuje, zda blok kódu spustí nebo ne.
* **Funkce správce**: Funkce správce je balíček aplikace, která zpracovává životního cyklu všechny příznaky funkcí v aplikaci. Funkce správce obvykle poskytuje další funkce, jako je například ukládání do mezipaměti příznaků funkcí a aktualizace příslušné stavy.
* **Filtr**: Filtr je pravidlo pro vyhodnocení stavu příznak funkce. Skupinu uživatelů, typu zařízení nebo prohlížeče, zeměpisného umístění a časovým intervalem jsou všechny příklady co může představovat filtru.

Efektivní provádění funkcí správy se skládá z práce ve vzájemné součinnosti alespoň dvě součásti:

* Aplikace, která umožňuje použití příznaků funkcí.
* Oddělené úložiště, která ukládá příznaků funkcí a jejich aktuální stavy.

Jak tyto komponenty interagují je znázorněn v následujícím příkladu.

## <a name="feature-flag-usage-in-code"></a>Příznak používání funkcí v kódu

Základní vzor pro implementaci příznaků funkcí v aplikaci je jednoduché. Příznak funkce si můžete představit jako logickou stavová proměnná se používá `if` podmíněný příkaz v kódu:

```csharp
if (featureFlag) {
    // Run the following code
}
```

V takovém případě pokud `featureFlag` je nastavena na `True`, blok uzavřené kódu je provedeno; jinak vrátí hodnotu, se přeskočí. Můžete nastavit hodnotu `featureFlag` staticky, jako v následujícím příkladu kódu:

```csharp
bool featureFlag = true;
```

Také můžete vyhodnotit stav příznaku na základě určitých pravidel:

```csharp
bool featureFlag = isBetaUser();
```

Obsahuje vzor poněkud složitější příznak funkce `else` také – příkaz:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Však můžete přepsat toto chování v základní vzor. [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje výhody standardizují používáním vzor jednoduchého kódu. Příklad:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Úložiště příznak funkce

Příznaky funkcí efektivně používat, budete muset využívajícím všechny příznaky funkcí použijí v aplikaci. Tento přístup vám umožní změnit stavy příznak funkce bez úprava a opětovné nasazení vlastní aplikace.

Konfigurace aplikace pro Azure je navržena jako centrální úložiště pro příznaky funkcí. Můžete ho použít k definování různých typů příznaků funkcí a manipulaci s jejich stavů rychle a bez obav. Potom můžete knihovny konfigurace aplikací pro různé programovací jazyk rozhraní pro snadný přístup k těmto příznaků funkcí z vaší aplikace.

[Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje, jak poskytovatel konfigurace pro aplikace .NET Core a knihovny pro správu funkce se používají společně k implementaci příznaky funkcí pro vaši webovou aplikaci ASP.NET.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přidat příznaků funkcí do webové aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
