---
title: Azure Service Fabric CLI – kontejner sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro kontejnery.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245887"
---
# <a name="sfctl-container"></a>sfctl container
Spusťte příkazy související s kontejnerem na uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vyvolání – rozhraní API | Vyvolá rozhraní API kontejneru na kontejneru nasazeném na Service Fabric uzlu pro daný balíček kódu. |
| Protokoly | Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>vyvolání sfctl kontejneru – rozhraní API
Vyvolá rozhraní API kontejneru na kontejneru nasazeném na Service Fabric uzlu pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Code-Package-instance-ID [požadováno] | ID, které jednoznačně identifikuje instanci balíčku kódu nasazenou v uzlu Service Fabric. <br><br> Dá se načíst pomocí kódu "Service-Package-list". |
| --Code-Package-Name [povinné] | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Container-API-URI-cesta [požadováno] | Cesta k identifikátoru URI REST API kontejneru, místo názvu nebo ID kontejneru použijte {ID}. |
| --Node-Name [povinné] | Název uzlu |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --Container-rozhraní API – tělo | Text požadavku HTTP pro REST API kontejneru |
| --Container-API-Content-Type | Typ obsahu pro REST API kontejnerů, výchozí hodnota je Application/JSON. |
| --Container-API-http-příkaz | Příkaz HTTP pro REST API kontejnerů, výchozí nastavení se získá. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-container-logs"></a>protokoly kontejneru sfctl
Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Code-Package-instance-ID [požadováno] | ID instance balíčku kódu, kterou lze načíst pomocí příkazu Service Code-Package-list. |
| --Code-Package-Name [povinné] | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Node-Name [povinné] | Název uzlu |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
