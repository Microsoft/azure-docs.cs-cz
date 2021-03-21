---
title: Porozumění konfiguračnímu klíči Azure App – úložiště hodnot
description: Pochopení úložiště klíč-hodnota v konfiguraci aplikace Azure, které ukládá konfigurační data jako klíčové hodnoty. Klíčové hodnoty jsou reprezentace nastavení aplikace.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930241"
---
# <a name="keys-and-values"></a>Klíče a hodnoty

Konfigurace aplikací Azure ukládá konfigurační data jako klíčové hodnoty. Klíčové hodnoty jsou jednoduché a flexibilní reprezentace nastavení aplikace používaná vývojáři.

## <a name="keys"></a>Klíče

Klíče slouží jako identifikátory pro klíčové hodnoty a slouží k ukládání a načítání odpovídajících hodnot. Je běžným postupem uspořádání klíčů do hierarchického oboru názvů pomocí oddělovače znaků, například `/` nebo `:` . Použijte konvenci, která je vhodná pro vaši aplikaci. Konfigurace aplikace považuje klíče za celek. Neanalyzuje klíče, abyste zjistili, jak jsou jejich názvy strukturované nebo vynutily jakékoli pravidlo.

Tady je příklad názvů klíčů strukturovaných v hierarchii založené na službě komponent:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

Použití konfiguračních dat v rámci aplikačních architektur může určovat konkrétní schémata názvů pro klíčové hodnoty. Například jarní cloudové rozhraní Java definuje `Environment` prostředky, které dodávají nastavení do aplikace pružiny.  Tyto parametry jsou parametrizované proměnnými, které obsahují název a *profil* *aplikace* . Klíče pro pružinová konfigurační data související s cloudem obvykle začínají těmito dvěma prvky oddělenými oddělovačem.

Klíče uložené v konfiguraci aplikace rozlišují velká a malá písmena, která jsou založená na kódování Unicode. Klíče *app1* a *app1* se liší v úložišti konfigurace aplikace. Mějte na paměti, že pokud použijete nastavení konfigurace v rámci aplikace, protože některé architektury zpracovávají konfigurační klíče bez rozlišování velkých a malých písmen. Pro odlišení klíčů nedoporučujeme používat velká a malá písmena.

V názvech klíčů můžete použít libovolný znak Unicode s výjimkou `%` . Název klíče nemůže být `.` nebo `..` buď. U klíčové hodnoty je omezení velikosti 10 KB. Tento limit zahrnuje všechny znaky v klíči, jeho hodnotu a všechny přidružené volitelné atributy. V rámci tohoto limitu můžete pro klíče mít mnoho úrovní hierarchie.

### <a name="design-key-namespaces"></a>Klíčové obory názvů pro návrh

Existují dva obecné přístupy k pojmenovávání klíčů používaných pro konfigurační data: plochý nebo hierarchický. Tyto metody jsou podobné jako v pohledu použití aplikace, ale hierarchické pojmenování nabízí řadu výhod:

* Jednodušší čtení. Oddělovače ve funkci názvu hierarchického klíče jako mezery ve větě. Poskytují také přirozené přestávky mezi slovy.
* Jednodušší Správa. Hierarchie názvů klíčů představuje logické skupiny konfiguračních dat.
* Jednodušší používání. Je jednodušší napsat dotaz, který vzor odpovídá klíčům v hierarchické struktuře a načítá pouze část konfiguračních dat. Mnoho novějších programovacích rozhraní navíc má nativní podporu pro hierarchická konfigurační data, takže vaše aplikace může používat konkrétní sady konfigurací.

