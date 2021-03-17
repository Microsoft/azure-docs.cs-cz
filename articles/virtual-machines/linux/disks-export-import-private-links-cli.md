---
title: Azure CLI – omezení přístupu pro import a export na spravované disky pomocí privátních odkazů
description: Povolte privátní odkazy na spravované disky pomocí Azure CLI. Umožňuje bezpečně exportovat a importovat disky jenom v rámci vaší virtuální sítě.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684521"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI – omezení přístupu pro import/export u spravovaných disků s privátními odkazy

Pomocí [privátních koncových bodů](../../private-link/private-endpoint-overview.md) můžete omezit export a import spravovaných disků a bezpečně přistupovat k datům přes [soukromé propojení](../../private-link/private-link-overview.md) z klientů ve službě Azure Virtual Network. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro službu Managed disks. Síťový provoz mezi klienty ve své virtuální síti a spravovanými disky se přesměruje jenom přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Pokud chcete používat privátní odkazy pro export a import spravovaných disků, nejdřív vytvořte prostředek pro přístup k disku a propojte ho s virtuální sítí ve stejném předplatném vytvořením privátního koncového bodu. Pak přidružte disk nebo snímek k instanci přístupu k disku. Nakonec nastavte vlastnost NetworkAccessPolicy disku nebo snímku na `AllowPrivate` . Tím se omezí přístup k vaší virtuální síti. 

Vlastnost NetworkAccessPolicy můžete nastavit tak, aby `DenyAll` nedocházelo k tomu, aby kdokoli mohl exportovat data disku nebo snímku. Výchozí hodnota pro vlastnost NetworkAccessPolicy je `AllowAll` .

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Přihlaste se ke svému předplatnému a nastavte proměnné.

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Vytvoření přístupu k disku pomocí Azure CLI
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Zásady sítě, jako jsou skupiny zabezpečení sítě (NSG), nejsou podporovány u privátních koncových bodů. Aby bylo možné v dané podsíti nasadit privátní koncový bod, je v této podsíti vyžadováno explicitní nastavení zakázání. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Zakázat zásady privátního koncového bodu podsítě

Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže je potřeba aktualizovat podsíť, aby se zakázaly zásady sítě privátního koncového bodu. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Vytvoření privátního koncového bodu pro objekt pro přístup k disku

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS

Vytvořte zónu Privátní DNS pro doménu objektů BLOB úložiště, vytvořte propojení přidružení s Virtual Network a vytvořte skupinu zón DNS pro přidružení privátního koncového bodu ke zóně Privátní DNS. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Vytvoření disku chráněného soukromými odkazy
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Vytvoření snímku disku chráněného soukromými odkazy
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy týkající se privátních odkazů](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Export nebo kopírování spravovaných snímků jako virtuálních pevných disků do účtu úložiště v jiné oblasti pomocí rozhraní příkazového řádku](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)