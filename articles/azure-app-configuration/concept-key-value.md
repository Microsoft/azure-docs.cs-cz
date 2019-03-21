---
title: Azure párů klíč hodnota konfigurace aplikací | Dokumentace Microsoftu
description: Přehled způsob uložení dat konfigurace v konfiguraci aplikací Azure
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
ms.openlocfilehash: 352bc20bb4082dd14b810a6afe85653cfd67e7e1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224465"
---
# <a name="key-value-store"></a>Ukládání hodnot klíče

Konfigurace aplikace pro Azure jsou uložena konfigurační data jako páry klíč hodnota. Páry klíč hodnota jsou jednoduché a přitom flexibilní způsob, jak reprezentují různé typy nastavení aplikace, která vývojářům znají.

## <a name="keys"></a>Klíče

Klíče jako název pro páry klíč hodnota a umožňují ukládat a načítat hodnoty, které odpovídají. Běžnou praxí uspořádat klíče do hierarchického oboru názvů pomocí znaku oddělovače, jako je `/` nebo `:`. Použijte konvenci, která je nejvhodnější pro vaši aplikaci. Konfigurace aplikace zpracovává klíče jako celek. Ho nebude analyzovat klíče zjistit, jak jejich názvy jsou strukturované nebo vynucování libovolného pravidla na ně.

Využití úložiště konfigurace v rámci aplikační architektury může určovat konkrétní pojmenování schémata pro hodnoty klíče. Jako příklad definuje jazyka Java Spring cloudem framework `Environment` prostředky, které určují nastavení aplikace Spring k být parametrizován proměnné, které zahrnují *název_aplikace* a *profilu*. Klíče pro Spring cloudem související konfigurační data obvykle začínat tyto dva prvky, které jsou oddělené oddělovačem.

Klíče uložené v konfiguraci aplikací jsou malá a velká písmena, na základě kódování unicode řetězce. Klíče *app1* a *App1* se liší v obchodě s aplikacemi konfigurace. Mějte to při použití nastavení konfigurace v rámci aplikace, protože některé architektury zpracovat konfigurační klíče ohled. Například systém konfigurace ASP.NET Core považuje za klíče nerozlišuje velikost písmen řetězců. Aby se zabránilo nepředvídatelné chování při dotazování konfigurace aplikací v rámci aplikace ASP.NET Core, nepoužívejte klíče, které se liší pouze malá a velká písmena.

