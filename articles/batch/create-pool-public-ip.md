---
title: Vytvoření fondu se zadanými veřejnými IP adresami
description: Naučte se, jak vytvořit fond služby Batch, který používá vaše vlastní veřejné IP adresy.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849324"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Vytvořit fond Azure Batch se zadanými veřejnými IP adresami

Když vytváříte fond Azure Batch, můžete [fond zřídit v podsíti virtuální sítě Azure](batch-virtual-network.md) , kterou zadáte. Virtuální počítače ve fondu Batch jsou přístupné prostřednictvím veřejných IP adres, které jsou vytvořeny službou Batch. Tyto veřejné IP adresy se můžou měnit po dobu života fondu, což znamená, že nastavení sítě může být zastaralá, pokud se IP adresy neaktualizují.

Můžete vytvořit seznam statických veřejných IP adres pro použití s virtuálními počítači ve vašem fondu. To vám umožní řídit seznam veřejných IP adres a zajistit, aby se neočekávaně nezměnily. To může být obzvláště užitečné, pokud pracujete se všemi externími službami, jako je třeba databáze, která omezuje přístup k určitým IP adresám.

Pokud chcete získat informace o vytváření fondů bez veřejných IP adres, přečtěte si téma [Vytvoření fondu Azure Batch bez veřejných IP adres](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Požadavky

- **Ověřování**. Aby bylo možné používat veřejnou IP adresu, musí klientské rozhraní API služby Batch používat [ověřování Azure Active Directory (AD)](batch-aad-auth.md).

- **Virtuální síť Azure**. Musíte použít [virtuální síť](batch-virtual-network.md) ze stejného předplatného Azure, ve kterém vytváříte fond a IP adresy. Můžou se použít jenom virtuální sítě založené na Azure Resource Manager. Ujistěte se, že virtuální síť splňuje všechny [Obecné požadavky](batch-virtual-network.md#vnet-requirements).

- **Aspoň jedna veřejná IP adresa Azure**. Pokud chcete vytvořit jednu nebo víc veřejných IP adres, můžete použít [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), [rozhraní Azure Command-Line (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)nebo [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Nezapomeňte postupovat podle níže uvedených požadavků.

> [!NOTE]
> Batch automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje veřejné IP adresy. Pro každý 100 vyhrazené uzly Batch obecně přiděluje jednu skupinu zabezpečení sítě (NSG) a jeden nástroj pro vyrovnávání zatížení. Tyto prostředky jsou omezené kvótou prostředků předplatného. Pokud používáte větší fondy, možná budete muset [požádat o zvýšení kvóty](batch-quota-limit.md#increase-a-quota) na jeden nebo více těchto prostředků.

## <a name="public-ip-address-requirements"></a>Požadavky na veřejné IP adresy

Při vytváření veřejných IP adres mějte na paměti následující požadavky:

- Veřejné IP adresy musí být ve stejném předplatném a oblasti jako účet Batch, který používáte k vytvoření fondu.
- **Přiřazení IP adresy** musí být nastavené na **static**.
- **SKU** musí být nastavená na hodnotu **Standard**.
- Je třeba zadat název DNS.
- Veřejné IP adresy se musí používat jenom pro fondy konfigurací virtuálních počítačů. Tyto IP adresy by neměly používat žádné další prostředky, jinak se fond může setkat s chybami přidělení.
- Žádné zásady zabezpečení ani zámky prostředků by neměly omezovat přístup uživatelů k veřejné IP adrese.
- Počet veřejných IP adres určených pro fond musí být dostatečně velký, aby odpovídal počtu virtuálních počítačů, které jsou pro fond cílené. Musí se jednat aspoň o součet vlastností **targetDedicatedNodes**   a **targetLowPriorityNodes**   fondu. Pokud není k dispozici dostatek IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti. V současné době služba Batch používá pro každé 100 virtuálních počítačů jednu veřejnou IP adresu.
- Vždy máte další vyrovnávací paměť veřejných IP adres. Doporučujeme přidat alespoň jednu další veřejnou IP adresu nebo přibližně 10% z celkového počtu veřejných IP adres, které přidáte do fondu, podle toho, co je větší. Tato dodatečná vyrovnávací paměť vám pomůže dávkovat se svou interní optimalizací při horizontálním navýšení kapacity a zároveň umožnit rychlejší škálování po neúspěšném navýšení nebo snížení kapacity.
- Po vytvoření fondu nemůžete přidat ani změnit seznam veřejných IP adres používaných fondem. Pokud potřebujete seznam upravit, musíte odstranit fond a pak ho znovu vytvořit.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Vytvoření fondu Batch s veřejnými IP adresami

Následující příklad ukazuje, jak používat [službu Azure Batch REST API](/rest/api/batchservice/pool/add) k vytvoření fondu, který používá veřejné IP adresy.

### <a name="batch-service-rest-api"></a>Rozhraní REST API služby Batch

Identifikátor URI v REST API

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Text požadavku

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Seznamte se [s vytvářením fondů v podsíti virtuální sítě Azure](batch-virtual-network.md).
- Přečtěte si o [vytváření Azure Batch fondu bez veřejných IP adres](./batch-pool-no-public-ip-address.md).

