---
title: Použití automatického škálování Azure s metrikami pro hosty v šabloně škálovací sady Linuxu
description: Zjistěte, jak automaticky škálovat pomocí metrik pro hosta v šabloně sady škálování virtuálních strojů v Linuxu
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 8021b7b8feb6dc06fb2e48bc4e825200a1baad33
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273643"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatické škálování pomocí metrik hosta v šabloně škálovací sady Linuxu

Existují dva široké typy metrik v Azure, které se shromažďují z virtuálních počítačů a škálovacích sad: metriky hosta a metriky hosta. Na vysoké úrovni, pokud chcete použít standardní metriky procesoru, disku a sítě, pak metriky hostitele jsou vhodné. Pokud však potřebujete větší výběr metrik, je třeba se podívat na metriky hosta.

Metriky hostitele nevyžadují další nastavení, protože jsou shromažďovány hostem virtuálního počítače, zatímco metriky hosta vyžadují instalaci [rozšíření Diagnostika Windows Azure](../virtual-machines/windows/extensions-diagnostics-template.md) nebo rozšíření [Diagnostika Linuxazure](../virtual-machines/linux/diagnostic-extension.md) v hostovaném virtuálním počítači. Jedním z běžných důvodů pro použití metrik hosta namísto metrik hostitele je to, že metriky hosta poskytují větší výběr metrik než metriky hostitele. Jedním z takových příkladů jsou metriky využití paměti, které jsou k dispozici pouze prostřednictvím metrik hosta. Podporované metriky hostitele jsou uvedeny [zde](../azure-monitor/platform/metrics-supported.md)a běžně používané metriky hosta jsou uvedeny [zde](../azure-monitor/platform/autoscale-common-metrics.md). Tento článek ukazuje, jak upravit [základní životaschopnou šablonu škálovací sady](virtual-machine-scale-sets-mvss-start.md) tak, aby používala pravidla automatického škálování na základě metrik hosta pro škálovací sady Linuxu.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu škálovací sady. Nyní použijeme tuto starší šablonu a upravíme ji, abychom vytvořili šablonu, která nasazuje škálovací sadu Linuxu s automatickým škálovatstupněm založeným na metrikách hosta.

Nejprve přidejte `storageAccountName` parametry pro a . `storageAccountSasToken` Agent diagnostiky ukládá metrická data v [tabulce](../cosmos-db/table-storage-how-to-use-dotnet.md) v tomto účtu úložiště. Od linuxového agenta diagnostiky verze 3.0 již není podporováno použití přístupového klíče úložiště. Místo toho použijte [token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Dále upravte škálovací sadu `extensionProfile` tak, aby zahrnovala rozšíření diagnostiky. V této konfiguraci zadejte ID prostředku škálovací sady pro shromažďování metrik z, stejně jako účet úložiště a token SAS pro uložení metriky. Určete, jak často jsou metriky agregovány (v tomto případě každou minutu) a které metriky se mají sledovat (v tomto případě procento použité paměti). Podrobnější informace o této konfiguraci a metriky než procento použité paměti naleznete v [této dokumentaci](../virtual-machines/linux/diagnostic-extension.md).

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

Nakonec přidejte `autoscaleSettings` prostředek pro konfiguraci automatického škálování na základě těchto metrik. Tento prostředek `dependsOn` má klauzuli, která odkazuje na škálovací sadu, aby bylo zajištěno, že škálovací sada existuje před pokusem o automatické škálování. Pokud zvolíte jinou metriku pro automatické `counterSpecifier` škálování na, měli `metricName` byste použít z konfigurace rozšíření diagnostiky jako v konfiguraci automatického škálování. Další informace o konfiguraci automatického škálování najdete v [doporučených postupech automatického škálování](../azure-monitor/platform/autoscale-best-practices.md) a [referenční dokumentaci k rozhraní AZURE Monitor REST API](/rest/api/monitor/autoscalesettings).

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
