---
title: Konfigurační klíč aplikace Azure – úložiště hodnot
description: Přehled způsobu, jakým jsou konfigurační data uložena v konfiguraci aplikace Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: fbb30b0a290011a5edfb05c1de9b5d4717a5f733
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898707"
---
# <a name="keys-and-values"></a>Klíče a hodnoty

Konfigurace aplikací Azure ukládá konfigurační data jako páry klíč-hodnota. Páry klíč-hodnota představují jednoduchý, ale flexibilní způsob reprezentace různých typů nastavení aplikace, se kterými je vývojář obeznámen.

## <a name="keys"></a>Klíče

Klíče slouží jako název párů klíč-hodnota a slouží k ukládání a načítání odpovídajících hodnot. Je běžným postupem uspořádání klíčů do hierarchického oboru názvů pomocí oddělovače znaků, jako je například `/` nebo `:`. Použijte konvenci, která je pro vaši aplikaci nejvhodnější. Konfigurace aplikace považuje klíče za celek. Neanalyzuje klíče, abyste zjistili, jak jsou jejich názvy strukturované nebo vynutily jakékoli pravidlo.

Použití konfiguračních dat v rámci aplikačních architektur může určovat konkrétní schémata názvů pro klíčové hodnoty. Příklad: jarní cloudové rozhraní Java definuje `Environment` prostředky, které dodávají nastavení do aplikace pružiny, aby byly parametrizované proměnnými, které zahrnují název a *profil* *aplikace* . Klíče pro pružinová konfigurační data související s cloudem obvykle začínají těmito dvěma prvky oddělenými oddělovačem.

Klíče uložené v konfiguraci aplikace rozlišují velká a malá písmena, která jsou založená na kódování Unicode. Klíče *app1* a *app1* se liší v úložišti konfigurace aplikace. Mějte na paměti, že pokud použijete nastavení konfigurace v rámci aplikace, protože některé architektury zpracovávají konfigurační klíče bez rozlišování velkých a malých písmen. Například systém konfigurace ASP.NET Core zpracovává klíče jako řetězce nerozlišující malá a velká písmena. Aby nedocházelo k nepředvídatelnému chování při dotazování na konfiguraci aplikace v rámci aplikace ASP.NET Core, nepoužívejte klíče, které se liší pouze velikostí písmen.

