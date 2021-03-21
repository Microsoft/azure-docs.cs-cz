---
title: Principy nastavení automatického škálování v Azure Monitor
description: Podrobný rozpis nastavení automatického škálování a způsobu jejich fungování. Platí pro Virtual Machines, Cloud Services Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100610859"
---
# <a name="understand-autoscale-settings"></a>Vysvětlení nastavení automatického škálování
Nastavení automatického škálování vám pomůžou zajistit, že máte správné množství prostředků, na kterých běží, aby bylo možné zvládnout kolísání vaší aplikace. Nastavení automatického škálování můžete nakonfigurovat tak, aby se aktivovalo na základě metrik, které indikují zatížení nebo výkon, nebo aktivované v naplánovaném datu a času. V tomto článku se podíváme na podrobné znalosti nastavení automatického škálování. Článek začíná schématem a vlastnostmi nastavení a pak projde různými typy profilů, které lze konfigurovat. Nakonec článek popisuje, jak funkce automatického škálování v Azure vyhodnocuje, který profil se má v daném okamžiku provést.

## <a name="autoscale-setting-schema"></a>Schéma nastavení automatického škálování
Pro ilustraci schématu nastavení automatického škálování se používá následující nastavení automatického škálování. Je důležité si uvědomit, že toto nastavení automatického škálování má následující:
- Jeden profil. 
- Dvě pravidla metrik v tomto profilu: jedno pro horizontální navýšení kapacity a jedno pro škálování v.
  - Pravidlo horizontálního navýšení kapacity se aktivuje, když je průměrné procento PROCESORové metriky sady škálování virtuálního počítače větší než 85 procent za posledních 10 minut.
  - Pravidlo pro škálování na více počítačů se aktivuje, když průměrná hodnota sady škálování virtuálního počítače je nižší než 60 procent za uplynulou minutu.

