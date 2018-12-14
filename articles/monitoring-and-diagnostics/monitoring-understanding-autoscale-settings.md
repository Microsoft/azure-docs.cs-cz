---
title: Vysvětlení nastavení automatického škálování ve službě Azure Monitor
description: Podrobný přehled nastavení automatického škálování a jak pracují. Platí pro Virtual Machines, Cloud Services, Web Apps
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 2347d82b8c2f5a08b944577e5b06cde3b68617b3
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385002"
---
# <a name="understand-autoscale-settings"></a>Vysvětlení nastavení automatického škálování
Nastavení automatického škálování pomáhají, ujistěte se, že máte správného množství prostředků systémem pro zpracování zátěže fluktuující vaší aplikace. Můžete nakonfigurovat nastavení automatického škálování tak, aby se dá spouštět podle metrik, které označují zátěžové nebo výkonové, nebo spouštěnou v naplánované datum a čas. V tomto článku se podíváme podrobné na anatomie nastavení automatického škálování. Článek začíná schématu a vlastnosti nastavení a potom provede odlišných typů profilů, které lze nastavit. Nakonec článek popisuje, jak funkce automatického škálování v Azure vyhodnocuje který profil ke spuštění v daném okamžiku.

## <a name="autoscale-setting-schema"></a>Schéma nastavení automatického škálování
Pro ilustraci schématu nastavení automatického škálování, je použít následující nastavení automatického škálování. Je důležité si uvědomit, že toto nastavení automatického škálování nemá:
- Jeden profil. 
- Dvě metriky pravidla v tomto profilu: jeden pro horizontální navýšení kapacity a jeden pro horizontální snížení kapacity.
  - Pravidlo pro horizontální navýšení kapacity se aktivuje v případě škálovací sady virtuálních počítačů průměrné procento CPU metrika je větší než 85 procent za posledních 10 minut.
  - Pravidlo škálování na méně instancí se aktivuje, pokud průměrná hodnota škálovací sadu virtuálních počítačů je míň než 60 procent za poslední minutu.

