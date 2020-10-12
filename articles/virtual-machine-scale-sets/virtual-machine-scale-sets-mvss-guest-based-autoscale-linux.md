---
title: Použití automatického škálování Azure s metrikami hosta v šabloně sady na platformě Linux
description: Informace o automatickém škálování pomocí metrik hosta v šabloně sady škálování virtuálních počítačů se systémem Linux
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 549f8fbc1e3acf435011f223faeb5b8240f0c55d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080416"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatické škálování pomocí metrik hosta v šabloně sady pro systém Linux

V Azure existují dva široké typy metrik, které se shromažďují z virtuálních počítačů a sad škálování: metriky hostitele a metriky hostů. Pokud chcete používat standardní metriky procesoru, disku a sítě, na nejvyšší úrovni je vhodné metriky hostitele. Pokud ale potřebujete větší výběr metrik, je třeba metriky hosta vyhledat.

Metriky hostitele nevyžadují další nastavení, protože jsou shromažďovány hostitelským počítačem hostitele, zatímco metriky hosta vyžadují, abyste v hostovaném virtuálním počítači nainstalovali [rozšíření Windows Azure Diagnostics](../virtual-machines/extensions/diagnostics-template.md) nebo [Linux Azure Diagnostics](../virtual-machines/extensions/diagnostics-linux.md) . Jedním z běžných důvodů použití metrik hostů namísto metrik hostitele je, že metriky hosta poskytují větší výběr metrik než metriky hostitele. Jedním z takových příkladů je metrika spotřeby paměti, která je dostupná jenom přes metriky hosta. [Tady jsou uvedené podporované](../azure-monitor/platform/metrics-supported.md)metriky hostitele a [tady](../azure-monitor/platform/autoscale-common-metrics.md)jsou uvedené běžně používané metriky hosta. Tento článek popisuje, jak upravit [šablonu základní životaschopná sada škálování](virtual-machine-scale-sets-mvss-start.md) tak, aby používala pravidla automatického škálování založená na metrikách hosta pro systémy Linux Scale Sets.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu sady škálování. Nyní použijeme tuto předchozí šablonu a upravíte ji k vytvoření šablony, která nasadí sadu škálování pro Linux pomocí automatického škálování na základě metriky hosta.

Nejprve přidejte parametry pro `storageAccountName` a `storageAccountSasToken` . Agent diagnostiky ukládá data metrik v [tabulce](../cosmos-db/tutorial-develop-table-dotnet.md) v tomto účtu úložiště. Od agenta diagnostiky Linux verze 3,0 už použití přístupového klíče k úložišti není podporované. Místo toho použijte [token SAS](../storage/common/storage-sas-overview.md).

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

Dále upravte sadu škálování tak, `extensionProfile` aby zahrnovala diagnostické rozšíření. V této konfiguraci zadejte ID prostředku pro sadu škálování, ze kterého se mají shromažďovat metriky, a také účet úložiště a token SAS, který se použije k uložení metrik. Určete, jak často se metriky agregují (v tomto případě každou minutu) a které metriky se mají sledovat (v tomto případě procento využité paměti). Podrobnější informace o této konfiguraci a metrikách jiných než procento využité paměti najdete v [této dokumentaci](../virtual-machines/extensions/diagnostics-linux.md).

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

Nakonec přidejte `autoscaleSettings` prostředek pro konfiguraci automatického škálování na základě těchto metrik. Tento prostředek obsahuje `dependsOn` klauzuli, která odkazuje na sadu škálování, aby bylo zajištěno, že sada škálování bude existovat před tím, než se pokusí o automatické škálování. Pokud zvolíte jinou metriku pro automatické škálování, použijete `counterSpecifier` z konfigurace rozšíření diagnostiky jako `metricName` v konfiguraci automatického škálování. Další informace o konfiguraci automatického škálování najdete v tématu [osvědčené postupy pro automatické škálování](../azure-monitor/platform/autoscale-best-practices.md) a [Azure monitor REST API Referenční dokumentace](/rest/api/monitor/autoscalesettings).

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
