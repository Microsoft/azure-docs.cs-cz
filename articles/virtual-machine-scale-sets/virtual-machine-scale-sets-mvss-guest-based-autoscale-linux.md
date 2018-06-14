---
title: Použití Azure škálování s hosta metriky v šabloně sady škálování Linux | Microsoft Docs
description: Zjistěte, jak chcete používat automatické škálování pomocí hosta metriky v šabloně sadu škálování virtuálního počítače systému Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781424"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Škálování pomocí hosta metriky v šabloně sady škálování Linux

Existují dva typy metriky v Azure, které jsou shromážděná z virtuálních počítačů a škálovat nastaví: některé pocházet z hostitele virtuálního počítače a jiné pocházet z virtuálním počítači hosta. Na vysoké úrovni, pokud používáte standard procesoru, disku a metriky sítě, potom hostitele metrics je pravděpodobně vhodné. Pokud však budete potřebovat větší výběr metriky, potom hosta metrics je pravděpodobně lépe odpovídaly. Podívejme se na rozdíly mezi těmito dvěma:

Hostitele metrics je jednodušší a spolehlivější. Nevyžaduje žádné další nastavení vzhledem k tomu, že se shromažďují pro hostitele virtuálního počítače, zatímco hosta metriky vyžadují k instalaci [rozšíření Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) nebo [rozšíření diagnostiky Azure Linux](../virtual-machines/linux/diagnostic-extension.md)ve virtuálním počítači hosta. Jeden běžným důvodem k použití hosta metriky místo metriky hostitele je, že hosta metriky poskytovat větší výběr metriky než metriky hostitele. Jedním z těchto příkladů je metriky využití paměti, které jsou k dispozici prostřednictvím metriky hosta. Jsou uvedeny podporované hostitele metriky [sem](../monitoring-and-diagnostics/monitoring-supported-metrics.md), a jsou uvedeny hosta běžně používané metriky [zde](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Tento článek ukazuje, jak upravit [minimální přijatelná měřítko nastavit šablonu](./virtual-machine-scale-sets-mvss-start.md) používat automatické škálování pravidla na základě hosta metriky pro Linux sady škálování.

## <a name="change-the-template-definition"></a>Změna definice šablony

Nakonfigurujte šablonu minimální přijatelná škálování si můžete prohlédnout [sem](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), a šablony pro nasazení měřítka Linux nastavit ochranu hosta škálování si můžete prohlédnout [zde](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Podívejme se na rozdílové použít k vytvoření této šablony (`git diff minimum-viable-scale-set existing-vnet`) část podle část:

Nejprve přidejte parametry pro `storageAccountName` a `storageAccountSasToken`. Diagnostika agenta ukládá data metriky v [tabulky](../cosmos-db/table-storage-how-to-use-dotnet.md) v rámci tohoto účtu úložiště. Od verze agenta diagnostiky Linux verze 3.0 pomocí přístupový klíč k úložišti není podporován. Místo toho použijte [tokenu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

V dalším kroku změnit škálovací sadu `extensionProfile` zahrnout rozšíření diagnostiky. V této konfiguraci zadejte ID prostředku sad shromažďovat metriky, jakož i účet úložiště a tokenu SAS škálování používat k uložení metriky. Zadejte, jak často jsou metriky agregovat (v tomto případě každou minutu) a které metriky pro sledování (v této případu, procento použité paměti). Podrobnější informace o této konfiguraci a metriky než procento využité paměti, najdete v části [této dokumentace](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Nakonec přidejte `autoscaleSettings` prostředku nakonfigurovat automatické škálování podle tyto metriky. Tento prostředek má `dependsOn` klauzuli, která odkazuje na měřítka nastavena na zkontrolujte, zda byly sadou škálování před pokusem o škálování ho. Pokud si zvolíte jiné metriky pro škálování na, byste použili `counterSpecifier` z konfiguraci rozšíření diagnostiky jako `metricName` v konfiguraci automatického škálování. Další informace o konfiguraci automatického škálování, najdete v článku [osvědčené postupy škálování](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) a [referenční dokumentace rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
