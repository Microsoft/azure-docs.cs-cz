---
title: Vytvoření fondu služby Azure Batch bez veřejných IP adres
description: Zjistěte, jak vytvořit fond bez veřejných IP adres.
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007666"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Vytvoření fondu služby Azure Batch bez veřejných IP adres

Když vytváříte fond Azure Batch, můžete zřídit fond konfigurací virtuálních počítačů bez veřejné IP adresy. Tento článek vysvětluje, jak nastavit fond dávek bez veřejných IP adres.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Proč používat fond bez veřejných IP adres?

Ve výchozím nastavení jsou všem výpočetním uzlům ve fondu konfigurací virtuálních počítačů Azure Batch přiřazeny veřejné IP adresy. Tuto adresu používá služba Batch k naplánování úloh a ke komunikaci s výpočetními uzly, včetně odchozího přístupu k Internetu.

Pokud chcete omezit přístup k těmto uzlům a snížit zjistitelnost těchto uzlů z Internetu, můžete fond zřídit bez veřejných IP adres.

> [!IMPORTANT]
> Podpora fondů bez veřejných IP adres v Azure Batch je v současnosti ve verzi Public Preview pro následující oblasti: Francie – střed, Východní Asie, Středozápadní USA, Střed USA – jih, Západní USA 2, Východní USA, Severní Evropa, Východní USA 2, Střed USA, Západní Evropa, Střed USA – sever, Západní USA, Austrálie – východ, Japonsko – východ, Japonsko – západ.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

- **Ověřování**. Aby bylo možné použít fond bez veřejných IP adres v rámci [virtuální sítě](./batch-virtual-network.md), musí klientské rozhraní API služby Batch používat ověřování Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). Pokud nevytváříte fond v rámci virtuální sítě, můžete použít ověřování Azure AD nebo ověřování založené na klíčích.

- **Virtuální síť Azure**. Pokud vytváříte fond ve [virtuální síti](batch-virtual-network.md), postupujte podle těchto požadavků a konfigurací. Pokud chcete předem připravit virtuální síť s jednou nebo více podsítěmi, můžete použít Azure Portal, Azure PowerShell, rozhraní příkazového řádku (CLI) Azure Command-Line nebo jiné metody.
  - Virtuální síť musí být ve stejném předplatném a stejné oblasti jako účet Batch, který použijete k vytvoření fondu.
  - Podsíť zadaná pro fond musí obsahovat dostatek nepřiřazených IP adres pro všechny virtuální počítače, na které fond cílí, jejichž počet je součtem vlastností `targetDedicatedNodes` a `targetLowPriorityNodes` fondu. Pokud podsíť nemá dostatek nepřiřazených IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti.
  - Je nutné zakázat zásady služby privátního propojení a sítě koncového bodu. To se dá udělat pomocí Azure CLI: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Pro každý 100 vyhrazený uzel nebo uzly s nízkou prioritou služba Batch přiděluje jednu službu privátního propojení a jeden nástroj pro vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. U rozsáhlých fondů možná budete muset [požádat o zvýšení kvóty](batch-quota-limit.md#increase-a-quota) u jednoho nebo více těchto prostředků. Kromě toho by se neměly žádné zámky prostředků použít pro všechny prostředky vytvořené službou Batch, protože to brání vyčištění prostředků v důsledku akcí iniciované uživatelem, jako je odstranění fondu nebo změna velikosti na nulu.

## <a name="current-limitations"></a>Aktuální omezení

1. Fondy bez veřejných IP adres musí používat konfiguraci virtuálních počítačů, a ne Cloud Services konfiguraci.
1. [Konfigurace vlastního koncového bodu](pool-endpoint-configuration.md) pro dávkové výpočetní uzly nefunguje s fondy bez veřejných IP adres.
1. Vzhledem k tomu, že neexistují žádné veřejné IP adresy, nemůžete [použít vlastní zadané veřejné IP adresy](create-pool-public-ip.md) s tímto typem fondu.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Vytvoření fondu bez veřejných IP adres v Azure Portal

1. Na webu Azure Portal přejděte ke svému účtu Batch.
1. V okně **Nastavení** na levé straně vyberte **fondy**.
1. V okně **fondy** vyberte **Přidat**.
1. V okně **Přidat fond** vyberte v rozevíracím seznamu **typ obrázku** možnost, kterou hodláte použít.
1. Vyberte správný **Vydavatel/nabídku/SKU** vaší image.
1. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových vyhrazených uzlů** a **uzlů s nízkou prioritou**, a také všech požadovaných volitelných nastavení.
1. Volitelně můžete vybrat virtuální síť a podsíť, které chcete použít. Tato virtuální síť musí být ve stejné skupině prostředků jako fond, který vytváříte.
1. V **typ zřizování IP adres** vyberte **NoPublicIPAddresses**.

![Snímek obrazovky Přidat fond s vybraným NoPublicIPAddresses](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Použití dávkové REST API k vytvoření fondu bez veřejných IP adres

Následující příklad ukazuje, jak použít [Azure Batch REST API](/rest/api/batchservice/pool/add) k vytvoření fondu, který používá veřejné IP adresy.

### <a name="rest-api-uri"></a>Identifikátor URI v REST API

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Text požadavku

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Odchozí přístup k Internetu

Ve fondu bez veřejných IP adres nebudou mít vaše virtuální počítače přístup k veřejnému Internetu, pokud nebudete správně konfigurovat nastavení sítě, například pomocí [překladu adres (NAT) virtuální sítě](../virtual-network/nat-overview.md). Upozorňujeme, že překlad adres (NAT) umožňuje pouze odchozí přístup k Internetu z virtuálních počítačů ve virtuální síti. Výpočetní uzly vytvořené dávkou nebudou veřejně přístupné, protože nemají přidružené veřejné IP adresy.

Dalším způsobem, jak poskytnout odchozí připojení, je použití uživatelsky definované trasy (UDR). To vám umožní směrovat provoz na proxy počítač, který má veřejný přístup k Internetu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vytváření fondů ve virtuální síti](batch-virtual-network.md).
- Naučte [se používat privátní koncové body s účty Batch](private-connectivity.md).
