---
title: Vyberte Image virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak používat rozhraní příkazového řádku Azure k určení vydavatele, nabídky, SKU a verze pro Image Marketplace virtuálních počítačů.
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
ms.date: 09/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ff08b0f155be54ee57d2d9999c47aa56a366d09
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852197"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Postup vyhledání imagí virtuálních počítačů s Linuxem na webu Azure Marketplace pomocí Azure CLI
Toto téma popisuje, jak používat rozhraní příkazového řádku Azure k vyhledání imagí virtuálních počítačů na webu Azure Marketplace. Tyto informace slouží k určení Marketplace image, pokud vytvoříte virtuální počítač programově pomocí rozhraní příkazového řádku, šablon Resource Manageru nebo jiných nástrojů.

Také procházet dostupné Image a pomocí nabídky [Azure Marketplace](https://azuremarketplace.microsoft.com/) výkladní skříň, [webu Azure portal](https://portal.azure.com), nebo [prostředí Azure PowerShell](../windows/cli-ps-findimage.md). 

Ujistěte se, že jste nainstalovali nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a přihlášení k účtu Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Seznam oblíbených imagí

Spustit [az vm image seznamu](/cli/azure/vm/image#az_vm_image_list) příkazu, aniž by `--all` možnost, chcete-li zobrazit seznam oblíbených imagí virtuálních počítačů na webu Azure Marketplace. Například spuštěním následujícího příkazu můžete zobrazit seznam oblíbených imagí v mezipaměti ve formátu tabulky:

```azurecli
az vm image list --output table
```

Výstup obsahuje image URN (hodnota v *Urn* sloupec). Při vytváření virtuálního počítače s jedním z těchto oblíbených imagí Marketplace, můžete alternativně zadat *UrnAlias*, zkrácený tvar, jako *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Vyhledání konkrétních imagí

Chcete-li najít konkrétní image virtuálního počítače na webu Marketplace, použijte `az vm image list` příkazů `--all` možnost. Tato verze příkazu trvá nějakou dobu pro dokončení a může vrátit zdlouhavé výstup, takže obvykle filtrovat seznam podle `--publisher` nebo jiný parametr. 

Například následující příkaz zobrazí všechny Debian nabídky (mějte na paměti, aniž by `--all` přepnout, prohledá pouze místní mezipaměť obvyklých imagí):

```azurecli
az vm image list --offer Debian --all --output table 

```

Částečný výstup: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
...
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

Podobné filtry se použijí `--location`, `--publisher`, a `--sku` možnosti. Můžete provádět částečné shody na filtr, jako je například hledání `--offer Deb` najít všechny Image Debian.

Pokud nezadáte konkrétní umístění s `--location` možnost, jsou vráceny hodnoty pro výchozí umístění. (Spuštěním nastavit jiné výchozí umístění `az configure --defaults location=<location>`.)

Například následující příkaz zobrazí seznam všech SKU Debian 8 v umístění západní Evropa:

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

## <a name="navigate-the-images"></a>Procházením imagí 
Dalším způsobem, jak najít image v umístění je spustit [az virtuálního počítače image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers), [az virtuální počítač image list-offers](/cli/azure/vm/image), a [az image list-skladové položky virtuálních počítačů](/cli/azure/vm/image#az_vm_image_list_skus) příkazy v pořadí. Tyto hodnoty můžete určit pomocí následujících příkazů:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Pak pro vybraná skladová položka, můžete vybrat verze nasazení.

Například následující příkaz zobrazí seznam vydavatelů imagí v umístění USA – západ:

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
Tyto informace slouží k hledání nabídek od konkrétního vydavatele. Třeba *Canonical* najít vydavatele v umístění USA – západ nabízí spuštěním `azure vm image list-offers`. Předejte umístění a vydavatele jako v následujícím příkladu:

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
Uvidíte, že v oblasti USA – západ Canonical publikuje *UbuntuServer* nabídky v Azure. Ale co skladové jednotky (SKU)? K získání těchto hodnot, spusťte `azure vm image list-skus` a nastavit umístění, vydavatele a nabídky, který jste zjistili:

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
westus      17.10
westus      17.10-DAILY
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10-DAILY
```

Nakonec použijte `az vm image list` příkaz k vyhledání konkrétní verze SKU, například *16.04 LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Částečný výstup:

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
...
```

Nyní můžete přesně tu image, kterou chcete použít provedením poznamenejte si hodnotu URN. Předat tuto hodnotu `--image` při vytváření virtuálního počítače se [vytvořit az vm](/cli/azure/vm#az_vm_create) příkaz. Mějte na paměti, že můžete volitelně nahradit číslo verze v URN "poslední zálohy". Tato verze je vždy nejnovější verzi image. 

Pokud provádíte nasazení virtuálního počítače pomocí šablony Resource Manageru, můžete nastavit parametry image jednotlivě v `imageReference` vlastnosti. Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupní obrazu, spusťte [az vm image show](/cli/azure/image#az_image_show) příkazu. Pokud `plan` vlastnost ve výstupu není `null`, bitová kopie je podmínky musíte přijmout před programové nasazení.

Image Canonical Ubuntu Server 16.04 LTS například nemá další podmínky, protože `plan` informace `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:16.04-LTS:latest
```

Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201809120",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Podobně jako příkazu pro RabbitMQ certifikovaný Bitnami obrázek ukazuje následující `plan` vlastnosti: `name`, `product`, a `publisher`. (Některé obrázky také mít `promotion code` vlastnosti.) Pokud chcete nasadit tuto bitovou kopii, naleznete v následujících částech k přijetí podmínek a povolit programové nasazení.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Výstup:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1807171506",
  "location": "westus",
  "name": "3.7.1809211005",
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

Chcete-li zobrazit a přijměte licenční podmínky, použijte [az vm image přijmout – podmínky](/cli/azure/vm/image?#az_vm_image_accept_terms) příkazu. Pokud souhlasíte s podmínkami, povolit programové nasazení v rámci vašeho předplatného. Stačí přijmout podmínky jednou za předplatné pro bitovou kopii. Příklad:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Výstup obsahuje `licenseTextLink` na licenční podmínky a znamená, že hodnotu `accepted` je `true`:

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
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů koupit plán

Po přijetí podmínek pro bitovou kopii, můžete nasadit virtuální počítač v rámci předplatného. K nasazení image s použitím `az vm create` příkaz, aby parametry plánu nákupů kromě URN bitové kopie. Chcete-li například nasazení virtuálního počítače s certifikací RabbitMQ Bitnami Image:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```

## <a name="next-steps"></a>Další postup
Pokud chcete rychle vytvořit virtuální počítač pomocí image informace, přečtěte si téma [vytvořit a spravovat virtuální počítače s Linuxem pomocí Azure CLI](tutorial-manage-vm.md).
