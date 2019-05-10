---
title: Správa funkce Azure konfigurace aplikace | Dokumentace Microsoftu
description: Základní informace o použití konfigurace aplikace pro Azure zapnout a vypnout aplikaci funkcí na vyžádání
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
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413768"
---
# <a name="feature-management"></a>Správa funkcí

Tradičně přesouvání novou aplikaci funkcí vyžaduje dokončení opakované nasazení vlastní aplikace. Chcete-li otestovat funkci, pravděpodobně máte pro nasazení aplikace v mnoha případech na ovládací prvek, když se stane viditelnou nebo příjemců a prohlédněte si ho. Funkce správy je zvykem vývoj moderních software, který odděluje vydání funkcí od nasazení kódu a umožňuje rychlé změny na dostupnost funkcí na vyžádání. Pomocí techniky označované jako *příznak funkce* (označované také jako *přepínač funkce*, *přepínač funkcí*, a tak dále) ke správě životního cyklu funkce dynamicky. Umožňuje vývojářům řešit tyto problémy:

* **Kód správu větví**: Příznaky funkcí jsou často slouží k zabalení novou funkčnost aplikace, která je ve vývoji. Tato funkce je "skrytá" ve výchozím nastavení. Můžete ji, když si bezpečně zaslat a zůstane neaktivní v produkčním prostředí. Pomocí tohoto přístupu, které se nazývá *tmavě nasazení*, uvolníte váš kód na konci každé vývojového cyklu. Už nemusíte udržovat všechny větve kódu napříč více cykly z důvodu trvá víc než jednu tečku. k dokončení funkce.
* **Testování v produkčním prostředí**: Příznaky funkcí můžete použít k udělení dřívější přístup k nové funkce v produkčním prostředí. Například můžete omezit přístup jenom na členy týmu nebo interní beta testerům. Tito uživatelé budou dostávat Plnohodnotná produkční prostředí, místo Simulovaná nebo jeho část prostředí v testovacím prostředí.
* **Publikování testovacích verzí**: Příznaky funkcí můžete také zavádět nové funkce pro koncové uživatele postupně. Můžete cílit na malé procento populace uživatel nejprve a postupem doby, po získali větší jistotou v implementaci postupně zvyšuje toto procento.
* **Přepínač kill rychlé**: Vlastní bezpečnostní nám poskytují příznaky funkcí pro uvolnění nové funkce. S nimi můžete nejen zapnout, ale také vypne funkce aplikace snadno. Získáte možnost rychle zakázat funkce, v případě potřeby, aniž by bylo nutné znovu sestavit a znova nasazovat aplikaci.
* **Selektivní aktivace**: Zatímco většina příznaků funkcí existují stejně, dokud jejich přidružené funkce byly úspěšně vydány, některé může trvat dlouhou dobu. Můžete je použít jako způsob, jak segmentovat uživatele a poskytovat konkrétní sadu funkcí pro každou skupinu. Například může mít funkce, která funguje na určité webový prohlížeč. Příznak funkce lze definovat tak, aby jenom uživatelé tohoto prohlížeče může zobrazit a používat funkci. Výhodou tohoto přístupu je, že můžete snadno rozšířit seznam podporovaných prohlížečů později bez nutnosti provádět žádný kód změnit.

## <a name="basic-concepts"></a>Základní koncepty

Tady je několik nové podmínky týkající se funkce správy.

* **Příznak funkce**: Příznak funkce je proměnná se binární stavem *na* nebo *vypnout* a obsahuje blok přidružený kód. Jeho stav rozhodne, zda blok kódu spustí nebo ne.
* **Funkce správce**: Funkce správce je balíček aplikace, která zpracovává životního cyklu všechny příznaky funkcí v aplikaci. Obvykle poskytuje další funkce, jako je například ukládání do mezipaměti příznaků a aktualizaci jejich stavů.
* **Filtr**: Filtr je pravidlo pro vyhodnocení stavu příznak funkce. Skupiny uživatelů, zařízení nebo prohlížeče, geografické polohy, časový interval jsou všechny příklady co může představovat filtru.

Efektivní provádění funkcí správy se skládá z nejméně dvou součástí práce ve vzájemné součinnosti: aplikace, která využívá příznaků funkcí a oddělené úložiště, který ukládá příznaků funkcí a jejich aktuální stavy. Jejich interakce jsou zobrazené níž.

## <a name="feature-flag-usage-in-code"></a>Příznak používání funkcí v kódu

Základní vzor pro implementaci příznaků funkcí v aplikaci je jednoduché. Můžete ho pohodlným jako logická stavu proměnné použité s `if` podmíněný příkaz v kódu.

```csharp
if (featureFlag) {
    // Run the following code
}
```

V takovém případě pokud `featureFlag` je nastavena na `True`, je proveden blok uzavřené kódu; v opačném případě se přeskočí. Hodnota `featureFlag` je možné nastavit staticky:

```csharp
bool featureFlag = true;
```

Také může být vyhodnocen na základě určitých pravidel:

```csharp
bool featureFlag = isBetaUser();
```

Obsahuje vzor poněkud složitější příznak funkce `else` také příkaz.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

To může být přepsán v základní vzor, ale. [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje výhod standardizují používáním vzor jednoduchého kódu.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Úložiště příznak funkce

Efektivně používat, budete muset využívajícím všechny příznaky funkcí použijí v aplikaci. Který vám umožní změnit stavy příznak funkce bez úprava a opětovné nasazení vlastní aplikace. Konfigurace aplikace pro Azure je navržena jako centrální úložiště pro příznaky funkcí. Můžete využívat ji k definování různých typů příznaků funkcí a manipulaci s jejich stavů rychle a bez obav. Tyto příznaky funkcí můžete pak jednoduše přistupovat z aplikace pomocí knihovny konfigurace aplikací pro různé programovací jazyk rozhraní. [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md) ukazuje, jak poskytovatel konfigurace pro aplikace .NET Core a knihovny pro správu funkce se používají společně jako tak získají kompletní řešení pro implementaci příznaky funkcí pro vaši webovou aplikaci ASP.NET.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přidat příznaků funkcí do webové aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
