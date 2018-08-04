---
title: Cluster Azure Service Fabric CLI - sfctl přidružení zabezpečení-| Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl samostatného clusteru.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: d7f33bf0657ca2a6888387b7651706f9de537bb4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494352"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Správa samostatné clustery Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Konfigurace | Získání konfigurace samostatného clusteru Service Fabric. |
| Konfigurace upgradu | Spuštění upgradu konfiguraci samostatného clusteru Service Fabric. |
| Stav upgradu | Získáte stav upgradu konfigurace clusteru samostatného clusteru Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Získání konfigurace samostatného clusteru Service Fabric.

Konfigurace clusteru obsahuje vlastnosti clusteru, které obsahují různé typy uzlů v clusteru, konfigurace zabezpečení, odolnost a upgradovací doména topologie atd.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Konfigurace api-version [povinné] | Verze rozhraní API konfigurace samostatného clusteru json. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config upgradu
Spuštění upgradu konfiguraci samostatného clusteru Service Fabric.

Ověření upgradu parametrů zadaných konfigurací a spusťte upgrade konfigurace clusteru, pokud jsou platné parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -clusteru config [povinné] | Konfigurace clusteru, která se použije ke clusteru. |
| – zásady stavu aplikace | JSON kódovaný slovník párů název typu aplikace a maximální procento před vyvoláním chyby není v pořádku. |
| --delta poškozené uzly | Maximální povolené procento rozdílových rozkladu stavu během upgradu. Povolené hodnoty jsou hodnoty celé číslo od 0 do 100. |
| --stavu. Zkontrolujte opakování | Doba mezi pokusy o provedení stavu kontroluje, pokud aplikace nebo clusteru není v pořádku.  Výchozí\: PT0H0M0S. |
| --stabilní verze stavu zaškrtnutí | Doba, aplikace nebo clusteru musí zůstat v pořádku.  Výchozí\: PT0H0M0S. |
| --health-check-wait | Doba čekání po dokončení upgradu domény před zahájením stavu kontroluje procesu.  Výchozí\: PT0H0M0S. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --poškozené aplikace | Maximální povolené procento žádostí, není v pořádku během upgradu. Povolené hodnoty jsou hodnoty celé číslo od 0 do 100. |
| --poškozené uzly | Maximální povolené procento poškozené uzly během upgradu. Povolené hodnoty jsou hodnoty celé číslo od 0 do 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento snížení stavu rozdílové upgradovací domény během upgradu. Povolené hodnoty jsou hodnoty celé číslo od 0 do 100. |
| --upgrade-domain-timeout | Časový limit domény upgradu.  Výchozí\: PT0H0M0S. |
| --upgrade vypršení časového limitu | Časový limit upgradu.  Výchozí\: PT0H0M0S. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

### <a name="examples"></a>Příklady

Spuštění clusteru konfigurace aktualizace sfctl sa-config upgradu clusteru – konfigurace clusteru <YOUR CLUSTER CONFIG> – zásady stavu "{" fabric: / systému ": {"ConsiderWarningAsError": true}}"

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa clusteru upgrade-status
Získáte stav upgradu konfigurace clusteru samostatného clusteru Service Fabric.

Konfigurace clusteru upgrade podrobnosti získání stavu ze samostatného clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).