---
title: Rozšířené automatické škálování pomocí Virtual Machines Azure
description: Používá Správce prostředků a službu VM Scale Sets s více pravidly a profily, které odesílají e-maily a volají adresy URL Webhooku s akcemi škálování.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717736"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Rozšířená konfigurace automatického škálování pomocí šablon Správce prostředků pro VM Scale Sets
Můžete škálovat a škálovat v Virtual Machine Scale Sets na základě prahových hodnot metriky výkonu, podle plánu opakování nebo podle konkrétního data. Můžete také nakonfigurovat oznámení e-mailu a Webhooku pro akce škálování. Tento názorný postup ukazuje příklad konfigurace všech těchto objektů pomocí Správce prostředků šablony v sadě škálování virtuálního počítače.

> [!NOTE]
> I když tento návod vysvětluje kroky pro VM Scale Sets, vztahují se stejné informace na automatické škálování [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management služby](../../api-management/api-management-key-concepts.md) pro jednoduché nastavení škálování na více systémů na základě jednoduché metriky výkonu, jako je CPU, najdete v dokumentech pro [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) a [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) .
>
>

## <a name="walkthrough"></a>Názorný postup
V tomto návodu použijeme [Azure Resource Explorer](https://resources.azure.com/) ke konfiguraci a aktualizaci nastavení automatického škálování pro sadu škálování. Azure Resource Explorer je snadný způsob, jak spravovat prostředky Azure prostřednictvím šablon Správce prostředků. Pokud nástroj Azure Resource Explorer Tool začínáte, přečtěte si [Tento Úvod](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Nasaďte novou škálu a nastavte základní nastavení automatického škálování. V tomto článku se používá ten z Galerie Azure pro rychlý Start, který má sadu Windows Scale nastavenou se základní šablonou automatického škálování. Systémy Linux Scale Sets fungují stejným způsobem.
2. Po vytvoření sady škálování přejděte do prostředku sady škálování z Azure Resource Explorer. V uzlu Microsoft. Insights se zobrazí následující.

    ![Průzkumník Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Provedení šablony vytvořilo výchozí nastavení automatického škálování s názvem **' autoscalewad '**. Na pravé straně můžete zobrazit úplnou definici tohoto nastavení automatického škálování. V takovém případě se výchozí nastavení automatického škálování dodává s pravidlem škálování na více instancí a škálováním na více instancí procesoru.  

3. Nyní můžete přidat další profily a pravidla na základě plánu nebo konkrétních požadavků. Vytvoříme nastavení automatického škálování se třemi profily. Pokud chcete pochopit profily a pravidla v automatickém škálování, Projděte si [osvědčené postupy pro automatické škálování](autoscale-best-practices.md).  

    | Profily & pravidla | Description |
    |--- | --- |
    | **Profil** |**Na základě výkonu nebo metriky** |
    | Pravidlo |Počet zpráv ve frontě Service Bus > x |
    | Pravidlo |Počet zpráv ve frontě Service Bus < y |
    | Pravidlo |PROCESOR% > n |
    | Pravidlo |PROCESOR% < p |
    | **Profil** |**Hodiny ráno v týdnu (bez pravidel)** |
    | **Profil** |**Den spuštění produktu (bez pravidel)** |

4. Tady je hypotetický scénář pro škálování, který používáme pro tento návod.

   * **Zatížení na základě toho** , jak se má škálovat nebo na základě zatížení naší aplikace hostované na mém měřítku. *
   * **Velikost fronty zpráv** – používám Service Bus frontu pro příchozí zprávy do aplikace. Používám počet zpráv ve frontě a procesor% a konfiguruje výchozí profil, který spustí akci škálování, pokud některý z počtů zpráv nebo CPU dosáhne prahové hodnoty.\*
   * **Čas v týdnu a den** , po který se má týdenní opakovaný profil založený na denním profilu s názvem dne v týdnu ráno hodin Na základě historických dat jsem vědět, že je lepší mít během této doby během této doby určitý počet instancí virtuálních počítačů ke zpracování zatížení vaší aplikace.\*
   * **Speciální data** – přidám profil "den spuštění produktu". Naplánoval jsem konkrétní datum, takže je moje aplikace připravená na zpracování oznámení marketingu a při vložení nového produktu do aplikace.\*
   * *Poslední dva profily mohou mít také další pravidla na základě metriky výkonu v rámci nich. V tomto případě jsem rozhodl, že nechcete, aby se spoléhal na výchozí pravidla založená na výkonu. Pravidla jsou volitelná pro opakující se profily a profily založené na datu.*

     Stanovení priorit profilů a pravidel modulu automatického škálování se také zaznamená v článku [osvědčené postupy automatického škálování](autoscale-best-practices.md) .
     Seznam běžných metrik pro automatické škálování najdete v tématu [běžné metriky pro automatické škálování](autoscale-common-metrics.md) .

5. Ujistěte se, že jste v Průzkumník prostředků režim **pro čtení a zápis** .

    ![Autoscalewad, výchozí nastavení automatického škálování](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klikněte na Upravit. **Nahraďte** element Profiles v nastavení automatického škálování následující konfigurací:

    ![Snímek obrazovky ukazuje element Profiles.](media/autoscale-virtual-machine-scale-sets/profiles.png)

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
    Podporovaná pole a jejich hodnoty najdete v tématu věnovaném [automatickému škálování REST API dokumentaci](/rest/api/monitor/autoscalesettings). Nastavení automatického škálování teď obsahuje tři popsané profily dřív.

7. Nakonec se podívejte na část **oznámení** automatického škálování. Oznámení automatického škálování umožňují provádět tři věci po úspěšném spuštění škálování nebo v akci.
   - Upozorněte správce a spolusprávce předplatného.
   - Poslat e-mail sadě uživatelů
   - Aktivujte volání Webhooku. Při vyvolání tento Webhook pošle metadata o stavu automatického škálování a prostředku sady škálování. Další informace o datové části Webhooku automatického škálování najdete v tématu [Konfigurace webhooku & e-mailových oznámení pro automatické škálování](autoscale-webhook-email.md).

   Přidejte následující do nastavení automatického škálování, které nahradí váš element **oznámení** , jehož hodnota je null.

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

   Tlačítko pro **vložení** v Průzkumník prostředků k aktualizaci nastavení automatického škálování.

Aktualizovali jste nastavení automatického škálování v sadě škálování virtuálního počítače tak, aby zahrnovalo víc profilů škálování a oznámení o škálování.

## <a name="next-steps"></a>Další kroky
Pomocí těchto odkazů můžete získat další informace o automatickém škálování.

[Řešení potíží s AutoScale pomocí Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Běžné metriky pro automatické škálování](autoscale-common-metrics.md)

[Osvědčené postupy pro automatické škálování Azure](autoscale-best-practices.md)

[Správa automatického škálování pomocí PowerShellu](../powershell-samples.md#create-and-manage-autoscale-settings)

[Správa automatického škálování pomocí rozhraní příkazového řádku](../cli-samples.md#autoscale)

[Nakonfigurovat Webhook & e-mailová oznámení pro automatické škálování](autoscale-webhook-email.md)

Referenční dokumentace k šablonám [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)