---
title: Principy nastavení automatického škálování ve službě Azure Monitor
description: Podrobný rozpis nastavení automatického škálování a jejich fungování. Platí pro virtuální počítače, cloudové služby, webové aplikace
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364301"
---
# <a name="understand-autoscale-settings"></a>Vysvětlení nastavení automatického škálování
Nastavení automatického škálování pomáhá zajistit, že máte správné množství prostředků spuštěných pro zpracování kolísající zatížení vaší aplikace. Nastavení automatického škálování můžete nakonfigurovat tak, aby se aktivovalo na základě metrik, které indikují zatížení nebo výkon, nebo naplánované datum a čas. Tento článek se podrobně podívat na anatomii nastavení automatického škálování. Článek začíná schématem a vlastnostmi nastavení a potom prochází různé typy profilů, které lze nakonfigurovat. Nakonec článek popisuje, jak funkce automatického škálování v Azure vyhodnocuje profil, který má být v daném okamžiku spuštěn.

## <a name="autoscale-setting-schema"></a>Schéma nastavení automatického škálování
Pro ilustraci schématu nastavení automatického škálování se použije následující nastavení automatického škálování. Je důležité si uvědomit, že toto nastavení automatického škálování obsahuje:
- Jeden profil. 
- Dvě metrická pravidla v tomto profilu: jedno pro horizontální navýšení kapacity a jedno pro škálování.
  - Pravidlo horizontálního navýšení kapacity se aktivuje, když průměrná procentuální metrika procesoru sady virtuálních strojů je větší než 85 procent za posledních 10 minut.
  - Pravidlo škálování se aktivuje, když průměr škálovací sady virtuálního počítače je menší než 60 procent za poslední minutu.

