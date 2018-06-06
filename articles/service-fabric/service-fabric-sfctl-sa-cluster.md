---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl sa-cluster | Microsoft Docs
description: Popisuje příkazy clusteru Service Fabric rozhraní příkazového řádku sfctl samostatné.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764003"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Správa samostatných clusterů Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Konfigurace | Získáte konfiguraci clusteru Service Fabric samostatné. |
| Konfigurace upgradu | Spusťte upgrade konfiguraci samostatný cluster Service Fabric. |
| Stav upgradu | Získáte stav upgradu konfigurace clusteru samostatný cluster Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>Konfigurace clusteru sa sfctl
Získáte konfiguraci clusteru Service Fabric samostatné.

Získáte konfiguraci clusteru Service Fabric samostatné. Konfigurace clusteru obsahuje vlastnosti clusteru, které obsahují různé typy uzlů v clusteru, konfigurace zabezpečení, chyby a topologií domény upgradu atd.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Konfigurace api-version [vyžaduje] | Verze rozhraní API v samostatné konfiguraci clusteru json. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>Konfigurace clusteru sa sfctl-upgradu
Spusťte upgrade konfiguraci samostatný cluster Service Fabric.

Ověření upgradu parametrů zadaných konfigurací a spusťte upgrade konfiguraci clusteru, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Konfigurace clusteru [vyžaduje] | Konfigurace clusteru. |
| --rozdílů není v pořádku uzly | Maximální povolené procento snížení stavu rozdílové během upgradu. Povolené hodnoty jsou celočíselné hodnoty od 0 do 100. |
| --stavu. Zkontrolujte opakování | Doba mezi pokusy o provedení stavu kontroluje, pokud aplikace nebo clusteru není v pořádku.  Výchozí\: PT0H0M0S. |
| --stavu. Zkontrolujte nestabilním | Doba, aplikace nebo clusteru musí zůstat v pořádku.  Výchozí\: PT0H0M0S. |
| --health-check-wait | Doba čekání po dokončení upgradu domény před zahájením stavu zkontroluje proces.  Výchozí\: PT0H0M0S. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --není v pořádku – aplikace | Maximální povolené procento žádostí, není v pořádku během upgradu. Povolené hodnoty jsou celočíselné hodnoty od 0 do 100. |
| --není v pořádku uzly | Maximální povolené procento není v pořádku uzlů během upgradu. Povolené hodnoty jsou celočíselné hodnoty od 0 do 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento snížení stavu rozdílové upgradu domény během upgradu. Povolené hodnoty jsou celočíselné hodnoty od 0 do 100. |
| --upgrade-domain-timeout | Časový limit domény upgradu.  Výchozí\: PT0H0M0S. |
| – upgrade vypršení časového limitu | Časový limit upgradu.  Výchozí\: PT0H0M0S. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>upgrade clusteru sa sfctl-status
Získáte stav upgradu konfigurace clusteru samostatný cluster Service Fabric.

Konfigurace clusteru upgradu podrobnosti získání stavu samostatné clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).