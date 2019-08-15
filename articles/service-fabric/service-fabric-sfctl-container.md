---
title: Azure Service Fabric CLI – kontejner sfctl | Microsoft Docs
description: Popisuje příkazy kontejneru Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0ce6cf7c627657cf757b0c1ef9aa22428c17a7e7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036470"
---
# <a name="sfctl-container"></a>sfctl container
Spusťte příkazy související s kontejnerem na uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vyvolání – rozhraní API | Vyvolá rozhraní API kontejneru na kontejneru nasazeném na Service Fabric uzlu pro daný balíček kódu. |
| logs | Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric pro daný balíček kódu. |

## <a name="sfctl-container-invoke-api"></a>vyvolání sfctl kontejneru – rozhraní API
Vyvolá rozhraní API kontejneru na kontejneru nasazeném na Service Fabric uzlu pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez schématu identifikátoru URI prostředků\:infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny znakem "\~". Například pokud je název aplikace "Fabric\:/MyApp/app1", identita aplikace by byla "MyApp\~app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Code-Package-instance-ID [požadováno] | ID, které jednoznačně identifikuje instanci balíčku kódu nasazenou v uzlu Service Fabric. <br><br> Dá se načíst pomocí kódu "Service-Package-list". |
| --Code-Package-Name [povinné] | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Container-API-URI-cesta [požadováno] | Cesta k identifikátoru URI REST API kontejneru, místo názvu nebo ID kontejneru použijte {ID}. |
| --Node-Name [povinné] | Název uzlu. |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --container-api-body | Text požadavku HTTP pro REST API kontejneru |
| --container-api-content-type | Typ obsahu pro REST API kontejnerů, výchozí hodnota je Application/JSON. |
| --container-api-http-verb | Příkaz HTTP pro REST API kontejnerů, výchozí nastavení se získá. |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-container-logs"></a>protokoly kontejneru sfctl
Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez schématu identifikátoru URI prostředků\:infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny znakem "\~". Například pokud je název aplikace "Fabric\:/MyApp/app1", identita aplikace by byla "MyApp\~app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Code-Package-instance-ID [požadováno] | ID instance balíčku kódu, kterou lze načíst pomocí příkazu Service Code-Package-list. |
| --Code-Package-Name [povinné] | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Node-Name [povinné] | Název uzlu. |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další postup
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).