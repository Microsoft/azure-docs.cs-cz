---
title: Pokročilé automatické škálování pomocí Azure Virtual Machines
description: Použití Resource Manageru a VM Scale Sets s více pravidly a profily, které odeslání e-mailu a volání adresy URL webhooku se akce škálování.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 995e93290e7c305e217c1f44609d383f69d83a9a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420553"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Rozšířená konfigurace škálování pro Škálovací sady virtuálních počítačů pomocí šablon Resource Manageru
Můžete škálování na méně instancí a škálování Škálovací sady virtuálních počítačů na základě výkonu metriky prahových hodnot, podle opakovaného plánu nebo podle konkrétního data. Můžete také nakonfigurovat emailová a webhooková oznámení pro akce škálování. Tento návod ukazuje příklad konfigurace všechny tyto objekty pomocí šablony Resource Manageru v sadě škálování virtuálního počítače.

> [!NOTE]
> Přestože tento návod popisuje kroky pro Škálovací sady virtuálních počítačů, stejné informace se vztahují k automatickému škálování [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Jednoduché škálování vstup a výstup nastavení v sadě škálování virtuálního počítače založeného na jednoduché výkonu metriky, například CPU, odkazovat [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) a [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) dokumentů
>
>

## <a name="walkthrough"></a>Názorný postup
V tomto názorném postupu používáme [Azure Resource Exploreru](https://resources.azure.com/) ke konfiguraci a aktualizovat nastavení automatického škálování pro škálovací sady. Průzkumník prostředků Azure je snadný způsob, jak spravovat prostředky Azure pomocí šablon Resource Manageru. Pokud jste ještě nástroje Azure Resource Exploreru, přečtěte si [tohoto úvodu](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Nasazení nové škálovací sady s automatickým Škálováním základní nastavení. Tento článek používá jeden z Galerie Azure rychlý start, která má Windows škálovací sadu pomocí šablony základní automatického škálování. Stejným způsobem fungovat Linux škálovací sady.
2. Po vytvoření škálovací sady přejděte k prostředku škálovací sady z Průzkumníku prostředků Azure. Zobrazí se následující pod uzlem Microsoft.Insights.

    ![Průzkumník služby Azure](media/insights-advanced-autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Zpracování šablony byla vytvořena ve výchozím nastavení automatického škálování s názvem **"autoscalewad"**. Na pravé straně se zobrazí úplné definice tohoto nastavení automatického škálování. V tomto případě obsahuje výchozí nastavení automatického škálování pravidlo horizontální navýšení kapacity a škálování na méně instancí % na základě procesoru.  

3. Nyní můžete přidat další profily a pravidla podle plánu nebo konkrétní požadavky. Nastavení automatického škálování se nám vytvořit pomocí tří profilů. Informace o tom profily a pravidla automatického škálování, zkontrolujte [osvědčené postupy automatického škálování](insights-autoscale-best-practices.md).  

    | Profily & pravidla | Popis |
    |--- | --- |
    | **Profil** |**Na základě výkonu a metriky** |
    | Pravidlo |Počet zpráv fronty služby Service Bus > x |
    | Pravidlo |Počet zpráv fronty služby Service Bus < y |
    | Pravidlo |Využití procesoru % > n |
    | Pravidlo |Využití procesoru % < p |
    | **Profil** |**Den v týdnu hodin ráno (žádná pravidla)** |
    | **Profil** |**Den uvedení produktu (žádná pravidla)** |

4. Tady je hypotetický škálování scénář, který jsme použili pro tento návod.

    * **Zatížení na základě** – chci horizontální navýšení kapacity nebo v podle zatížení ve své aplikaci hostované na můj set.* škálování
    * **Velikost fronty zprávy** – použití fronty Service Bus pro příchozí zprávy pro mojí aplikaci. Můžu používat počet zpráv do fronty a % procesoru a nakonfigurovat výchozí profil aktivovat akci škálování, pokud buď počet zpráv nebo procesoru narazí threshold.*
    * **Čas týden a den** – chci, aby týdenní opakované "čas" na základě profilu nazývá 'Hodiny ráno den v týdnu'. Na základě historických dat o vím, že je lepší mít určitý počet instancí virtuálních počítačů pro zpracování zátěže vaší aplikace během této time.*
    * **Speciální datum** – po přidání profilu produktu spuštění den. Můžu Plánujte dopředu a připravte konkrétní data, aplikace je připravená pro zpracování zátěže z důvodu marketingové oznámení a máme nového produktu v aplikaci.*
    * *Poslední dva profily může mít také další metriky na základě pravidel výkonu v nich. V tomto případě jsem se rozhodla nechcete mít jeden a místo toho přináší setrvávání u výchozí metriku výkonu na základě pravidel. Pravidla jsou volitelné pro profily opakované a na základě data.*

    Stanovení priorit modul automatického škálování profilů a pravidla i zaznamenány [osvědčené postupy automatického škálování](insights-autoscale-best-practices.md) článku.
    Seznam běžné metriky pro automatické škálování, najdete v části [běžné metriky pro automatické škálování](insights-autoscale-common-metrics.md)

5. Ujistěte se, že jste na **r/w** režimu v Průzkumníku prostředků

    ![Autoscalewad, výchozí nastavení automatického škálování](media/insights-advanced-autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klikněte na Upravit. **Nahraďte** 'profily' element v zadaném nastavení automatického škálování s následující konfigurací:

    ![Profily](media/insights-advanced-autoscale-virtual-machine-scale-sets/profiles.png)

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
    Podporované pole a jejich hodnoty, najdete v části [dokumentace k rozhraní REST API pro automatické škálování](https://msdn.microsoft.com/library/azure/dn931928.aspx). Nastavení automatického škálování teď obsahuje tři profily, které bylo popsáno dříve.

7. Nakonec se podíváme na automatického škálování **oznámení** oddílu. Oznámení o automatickém škálování umožňují provést tři věci, když horizontální navýšení kapacity nebo v akci se úspěšně aktivuje.
   - Upozornit správce a spolusprávci předplatného
   - E-mailem sady uživatelů
   - Aktivujte volání webhooku. Při vyvolání, odešle tento webhook metadata o podmínce automatické škálování a škálovací sady prostředků. Další informace o datová část webhooku automatického škálování najdete v tématu [konfigurace Webhooků a e-mailová oznámení pro automatické škálování](insights-autoscale-to-webhook-email.md).

   Přidejte následující k nahrazení nastavení automatického škálování vašeho **oznámení** element, jehož hodnota je null

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

   Spuštění **umístit** tlačítko v Průzkumníku prostředků se aktualizovat nastavení automatického škálování.

Nastavení automatického škálování na Škálovací sady obsahovat několik profilů škálování a škálovat oznámení virtuálních počítačů aktualizována.

## <a name="next-steps"></a>Další kroky
Další informace o automatické škálování pomocí těchto odkazů.

[Řešení potíží s automatickým Škálováním se Škálovacími sadami virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Běžné metriky pro automatické škálování](insights-autoscale-common-metrics.md)

[Osvědčené postupy pro automatické škálování v Azure](insights-autoscale-best-practices.md)

[Správa automatické škálování s použitím prostředí PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Správa automatické škálování s použitím rozhraní příkazového řádku](insights-cli-samples.md#autoscale)

[Konfigurace Webhooků a e-mailová oznámení pro automatické škálování](insights-autoscale-to-webhook-email.md)
