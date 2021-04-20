---
title: Přepsat informace o SKU přes CSCFG/CSDEF pro Azure Cloud Services (Rozšířená podpora)
description: Přepsat informace o SKU přes CSCFG/CSDEF pro Azure Cloud Services (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739255"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Přepsat informace o SKU přes CSCFG/CSDEF v Cloud Services (Rozšířená podpora) 

Tato funkce umožní uživateli aktualizovat velikost role a počet instancí ve své cloudové službě pomocí vlastnosti **allowModelOverride** , aniž by museli aktualizovat konfiguraci služby a soubory definice služby. tím se umožní, aby cloudová služba mohla škálovat nahoru/dolů, aniž by se musela znovu zabalit a znovu nasadit.

## <a name="set-allowmodeloverride-property"></a>Nastavit vlastnost allowModelOverride
Vlastnost allowModelOverride lze nastavit následujícími způsoby:
* Pokud allowModelOverride = true, volání rozhraní API aktualizuje velikost role a počet instancí pro cloudovou službu bez ověřování hodnot pomocí souborů csdef a cscfg. 
> [!Note]
> Hodnota cscfg se aktualizuje tak, aby odrážela počet instancí rolí, ale role csdef (v rámci cspkg) zachová staré hodnoty.
* Při allowModelOverride = false vyvolá volání rozhraní API chybu, když se hodnoty velikosti role a počtu instancí neshodují se soubory csdef a cscfg.

Výchozí hodnota je false (NEPRAVDA). Pokud je vlastnost nastavena na hodnotu false zpět z hodnoty true, budou pro ověření znovu zkontrolovány soubory csdef a cscfg.

Pokud chcete použít vlastnost v PowerShellu, šabloně a sadě SDK, přečtěte si níže uvedené ukázky.

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Nastavením vlastnosti "allowModelOverride" = true zde dojde k aktualizaci cloudové služby pomocí vlastností role definovaných v části roleProfile.
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Když nastavíte přepínač "AllowModelOverride" v nové rutině New-AzCloudService, aktualizuje se cloudová služba pomocí vlastností SKU definovaných v RoleProfile.
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>Sada SDK
Nastavením proměnné AllowModelOverride = true se cloudová služba aktualizuje pomocí vlastností SKU definovaných v RoleProfile.

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>portál Azure
Portál neumožňuje, aby výše uvedená vlastnost přepsala velikost role a počet instancí v parametrech csdef a cscfg. 


## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