> [!NOTE]
> Nastavení může mít několik profilů. Další informace najdete v tématu [profily](#autoscale-profiles) oddílu. Profil, který může mít také více pravidel škálování na víc systémů a definovaných pravidel škálování na méně instancí. Jak se vyhodnocují najdete v tématu [vyhodnocení](#autoscale-evaluation) oddílu.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sekce | Název elementu | Popis |
| --- | --- | --- |
| Nastavení | ID | ID prostředku nastavení automatického škálování Nastavení automatického škálování je prostředek Azure Resource Manageru. |
| Nastavení | jméno | Název nastavení automatického škálování. |
| Nastavení | location | Umístění zadané nastavení automatického škálování. Toto umístění se může lišit od umístění prostředku se škálovat. |
| properties | targetResourceUri | ID prostředku se škálovat prostředek. Můžete mít jenom jedno nastavení automatického škálování pro každý prostředek. |
| properties | Profily | Nastavení automatického škálování se skládá z jednoho nebo více profilů. Při každém spuštění modul automatického škálování je spuštěn v jednom profilu. |
| profil | jméno | Název profilu. Můžete použít libovolný název, který vám pomůže určit, profil. |
| profil | Capacity.maximum | Maximální kapacita povolené. Zajišťuje, že automatické škálování, při provádění tohoto profilu není škálovat prostředek vyšší než toto číslo. |
| profil | Capacity.minimum | Minimální kapacitu povolené. Zajišťuje, že automatické škálování, při provádění tohoto profilu není škálovat prostředek pod tuto hodnotu. |
| profil | Capacity.default | Pokud dojde k nějakému problému čtení metrik prostředku (v tomto případě procesoru "vmss1") a aktuální kapacita bude pod výchozí, automatického škálování horizontálně navýší kapacitu na výchozí hodnotu. Toto je zajištění dostupnosti prostředků. Pokud už je aktuální kapacita vyšší než výchozí kapacita pro dotazy, automatické škálování v škálování. |
| profil | pravidla | Automatické škálování se automaticky škáluje mezi minimální a maximální kapacity, pomocí pravidel v profilu. Můžete mít víc pravidel v profilu. Obvykle existují dvě pravidla: jednu k určení toho, kdy pro horizontální navýšení kapacity a druhým k určení toho, kdy škálovat v. |
| pravidlo | metricTrigger | Definuje podmínku metriky pravidla. |
| metricTrigger | metricName | Název metriky. |
| metricTrigger |  metricResourceUri | ID prostředku prostředku, který vysílá metriky. Ve většině případů je stejný jako prostředek nastavuje. V některých případech může být jiný. Například je možné škálovat škálovací sadu virtuálních počítačů, na základě počtu zpráv ve frontě úložiště. |
| metricTrigger | timeGrain | Doba trvání vzorkování metriky. Například **TimeGrain = "PT1M"** znamená, že metrika má agregovat každou 1 minutu pomocí způsobu agregace určeném v elementu statistiky. |
| metricTrigger | statistiky | Způsob agregace v období timeGrain. Například **statistiky = "Average"** a **timeGrain = "PT1M"** znamená, že metrika má agregovat každou 1 minutu zprůměrováním. Tato vlastnost určuje, jak se definuje tak metriku. |
| metricTrigger | timeWindow | Množství času do minulosti pro metriky. Například **timeWindow = "PT10M"** znamená, že pokaždé, když se spustí automatické škálování, dotáže metriky za posledních 10 minut. Časový interval umožňuje metriky budou normalizovány a přitom vylučuje reakcím na přechodné špičky. |
| metricTrigger | timeAggregation | Agregace metodu používanou k agregaci vzorkovaných metrik. Například **TimeAggregation = "Average"** by měl agregaci vzorkovaných metrik zprůměrováním. V předchozím případě vzorky deseti 1 minutu a průměrné je. |
| pravidlo | scaleAction | Akce, který se má provést při aktivaci metricTrigger pravidla. |
| scaleAction | směr | "Zvýšit" pro horizontální navýšení kapacity, nebo "Snížení" pro horizontální snížení kapacity.|
| scaleAction | hodnota | Kolik zvýšení nebo snížení kapacity prostředku. |
| scaleAction | která | Množství času po operaci škálování než škálování provedete znovu. Například pokud **která = "PT10M"**, automatické škálování nebude pokoušet o škálování pro jiné 10 minut. Která se má povolit metriky stabilizovat po přidání nebo odebrání instance. |

## <a name="autoscale-profiles"></a>Profily automatického škálování

Existují tři typy profilů automatického škálování:

- **Pravidelné profil:** Nejběžnější profilu. Pokud není nutné škálovat vaše prostředků na základě den v týdnu, nebo v určitý den, můžete použít regulární profilu. Tento profil může mít nakonfigurovanou pak metriky pravidla určující, kdy pro horizontální navýšení kapacity a kdy které horizontálně sníží. Byste měli mít jenom jeden profil regulární definované.

    Příklad profilu použitou dříve v tomto článku je příkladem regulární profilu. Všimněte si, že je také možné nastavit profil, který chcete škálovat na počet statických instancí pro váš prostředek.

- **Pevné datum profil:** Tento profil je pro zvláštní případy. Řekněme například, že máte důležité události naráželi 26. prosince 2017 (PST). Chcete, aby minimální a maximální kapacity prostředku v tento den, ale stále škálování na stejné metriky lišit. V takovém případě by měl přidat profil pevného data do vašeho nastavení seznamu profilů. Profil, který je nakonfigurován pro spouštění jenom v události. den. Za další den automatického škálování používá regulární profil.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Profil opakování:** Tento typ profilu umožňuje zajistit, aby tento profil je vždy používal v určitý den v týdnu. Opakování profily mají pouze čas spuštění. Spuštění až do další profil opakování nebo pevného data profilu je nastaven na spuštění. Nastavení automatického škálování s profilem pouze jednoho opakování spouští tento profil, i v případě regulární profilu definován ve stejné nastavení. Následující dva příklady ilustrují, jak tento profil se používá:

    **Příklad 1: Pracovní dny a víkendy**
    
    Řekněme, že o víkendech, má maximální kapacitu na 4. Ve všední dny protože očekáváte větší zátěže, má maximální kapacitu pro 10. Nastavení v tomto případě bude obsahovat dva profily opakování, z nich se má spustit na víkendy a druhý ve všední dny.
    Nastavení vypadá takto:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Předchozí nastavení se zobrazí, že má každý profil opakování plánu. Tento plán Určuje při spuštění profilu spuštění. Profil se zastaví, když je čas spustit jiný profil.

    V předchozí nastavení, například "weekdayProfile" nastavená na spuštění v pondělí v 12:00:00. To znamená, že tento profil spuštění v pondělí v 12:00:00. To pokračuje, dokud sobota ve 12:00, kdy je naplánováno spuštění "weekendProfile".

    **Příklad 2: pracovní doba**
    
    Řekněme, že budete chtít mít jeden mezní hodnota metriky během pracovní doby (9:00:00 do 17:00 hodin) a jinou používat pro jinou dobu. Toto nastavení bude vypadat takto:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Předchozí nastavení ukazuje, že zahájí spuštění v pondělí v 9:00:00 "businessHoursProfile" a pokračuje v 17:00:00. Který je při spuštění "nonBusinessHoursProfile". "nonBusinessHoursProfile" běží až do 9:00:00 úterý, a potom "businessHoursProfile" převezme znovu. To se opakuje dokud pátek v 17:00:00. V tomto okamžiku "nonBusinessHoursProfile" se spustí až po pondělí v 9:00:00.
    
> [!Note]
> Automatické škálování uživatelského rozhraní na webu Azure Portal vynucuje koncový čas opakování profilů a zahájí spuštění výchozího profilu nastavení automatického škálování mezi profily opakování.
    
## <a name="autoscale-evaluation"></a>Vyhodnocování automatického škálování
Oznámeno, že nastavení automatického škálování může mít několik profilů a každý profil může mít několik metrik pravidla, je důležité pochopit, jak se vyhodnocují nastavení automatického škálování. Při každém spuštění úlohy automatického škálování začne výběrem profil, který se vztahuje. Automatické škálování pak vyhodnotí jako minimální a maximální hodnoty a všechny metriky pravidel v profilu a rozhodne, pokud je akce škálování nezbytné.

### <a name="which-profile-will-autoscale-pick"></a>Který profil bude vybrat automatického škálování?

Vybrat profil, který využívá automatické škálování následující posloupnost:
1. Nejprve vyhledá všechny pevného data profilu, který je konfigurován pro běh nyní. Pokud existuje, automatické škálování ji spustí. Pokud existuje víc profilů pevného data, které se mají spustit, automatické škálování vybere první z nich.
2. Pokud neexistují žádné profily pevného data, bude vypadat na opakování profily automatického škálování. Pokud je nalezen profil opakování, spustí ho.
3. Pokud neexistují žádné pevné datum nebo profily opakování, spustí automatické škálování regulární profilu.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak Autoscale evaluate více pravidel?

Po automatické škálování Určuje, který profil spuštění, vyhodnotí všechna pravidla horizontální navýšení kapacity v profilu (jedná se o pravidla s **směr = "Zvýšit"**).

Pokud se jedno nebo více pravidel škálování na víc systémů se aktivuje, automatické škálování vypočítá nové kapacity určené **scaleAction** každé z těchto pravidel. Potom, provede se škálování na maximální počet těchto kapacity, abyste zajistili dostupnost služby.

Například Řekněme, že existuje je škálovací sady virtuálních počítačů s aktuální kapacita 10. Existují dvě pravidla horizontální navýšení kapacity: ten, který zvyšuje kapacitu 10 procent a ten, který zvyšuje kapacitu podle počtu 3. První pravidlo by mělo za následek novou kapacitu 11 a druhé pravidlo by mělo za následek kapacitou 13. K zajištění dostupnosti služby, automatické škálování zvolí, že je vybrán akci, která má za následek maximální kapacitu, tak druhé pravidlo.

Pokud se žádná pravidla horizontální navýšení kapacity se aktivuje, automatické škálování vyhodnotí všechna škálování na méně instancí pravidla (pravidla s **směr = "Snížit"**). Automatické škálování akci škálování na méně instancí zabere jenom, pokud všechna pravidla škálování na méně instancí se aktivuje.

Automatické škálování vypočítá nové kapacity určené **scaleAction** každé z těchto pravidel. Potom vybere akci škálování, jejímž výsledkem maximální počet těchto kapacity pro zajištění dostupnosti služby.

Například Řekněme, že existuje je škálovací sady virtuálních počítačů s aktuální kapacita 10. Existují dvě pravidla škálování na méně instancí: ten, který snižuje kapacita o 50 procent a jednu, která sníží kapacitu počtu 3. První pravidlo by mělo za následek novou kapacitu 5 a druhé pravidlo by mělo za následek kapacitou 7. K zajištění dostupnosti služby, automatické škálování zvolí, že je vybrán akci, která má za následek maximální kapacitu, tak druhé pravidlo.

## <a name="next-steps"></a>Další postup
Další informace o automatické škálování rekapitulací takto:

* [Přehled automatického škálování](../azure-monitor/platform/autoscale-overview.md)
* [Azure Monitor běžné metriky automatického škálování](../azure-monitor/platform/autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md)
* [Pomocí akcí automatického škálování můžete odeslat emailová a webhooková oznámení výstrah](../azure-monitor/platform/autoscale-webhook-email.md)
* [Rozhraní REST API pro automatické škálování](https://msdn.microsoft.com/library/dn931953.aspx)
