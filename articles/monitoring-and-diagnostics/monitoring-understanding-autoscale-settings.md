---
title: Principy nastavení škálování v Azure monitorování
description: Podrobné rozpis nastavení automatického škálování a jak pracují. Platí pro virtuální počítače, cloudové služby, webové aplikace
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 982bc43fd86a808da07833d77bde17e17789b2d6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264992"
---
# <a name="understand-autoscale-settings"></a>Vysvětlení nastavení automatického škálování
Nastavení automatického škálování pomáhají zajistit, že máte správného množství prostředků pro zpracování kolísání zátěže vaší aplikace spuštěna. Můžete nakonfigurovat nastavení automatického škálování, aby se spouštěly podle metriky, které znamenat výkon nebo zatížení nebo spouštěná v naplánované datum a čas. V tomto článku se podíváme podrobné na anatomy nastavení automatického škálování. Článek začíná schéma a vlastnosti nastavení a potom provede jiný profil typy, které lze konfigurovat. Nakonec článek popisuje, jak funkce škálování v Azure vyhodnotí který profil provést v daném okamžiku.

## <a name="autoscale-setting-schema"></a>Schéma nastavení automatického škálování
Pro ilustraci schéma nastavení automatického škálování, je použít následující nastavení automatického škálování. Je důležité si uvědomit, že toto nastavení automatického škálování nemá:
- Jeden profil. 
- Dvě pravidla metriky v tomto profilu: jeden pro škálování a jeden pro škálování v.
  - Pravidlo Škálováním na více systémů se aktivuje, když metrika procesoru průměrnou procentuální hodnotu škálovací sadu virtuálních počítačů je větší než 85 procent za posledních 10 minut.
  - Pravidlo škálování v aktivováno, když průměr škálovací sadu virtuálních počítačů je menší než 60 procent pro uplynulé minuty.

