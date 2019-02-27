---
title: Azure párů klíč hodnota konfigurace aplikací | Dokumentace Microsoftu
description: Přehled jak konfigurační data se ukládají v konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884818"
---
# <a name="key-value-store"></a>Ukládání hodnot klíče

Konfigurace aplikace pro Azure jsou uložena konfigurační data jako páry klíč hodnota, jednoduché a přitom flexibilní způsob, jak představujících různé typy nastavení aplikace, která vývojářům znají.

## <a name="keys"></a>Klíče

Klíče jako název pro páry klíč hodnota a slouží k ukládání a načítání hodnoty, které odpovídají. To je běžný postup uspořádat klíče do hierarchického oboru názvů pomocí znaku oddělovače (například `/` nebo `:`) založené na konvenci, která je nejvhodnější pro vaši aplikaci. Konfigurace aplikace zpracovává klíče jako celek. Jejich analyzovat klíče zjistit, jak jejich názvy jsou strukturované nebo vynucování libovolného pravidla na ně.

Využití úložiště konfigurace v rámci aplikační architektury může určovat konkrétní pojmenování schémata pro hodnoty klíče. Jako příklad definuje jazyka Java Spring cloudem framework `Environment` prostředky, které určují nastavení aplikace Spring k být parametrizován proměnných včetně *název_aplikace* a *profilu*. Klíče pro Spring Clould související konfigurační data se obvykle začínat tyto dva prvky samostatně pomocí oddělovače.

Klíče uložené v konfiguraci aplikací jsou malá a velká písmena, na základě kódování unicode řetězce. Klíče *app1* a *App1* se liší v obchodě s aplikacemi konfigurace. Mějte to při použití nastavení konfigurace v rámci aplikace, protože některé architektury zpracování konfigurační klíče ohled. Například systém konfigurace ASP.NET Core považuje za klíče nerozlišuje velikost písmen řetězců. Aby se zabránilo nepředvídatelné chování při dotazování na konfiguraci aplikace v rámci aplikace ASP.NET Core, není vhodné používat klíče, které se liší pouze velikostí písmen.

