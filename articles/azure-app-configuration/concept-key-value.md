---
title: Principy úložiště klíč-hodnota konfigurace aplikace Azure
description: Zjistěte, jak se konfigurační data ukládají v azure app configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523600"
---
# <a name="keys-and-values"></a>Klíče a hodnoty

Azure App Configuration ukládá konfigurační data jako páry klíč-hodnota. Dvojice klíč-hodnota jsou jednoduché a flexibilní reprezentace nastavení aplikací používaných vývojáři.

## <a name="keys"></a>Klíče

Klíče slouží jako identifikátory pro dvojice klíč hodnota a slouží k ukládání a načtení odpovídající hodnoty. Je běžnou praxí uspořádat klíče do hierarchického oboru názvů pomocí oddělovače znaků, například `/` nebo `:`. Použijte konvence, která nejlépe vyhovuje vaší aplikaci. Konfigurace aplikace zachází s klíči jako s celkem. Neanalyzuje klíče, aby zjistil, jak jsou jejich názvy strukturovány, ani nevynucuje jakékoli pravidlo.

Zde jsou dva příklady názvů klíčů strukturovaných do hierarchie:

* Na základě dílčích služeb

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na základě oblastí nasazení

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Použití konfiguračních dat v rámci aplikačních architektur může diktovat konkrétní schémata pojmenování pro hodnoty klíčů. Například java framework Spring Cloud `Environment` definuje prostředky, které dodávají nastavení jarní aplikaci.  Ty jsou parametrizovány proměnnými, které zahrnují *název aplikace* a *profil*. Klíče pro konfigurační data související s Spring Cloud obvykle začínají těmito dvěma prvky oddělenými oddělovačem.

Klíče uložené v konfiguraci aplikace jsou řetězce založené na kódu, které rozlišují malá a velká písmena. Klíče *app1* a *App1* jsou odlišné v obchodě konfigurace aplikací. Mějte to na paměti při použití nastavení konfigurace v rámci aplikace, protože některé architektury zpracovávají konfigurační klíče necitlivě. Nedoporučujeme používat případ k rozlišení klíčů.

V názvech klíčů můžete použít libovolný `*` `,`znak `\`unicode s výjimkou znaků , a .  Pokud potřebujete zahrnout jeden z těchto vyhrazených `\{Reserved Character}`znaků, uniknout pomocí . 

Je kombinovaný limit velikosti 10 KB na pár klíč hodnota. Tento limit zahrnuje všechny znaky v klíči, jeho hodnotu a všechny přidružené volitelné atributy. V rámci tohoto limitu můžete mít mnoho hierarchických úrovní pro klíče.

### <a name="design-key-namespaces"></a>Návrh jmenných prostorů klíčů

Existují dva obecné přístupy k pojmenování klíčů používaných pro konfigurační data: ploché nebo hierarchické. Tyto metody jsou podobné z hlediska použití aplikace, ale hierarchické pojmenování nabízí řadu výhod:

* Snadněji čitelný. Oddělovače v názvu hierarchického klíče fungují jako mezery ve větě. Poskytují také přirozené přestávky mezi slovy.
* Snadněji se to spravuje. Hierarchie názvů klíčů představuje logické skupiny konfiguračních dat.
* Snadnější použití. Je jednodušší napsat dotaz, který vzor odpovídá klíčům v hierarchické struktuře a načte pouze část konfiguračních dat. Mnoho novějších programovacích architektur má také nativní podporu hierarchických konfiguračních dat, takže vaše aplikace může využívat konkrétní sady konfigurace.

Klíče v konfiguraci aplikace můžete uspořádat hierarchicky mnoha způsoby. Představte si takové klíče, jako [jsou identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý hierarchický klíč je *cesta* k prostředku složená z jedné nebo více součástí, které jsou spojeny oddělovači. Zvolte, jaký znak se má použít jako oddělovač na základě potřeb aplikace, programovacího jazyka nebo architektury. Použijte více oddělovačů pro různé klíče v konfiguraci aplikace.

### <a name="label-keys"></a>Klávesy popisků

Hodnoty klíčů v konfiguraci aplikace mohou mít volitelně atribut popisku. Popisky se používají k rozlišení hodnot klíčů se stejným klíčem. Aplikace pro *klíče1* s popisky *A* a *B* tvoří dva samostatné klíče v úložišti konfigurace aplikací. Ve výchozím nastavení nemá hodnota klíče žádný popisek. Chcete-li explicitně odkazovat na `\0` hodnotu klíče `%00`bez popisku, použijte (ADRESA URL kódovaná jako ).

Popisek poskytuje pohodlný způsob, jak vytvořit varianty klíče. Běžné použití popisků je určit více prostředí pro stejný klíč:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Hodnoty klíče verze

Konfigurace aplikace neaktualizuje hodnoty klíčů automaticky. Popisky slouží k vytvoření více verzí hodnoty klíče. Můžete například zadat číslo verze aplikace nebo ID potvrzení Git u popisků a identifikovat hodnoty klíčů přidružené k určitému softwarovému sestavení.

V popiscích můžete použít libovolný `*`znak `,`unicode s výjimkou znaků , a `\`. Tyto znaky jsou vyhrazeny. Chcete-li zahrnout vyhrazený znak, `\{Reserved Character}`musíte jej uniknout pomocí .

### <a name="query-key-values"></a>Hodnoty klíče dotazu

Každá hodnota klíče je jednoznačně identifikována jeho klíčem `null`plus popiskem, který může být . Vytvoření dotazu úložiště konfigurace aplikace pro hodnoty klíče zadáním vzoru. Úložiště konfigurace aplikace vrátí všechny hodnoty klíče, které odpovídají vzoru a jejich odpovídající hodnoty a atributy. Při volání rozhraní REST API ke konfiguraci aplikace použijte následující vzory klíčů:

| Klíč | |
|---|---|
| `key`je vynechán nebo`key=*` | Odpovídá všem klíčům |
| `key=abc` | Přesně odpovídá názvu klíče **abc.** |
| `key=abc*` | Odpovídá názvům klíčů začínajícím **na abc.** |
| `key=abc,xyz` | Odpovídá názvům klíčů **abc** nebo **xyz**. Omezeno na pět CSV |

Můžete také zahrnout následující vzory popisků:

| Popisek | |
|---|---|
| `label`je vynechán nebo`label=*` | Odpovídá jakémukoli štítku, který obsahuje`null` |
| `label=%00` | Popisek shody `null` |
| `label=1.0.0` | Přesně odpovídá popisku **1.0.0** |
| `label=1.0.*` | Odpovídá popiskům začínajícím **na 1.0.** |
| `label=%00,1.0.0` | Odpovídá `null` popiskům nebo **1.0.0**, omezeno na pět CSV |

## <a name="values"></a>Hodnoty

Hodnoty přiřazené ke klíčům jsou také řetězce unicode. Pro hodnoty můžete použít všechny znaky unicode. Ke každé hodnotě je přidružen volitelný uživatelem definovaný typ obsahu. Tento atribut slouží k ukládání informací o hodnotě, která pomáhá aplikaci správně zpracovat.

Konfigurační data uložená v úložišti konfigurace aplikací jsou šifrována v klidovém stavu a při přenosu. Klíče nejsou šifrovány v klidovém stavu. Konfigurace aplikace není náhradní řešení pro Azure Key Vault. Neuklápějte do něj tajné kódy aplikací.

## <a name="next-steps"></a>Další kroky

* [Snímek k určitému časovému okamžiku](./concept-point-time-snapshot.md)  
* [Správa funkcí](./concept-feature-management.md)  
