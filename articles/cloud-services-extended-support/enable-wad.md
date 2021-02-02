---
title: Použijte rozšíření Windows Azure Diagnostics v Cloud Services (Rozšířená podpora).
description: Použijte rozšíření Windows Azure Diagnostics pro Cloud Services (Rozšířená podpora).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ad2a27d1e41ba8e589aa98542c4a0cb3d92afbea
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430861"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Použijte rozšíření Windows Azure Diagnostics v Cloud Services (Rozšířená podpora). 
Můžete monitorovat klíčové metriky výkonu pro libovolnou cloudovou službu. Každá role cloudové služby shromažďuje minimální data: využití CPU, využití sítě a využití disku. Pokud má cloudová služba rozšíření Microsoft. Azure. Diagnostics použité pro roli, může tato role shromažďovat další body dat. Další informace najdete v tématu [Přehled rozšíření](extensions.md) .

U rozšíření Windows Azure Diagnostics se dá povolit Cloud Services (Rozšířená podpora) prostřednictvím [PowerShellu](deploy-powershell.md) nebo [šablony ARM](deploy-template.md) .

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Použití rozšíření Windows Azure Diagnostics s využitím PowerShellu

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFile = "<WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Použít rozšíření Windows Azure Diagnostics pomocí šablony ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
