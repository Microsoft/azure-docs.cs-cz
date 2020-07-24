---
title: Porozumění konfiguračnímu klíči Azure App – úložiště hodnot
description: Pochopte, jak jsou konfigurační data uložená v konfiguraci aplikace Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 14ff1a00b40d956f369b1978f15f01f113c50270
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050134"
---
# <a name="keys-and-values"></a>Klíče a hodnoty

Konfigurace aplikací Azure ukládá konfigurační data jako páry klíč-hodnota. Páry klíč-hodnota jsou jednoduchá a flexibilní reprezentace nastavení aplikace používaná vývojáři.

## <a name="keys"></a>Klíče

Klíče slouží jako identifikátory párů klíč-hodnota a slouží k ukládání a načítání odpovídajících hodnot. Je běžným postupem uspořádání klíčů do hierarchického oboru názvů pomocí oddělovače znaků, například `/` nebo `:` . Použijte konvenci, která je vhodná pro vaši aplikaci. Konfigurace aplikace považuje klíče za celek. Neanalyzuje klíče, abyste zjistili, jak jsou jejich názvy strukturované nebo vynutily jakékoli pravidlo.

Tady jsou dva příklady názvů klíčů strukturovaných do hierarchie:

* Založené na službě Component Services

```aspx
        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint
```

* Na základě oblastí nasazení

```aspx
        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint
```

Použití konfiguračních dat v rámci aplikačních architektur může určovat konkrétní schémata názvů pro klíčové hodnoty. Například jarní cloudové rozhraní Java definuje `Environment` prostředky, které dodávají nastavení do aplikace pružiny.  Tyto parametry jsou parametrizované proměnnými, které obsahují název a *profil* *aplikace* . Klíče pro pružinová konfigurační data související s cloudem obvykle začínají těmito dvěma prvky oddělenými oddělovačem.

Klíče uložené v konfiguraci aplikace rozlišují velká a malá písmena, která jsou založená na kódování Unicode. Klíče *app1* a *app1* se liší v úložišti konfigurace aplikace. Mějte na paměti, že pokud použijete nastavení konfigurace v rámci aplikace, protože některé architektury zpracovávají konfigurační klíče bez rozlišování velkých a malých písmen. Pro odlišení klíčů nedoporučujeme používat velká a malá písmena.

V názvech klíčů můžete použít libovolný znak Unicode s výjimkou `*` , `,` a `\` .  Pokud potřebujete zahrnout jeden z těchto vyhrazených znaků, vydejte ho pomocí `\{Reserved Character}` . 

U páru klíč-hodnota je omezení velikosti 10 KB. Tento limit zahrnuje všechny znaky v klíči, jeho hodnotu a všechny přidružené volitelné atributy. V rámci tohoto limitu můžete pro klíče mít mnoho úrovní hierarchie.

### <a name="design-key-namespaces"></a>Klíčové obory názvů pro návrh

Existují dva obecné přístupy k pojmenovávání klíčů používaných pro konfigurační data: plochý nebo hierarchický. Tyto metody jsou podobné jako v pohledu použití aplikace, ale hierarchické pojmenování nabízí řadu výhod:

* Jednodušší čtení. Oddělovače ve funkci názvu hierarchického klíče jako mezery ve větě. Poskytují také přirozené přestávky mezi slovy.
* Jednodušší Správa. Hierarchie názvů klíčů představuje logické skupiny konfiguračních dat.
* Jednodušší používání. Je jednodušší napsat dotaz, který vzor odpovídá klíčům v hierarchické struktuře a načítá pouze část konfiguračních dat. Mnoho novějších programovacích rozhraní navíc má nativní podporu pro hierarchická konfigurační data, takže vaše aplikace může používat konkrétní sady konfigurací.

Klíče můžete v konfiguraci aplikací hierarchicky uspořádat mnoha způsoby. Tyto klíče si můžete představit jako [identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý hierarchický klíč je *cesta* prostředku skládající se z jedné nebo více součástí, které jsou spojeny pomocí oddělovačů. Vyberte znak, který se použije jako oddělovač na základě toho, co vaše aplikace, programovací jazyk nebo rozhraní potřebuje. Použijte více oddělovačů pro různé klíče v konfiguraci aplikace.

### <a name="label-keys"></a>Klíče popisků

Hodnoty klíče v konfiguraci aplikace můžou volitelně mít atribut Label. Popisky slouží k odlišení klíčových hodnot se stejným klíčem. Key *app1* *s popisky a* a *B* tvoří dva samostatné klíče v úložišti konfigurace aplikace. Ve výchozím nastavení nemá hodnota klíče žádný popisek. Chcete-li explicitně odkazovat na hodnotu klíče bez popisku, použijte `\0` (adresa URL je zakódována jako `%00` ).

Label nabízí pohodlný způsob, jak vytvořit varianty klíče. Běžné použití popisků je zadání více prostředí pro stejný klíč:

```aspx
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Hodnoty klíče verze

