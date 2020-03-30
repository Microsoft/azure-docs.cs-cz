---
title: Výběr ibi obrázků virtuálních počítačových uživatelů Linuxu pomocí azure cli
description: Zjistěte, jak pomocí azure cli určit vydavatele, nabídky, skladové položky a verze pro image virtuálního počítače Marketplace.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 0026c70a3a1a6b5e635e6b43e74b557d4218e6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250306"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Vyhledání imagí virtuálních počítačů s Linuxem na Azure Marketplace pomocí Azure CLI

Toto téma popisuje, jak pomocí azure cli najít image virtuálních počítače na Azure Marketplace. Tyto informace slouží k určení image Marketplace při programovém vytvoření virtuálního počítače pomocí cli, šablony Správce prostředků nebo jiných nástrojů.

Taky projděte dostupné image a nabídky pomocí výlohy [Azure Marketplace,](https://azuremarketplace.microsoft.com/) [portálu Azure](https://portal.azure.com)nebo [Azure PowerShellu](../windows/cli-ps-findimage.md). 

Ujistěte se, že jste nainstalovali nejnovější [vyúčtování příkazového příkazu Azure](/cli/azure/install-azure-cli) a jste přihlášeni k účtu Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Seznam oblíbených obrázků

Spusťte příkaz [seznamu obrázků az vm](/cli/azure/vm/image) bez možnosti `--all` a zobrazte seznam oblíbených ibi virtuálních počítačích na Azure Marketplace. Chcete-li například zobrazit seznam oblíbených obrázků v mezipaměti ve formátu tabulky, spusťte následující příkaz:

```azurecli
az vm image list --output table
```

Výstup obsahuje obrázek URN (hodnota ve sloupci *Urna).* Při vytváření virtuálního virtuálního virtuálního bodu s jedním z těchto oblíbených obrázků Marketplace můžete alternativně zadat *UrnAlias*, zkrácený formulář, například *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Vyhledání konkrétních imagí

Chcete-li najít konkrétní image virtuálního `az vm image list` virtuálního `--all` virtuálního trhu na marketplace, použijte příkaz s možností. Tato verze příkazu trvá nějakou dobu a může vrátit zdlouhavý výstup, `--publisher` takže obvykle filtrujete seznam podle nebo jiného parametru. 

Například následující příkaz zobrazuje všechny nabídky Debianu `--all` (nezapomeňte, že bez přepínače prohledává pouze místní cache běžných obrázků):

```azurecli
az vm image list --offer Debian --all --output table 

```

Částečný výstup: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Použijte podobné filtry `--publisher`v `--sku` možnostech `--location`, a možnostech. Na filtru můžete provádět částečné shody, `--offer Deb` například vyhledat všechny obrazy Debianu.

Pokud s `--location` možností nezadáte konkrétní umístění, budou vráceny hodnoty výchozího umístění. (Spuštěním nastavte jiné `az configure --defaults location=<location>`výchozí umístění .)

Například následující příkaz uvádí všechny skladové sady Debian 8 v umístění v západní Evropě:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Částečný výstup:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navigace v obrazech
 
Dalším způsobem, jak najít obrázek v umístění, je spustit [příkazy az vm seznam obrázků-vydavatelé](/cli/azure/vm/image), [az vm seznam obrázků a](/cli/azure/vm/image) [az vm seznam obrázků-skus](/cli/azure/vm/image) příkazy v pořadí. Pomocí těchto příkazů určíte tyto hodnoty:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Potom pro vybranou skladovou položku můžete zvolit verzi, kterou chcete nasadit.

Následující příkaz například uvádí vydavatele obrázků v umístění v USA v USA:

```azurecli
az vm image list-publishers --location westus --output table
```

Částečný výstup:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Tyto informace slouží k vyhledání nabídek od konkrétního vydavatele. Například pro *vydavatele Canonical* v umístění v USA `azure vm image list-offers`v usa v západní usa, najít nabídky spuštěním . Předají umístění a vydavatele jako v následujícím příkladu:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Výstup:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Vidíte, že v oblasti západní USA Canonical publikuje nabídku *UbuntuServer* v Azure. Ale co skladové jednotky (SKU)? Chcete-li tyto `azure vm image list-skus` hodnoty získat, spusťte a nastavte umístění, vydavatele a nabídku, které jste zjistili:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Výstup:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Nakonec pomocí `az vm image list` příkazu vyhledejte konkrétní verzi skladové položky, kterou chcete, například *18.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Částečný výstup:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Nyní si můžete vybrat přesně obrázek, který chcete použít, a to tak, že vezmete na vědomí hodnotu URN. Předajtuto `--image` hodnotu s parametrem při vytváření virtuálního virtuálního soudu s [příkazem az vm create.](/cli/azure/vm) Nezapomeňte, že volitelně můžete nahradit číslo verze v URL "nejnovější". Tato verze je vždy nejnovější verze obrázku. 

Pokud nasadíte virtuální ho s šablonou Správce prostředků, nastavíte parametry image jednotlivě ve vlastnostech. `imageReference` Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupu obrázku, spusťte příkaz [az vm image show.](/cli/azure/image) Pokud `plan` vlastnost ve výstupu `null`není , bitová kopie má podmínky, které je třeba přijmout před programovým nasazením.

Například kanonický obraz Ubuntu Server 18.04 LTS nemá další `plan` termíny, protože informace jsou: `null`

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Spuštění podobného příkazu pro obrázek RabbitMQ Certified `plan` by `name` `product`Bitnami ukazuje následující vlastnosti: , a `publisher`. (Některé obrázky `promotion code` mají také vlastnost.) Chcete-li nasadit tuto bitovou kopii, naleznete v následujících částech přijmout podmínky a povolit programové nasazení.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Přijetí podmínek použití

Chcete-li zobrazit a přijmout licenční podmínky, použijte příkaz [accept-terms image az vm.](/cli/azure/vm/image?) Když přijmete podmínky, povolíte programové nasazení ve vašem předplatném. Podmínky pro bitovou kopii je třeba přijmout pouze jednou za předplatné. Například:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Výstup obsahuje `licenseTextLink` licenční podmínky a označuje, že `accepted` hodnota `true`je :

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů plánu nákupu

Po přijetí podmínek pro image, můžete nasadit virtuální ho v předplatném. Chcete-li nasadit `az vm create` bitovou kopii pomocí příkazu, zadejte parametry pro plán nákupu kromě URN pro bitovou kopii. Například nasazení virtuálního virtuálního zařízení s image RabbitMQ Certified by Bitnami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>Další kroky
Pokud chcete rychle vytvořit virtuální počítač pomocí informací o image, přečtěte si informace [o vytváření a správě virtuálních počítačích SIO s azure CLI](tutorial-manage-vm.md).