Klíče můžete v konfiguraci aplikací hierarchicky uspořádat mnoha způsoby. Tyto klíče si můžete představit jako [identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý hierarchický klíč je *cesta* prostředku skládající se z jedné nebo více součástí, které jsou spojeny pomocí oddělovačů. Vyberte znak, který se použije jako oddělovač na základě toho, co vaše aplikace, programovací jazyk nebo rozhraní potřebuje. Použijte více oddělovačů pro různé klíče v konfiguraci aplikace.

### <a name="label-keys"></a>Klíče popisků

Hodnoty klíč-hodnota v konfiguraci aplikace mohou volitelně mít atribut Label. Popisky slouží k odlišení klíčových hodnot se stejným klíčem. Key *app1* *s popisky a* a *B* tvoří dva samostatné klíče v úložišti konfigurace aplikace. Ve výchozím nastavení nemá klíč-hodnota žádný popisek. Chcete-li explicitně odkazovat na klíčovou hodnotu bez popisku, použijte `\0` (adresa URL je zakódována jako `%00` ).

Label nabízí pohodlný způsob, jak vytvořit varianty klíče. Běžné použití popisků je zadání více prostředí pro stejný klíč:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Klíč verze – hodnoty

Použijte popisky jako způsob vytvoření více verzí klíč-hodnota. Můžete například zadat číslo verze aplikace nebo ID potvrzení Git v popisech k identifikaci klíčových hodnot přidružených k určitému sestavení softwaru.

> [!NOTE]
> Pokud hledáte verze změn, v konfiguraci aplikace jsou všechny změny za klíčovou hodnotou v průběhu minulé doby automaticky provedeny. Další podrobnosti najdete [v tématu snímek v čase](./concept-point-time-snapshot.md) .

### <a name="query-key-values"></a>Hodnoty klíč dotazu

Každý klíč-hodnota je jednoznačně identifikovaný pomocí klíče a popisku, který může být `\0` . Zadáním vzoru zadáte dotaz na úložiště konfigurace aplikace pro klíčové hodnoty. Úložiště konfigurace aplikace vrátí všechny klíčové hodnoty, které odpovídají vzoru, včetně odpovídajících hodnot a atributů. Použijte následující vzorové vzory v REST API volání do konfigurace aplikace:

| Klíč | Popis |
|---|---|
| `key` je vynecháno nebo `key=*` | Odpovídá všem klíčům |
| `key=abc` | Odpovídá přesně názvu klíče **ABC** |
| `key=abc*` | Odpovídá názvům klíčů, které začínají na **ABC** |
| `key=abc,xyz` | Odpovídá názvům klíčů **ABC** nebo **XYZ**. Omezeno na pět CSV |

Můžete také zahrnout následující vzory popisku:

| Popisek | Description |
|---|---|
| `label` je vynecháno nebo `label=*` | Odpovídá jakémukoli popisku, který obsahuje `\0` |
| `label=%00` | Odpovídá `\0` popisku |
| `label=1.0.0` | Přesně odpovídá popisku **1.0.0** |
| `label=1.0.*` | Odpovídá popiskům, které začínají na **1,0.** |
| `label=%00,1.0.0` | Odpovídá jmenovkám `\0` nebo **1.0.0**, omezené na pět CSV |

> [!NOTE]
> `*`, `,` , a `\` jsou rezervované znaky v dotazech. Pokud se vyhrazený znak používá v názvech nebo popisech klíčů, musíte ho řídicím příkazem `\{Reserved Character}` v dotazech vytvořit.

## <a name="values"></a>Hodnoty

Hodnoty přiřazené klíčům jsou také řetězce Unicode. Pro hodnoty můžete použít všechny znaky Unicode.

### <a name="use-content-type"></a>Použít typ obsahu
Každá hodnota klíče v konfiguraci aplikace má atribut Content-Type. Volitelně můžete použít tento atribut k uložení informací o typu hodnoty v hodnotě klíč-hodnota, která aplikaci pomáhá zpracovat správně. Pro typ obsahu můžete použít libovolný formát. Konfigurace aplikace používá [typy médií]( https://www.iana.org/assignments/media-types/media-types.xhtml) (označované také jako typy MIME) pro předdefinované datové typy, jako jsou například příznaky funkcí, Key Vault odkazy a hodnoty klíčů JSON.

## <a name="next-steps"></a>Další kroky

* [Snímek k určitému časovému okamžiku](./concept-point-time-snapshot.md)
* [Správa funkcí](./concept-feature-management.md)
* [Zpracování událostí](./concept-app-configuration-event.md)