Budete moct použít jakýkoli znak unicode s názvy klíčů zadaný v konfiguraci aplikace, s výjimkou `*`, `,` a `\` jsou vyhrazena. Pokud je potřeba zahrnout rezervovaným znakem, musíte před něj pomocí `\{Reserved Character}`. Existuje limit 10 tisíc znaků v páru klíč hodnota kombinované velikosti. To zahrnuje všechny znaky v klíči a jeho hodnotu, a všechny přidružené volitelné atributy. V rámci tohoto limitu může mít mnoho hierarchické úrovní pro klíče.

### <a name="designing-key-namespaces"></a>Navrhování oborů názvů klíčů

Existují dvě obecné přístupy k pojmenování klíče používané pro konfigurační data: ploché nebo hierarchické. Tyto metody jsou velmi podobné z hlediska využití aplikace, ale ten nabízí celou řadu výhod:

* Snadněji čtou. Místo jeden dlouhý sekvence znaků oddělovače ve hierarchického oboru názvů klíčů funguje jako mezery ve větě a poskytuje přirozené zalomení mezi slovy.
* Snazší správa. Název klíče hierarchie představuje logické skupiny konfigurační data.
* Usnadňuje používání. Je jednodušší napsat dotaz, který vzorů klíče v hierarchické struktuře a načte jenom část konfigurační data. Kromě toho mnoho novější programovací rozhraní mají nativní podpory pro hierarchické konfigurační data, které takové, které vaše aplikace může provádět pomocí konkrétní sady configuration.

Můžete uspořádat klíče v konfiguraci aplikace hierarchicky mnoha způsoby. Si můžete představit jako tyto klíče [identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý klíč hierarchické je prostředek *cesta* skládá z jednoho nebo více komponent propojeny pomocí oddělovače. Můžete zvolit, jaké znak, který se použije jako oddělovač, podle které vaše aplikace, programovací jazyk nebo architekturu potřebuje. Pro různé klíče v konfiguraci aplikací můžete více oddělovačů.

Tady je několik příkladů můžete jak strukturovat vaše názvy klíčů do hierarchie:

* Podle prostředí

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* Podle služby komponent

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* Podle oblastí nasazení

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Správa verzí klíč hodnota

Klíč hodnota v konfiguraci aplikace můžou mít **popisek** atribut. Popisky se používají k odlišení klíč hodnota se stejným klíčem. Klíč *app1* popisky *v1* a *v2* tvoří dvě samostatné – hodnoty klíče v obchodě s aplikacemi konfigurace. Ve výchozím nastavení, popisek páru klíč hodnota je prázdná (nebo `null`).

Konfigurace aplikace není verze klíč hodnota nepodporuje automaticky podle jejich úpravou. Popisky lze použít jako způsob, jak vytvořit více verzí tohoto páru klíč hodnota. Například můžete zadat číslo verze aplikace nebo Identifikátor potvrzení Git v popiscích k identifikaci hodnoty klíče přidružené k sestavení určitého softwaru.

Budete moct použít libovolný znak unicode v popiscích, s výjimkou `*`, `,` a `\`, které jsou vyhrazené. Pokud je potřeba zahrnout rezervovaným znakem, musíte před něj pomocí `\{Reserved Character}`.

### <a name="querying-key-values"></a>Dotazování hodnoty klíče

Každý klíč hodnota se jednoznačně identifikují pomocí jeho klíče a popisek, který může být `null`. Tak, že zadáte vzor dotazu obchod s aplikacemi konfigurace pro hodnoty klíče Vrátí všechny hodnoty klíče, které odpovídají vzoru konfigurace obchodu s aplikacemi a jejich odpovídající hodnoty a atributů. Ve volání rozhraní REST API pro konfiguraci aplikací můžete použít následující klíčové vzorce:

| Klíč | |
|---|---|
| `key` je tento parametr vynechán nebo `key=*` | Odpovídá všechny klíče |
| `key=abc` | Název klíče shody **abc** přesně. |
| `key=abc*` | Klíče shody s názvy začínajícími **abc** |
| `key=*abc` | Názvy, které končí klíče shody **abc** |
| `key=*abc*` | Názvy, které obsahují klíče shody **abc** |
| `key=abc,xyz` | Odpovídá názvů klíčů **abc** nebo **xyz**omezené na 5 sdílené svazky clusteru |

Můžete použít také následující modely popisku:

| Štítek | |
|---|---|
| `label` je tento parametr vynechán nebo `label=*` | Odpovídá libovolný popisek, včetně `null` |
| `label=%00` | Odpovídá `null` popisek. |
| `label=1.0.0` | Popisek shody **1.0.0** přesně |
| `label=1.0.*` | Odpovídá popisky, které začínají **1.0.** |
| `label=*.0.0` | Odpovídá popisky, které končí **.0.0** |
| `label=*.0.*` | Odpovídá popisky, které obsahují **.0.** |
| `label=%00,1.0.0` | Odpovídá popisky `null` nebo **1.0.1**omezené na 5 sdílené svazky clusteru |

## <a name="values"></a>Hodnoty

Hodnoty přiřazené k klíče jsou také řetězců v kódu unicode. Pro hodnoty můžete použít všechny znaky unicode. Je volitelný uživatelem definovaný **typ obsahu** spojené s každou hodnotu. Tento atribut slouží k ukládání informací (například schéma kódování) o hodnotu, která pomáhá zajistit u aplikací ke zpracování správně.

Konfigurační data uložená v úložišti konfigurace aplikace, včetně všech klíčů a hodnot, jsou v klidovém stavu a přenosu šifrovaný. Aplikace konfigurace však není náhradní řešení pro Azure Key Vault. V něm neměli ukládat tajné kódy aplikace.

## <a name="next-steps"></a>Další postup

* [Koncept: Snímek bodu v čase](concept-point-time-snapshot.md)  
