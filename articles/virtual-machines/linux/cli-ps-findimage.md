---
title: Vyberte bitové kopie virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku Azure k určení vydavatele, nabídky, SKU a verze pro Image Marketplace virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c65ebbc8a61c13b96364dadde45bd4bca828e337
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29740879"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Postup nalezení bitové kopie virtuálního počítače s Linuxem v Azure Marketplace pomocí Azure CLI
Toto téma popisuje, jak pomocí Azure CLI 2.0 najít Image virtuálních počítačů v Azure Marketplace. Tyto informace slouží k určení image pořízenou prostřednictvím Marketplace, při vytváření virtuálního počítače programově pomocí rozhraní příkazového řádku šablony správce prostředků nebo jiných nástrojů.

Ujistěte se, že jste nainstalovali nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a jsou přihlášení k účtu Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Seznam oblíbených obrázků

Spustit [seznamu obrázků virtuálních počítačů az](/cli/azure/vm/image#az_vm_image_list) příkazu, aniž by `--all` možnost, podívejte se do seznamu oblíbených Image virtuálních počítačů v Azure Marketplace. Například spusťte následující příkaz, který zobrazí seznam oblíbených bitové kopie v mezipaměti ve formátu tabulky:

```azurecli
az vm image list --output table
```

Výstup obsahuje bitovou kopii URN (hodnota v *Urn* sloupce). Při vytváření virtuálního počítače s jedním z těchto oblíbených bitových kopií Marketplace, můžete alternativně zadat *UrnAlias*, zkrácený tvar jako *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Vyhledání konkrétních imagí

Chcete-li vyhledat konkrétní image virtuálního počítače na Marketplace, použijte `az vm image list` s `--all` možnost. Tato verze příkazu trvá delší dobu a lze vrátit zdlouhavé výstup, takže obvykle filtrovat seznam podle `--publisher` nebo jiný parametr. 

Například následující příkaz zobrazí všechny Debian nabídky (Nezapomeňte, že bez `--all` přepnout, prochází pouze místní mezipaměti běžné bitových kopií):

```azurecli
az vm image list --offer Debian --all --output table 

```

Částečný výstup: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
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
...
```

Použití filtrů podobné s `--location`, `--publisher`, a `--sku` možnosti. Termínem shodují jen částečně můžete provést i na filtrem, třeba hledání `--offer Deb` najít všechny Debian Image.

Pokud nezadáte konkrétního umístění s `--location` možnost, jsou vráceny hodnoty pro výchozí umístění. (Spuštěním nastavte jiné výchozí umístění `az configure --defaults location=<location>`.)

Například následující příkaz zobrazí všechny Debian SKU 8 v oblasti západní Evropa umístění:

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
...
```

## <a name="navigate-the-images"></a>Přejděte bitové kopie 
Jiný způsob, jak najít bitovou kopii v umístění je spuštění [bitové kopie virtuálních počítačů az seznamu vydavatelů](/cli/azure/vm/image#az_vm_image_list_publishers), [bitové kopie virtuálních počítačů az seznamu nabízí](/cli/azure/vm/image#az_vm_image_list_offers), a [bitové kopie virtuálních počítačů az seznamu SKU](/cli/azure/vm/image#az_vm_image_list_skus) příkazy v pořadí. Pomocí těchto příkazů určit tyto hodnoty:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Potom pro vybraný SKU, můžete verze pro nasazení.

Například následující příkaz seznam vydavatelů bitové kopie v umístění západní USA:

```azurecli
az vm image list-publishers --location westus --output table
```

Částečný výstup:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Tyto informace použijte k vyhledání nabídky od konkrétní vydavatele. Například pokud *Canonical* se vydavatele bitové kopie v umístění západní USA, najít jejich nabídky spuštěním `azure vm image list-offers`. Předejte umístění a vydavatele jako v následujícím příkladu:

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
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Uvidíte, že v oblasti západní USA Canonical publikuje *UbuntuServer* nabídku Azure. Ale co skladové jednotky (SKU)? Chcete-li získat tyto hodnoty, spusťte `azure vm image list-skus` a nastavte umístění, vydavatele a nabídka, která můžete zjistit:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Výstup:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Nakonec použijte `az vm image list` příkazu najděte na konkrétní verzi verze SKU chcete například *16.04 LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Výstup:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```

Nyní můžete přesněji bitovou kopii, kterou chcete použít provedením Poznámka URN hodnoty. Předat tuto hodnotu s `--image` parametr při vytváření virtuálního počítače s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Mějte na paměti, že můžete volitelně nahradit číslo verze v název URN "poslední zálohy". Tato verze je vždy nejnovější verze bitové kopie. 

Pokud nasadíte virtuální počítač pomocí šablony Resource Manageru, můžete nastavit parametry image jednotlivě v `imageReference` vlastnosti. Najdete v článku [odkaz na šablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu
Chcete-li zobrazit informace o plánu nákupu bitové kopie, spusťte [zobrazit bitové kopie virtuálních počítačů az](/cli/azure/image#az_image_show) příkaz. Pokud `plan` vlastnost ve výstupu není `null`, bitová kopie je podmínky je nutné splnit, než se programové nasazení.

Bitovou kopii kanonický Ubuntu Server 16.04 LTS například nemá další podmínky, protože `plan` informace `null`:

```azurecli
az vm image show --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --version 16.04.201801260
```

Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201801260",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Provádí podobné příkaz RabbitMQ certifikované Bitnami obrázek zobrazí následující `plan` vlastnosti: `name`, `product`, a `publisher`. (Také mít některé obrázky, `promotion code` vlastnost.) Pokud chcete nasadit tuto bitovou kopii, najdete v následujících částech přijmout podmínky a povolit programové nasazení.

```azurecli
az vm image show --location westus --publisher bitnami --offer rabbitmq --sku rabbitmq --version 3.7.1801130730
```
Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1801130730",
  "location": "westus",
  "name": "3.7.1801130730",
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

### <a name="accept-the-terms"></a>Přijmout podmínky
Chcete-li zobrazit a přijměte licenční podmínky, použijte [az virtuálních počítačů bitové kopie přijmout podmínky](/cli/azure/vm/image?#az_vm_image_accept_terms) příkaz. Pokud vyjadřujete souhlas s podmínkami, můžete povolit programové nasazení ve vašem předplatném. Potřebujete souhlas s podmínkami jednou za předplatné pro bitovou kopii. Příklad:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Výstup obsahuje `licenseTextLink` na licenční podmínky a informuje, že hodnota `accepted` je `true`:

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
  "retrieveDatetime": "2018-02-22T04:06:28.7641907Z",
  "signature": "WVIEA3LAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNKLNLWI",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů plánu nákupu
Po vyjádření souhlasu s podmínkami pro bitovou kopii, můžete nasadit virtuální počítač v rámci předplatného. Nasazení bitové kopie pomocí `az vm create` příkazu, zadejte parametry pro plánu nákupů kromě k URN pro bitovou kopii. Chcete-li například nasadit virtuální počítač s certifikovaných RabbitMQ Bitnami obrázek:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```



## <a name="next-steps"></a>Další postup
Rychle vytvořit virtuální počítač s použitím informací, image, najdete v tématu [vytvořit a spravovat virtuální počítače s Linuxem pomocí rozhraní příkazového řádku Azure](tutorial-manage-vm.md).