---
title: Rozšířené škálování pomocí Azure Virtual Machines | Microsoft Docs
description: Používá správce prostředků a škálovatelné sady virtuálních počítačů s více pravidel a profily, které odeslání e-mailu a volání adresy URL webhooku s akcí škálování.
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: 80955535c8d863cd3d8d1b77e2ab8bc016b6d9f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Pokročilé škálování konfigurace pomocí šablony Resource Manageru pro škálovatelné sady virtuálních počítačů
Můžete v škálování a horizontální sady škálování virtuálního počítače na základě výkonu metriky prahových hodnot, podle plánu opakování, nebo podle konkrétní data. Můžete také nakonfigurovat e-mailu a webhooku oznámení pro akce škálování. Tento návod ukazuje příklad konfigurace všechny tyto objekty ve Škálovací sadě virtuálních počítačů pomocí šablony Resource Manageru.

> [!NOTE]
> Když tento postup vysvětluje kroky pro škálovatelné sady virtuálních počítačů, stejné informace platí pro automatické škálování [cloudové služby](https://azure.microsoft.com/services/cloud-services/), a [služby App Service – webové aplikace](https://azure.microsoft.com/services/app-service/web/).
> Jednoduché škálování vstupně -výstupnímu nastavení ve Škálovací sadě virtuálních počítačů podle metriky výkonu jednoduché například CPU, naleznete na [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) a [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) dokumenty
>
>

## <a name="walkthrough"></a>Názorný postup
V tomto návodu použijeme [Průzkumníka prostředků Azure](https://resources.azure.com/) ke konfiguraci a nastavení automatického škálování pro sadu škálování aktualizovat. Azure Průzkumníka prostředků je snadný způsob, jak spravovat prostředky Azure pomocí šablony Resource Manageru. Pokud jste nový nástroj Průzkumníka prostředků Azure, přečtěte si [tento ÚVOD](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Nasaďte nové škálování nastavit s nastavením základní škálování. Tento článek používá jeden z Galerie pro rychlý start Azure, který má Windows pomocí šablony základní škálování sady škálování. Sady škálování Linux fungovat stejným způsobem.
2. Jakmile se vytvoří sada škálování, přejděte do prostředků sady škálování z Průzkumníka prostředků Azure. Zobrazí následující uzlu Microsoft.Insights.

    ![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    Provádění šablony vytvořil výchozí nastavení automatického škálování s názvem **'autoscalewad'**. Na pravé straně můžete zobrazit úplnou definici tohoto nastavení automatického škálování. V takovém případě výchozí nastavení automatického škálování se dodává s pravidlo Škálováním na více systémů a škálování v % na základě využití procesoru.  

3. Nyní můžete přidat další profily a pravidla podle plánu nebo konkrétní požadavky. Nastavení automatického škálování se nám vytvořit pomocí tří profilů. Abyste pochopili, profily a pravidla v škálování, zkontrolujte [škálování osvědčené postupy](insights-autoscale-best-practices.md).  

    | Profily & pravidla | Popis |
    |--- | --- |
    | **Profil** |**Na základě výkonu nebo metrika** |
    | Pravidlo |Počet zpráv fronty Service Bus > x |
    | Pravidlo |Počet zpráv fronty Service Bus < y |
    | Pravidlo |Využití procesoru % > n |
    | Pravidlo |% Využití procesoru < p |
    | **Profil** |**Den v týdnu hodin ráno (žádná pravidla)** |
    | **Profil** |**Den spuštění produktu (žádná pravidla)** |

4. Zde je hypotetický škálování scénář, který používáme pro tento návod.

    * **Zatížení na základě** -nastavit jako škálování nebo v závislosti na zatížení na Moje aplikace hostované na Moje set.* škálování
    * **Velikost fronty zpráv** -použít frontu sběrnice pro příchozí zprávy pro Moje aplikace. I pomocí fronty počtu zpráv a % využití procesoru a nakonfigurovat výchozí profil spustíte akci škálování, pokud některý z počtu zpráv nebo procesor přístupy threshold.*
    * **Čas týden a den** – chci, aby týdenní opakovaného 'čas dne, na základě profilu volat 'Hodiny ráno den v týdnu'. Na základě historických dat, lze zjistit, že je lepší určitého počtu instancí virtuálních počítačů pro zpracování během této time.* zatížení Moje aplikace
    * **Speciální datum** – po přidání profil produktu spusťte den. I připravte se na konkrétní kalendářní data tak, aby Moje aplikace připravené pro zpracování zátěže z důvodu marketing oznámení a application.* jsme chápat nového produktu
    * *Poslední dva profily může mít jiné metriky na základě pravidel výkonu v nich. V takovém případě rozhodla, že nemají jeden a místo toho spoléhají na výchozí metriku výkonu na základě pravidla. Pravidla jsou volitelné pro profily opakování a na základě data.*

    Stanovení priorit škálování modul profily a pravidel, je také zachycené v [osvědčené postupy automatické škálování](insights-autoscale-best-practices.md) článku.
    Seznam běžných metriky pro škálování, najdete v části [běžné metriky pro škálování](insights-autoscale-common-metrics.md)

5. Ujistěte se, že jste na **pro čtení a zápis** režimu v Průzkumníku prostředků

    ![Autoscalewad škálování výchozí nastavení](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Klikněte na tlačítko Upravit. **Nahraďte** 'profily' element v nastavení automatického škálování s následující konfigurací:

    ![Profily](./media/insights-advanced-autoscale-vmss/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Podporované pole a jejich hodnoty, najdete v tématu [dokumentace k REST API škálování](https://msdn.microsoft.com/en-us/library/azure/dn931928.aspx). Vaše nastavení automatického škálování nyní obsahuje tři profily vysvětlené dřív.

7. Nakonec, podívejte se na škálování **oznámení** části. Oznámení o automatickém škálování umožňují provádějí tři věci, pokud škálování nebo v akci je úspěšně spuštěné.
   - Oznámit správce a spolusprávci předplatného
   - E-mailu sadu uživatelů
   - Aktivovat webhooku volání. Při vyvolání, odešle tento webhook metadata o automatické škálování podmínku a měřítka nastavení prostředku. Další informace o datové části webhooku škálování najdete v tématu [konfigurace Webhooku & e-mailová oznámení pro škálování](insights-autoscale-to-webhook-email.md).

   Přidejte následující nahrazení nastavení automatického škálování vašeho **oznámení** element, jehož hodnota je null.

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Stiskněte tlačítko **Put** tlačítko v Průzkumníku prostředků se aktualizovat nastavení automatického škálování.

Jste aktualizovali nastavení automatického škálování na škálování virtuálního počítače nastavit zahrnují několik profilů škálování a škálovat oznámení.

## <a name="next-steps"></a>Další kroky
Pomocí těchto odkazů můžete získat další informace o automatické škálování.

[Řešení potíží s škálování s sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Běžné metriky pro škálování](insights-autoscale-common-metrics.md)

[Osvědčené postupy pro Azure škálování](insights-autoscale-best-practices.md)

[Spravovat škálování pomocí prostředí PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Spravovat škálování pomocí rozhraní příkazového řádku](insights-cli-samples.md#autoscale)

[Nakonfigurovat Webhooku & e-mailová oznámení pro škálování](insights-autoscale-to-webhook-email.md)
