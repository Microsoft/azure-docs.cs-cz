---
title: Automatické škálování v Azure pomocí metrik hosta v šablony škálovací sady Linux | Dokumentace Microsoftu
description: Zjistěte, jak automatické škálování s použitím metrik hosta v šabloně Linux Virtual Machine Scale Sets
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: e30fdb684fbabbdcea334115e3f645e63dec6623
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322632"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatické škálování s použitím metrik hosta v Linuxu šablony škálovací sady

Existují dva typy metrik v Azure, která se shromažďují z virtuálních počítačů a škálovací sady: některé pocházejí z hostitelského virtuálního počítače a jiné pocházejí z hostovaného virtuálního počítače. Na vysoké úrovni Pokud používáte standardní procesoru, disku a sítě metriky, pak metriky hostitele jsou pravděpodobně vhodné. Pokud však potřebujete větší výběr metrik, metrik hosta budou pravděpodobně lépe vyhovovat. Pojďme se podívat na rozdíly mezi těmito dvěma:

Metriky hostitele jsou jednodušší a spolehlivější. Nevyžaduje žádné další nastavení vzhledem k tomu, že byly shromážděny sadou hostitelského virtuálního počítače, zatímco metriky hosta vyžadují k instalaci [rozšíření Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) nebo [rozšíření diagnostiky Azure Linux](../virtual-machines/linux/diagnostic-extension.md)ve virtuálním počítači hosta. Jedním z běžných důvodů metrik hosta nahrazujícím metriky hostitele je, že metrik hosta poskytují větší výběr metrik než metriky hostitele. Jedním z takových příkladů je metriky využití paměti, které jsou dostupné jen přes metrik hosta. Jsou uvedeny podporované hostitele metriky [tady](../monitoring-and-diagnostics/monitoring-supported-metrics.md), a jsou uvedeny běžně používané hosta metriky [tady](../azure-monitor/platform/autoscale-common-metrics.md). Tento článek popisuje, jak změnit [šablonu minimální přijatelné škálovací sady](./virtual-machine-scale-sets-mvss-start.md) použití pravidel automatického škálování na základě metrik hosta pro Linux škálovací sady.

## <a name="change-the-template-definition"></a>Změna definice šablony

Šablonu minimální přijatelné škálovací sady můžete zobrazit [tady](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), a šablony pro nasazení systému Linux škálovací sady s automatickým Škálováním hosta můžete vidět [tady](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Podívejme se na rozdíl použít k vytvoření této šablony (`git diff minimum-viable-scale-set existing-vnet`) jeden po druhém:

Nejprve přidejte parametry pro `storageAccountName` a `storageAccountSasToken`. Agenta diagnostiky ukládá data metriky v [tabulky](../cosmos-db/table-storage-how-to-use-dotnet.md) v tomto účtu úložiště. Od verze diagnostiky agenta pro Linux verze 3.0 pomocí přístupového klíče úložiště se už nepodporuje. Místo toho použijte [tokenů SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

V dalším kroku změnit škálovací sady `extensionProfile` zahrnout diagnostického rozšíření. V této konfiguraci zadejte ID prostředku škálovací sady, chcete-li shromažďovat metriky, jakož i účet úložiště a tokenu SAS pro ukládání metriky. Zadejte, jak často se metriky se agregují (v tomto případě každou minutu) a jaké metriky pro sledování (v této případu, procento využité paměti). Podrobnější informace o této konfiguraci a metriky než % využité paměti, naleznete v tématu [této dokumentace](../virtual-machines/linux/diagnostic-extension.md).

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

Nakonec přidejte `autoscaleSettings` prostředek, který se konfigurace automatického škálování na základě těchto metrik. Tento prostředek nemá `dependsOn` klauzuli, která odkazuje na ose nastavena na zajistěte, aby existovala před pokusem automatického škálování škálovací sady. Pokud se rozhodnete na jiné metriky automatického škálování, můžete využít `counterSpecifier` v konfiguraci rozšíření diagnostiky jako `metricName` v konfiguraci automatického škálování. Další informace o konfiguraci automatického škálování najdete v článku [osvědčené postupy automatického škálování](..//azure-monitor/platform/autoscale-best-practices.md) a [referenční dokumentaci rozhraní API REST služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

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





## <a name="next-steps"></a>Další postup

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