Konfigurace aplikace nepoužívá hodnoty klíče pro automatické verze. Použijte popisky jako způsob vytvoření více verzí hodnoty klíče. Můžete například zadat číslo verze aplikace nebo ID potvrzení Git v popisech k identifikaci klíčových hodnot přidružených k určitému sestavení softwaru.

V popisech můžete použít libovolný znak Unicode s výjimkou `*` , `,` a `\` . Tyto znaky jsou vyhrazené. Chcete-li zahrnout vyhrazený znak, je nutné jej řídicím příkazem použít `\{Reserved Character}` .

### <a name="query-key-values"></a>Hodnoty klíče dotazu

Jednotlivé hodnoty klíče se jednoznačně identifikují pomocí klíče a popisku, který může být `null` . Zadáním vzoru zadáte dotaz na úložiště konfigurace aplikace pro klíčové hodnoty. Úložiště konfigurace aplikace vrátí všechny klíčové hodnoty, které odpovídají vzoru, a jejich odpovídající hodnoty a atributy. Použijte následující vzorové vzory v REST API volání do konfigurace aplikace:

| Klíč | Popis |
|---|---|
| `key`je vynecháno nebo`key=*` | Odpovídá všem klíčům |
| `key=abc` | Odpovídá přesně názvu klíče **ABC** |
| `key=abc*` | Odpovídá názvům klíčů, které začínají na **ABC** |
| `key=abc,xyz` | Odpovídá názvům klíčů **ABC** nebo **XYZ**. Omezeno na pět CSV |

Můžete také zahrnout následující vzory popisku:

| Popisek | Popis |
|---|---|
| `label`je vynecháno nebo`label=*` | Odpovídá jakémukoli popisku, který obsahuje`null` |
| `label=%00` | Odpovídá `null` popisku |
| `label=1.0.0` | Přesně odpovídá popisku **1.0.0** |
| `label=1.0.*` | Odpovídá popiskům, které začínají na **1,0.** |
| `label=%00,1.0.0` | Odpovídá jmenovkám `null` nebo **1.0.0**, omezené na pět CSV |

## <a name="values"></a>Hodnoty

Hodnoty přiřazené klíčům jsou také řetězce Unicode. Pro hodnoty můžete použít všechny znaky Unicode. K jednotlivým hodnotám je přidružen volitelný uživatelem definovaný typ obsahu. Tento atribut slouží k ukládání informací o hodnotě, která pomáhá aplikaci správně zpracovat.

Konfigurační data uložená v úložišti konfigurace aplikace se šifrují v klidovém stavu a při přenosu. Klíče nejsou v klidovém stavu šifrované. Konfigurace aplikace není náhradní řešení pro Azure Key Vault. Neukládat tajné klíče aplikace do IT.

## <a name="next-steps"></a>Další kroky

* [Snímek k určitému časovému okamžiku](./concept-point-time-snapshot.md)  
* [Správa funkcí](./concept-feature-management.md)  
