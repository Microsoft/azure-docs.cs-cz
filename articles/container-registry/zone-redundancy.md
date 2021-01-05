---
title: Registr redundantního registru pro vysokou dostupnost
description: Seznamte se s povolením redundance zóny v Azure Container Registry vytvořením registru kontejnerů nebo replikace v zóně dostupnosti Azure. Redundance zóny je funkcí úrovně Premium Service.
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: 1553beef47a3d493f066e47cd39751093d83fc24
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803506"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Povolit redundanci zóny v Azure Container Registry pro odolnost a vysokou dostupnost

Kromě [geografické replikace](container-registry-geo-replication.md), která replikuje data registru v jedné nebo více oblastech Azure, aby poskytovala dostupnost a snížila latenci pro regionální operace, Azure Container Registry podporuje *redundanci zóny*. [Redundance zóny](../availability-zones/az-overview.md#availability-zones) poskytuje odolnost a vysokou dostupnost registru nebo prostředku replikace (Replika) v konkrétní oblasti.

V tomto článku se dozvíte, jak nastavit redundantní Registry kontejneru nebo redundantní repliku zóny pomocí Azure Portal nebo šablony Azure Resource Manager. 

Redundance zóny je **ukázkovou** funkcí úrovně služby registru kontejneru Premium. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

## <a name="preview-limitations"></a>Omezení verze Preview

* V současné době jsou podporovány v následujících oblastech: Východní USA, Východní USA 2 a Západní USA 2.
* Převody oblastí na zóny dostupnosti se aktuálně nepodporují. Pokud chcete povolit podporu zóny dostupnosti v oblasti, je nutné, aby se registr vytvořil v požadované oblasti, kde je povolená podpora zóny dostupnosti, nebo musí být replikovaná oblast přidána s povolenou podporou zóny dostupnosti.
* Redundanci zóny nelze v oblasti zakázat.
* [ACR úlohy](container-registry-tasks-overview.md) zatím nepodporují zóny dostupnosti.
* V současné době jsou podporovány prostřednictvím šablon Azure Resource Manager nebo Azure Portal. Podpora Azure CLI bude povolená v budoucí verzi.

## <a name="about-zone-redundancy"></a>O redundanci zóny

Pomocí [zón dostupnosti](../availability-zones/az-overview.md) Azure můžete vytvořit odolný a vysoce dostupný registr Azure Container Registry v oblasti Azure. Organizace můžou například nastavit zónu redundantního služby Azure Container Registry s dalšími [podporovanými prostředky Azure](../availability-zones/az-region.md) tak, aby splňovaly datové zaregistrování nebo jiné požadavky na dodržování předpisů a zároveň poskytovaly vysokou dostupnost v rámci určité oblasti.

Azure Container Registry podporuje taky [geografickou replikaci](container-registry-geo-replication.md), která replikuje službu napříč několika oblastmi. díky tomu můžete redundanci a lokalitu počítat s výpočetními prostředky v jiných umístěních. Kombinace zón dostupnosti pro redundanci v rámci jedné oblasti a geografická replikace napříč několika oblastmi zvyšuje spolehlivost a výkon registru.

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny. Každá zóna má jedno nebo více datových center vybavených nezávislým napájením, chlazením a sítí. Při konfiguraci redundance zóny se registr (nebo replika registru v jiné oblasti) replikuje napříč všemi zónami dostupnosti v dané oblasti, takže když dojde k selhání datacentra, zůstane v ní dostupná.

## <a name="create-a-zone-redundant-registry---portal"></a>Vytvoření zóny – redundantní Registry – portál

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. Vyberte **vytvořit**  >  **kontejnery** prostředků  >  **Container Registry**.
1. Na kartě **základy** vyberte nebo vytvořte skupinu prostředků a zadejte jedinečný název registru. 
1. V oblasti **umístění** vyberte oblast, která podporuje redundanci zóny pro Azure Container Registry, například *východní USA*.
1. V položce **SKU** vyberte **Premium**.
1. V **oblasti dostupnosti** vyberte **povoleno**.
1. Volitelně můžete nakonfigurovat další nastavení registru a pak vybrat **zkontrolovat + vytvořit**.
1. Vyberte **vytvořit** a nasaďte instanci registru.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Povolit redundanci zóny v Azure Portal":::

Postup vytvoření replikace redundantní v zóně:

1. Přejděte do registru kontejneru úrovně Premium a vyberte **replikace**.
1. Na mapě, která se zobrazí, vyberte zelený šestiúhelník v oblasti, která podporuje redundanci zóny pro Azure Container Registry, jako je například **západní USA 2**. Potom vyberte **Vytvořit**.
1. V okně **vytvořit replikaci** v části **zóny dostupnosti** vyberte **povoleno** a pak vyberte **vytvořit**.

## <a name="create-a-zone-redundant-registry---template"></a>Vytvoření zóny – redundantní Registry – šablona

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V případě potřeby spuštěním příkazu [AZ Group Create](/cli/azure/group) vytvořte skupinu prostředků pro registr v oblasti, která [podporuje zóny dostupnosti](../availability-zones/az-region.md) pro Azure Container Registry, jako je například *eastus*.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>Nasazení šablony 

Následující šablonu Správce prostředků můžete použít k vytvoření geograficky redundantního registru zóny, který je v geograficky replikované. Tato šablona standardně povoluje redundanci zóny v registru a další místní repliku. 

Zkopírujte následující obsah do nového souboru a uložte ho pomocí názvu souboru, například `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Spusťte následující příkaz [AZ Deployment Group Create](/cli/azure/deployment?view=azure-cli-latest) a vytvořte registr pomocí předchozího souboru šablony. Pokud je uvedeno, zadejte:

* jedinečný název registru nebo šablona se nasadí bez parametrů a pro vás vytvoří jedinečný název.
* umístění repliky, která podporuje zóny dostupnosti, například *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Ve výstupu příkazu si poznamenejte `zoneRedundancy` vlastnost registru a repliky. Je-li tato možnost povolena, je každý prostředek redundantní v zóně:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [oblastech, které podporují zóny dostupnosti](../availability-zones/az-region.md).
* Přečtěte si další informace o sestavování a [spolehlivosti](/azure/architecture/framework/resiliency/overview) v Azure.