V názvech klíčů zadaných do konfigurace aplikace můžete použít libovolný znak Unicode s výjimkou `*`, `,`a `\`. Tyto znaky jsou vyhrazené. Pokud potřebujete zahrnout vyhrazený znak, musíte ho pomocí `\{Reserved Character}`vytvořit. V páru klíč-hodnota je povolená celková velikost 10 000 znaků. Tento limit zahrnuje všechny znaky v klíči, jeho hodnotu a všechny přidružené volitelné atributy. V rámci tohoto limitu můžete pro klíče mít mnoho úrovní hierarchie.

### <a name="design-key-namespaces"></a>Klíčové obory názvů pro návrh

Existují dva obecné přístupy k pojmenovávání klíčů používaných pro konfigurační data: plochý nebo hierarchický. Tyto metody jsou podobné jako v pohledu použití aplikace, ale hierarchické pojmenování nabízí řadu výhod:

* Jednodušší čtení. Místo jednoho dlouhého pořadí znaků oddělovače ve funkci názvu hierarchického klíče jako mezery ve větě. Poskytují také přirozené přestávky mezi slovy.
* Jednodušší Správa. Hierarchie názvů klíčů představuje logické skupiny konfiguračních dat.
* Jednodušší používání. Je jednodušší napsat dotaz, který vzor odpovídá klíčům v hierarchické struktuře a načítá pouze část konfiguračních dat. Mnoho novějších programovacích rozhraní navíc má nativní podporu pro hierarchická konfigurační data, takže vaše aplikace může používat konkrétní sady konfigurací.

Klíče můžete v konfiguraci aplikací hierarchicky uspořádat mnoha způsoby. Tyto klíče si můžete představit jako [identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý hierarchický klíč je *cesta* prostředku skládající se z jedné nebo více součástí, které jsou spojeny pomocí oddělovačů. Vyberte znak, který se použije jako oddělovač na základě toho, co vaše aplikace, programovací jazyk nebo rozhraní potřebuje. Použijte více oddělovačů pro různé klíče v konfiguraci aplikace.

Tady je několik příkladů, jak můžete strukturovat názvy klíčů v hierarchii:

* Založené na službě Component Services

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na základě oblastí nasazení

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Klíče popisků

Hodnoty klíče v konfiguraci aplikace můžou volitelně mít atribut Label. Popisky slouží k odlišení klíčových hodnot se stejným klíčem. Key *app1* *s popisky a* a *B* tvoří dva samostatné klíče v úložišti konfigurace aplikace. Ve výchozím nastavení je popisek pro klíčovou hodnotou prázdný nebo `null`.

Label nabízí pohodlný způsob, jak vytvořit varianty klíče. Běžné použití popisků je zadání více prostředí pro stejný klíč:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Hodnoty klíče verze

Konfigurace aplikace neaktualizuje hodnoty klíče automaticky při jejich úpravě. Použijte popisky jako způsob vytvoření více verzí hodnoty klíče. Můžete například zadat číslo verze aplikace nebo ID potvrzení Git v popisech k identifikaci klíčových hodnot přidružených k určitému sestavení softwaru.

V popisech můžete použít libovolný znak Unicode s výjimkou `*`, `,`a `\`. Tyto znaky jsou vyhrazené. Chcete-li zahrnout vyhrazený znak, je nutné jej pomocí `\{Reserved Character}`vytvořit.

### <a name="query-key-values"></a>Hodnoty klíče dotazu

Jednotlivé hodnoty klíče se jednoznačně identifikují pomocí klíče a popisku, který může být `null`. Zadáním vzoru zadáte dotaz na úložiště konfigurace aplikace pro klíčové hodnoty. Úložiště konfigurace aplikace vrátí všechny klíčové hodnoty, které odpovídají vzoru, a jejich odpovídající hodnoty a atributy. Použijte následující vzorové vzory v REST API volání do konfigurace aplikace:

| Klíč | |
|---|---|
| `key` se vynechá nebo `key=*` | Odpovídá všem klíčům |
| `key=abc` | Odpovídá přesně názvu klíče **ABC** |
| `key=abc*` | Odpovídá názvům klíčů, které začínají na **ABC** |
| `key=*abc` | Odpovídá názvům klíčů, které končí **ABC** |
| `key=*abc*` | Odpovídá názvům klíčů, které obsahují **ABC** |
| `key=abc,xyz` | Odpovídá názvům klíčů **ABC** nebo **XYZ**, které jsou omezeny na pět CSV |

Můžete také zahrnout následující vzory popisku:

| Štítek | |
|---|---|
| `label` se vynechá nebo `label=*` | Odpovídá jakémukoli popisku, který zahrnuje `null` |
| `label=%00` | Odpovídá popisku `null` |
| `label=1.0.0` | Přesně odpovídá popisku **1.0.0** |
| `label=1.0.*` | Odpovídá popiskům, které začínají na **1,0.** |
| `label=*.0.0` | Odpovídá jmenovkám, které končí na **. 0,0** |
| `label=*.0.*` | Odpovídá popiskům, které obsahují **. 0.** |
| `label=%00,1.0.0` | Odpovídá popiskům `null` nebo **1.0.0**, které jsou omezeny na pět CSV |

## <a name="values"></a>Hodnoty

Hodnoty přiřazené klíčům jsou také řetězce Unicode. Pro hodnoty můžete použít všechny znaky Unicode. K jednotlivým hodnotám je přidružen volitelný uživatelem definovaný typ obsahu. Tento atribut slouží k ukládání informací, například schématu kódování, o hodnotě, která pomáhá vaší aplikaci správně zpracovat.

Konfigurační data uložená v úložišti konfigurace aplikace, které zahrnuje všechny klíče a hodnoty, jsou v klidovém stavu a při přenosu šifrovaná. Konfigurace aplikace není náhradní řešení pro Azure Key Vault. Neukládat tajné klíče aplikace do IT.

## <a name="next-steps"></a>Další kroky

* [Snímek v daném časovém okamžiku](./concept-point-time-snapshot.md)  
* [Správa funkcí](./concept-feature-management.md)  