> [!NOTE]
> Nastavení může mít více profilů. Další informace najdete v části [Profily.](#autoscale-profiles) Profil může mít také více pravidel škálování a pravidla škálování. Chcete-li zjistit, jak jsou vyhodnocovány, naleznete v části [hodnocení.](#autoscale-evaluation)

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
| Nastavení | ID | ID prostředku nastavení automatického škálování. Nastavení automatického škálování jsou prostředek Azure Resource Manager. |
| Nastavení | jméno | Název nastavení automatického škálování. |
| Nastavení | location | Umístění nastavení automatického škálování. Toto umístění se může lišit od umístění zdroje, který je škálován. |
| properties | cílResourceUri | ID prostředku zdroje, který je škálován. Můžete mít pouze jedno nastavení automatického škálování na prostředek. |
| properties | Profily | Nastavení automatického škálování se skládá z jednoho nebo více profilů. Pokaždé, když je modul automatického škálování spuštěn, provede jeden profil. |
| profil | jméno | Název profilu. Můžete zvolit libovolný název, který vám pomůže identifikovat profil. |
| profil | Kapacita.maximum | Maximální povolená kapacita. Zajišťuje, že automatické škálování při provádění tohoto profilu neškáluje prostředek nad toto číslo. |
| profil | Kapacita.minimum | Minimální povolená kapacita. Zajišťuje, že automatické škálování při provádění tohoto profilu neškáluje prostředek pod tímto číslem. |
| profil | Kapacita.výchozí | Pokud je problém čtení metriky prostředků (v tomto případě procesoru "vmss1"), a aktuální kapacita je nižší než výchozí, automatické škálování se škáluje na výchozí. Tím je zajištěna dostupnost prostředku. Pokud je aktuální kapacita již vyšší než výchozí kapacita, automatické škálování není škálování. |
| profil | pravidla | Automatické škálování se automaticky škáluje mezi maximální a minimální kapacitou pomocí pravidel v profilu. V profilu můžete mít více pravidel. Obvykle existují dvě pravidla: jeden určit, kdy horizontální navýšení kapacity a druhý určit, kdy škálovat. |
| Pravidlo | metricTrigger | Definuje metrickou podmínku pravidla. |
| metricTrigger | metricName | Název metriky. |
| metricTrigger |  metrikaResourceUri | ID prostředku, který vydává metriku. Ve většině případů je stejný jako prostředek škálování. V některých případech to může být jiné. Můžete například škálovat škálovací sadu virtuálního počítače na základě počtu zpráv ve frontě úložiště. |
| metricTrigger | timeGrain | Doba odběru vzorků metriky. Například **TimeGrain = "PT1M"** znamená, že metriky by měly být agregovány každých 1 minutu pomocí metody agregace zadané v elementu statistiky. |
| metricTrigger | Statistika | Metoda agregace v rámci timeGrain období. Například **statistika = "Průměr"** a **timeGrain = "PT1M"** znamená, že metriky by měly být agregovány každých 1 minutu, přičemž průměr. Tato vlastnost určuje, jak je metrika vzorkována. |
| metricTrigger | timeWindow | Doba, po kterou se můžete podívat zpět na metriky. Například **timeWindow = "PT10M"** znamená, že při každém spuštění automatického škálování se dotazuje metriky za posledních 10 minut. Časové okno umožňuje normalizovat metriky a zabraňuje reagovat na přechodné špičky. |
| metricTrigger | timeAggregation | Metoda agregace použitá k agregaci vzorkovaných metrik. Například **TimeAggregation = "Průměr"** by měl agregovat vzorkované metriky s ohledem na průměr. V předchozím případě odeberedeset minut ových vzorků a zprůměruje je. |
| Pravidlo | scaleAction | Akce, která má být v případě, že je aktivována metricTrigger pravidla. |
| scaleAction | směr | "Zvýšení" na horizontální navýšení kapacity, nebo "Snížení" pro škálování palců|
| scaleAction | value | Kolik zvýšit nebo snížit kapacitu zdroje. |
| scaleAction | cooldown | Doba čekání po operaci škálování před změnou měřítka znovu. Například pokud **cooldown = "PT10M"**, automatické škálování se nepokusí škálovat znovu po dobu dalších 10 minut. Přebíjecí doba je umožnit metriky stabilizovat po přidání nebo odebrání instancí. |

## <a name="autoscale-profiles"></a>Profily automatického škálování

Existují tři typy profilů automatického škálování:

- **Pravidelný profil:** Nejběžnější profil. Pokud nepotřebujete škálovat zdroj na základě dne v týdnu nebo v určitý den, můžete použít běžný profil. Tento profil pak lze nakonfigurovat s metrická pravidla, která určují, kdy horizontální navýšení kapacity a kdy škálovat. Měli byste mít definován pouze jeden pravidelný profil.

    Ukázkový profil použitý dříve v tomto článku je příkladem běžného profilu. Všimněte si, že je také možné nastavit profil škálovat na statický počet instancí pro váš prostředek.

- **Profil pevného data:** Tento profil je určen pro zvláštní případy. Například řekněme, že máte důležitou událost, která se blíží k prosinci 26, 2017 (PST). Chcete, aby se minimální a maximální kapacita vašeho prostředku v tento den lišila, ale stále se škáluje podle stejných metrik. V takovém případě byste měli do seznamu profilů nastavení přidat profil s pevným datem. Profil je nakonfigurován tak, aby byl spuštěn pouze v den události. Pro každý jiný den používá automatické škálování běžný profil.

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
    
- **Profil opakování:** Tento typ profilu umožňuje zajistit, aby byl tento profil vždy používán v určitý den v týdnu. Profily opakování mají pouze čas zahájení. Spustí se, dokud nebude nastaven další profil opakování nebo profil s pevným datem. Nastavení automatického škálování s pouze jedním profilem opakování spustí tento profil, i když je ve stejném nastavení definován pravidelný profil. Následující dva příklady ilustrují, jak se tento profil používá:

    **Příklad 1: Pracovní dny vs. víkendy**
    
    Řekněme, že o víkendech chcete, aby vaše maximální kapacita byla 4. Ve všední dny, protože očekáváte větší zatížení, chcete, aby vaše maximální kapacita byla 10. V takovém případě by vaše nastavení obsahovalo dva profily opakování, jeden pro víkendy a druhý ve všední dny.
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

    Předchozí nastavení ukazuje, že každý profil opakování má plán. Tento plán určuje, kdy se profil spustí. Profil se zastaví, když je čas spustit jiný profil.

    Například v předchozím nastavení "weekdayProfile" je nastavena na začátek pondělí v 12:00. To znamená, že tento profil začne běžet v pondělí ve 12:00. Pokračuje až do soboty v 12:00, kdy je naplánováno spuštění "weekendprofile".

    **Příklad 2: Pracovní doba**
    
    Řekněme, že chcete mít jednu prahovou hodnotu metriky během pracovní doby (od 9:00 do 17:00) a jinou pro všechny ostatní časy. Nastavení bude vypadat takto:
    
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
    
    Předchozí nastavení ukazuje, že "businessHoursProfile" začíná běžet v pondělí v 9:00 a pokračuje do 17:00. To je, když "nonBusinessHoursProfile" spustí. "NonBusinessHoursProfile" běží až do 9:00 úterý a pak "businessHoursProfile" převezme znovu. To se opakuje až do pátku v 17:00. V tomto okamžiku "nonBusinessHoursProfile" běží celou cestu do pondělí v 9:00.
    
> [!Note]
> Uživatelské rozhraní automatického škálování na portálu Azure vynucuje koncové časy pro profily opakování a začne spouštět výchozí profil nastavení automatického škálování mezi profily opakování.
    
## <a name="autoscale-evaluation"></a>Vyhodnocení automatického škálování
Vzhledem k tomu, že nastavení automatického škálování může mít více profilů a každý profil může mít více pravidel metriky, je důležité pochopit, jak se vyhodnocuje nastavení automatického škálování. Pokaždé, když se úloha automatického škálování spustí, začne výběrem profilu, který je použitelný. Potom automatické škálování vyhodnotí minimální a maximální hodnoty a všechna pravidla metriky v profilu a rozhodne, zda je nutná akce škálování.

### <a name="which-profile-will-autoscale-pick"></a>Který profil bude automatický škálování vybrat?

Automatické škálování používá následující pořadí k výběru profilu:
1. Nejprve vyhledá jakýkoli profil pevného data, který je nakonfigurován tak, aby byl nyní spuštěn. Pokud existuje, automatické škálování jej spustí. Pokud existuje více pevných profilů data, které mají být spuštěny, automatické škálování vybere první.
2. Pokud neexistují žádné profily s pevným datem, automatické škálování se podívá na profily opakování. Pokud je nalezen profil opakování, spustí jej.
3. Pokud neexistují žádné pevné datum nebo opakování profily, automatické škálování spustí běžný profil.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak automatické škálování vyhodnocuje více pravidel?

Po automatickéškálování určuje, který profil spustit, vyhodnotí všechna pravidla škálování v profilu (jedná se o pravidla se **směrem = "Zvýšení"**).

Pokud se aktivuje jedno nebo více pravidel škálování, automatické škálování vypočítá novou kapacitu určenou **měřítkem akce** každého z těchto pravidel. Pak se škáluje na maximum těchto kapacit, aby byla zajištěna dostupnost služeb.

Řekněme například, že existuje škálovací sada virtuálních strojů s aktuální kapacitou 10. Existují dvě pravidla škálování: jedno, které zvyšuje kapacitu o 10 procent, a jedno, které zvyšuje kapacitu o 3 počty. První pravidlo by mělo za následek novou kapacitu 11 a druhé pravidlo by mělo kapacitu 13. Chcete-li zajistit dostupnost služby, automatické škálování zvolí akci, která má za následek maximální kapacitu, takže je vybráno druhé pravidlo.

Pokud nejsou spuštěna žádná pravidla škálování, automatické škálování vyhodnotí všechna pravidla škálování (pravidla se **směrem = "Snížení"**). Automatické škálování provede akci škálování pouze v případě, že jsou spuštěna všechna pravidla škálování.

Automatické škálování vypočítá novou kapacitu určenou **akci scaleAction** každého z těchto pravidel. Pak vybere akci škálování, která má za následek maximální tyto kapacity k zajištění dostupnosti služby.

Řekněme například, že existuje škálovací sada virtuálních strojů s aktuální kapacitou 10. Existují dvě pravidla škálování: jedno, které snižuje kapacitu o 50 procent, a jedno, které snižuje kapacitu o 3 počty. První pravidlo by mělo za následek novou kapacitu 5 a druhé pravidlo by mělo kapacitu 7. Chcete-li zajistit dostupnost služby, automatické škálování zvolí akci, která má za následek maximální kapacitu, takže je vybráno druhé pravidlo.

## <a name="next-steps"></a>Další kroky
Další informace o automatickém škálování najdete v následujících věcech:

* [Přehled automatického škálování](../../azure-monitor/platform/autoscale-overview.md)
* [Běžné metriky automatického škálování Azure Monitoru](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Použití akcí automatického škálování k odesílání e-mailů a upozornění webhooku](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Automatické škálování rozhraní REST API](https://msdn.microsoft.com/library/dn931953.aspx)

