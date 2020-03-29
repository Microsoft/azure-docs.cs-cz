---
title: Pokročilé automatické škálování pomocí virtuálních počítačů Azure
description: Používá Resource Manager a Škálovací sady virtuálních vod s více pravidly a profily, které odesílají e-maily a volají adresy URL webhooku s akcemi škálování.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364216"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Pokročilá konfigurace automatického škálování pomocí šablon Správce prostředků pro škálovací sady virtuálních počítačů
Škálování a škálování v škálovacích sadách virtuálních strojů můžete škálovat na základě prahových hodnot metrik výkonu, podle opakovaného plánu nebo podle konkrétního data. Můžete také nakonfigurovat e-mailová oznámení a oznámení webhooku pro akce škálování. Tento návod ukazuje příklad konfigurace všech těchto objektů pomocí šablony Správce prostředků v škálovací sadě virtuálních počítačích.

> [!NOTE]
> Zatímco tento návod vysvětluje kroky pro škálovací sady virtuálních počítačích, stejné informace platí pro automatické škálování [cloudových služeb](https://azure.microsoft.com/services/cloud-services/), [app service - webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Pro jednoduché nastavení škálování a videa na škálovací sadě virtuálních zařízení na základě jednoduché metriky výkonu, jako je procesor, naleznete v dokumentech [Linuxu](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) a [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Názorný postup
V tomto návodu používáme [Průzkumník prostředků Azure](https://resources.azure.com/) ke konfiguraci a aktualizaci nastavení automatického škálování pro škálovací sadu. Azure Resource Explorer je snadný způsob, jak spravovat prostředky Azure prostřednictvím šablon Správce prostředků. Pokud s nástrojem Azure Resource Explorer tečujete, přečtěte si [tento úvod](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Nasaďte novou škálovací sadu se základním nastavením automatického škálování. Tento článek používá jeden z Galerie Rychlého startu Azure, který má škálovací sadu Windows se základní šablonou automatického škálování. Linuxové škálovací sady fungují stejným způsobem.
2. Po vytvoření škálovací sady přejděte na prostředek škálovací sady z Průzkumníka prostředků Azure. V části Microsoft.Insights se zobrazí následující text.

    ![Průzkumník Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Spuštění šablony vytvořilo výchozí nastavení automatického škálování s názvem **"autoscalewad"**. Na pravé straně můžete zobrazit úplnou definici tohoto nastavení automatického škálování. V tomto případě je výchozí nastavení automatického škálování dodáváno s pravidlem škálování založeného na procesoru% a škálování.  

3. Nyní můžete přidat další profily a pravidla na základě plánu nebo konkrétní požadavky. Vytvoříme nastavení automatického škálování se třemi profily. Chcete-li porozumět profilům a pravidlům v automatickém škálování, přečtěte [si doporučené postupy automatického škálování](autoscale-best-practices.md).  

    | Profily & pravidla | Popis |
    |--- | --- |
    | **Profil** |**Na základě výkonu/metriky** |
    | Pravidlo |Počet zpráv fronty služby Service Bus > x |
    | Pravidlo |Počet zpráv fronty služby Service Bus < y |
    | Pravidlo |CPU% > n |
    | Pravidlo |CPU% < p |
    | **Profil** |**Denní ranní hodiny (bez pravidel)** |
    | **Profil** |**Den uvedení produktu na trh (bez pravidel)** |

4. Zde je hypotetický scénář škálování, který používáme pro tento návod.

   * **Nazákladě zatížení** – chtěl bych horizontální navýšení kapacity nebo v závislosti na zatížení mé aplikace hostované na škálovací sadě.*
   * **Velikost fronty zpráv** – pro příchozí zprávy do aplikace používám frontu služby Service Bus. Používám počet zpráv fronty a CPU% a nakonfiguruji výchozí profil pro aktivaci akce škálování, pokud jeden z počtu zpráv nebo cpu narazí na prahovou hodnotu.\*
   * **Čas v týdnu a den** - Chci týdenní opakující se 'denní čas' na základě profilu s názvem 'Weekday Morning Hours'. Na základě historických dat vím, že je lepší mít určitý počet instancí virtuálních počítačů pro zpracování zatížení mé aplikace během této doby.\*
   * **Speciální termíny** - Přidal jsem profil "Den uvedení produktu na trh". Mám v plánu dopředu pro konkrétní data, takže moje aplikace je připravena zvládnout zatížení kvůli marketingových oznámení, a když jsme dali nový produkt v aplikaci.\*
   * *Poslední dva profily mohou mít také další pravidla založená na metrikách výkonu v nich. V tomto případě jsem se rozhodl, že jeden nemám a místo toho se budu spoléhat na výchozí pravidla založená na metrikách výkonu. Pravidla jsou volitelná pro opakované profily a profily založené na datu.*

     Stanovení priorit profilů a pravidel modulu automatického škálování je také zachyceno v článku [osvědčených postupů automatického škálování.](autoscale-best-practices.md)
     Seznam běžných metrik pro automatické škálování naleznete [v tématu Běžné metriky pro automatické škálování](autoscale-common-metrics.md)

5. Ujistěte se, že jste v Průzkumníku zdrojů v režimu **čtení a zápisu.**

    ![Autoscalewad, výchozí nastavení automatického škálování](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klikněte na Upravit. **Nahraďte** element "profily" v nastavení automatického škálování následující konfigurací:

    ![Profily](media/autoscale-virtual-machine-scale-sets/profiles.png)

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
    Podporovaná pole a jejich hodnoty naleznete [v dokumentaci k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931928.aspx). Nyní nastavení automatického škálování obsahuje tři profily, které byly vysvětleny dříve.

7. Nakonec se podívejte na část **automatického škálování** oznámení. Automatické škálování oznámení umožňují provádět tři věci, když horizontální navýšení kapacity nebo v akci se úspěšně aktivuje.
   - Upozorněte správce a spolusprávce svého předplatného
   - Odeslání e-mailu sadě uživatelů
   - Spusťte volání webhooku. Při aktivována, tento webhook odešle metadata o podstavě automatického škálování a prostředek škálovací sady. Další informace o datové části webového háku automatického škálování naleznete v [tématu Konfigurace webového háku & e-mailová oznámení pro automatické škálování](autoscale-webhook-email.md).

   Přidejte následující do nastavení automatického škálování, které nahradí prvek **oznámení,** jehož hodnota je null

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

   Stisknutím tlačítka **Put** v Průzkumníku prostředků aktualizujte nastavení automatického škálování.

Aktualizovali jste nastavení automatického škálování na škálovací sadě virtuálních počítačích tak, aby zahrnovalo více profilů škálování a oznámení škálování.

## <a name="next-steps"></a>Další kroky
Pomocí těchto odkazů se dozvíte více o automatickém škálování.

[Automatické škálování funkce TroubleShoot pomocí škálovacích sad virtuálních strojů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Běžné metriky pro automatické škálování](autoscale-common-metrics.md)

[Doporučené postupy pro automatické škálování Azure](autoscale-best-practices.md)

[Správa automatického škálování pomocí PowerShellu](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Správa automatického škálování pomocí funkce cli](cli-samples.md#autoscale)

[Konfigurace & e-mailových oznámení pro automatické škálování](autoscale-webhook-email.md)

Odkaz na [šablonu Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
