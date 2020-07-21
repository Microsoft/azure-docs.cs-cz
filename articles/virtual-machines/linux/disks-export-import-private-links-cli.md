---
title: Azure CLI – omezení přístupu pro import a export na spravované disky pomocí privátních odkazů (Preview)
description: Povolte privátní odkazy (Preview) pro vaše spravované disky pomocí Azure CLI. Umožňuje bezpečně exportovat a importovat disky jenom v rámci vaší virtuální sítě.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9184dc78f0f9f8d7997e0bb64bc4521e19364cfe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535743"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>Azure CLI – omezení přístupu pro import/export u spravovaných disků s privátními odkazy (Preview)

Pomocí [privátních koncových bodů](../../private-link/private-endpoint-overview.md) (Preview) můžete omezit export a import spravovaných disků a bezpečně přistupovat k datům přes [soukromé propojení](../../private-link/private-link-overview.md) z klientů ve službě Azure Virtual Network. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro službu Managed disks. Síťový provoz mezi klienty virtuální sítě a spravovanými disky prochází přes virtuální síť a privátním odkazem v páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. 

Pokud chcete používat privátní odkazy pro export a import spravovaných disků, nejdřív vytvořte prostředek pro přístup k disku a propojte ho s virtuální sítí ve stejném předplatném vytvořením privátního koncového bodu. Pak přidružte disk nebo snímek k instanci přístupu k disku. Nakonec nastavte vlastnost NetworkAccessPolicy disku nebo snímku na `AllowPrivate` . Tím se omezí přístup k vaší virtuální síti. 

Vlastnost NetworkAccessPolicy můžete nastavit tak, aby `DenyAll` nedocházelo k tomu, aby kdokoli mohl exportovat data disku nebo snímku. Výchozí hodnota pro vlastnost NetworkAccessPolicy je `AllowAll` .

## <a name="limitations"></a>Omezení

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat privátní koncové body pro export a import spravovaných disků, musíte mít ve svém předplatném povolenou funkci. Pokud chcete mdprivatelinks@microsoft funkci povolit pro vaše předplatná, odešlete e-mail na adresu. com s ID předplatného.

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Přihlaste se ke svému předplatnému a nastavte proměnné.

```azurecli-interactive

subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=CentralUSEUAP
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
```azurecli-interactive
az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Zásady sítě, jako jsou skupiny zabezpečení sítě (NSG), nejsou podporovány u privátních koncových bodů. Aby bylo možné v dané podsíti nasadit privátní koncový bod, je v této podsíti vyžadováno explicitní nastavení zakázání. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Zakázat zásady privátního koncového bodu podsítě

Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže je potřeba aktualizovat podsíť, aby se zakázaly zásady sítě privátního koncového bodu. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Vytvoření privátního koncového bodu pro objekt pro přístup k disku

```azurecli-interactive
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

```azurecli-interactive
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
## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Vytvoření snímku disku chráněného soukromými odkazy
   ```cli
   diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)
   
   az group deployment create -g $resourceGroupName \
      --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateSnapshotWithExportViaPrivateLink.json" \
      --parameters "snapshotNameSecuredWithPL=$snapshotNameSecuredWithPL" \
      "sourceResourceId=$diskId" \
      "diskAccessId=$diskAccessId" \
      "region=$region" \
      "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy týkající se privátních odkazů](faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Export/kopírování spravovaných snímků jako VHD do účtu úložiště v jiné oblasti pomocí PowerShellu](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)