> [!NOTE]
> Nastavení může mít více profilů. Další informace najdete v části [profily](#autoscale-profiles) . Profil může mít také více pravidel škálování na více instancí a definovaných pravidel pro horizontální navýšení kapacity. Chcete-li zjistit, jak jsou vyhodnocovány, přečtěte si část [vyhodnocení](#autoscale-evaluation) .

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
| Nastavení | ID | ID prostředku nastavení automatického škálování. Nastavení automatického škálování je Azure Resource Manager prostředek. |
| Nastavení | name | Název nastavení automatického škálování. |
| Nastavení | location | Umístění nastavení automatického škálování. Toto umístění se může lišit od umístění prostředku, který se škáluje. |
| properties | targetResourceUri | ID prostředku pro škálování prostředku Pro každý prostředek můžete mít jenom jedno nastavení automatického škálování. |
| properties | uživatelů | Nastavení automatického škálování se skládá z jednoho nebo více profilů. Pokaždé, když je spuštěný modul automatického škálování, se spustí jeden profil. |
| profil | name | Název profilu. Můžete si vybrat libovolný název, který vám pomůže tento profil identifikovat. |
| profil | Kapacita. maximum | Maximální povolená kapacita. Zajišťuje, aby při spuštění tohoto profilu se při automatickém škálování nezměnila velikost prostředků nad tímto číslem. |
| profil | Kapacita. minimum | Minimální povolená kapacita. Zajišťuje automatické škálování při spouštění tohoto profilu, ale neumožňuje škálovat prostředky pod tímto číslem. |
| profil | Kapacita. výchozí | Pokud dojde k potížím při čtení metriky prostředků (v tomto případě CPU "vmss1") a aktuální kapacita je nižší než výchozí, automatické škálování se škáluje na výchozí. To zajistí dostupnost prostředku. Pokud je aktuální kapacita již vyšší, než je výchozí kapacita, automatické škálování se neškáluje. |
| profil | pravidla | Automatické škálování se automaticky škáluje mezi maximální a minimální kapacitou pomocí pravidel v profilu. V profilu můžete mít více pravidel. Obvykle existují dvě pravidla: jeden pro určení, kdy horizontální navýšení kapacity a druhý k určení, kdy se má škálovat. |
| pravidlo | metricTrigger | Definuje podmínku metriky pravidla. |
| metricTrigger | metricName | Název metriky. |
| metricTrigger |  metricResourceUri | ID prostředku prostředku, který generuje metriku. Ve většině případů je to stejné jako u prostředku, který se škáluje. V některých případech se může lišit. Můžete například škálovat sadu škálování virtuálního počítače na základě počtu zpráv ve frontě úložiště. |
| metricTrigger | timeGrain | Doba trvání vzorkování metriky. Například **TimeGrain = "PT1M"** znamená, že metriky by měly být agregovány každé 1 minuty pomocí metody agregace určené v prvku statistiky. |
| metricTrigger | údaj | Agregační metoda v rámci timeGrain období Například **Statistika = "průměr"** a **TIMEGRAIN = "PT1M"** znamená, že metriky by měly být agregovány každé 1 minuty, a to tak, že vyberou průměr. Tato vlastnost určuje, jak je tato metrika vzorkovaná. |
| metricTrigger | timeWindow | Doba, po kterou se bude hledat metrika. Například **TimeWindow = "PT10M"** znamená, že při každém spuštění automatického škálování se dotazuje metriky za posledních 10 minut. Časový interval umožňuje normalizovat metriky a vyhnout se tomu, aby se znovu zobrazovaly přechodné špičky. |
| metricTrigger | timeAggregation | Agregační metoda sloužící k agregaci vzorků metrik. Například **TimeAggregation = "Average"** by měl agregovat vzorkování metriky tím, že přijímá průměr. V předchozím případě vezměte v úvahu ukázky 10 1 minut a průměrně. |
| pravidlo | scaleAction | Akce, která se má provést, když se aktivuje metricTrigger pravidla |
| scaleAction | směr | "Zvětšit" pro horizontální navýšení kapacity nebo "zmenšení" pro horizontální navýšení kapacity.|
| scaleAction | hodnota | Kolik se má zvýšit nebo snížit kapacita prostředku. |
| scaleAction | cooldown | Doba, po kterou se má počkat po operaci škálování, než se znovu změní velikost Například pokud **cooldown = "PT10M"**, automatické škálování se znovu nepokouší o horizontální navýšení kapacity po dobu dalších 10 minut. Cooldown je, aby bylo možné metriky stabilizovat po přidání nebo odebrání instancí. |

## <a name="autoscale-profiles"></a>Profily automatického škálování

Existují tři typy profilů automatického škálování:

- **Pravidelný profil:** Nejběžnější profil. Pokud nepotřebujete škálovat prostředky na základě dne v týdnu nebo konkrétního dne, můžete použít pravidelný profil. Tento profil se pak dá nakonfigurovat s použitím pravidel metrik, která určují, kdy se má škálovat a kdy se má škálovat. Měli byste mít definován pouze jeden pravidelný profil.

    Vzorový profil použitý dříve v tomto článku je příkladem běžného profilu. Všimněte si, že je také možné nastavit profil pro škálování na počet statických instancí pro váš prostředek.

- **Pevný profil data:** Tento profil je určen pro zvláštní případy. Řekněme například, že máte důležitou událost, která se dokončí 26. prosince 2017 (PST). Chcete, aby se minimální a maximální kapacita vašeho prostředku v daném dni lišila, ale pořád škálovat na stejné metriky. V takovém případě byste měli do seznamu profilů nastavení přidat pevný profil data. Profil je nakonfigurován tak, aby běžel pouze v den události. V jakémkoli jiném dni používá automatické škálování běžný profil.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Profil opakování:** Tento typ profilu vám umožní zajistit, aby se tento profil vždycky používal v konkrétní den v týdnu. Profily opakování mají jenom čas spuštění. Spustí se, až do chvíle, kdy se nastaví počáteční profil opakování nebo pevný datum. Nastavení automatického škálování s jediným profilem opakování spouští tento profil, i když je ve stejném nastavení definován pravidelný profil. Následující dva příklady ilustrují, jak se tento profil používá:

    **Příklad 1: pracovní dny a víkendy**
    
    Řekněme, že na víkendech chcete mít maximální kapacitu 4. V pracovních dnech, protože očekáváte větší zatížení, chcete mít maximální kapacitu 10. V takovém případě by nastavení obsahovalo dva profily opakování, jeden pro spuštění na víkendech a druhý v pracovních dnech.
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

    Předchozí nastavení uvádí, že každý profil opakování má plán. Tento plán určuje, kdy se profil začne spouštět. Profil se zastaví, když je čas spustit jiný profil.

    Například v předchozím nastavení je "weekdayProfile" nastaveno na začátek v pondělí v 12:00. To znamená, že tento profil začíná běžet v pondělí v 12:00. V případě, že je naplánováno spuštění příkazu "weekendProfile", pokračuje až do soboty v 12:00.

    **Příklad 2: pracovní doba**
    
    Řekněme, že chcete mít jednu prahovou hodnotu metriky během pracovní doby (9:00 až 5:00 odp.), a pro všechny ostatní časy jednu jinou. Nastavení by vypadalo takto:
    
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
    
    Předchozí nastavení ukazuje, že "businessHoursProfile" začíná v pondělí v 9:00. a pokračuje na 5:00 odp. To je, když začíná běžet "nonBusinessHoursProfile". "NonBusinessHoursProfile" se spustí do 9:00 Úterý a pak se znovu "businessHoursProfile" převezme. To se opakuje do pátku v 5:00. odp. V tomto okamžiku se "nonBusinessHoursProfile" spouští celým způsobem až do pondělí v 9:00.
    
> [!Note]
> Uživatelské rozhraní automatického škálování v Azure Portal vynutilo koncové časy profilů opakování a v mezi profily opakování začne spouštět výchozí profil nastavení automatického škálování.
    
## <a name="autoscale-evaluation"></a>Vyhodnocení automatického škálování
Vzhledem k tomu, že nastavení automatického škálování může mít několik profilů a každý profil může mít několik pravidel metrik, je důležité pochopit, jak se vyhodnocuje nastavení automatického škálování. Pokaždé, když se úloha automatického škálování spustí, začnete tím, že vyberete profil, který se dá použít. Pak automatické škálování vyhodnocuje minimální a maximální hodnoty a veškerá pravidla metrik v profilu a rozhodne, zda je nutná akce škálování.

### <a name="which-profile-will-autoscale-pick"></a>Který profil bude automatické škálování výběru?

Automatické škálování používá k výběru profilu následující sekvenci:
1. Nejprve vyhledá libovolný pevný profil data, který je nakonfigurován tak, aby běžel nyní. Pokud je, automatické škálování ho spustí. Pokud existuje více pevných profilů s pevným datem, které mají být spuštěny, automatické škálování vybere první z nich.
2. Pokud neexistují žádné pevné profily kalendářních dat, automatické škálování prohledává profily opakování. Pokud se nalezne profil opakování, spustí se.
3. Pokud neexistují žádné pevné profily data nebo opakování, automatické škálování spustí normální profil.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak automatické škálování vyhodnocuje více pravidel?

Když automatické škálování určí, který profil se spustí, vyhodnotí všechna pravidla škálování na více instancí v profilu (Jedná se o pravidla se **směrováním = "zvýšení"**).

Pokud je aktivováno jedno nebo více pravidel škálování na více instancí, funkce automatického škálování vypočítá novou kapacitu určenou **scaleAction** každého z těchto pravidel. Pak se škáluje na maximum těchto kapacit, aby se zajistila dostupnost služby.

Řekněme například, že je k dispozici sada škálování virtuálního počítače s aktuální kapacitou 10. Existují dvě pravidla škálování na více instancí: jednu, která zvyšuje kapacitu o 10 procent a jednu, která zvyšuje kapacitu o 3 počty. První pravidlo by vedlo k nové kapacitě 11 a druhé pravidlo by mělo mít kapacitu 13. Pro zajištění dostupnosti služby zvolí automatické škálování akci, která má za následek maximální kapacitu, takže se vybere druhé pravidlo.

Pokud nejsou aktivována žádná pravidla škálování na více instancí, automatické škálování vyhodnotí všechna pravidla škálování (pravidla se **směrováním = "zeslabení"**). Pokud jsou všechna pravidla škálování, která jsou aktivována, má automatické škálování pouze akci škálování v rámci.

Automatické škálování vypočítá novou kapacitu určenou **scaleAction** každého z těchto pravidel. Pak zvolí akci škálování, která má za následek maximum těchto kapacit k zajištění dostupnosti služby.

Řekněme například, že je k dispozici sada škálování virtuálního počítače s aktuální kapacitou 10. Existují dvě pravidla škálování: jeden, který snižuje kapacitu o 50%, a jednu, která snižuje kapacitu o 3 počty. První pravidlo by vedlo k nové kapacitě 5 a druhé pravidlo by mělo mít kapacitu 7. Pro zajištění dostupnosti služby zvolí automatické škálování akci, která má za následek maximální kapacitu, takže se vybere druhé pravidlo.

## <a name="next-steps"></a>Další kroky
Další informace o automatickém škálování najdete v následujících odkazech:

* [Přehled automatického škálování](./autoscale-overview.md)
* [Azure Monitor běžné metriky automatického škálování](./autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](./autoscale-best-practices.md)
* [Použití akcí automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku](./autoscale-webhook-email.md)
* [REST API automatického škálování](/rest/api/monitor/autoscalesettings)