Můžete použít libovolný znak unicode v názvech klíčů do konfigurace aplikace s výjimkou `*`, `,`, a `\`. Tyto znaky jsou rezervované. Pokud je potřeba zahrnout rezervovaným znakem, musíte před něj s použitím `\{Reserved Character}`. Je celková velikost maximálně 10 000 znaků v páru klíč hodnota. Toto omezení zahrnuje všechny znaky v klíči a jeho hodnotu, a všechny přidružené volitelné atributy. V rámci tohoto limitu může mít mnoho hierarchické úrovní pro klíče.

### <a name="design-key-namespaces"></a>Obory názvů klíčů návrhu

Existují dvě obecné přístupy k pojmenování klíče používané pro konfigurační data: ploché nebo hierarchické. Tyto metody jsou podobné z hlediska využití aplikace, ale hierarchické pojmenování nabízí celou řadu výhod:

* Snadněji čtou. Místo jeden dlouhý sekvence znaků oddělovače ve hierarchického oboru názvů klíčů fungovat jako mezery ve větě. Obsahují taky přirozeným zalomení mezi slovy.
* Snazší správa. Název klíče hierarchie představuje logické skupiny konfigurační data.
* Usnadňuje používání. Je jednodušší napsat dotaz, který vzorů klíče v hierarchické struktuře a načte jenom část konfigurační data. Navíc mnoho novější programovací rozhraní mají nativní podpory pro hierarchické konfigurační data, které takové, které vaše aplikace může provádět pomocí konkrétní sady configuration.

Můžete uspořádat klíče v konfiguraci aplikace hierarchicky mnoha způsoby. Představte si, že tyto klíče jako [identifikátory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Každý klíč hierarchické je prostředek *cesta* skládá z jedné nebo více součástí, které jsou propojeny pomocí oddělovače. Zvolte počátečního znaku, který se použije jako oddělovač, podle které vaše aplikace, programovací jazyk, nebo je nutné rozhraní framework. Použití více oddělovačů pro různé klíče v konfiguraci aplikací.

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

### <a name="version-key-values"></a>Hodnoty klíče verze

Hodnoty klíče v konfiguraci aplikace můžou mít atribut label. Popisky se používají k odlišení hodnoty klíče se stejným klíčem. Klíč *app1* popisky *v1* a *v2* tvoří dvě samostatné hodnoty klíče v obchodě s aplikacemi konfigurace. Ve výchozím nastavení, popisek s hodnotou klíče je prázdný, nebo `null`.

Konfigurace aplikace není hodnoty klíče verze automaticky podle jejich. Použití popisků jako způsob, jak vytvořit více verzí modulu hodnotu klíče. Například můžete zadat číslo verze aplikace nebo Identifikátor potvrzení Git v popiscích k identifikaci hodnoty klíče přidružené k sestavení určitého softwaru.

Můžete použít libovolný znak unicode v popiscích s výjimkou `*`, `,`, a `\`. Tyto znaky jsou rezervované. Zahrnout rezervovaným znakem, musíte před něj s použitím `\{Reserved Character}`.

### <a name="query-key-values"></a>Hodnoty klíče dotazu

Každá hodnota klíče je jednoznačně identifikují pomocí jeho klíče a popisek, který může být `null`. Dotazování obchod s aplikacemi konfigurační hodnoty klíčů tak, že zadáte vzor. Konfigurace úložiště aplikace vrátí všechny klíčové hodnoty, které odpovídají vzoru a jejich odpovídající hodnoty a atributů. Ve volání rozhraní REST API pro konfiguraci aplikací použijte následující klíčové vzorce:

| Klíč | |
|---|---|
| `key` je tento parametr vynechán nebo `key=*` | Odpovídá všechny klíče |
| `key=abc` | Název klíče shody **abc** přesně |
| `key=abc*` | Klíče shody s názvy začínajícími **abc** |
| `key=*abc` | Názvy, které končí klíče shody **abc** |
| `key=*abc*` | Názvy, které obsahují klíče shody **abc** |
| `key=abc,xyz` | Odpovídá názvů klíčů **abc** nebo **xyz**, je omezený na pět sdílené svazky clusteru |

Můžete zahrnout také následující modely popisku:

| Štítek | |
|---|---|
| `label` je tento parametr vynechán nebo `label=*` | Odpovídá libovolný popisek, který obsahuje `null` |
| `label=%00` | Odpovídá `null` popisek |
| `label=1.0.0` | Popisek shody **1.0.0** přesně |
| `label=1.0.*` | Odpovídá popisky, které začínají **1.0.** |
| `label=*.0.0` | Odpovídá popisky, které končí **.0.0** |
| `label=*.0.*` | Odpovídá popisky, které obsahují **.0.** |
| `label=%00,1.0.0` | Odpovídá popisky `null` nebo **1.0.1**, je omezený na pět sdílené svazky clusteru |

## <a name="values"></a>Hodnoty

Hodnoty přiřazené k klíče jsou také řetězců v kódu unicode. Pro hodnoty můžete použít všechny znaky unicode. Není volitelný uživatelem definovaný typ obsahu spojené s každou hodnotu. Tento atribut použijte k ukládání informací, například kódování schématu, o hodnotu, která pomáhá zajistit u aplikací ke zpracování správně.

Konfigurační data uložená v úložišti konfigurace aplikace, která zahrnuje všechny klíče a hodnoty, je v klidovém stavu a přenosu šifrovaný. Konfigurace aplikace není náhradní řešení pro Azure Key Vault. V něm není ukládání tajných klíčů aplikací.

## <a name="next-steps"></a>Další postup

* [Koncept: Snímek bodu v čase](concept-point-time-snapshot.md)  