> [!NOTE]
> Nastavení může mít několik profilů. Další informace najdete v tématu [profily](#autoscale-profiles) části. Profil může mít víc pravidel Škálováním na více systémů a pravidel škálování v definované. Jak se vyhodnocují najdete v tématu [vyhodnocení](#autoscale-evaluation) části.

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
| Nastavení | ID | ID nastavení automatického škálování prostředku. Nastavení automatického škálování je prostředek Azure Resource Manager. |
| Nastavení | jméno | Název nastavení automatického škálování. |
| Nastavení | location | Umístění, nastavení automatického škálování. Toto umístění se může lišit od umístění prostředku se škálovat. |
| properties | targetResourceUri | ID prostředku se škálovat prostředek. Může mít pouze jeden nastavení automatického škálování na prostředku. |
| properties | Profily | Nastavení automatického škálování se skládá z nejméně jeden profil. Pokaždé, když modul škálování běží, provede jeden profil. |
| profil | jméno | Název profilu. Můžete použít libovolný název, který pomáhá identifikovat profil. |
| profil | Capacity.maximum | Maximální kapacita povoleny. Zajišťuje, že škálování při provádění tento profil, není škálovat prostředek výše toto číslo. |
| profil | Capacity.minimum | Minimální kapacita povoleny. Zajišťuje, že škálování při provádění tento profil, není škálování prostředku pod tuto hodnotu. |
| profil | Capacity.default | Pokud dojde k problému čtení metrika prostředků (v tomto případě procesoru "vmss1"), a aktuální kapacita je nižší než výchozí, automatické škálování horizontálně navýší kapacitu na výchozí hodnoty. To je potřeba zajistit dostupnost prostředku. Pokud je aktuální kapacita již vyšší než výchozí kapacita, automatické škálování se nedá použít v. |
| profil | pravidla | Škálování automaticky přizpůsobí mezi maximální a minimální kapacitou, která pomocí pravidel v profilu. Můžete mít více pravidel v profilu. Obvykle existují dvě pravidla: jeden k určení, kdy chcete škálovat a druhým k určení, kdy škálovat v. |
| pravidlo | metricTrigger | Definuje metriky podmínku pravidla. |
| metricTrigger | metricName | Název metriky. |
| metricTrigger |  metricResourceUri | ID prostředku prostředek, který vysílá metriku. Ve většině případů je stejný jako prostředek se škálovat. V některých případech může být různé. Například je možné škálovat škálovací sadu virtuálních počítačů, na základě počtu zpráv ve frontě úložiště. |
| metricTrigger | Časovými úseky | Doba trvání metriky vzorkování. Například **časovými úseky = "PT1M"** znamená, že podle metrik, které by měl agregovat každých 1 minuta, pomocí metody agregace zadaný v elementu statistiky. |
| metricTrigger | statistiky | Metoda agregace v období časovými úseky. Například **statistiky = "Střední"** a **časovými úseky = "PT1M"** znamená, že metriky by měl být agregován každých 1 minuta, provedením průměr. Tato vlastnost určuje, jak je metrika vzorků. |
| metricTrigger | Hodnota timeWindow | Množství času do minulosti metrik. Například **hodnota timeWindow = "PT10M"** znamená, že pokaždé, když spustí škálování, dotazuje metriky pro za posledních 10 minut. Časový interval umožňuje vaše metriky budou normalizovány a zabraňuje reagovat na přechodný špičky. |
| metricTrigger | Agregace času | Metoda agregace použitá k agregaci jen Vzorkovaná metriky. Například **agregace času = "Střední"** by měl agregovat jen Vzorkovaná metriky provedením průměr. V předchozím případě vzorky deseti 1 minutu a průměrné je. |
| pravidlo | scaleAction | Akce, který se má provést, když se aktivuje metricTrigger pravidla. |
| scaleAction | směr | "Zvýšit" horizontální navýšení kapacity, nebo "Snížíte" škálování v.|
| scaleAction | hodnota | Kolik můžete zvýšit nebo snížit kapacitu prostředku. |
| scaleAction | cooldown | Množství času čekání po operaci škálování před škálování znovu. Například pokud **cooldown = "PT10M"**, automatické škálování nebude pokoušet o škálování znovu pro jiné 10 minut. Cooldown je umožnit metriky stabilizovat po přidání nebo odebrání instance. |

## <a name="autoscale-profiles"></a>Profilů automatického škálování

Existují tři typy profilů automatického škálování:

- **Regulární profilu:** nejběžnější profilu. Pokud nepotřebujete škálování prostředku na základě den v týdnu nebo v určitý den, můžete regulární profil. Tento profil můžete pak nakonfigurovat metriky pravidla, která určují, kdy chcete škálovat a kdy škálovat v. Měli byste mít jenom jeden profil regulární definované.

    Příklad profilu použít dříve v tomto článku je příklad regulární profilu. Všimněte si, že je také možné nastavit profil škálovat na počet statická instance prostředku.

- **Pevné datum profilu:** tento profil je pro zvláštní případy. Řekněme například, že máte důležité události objevuje na 26 prosinec 2017 (PST). Chcete minimální a maximální kapacity prostředku lišit na tento den, ale stále škálování na stejné metriky. V takovém případě by měl přidat profil pevné datum vaše nastavení seznam profilů. Profil je nakonfigurována pro spuštění pouze dne události. Pro další den používá škálování regulární profil.

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
    
- **Profil opakování:** tohoto typu profilu umožňuje zajistit, aby tento profil je vždy používaly v určitý den v týdnu. Profily opakování mít pouze čas spuštění. Spuštění až do další profil opakování nebo pevného data profilu je nastaven na spuštění. Nastavení automatického škálování s opakování jenom jeden profil se spustí tento profil, i v případě, že je profil regulární definovaný ve stejné nastavení. Následující dva příklady ilustrují, jak tento profil se používá:

    **Příklad 1: Dny v týdnu oproti víkendů**
    
    Řekněme, že na víkendy, má vaše maximální kapacita být 4. Ve všední dny protože očekáváte, že další zatížení, budete chtít vaší maximální kapacita být 10. V takovém případě vaše nastavení by obsahovat dva profily opakování, jednu pro spuštění na víkendy a druhá ve všední dny.
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

    Předchozí nastavení ukazuje, že má každý profil opakování plánu. Tento plán Určuje při spuštění profil spuštění. Profil zastaví, když je na čase spuštění jiný profil.

    V předchozí nastavení, například "weekdayProfile" nastavená na spuštění v pondělí ve 12:00. To znamená, tento profil spuštění v pondělí ve 12:00. Až sobotu pokračuje ve 12:00, kdy je naplánováno spustit "weekendProfile".

    **Příklad 2: pracovní dobu**
    
    Řekněme, že budete chtít mít jeden metriky prahová hodnota během pracovní doby (9:00:00 do 5:00 PM) a jinou pro všechny ostatní časy. Toto nastavení bude vypadat takto:
    
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
    
    Předchozí nastavení uvádí, že začíná v pondělí spuštěné v 9:00:00 "businessHoursProfile" a nadále 17:00:00. Je při spuštění "nonBusinessHoursProfile". "nonBusinessHoursProfile" se spustí až 9:00:00 úterý, a potom "businessHoursProfile" převezme znovu. To se opakuje až pátek v 17:00:00. V tomto bodě "nonBusinessHoursProfile" se spustí až pondělí v 9:00:00.
    
> [!Note]
> Uživatelské rozhraní škálování na portálu Azure vynucuje koncový čas pro opakování profily a zahájí spuštění nastavení automatického škálování výchozí profil mezi opakování profily.
    
## <a name="autoscale-evaluation"></a>Vyhodnocení škálování
Oznámeno, že nastavení automatického škálování může mít několik profilů a každý profil může mít více metriky pravidel, je důležité pochopit, jak se vyhodnocují na nastavení automatického škálování. Pokaždé, když se úloha automatického škálování spustí, začne výběrem profil, který se vztahuje. Potom škálování vyhodnotí minimální a maximální hodnoty a všechny metriky pravidel v profilu a rozhodne, pokud je akce škálování nezbytné.

### <a name="which-profile-will-autoscale-pick"></a>Který profil vyzvedne bude škálování?

Škálování používá následující pořadí a vyberte profil:
1. Nejprve hledá všechny pevné datum profil, který je nakonfigurován na spuštění teď. Pokud existuje, automatické škálování ho spouští. Pokud existuje víc profilů pevné datum, které se má spustit, automatické škálování vybere první z nich.
2. Pokud neexistují žádné profily pevné datum, zjistí opakování profilů automatického škálování. Pokud je nalezen profil opakování, spustí ji.
3. Pokud neexistují žádné opravené datum nebo opakování profily, spustí škálování regulární profilu.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak škálování vyhodnocení více pravidel?

Po škálování Určuje, který profil spuštění, vyhodnocuje všechna pravidla Škálováním na více systémů v profilu (jedná se o pravidla s **směr = "Zvýšit"**).

Pokud jeden nebo více pravidel Škálováním na více systémů se aktivují, automatické škálování vypočítá nové kapacity dáno **scaleAction** každého z těchto pravidel. Potom ji horizontálně navýší kapacitu na maximální těchto kapacitou, aby se zajistila dostupnost služby.

Například Řekněme, že existuje s aktuální kapacitou 10 nastavena škálování virtuálních počítačů. Existují dvě pravidla Škálováním na více systémů: jeden, který zvyšuje kapacitu o 10 procent a ten, který zvyšuje kapacitu o 3 počty. První pravidlo by mělo za následek nové kapacity 11 a druhé pravidlo by mělo za následek kapacitou 13. Zkontrolujte dostupnost služby, automatické škálování rozhodne, že je zvolen akci, která je výsledkem maximální kapacitu, takže druhé pravidlo.

Pokud žádná pravidla Škálováním na více systémů se aktivují, automatické škálování vyhodnotí všechny škálovací v pravidla (pravidel s **směr = "Snížit"**). Škálování akci škálování v přijímá pouze, pokud všechna pravidla v škálování se aktivují.

Škálování vypočítá nové kapacity dáno **scaleAction** každého z těchto pravidel. Potom vybere akci škálování, jejímž výsledkem maximálně těchto kapacity k zajištění dostupnosti služeb.

Například Řekněme, že existuje s aktuální kapacitou 10 nastavena škálování virtuálních počítačů. Existují dvě pravidla škálování: ten, který snižuje kapacitu o 50 procent a ten, který snižuje kapacitu dle počtů 3. První pravidlo by mělo za následek nové kapacity 5 a druhé pravidlo by mělo za následek kapacitou 7. Zkontrolujte dostupnost služby, automatické škálování rozhodne, že je zvolen akci, která je výsledkem maximální kapacitu, takže druhé pravidlo.

## <a name="next-steps"></a>Další postup
Další informace o škálování tím, že odkazuje na následující:

* [Přehled automatického škálování](monitoring-overview-autoscale.md)
* [Azure monitorování běžné metriky automatického škálování](insights-autoscale-common-metrics.md)
* [Osvědčené postupy pro monitorování Azure škálování](insights-autoscale-best-practices.md)
* [Akce škálování používat k odesílání e-mailu a webhooku oznámení výstrah](insights-autoscale-to-webhook-email.md)
* [Škálování REST API](https://msdn.microsoft.com/library/dn931953.aspx)
