---
title: Vyhledání a použití informací o plánu nákupu na webu Marketplace pomocí rozhraní příkazového řádku
description: Naučte se používat rozhraní příkazového řádku Azure CLI k vyhledání parametrů plánu image urny a nákupu, jako je Vydavatel, nabídka, SKU a verze, pro image virtuálních počítačů Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484196"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Vyhledání informací o Azure Marketplace imagí pomocí Azure CLI

Toto téma popisuje, jak pomocí rozhraní příkazového řádku Azure vyhledat image virtuálních počítačů v Azure Marketplace. Tyto informace slouží k určení image Marketplace při programovém vytvoření virtuálního počítače pomocí rozhraní příkazového řádku, Správce prostředků šablon nebo jiných nástrojů.

K dispozici je také možnost procházení dostupných imagí a nabídek pomocí [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Image na Marketplace v Azure má následující atributy:

* **Vydavatel**: organizace, která bitovou kopii vytvořila. Příklady: Canonical, MicrosoftWindowsServer
* **Nabídka**: název skupiny souvisejících imagí vytvořených vydavatelem. Příklady: UbuntuServer, WindowsServer
* **SKU**: instance nabídky, jako je například hlavní verze distribuce. Příklady: 18,04-LTS, 2019-Datacenter
* **Version (verze**): číslo verze SKU image. 

Tyto hodnoty je možné předat individuálně nebo jako *název URN* obrázku a kombinovat hodnoty oddělené dvojtečkou (:). Příklad: *Vydavatel*:*Nabídka*:*SKU*:*verze*. Číslo verze v názvu URN můžete nahradit, pokud `latest` chcete použít nejnovější verzi image. 

Pokud Vydavatel obrázku poskytuje další licenční a nákupní podmínky, musíte je přijmout, než budete moct image použít.  Další informace najdete v tématu [o kontrole informací o plánu nákupu](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Výpis oblíbených imagí

Spuštěním příkazu [AZ VM Image list](/cli/azure/vm/image) bez `--all` možnosti zobrazíte seznam oblíbených imagí virtuálních počítačů v Azure Marketplace. Spusťte například následující příkaz, který zobrazí seznam oblíbených imagí v mezipaměti ve formátu tabulky:

```azurecli
az vm image list --output table
```

Výstup obsahuje identifikátor URN bitové kopie. Můžete také použít *UrnAlias* , což je zkrácená verze vytvořená pro oblíbené obrázky, jako je *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Vyhledání konkrétních imagí

Pokud chcete na webu Marketplace najít konkrétní image virtuálního počítače, použijte `az vm image list` příkaz s `--all` možností. Dokončení této verze příkazu trvá nějakou dobu a může vracet výstup s délkou, takže seznam byste obvykle vyfiltroval podle `--publisher` nebo jiného parametru. 

Například následující příkaz zobrazí všechny nabídky Debian (Nezapomeňte, že bez `--all` přepínače hledá pouze místní mezipaměť běžných imagí):

```azurecli
az vm image list --offer Debian --all --output table 
```

Částečný výstup: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Podívejte se na všechny dostupné obrázky
 
Dalším způsobem, jak najít obrázek v umístění, je spuštění příkazu [AZ VM Image list-Publishers](/cli/azure/vm/image), [AZ VM Image list-nabídky](/cli/azure/vm/image)a [AZ VM Image list-SKU](/cli/azure/vm/image) Commands v sekvenci. Pomocí těchto příkazů určíte tyto hodnoty:

1. Vypíše vydavatele imagí pro umístění. V tomto příkladu se díváte na oblast *západní USA* .
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Pro daného vydavatele vypsat jeho nabídky. V tomto příkladu přidáme *kanonický* jako vydavatel.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Pro danou nabídku vypsat její skladovou jednotku (SKU). V tomto příkladu přidáme *UbuntuServer* jako nabídku.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Pro daného vydavatele, nabídku a SKU, zobrazte všechny verze image. V tomto příkladu přidáme jako SKU *18,04-LTS* .

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

`--image`Pokud vytvoříte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) , předejte tuto hodnotu sloupce urn s parametrem. Můžete také nahradit číslo verze v názvu URN řetězcem "poslední", abyste mohli jednoduše použít nejnovější verzi image. 

Pokud nasadíte virtuální počítač s Správce prostředků šablonou, nastavte parametry obrázku jednotlivě ve `imageReference` vlastnostech. Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Podívejte se na informace o plánu nákupu.

Některé image virtuálních počítačů v Azure Marketplace mají další licenční a nákupní podmínky, které musíte přijmout, než je můžete nasadit programově.  

Pokud chcete nasadit virtuální počítač z takové image, musíte přijmout podmínky obrázku při jeho prvním použití, a to jednou za předplatné. Budete taky muset zadat parametry *plánu nákupu* , aby se virtuální počítač nasadil z této image.

Chcete-li zobrazit informace o plánu nákupu obrázku, spusťte příkaz [AZ VM Image show](/cli/azure/image) s názvem URN obrázku. Pokud `plan` vlastnost ve výstupu není `null` , obrázek obsahuje podmínky, které je třeba přijmout před programovým nasazením.

Například obrázek kanonického Ubuntu serveru 18,04 LTS nemá další výrazy, protože tyto `plan` informace jsou `null` :

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Výstup:

```output
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

Spuštění podobného příkazu pro Image RabbitMQ Certified by Bitnami ukazuje následující `plan` vlastnosti: `name` , `product` , a `publisher` . (Některé obrázky také obsahují `promotion code` vlastnost.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Výstup:

```output
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

Chcete-li nasadit tuto bitovou kopii, je nutné podmínky přijmout a zadat parametry plánu nákupu při nasazení virtuálního počítače pomocí této image.

## <a name="accept-the-terms"></a>Přijetí podmínek použití

Pokud si chcete zobrazit a přijmout licenční podmínky, použijte příkaz [AZ VM Image Accept-terms](/cli/azure/vm/image/terms) . Když souhlasíte s podmínkami, povolíte v předplatném programové nasazení. Pro bitovou kopii musíte pro Image přijmout jenom jednou za odběr. Například:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Výstup obsahuje `licenseTextLink` licenční smlouvy a označuje, že hodnota `accepted` je `true` :

```output
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

Pokud chcete podmínky přijmout, zadejte:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Nasazení nového virtuálního počítače pomocí parametrů image

S informacemi o imagi ji můžete nasadit pomocí `az vm create` příkazu. 

Pokud chcete nasadit image, která nemá informace o plánu, třeba nejnovější image Ubuntu serveru 18,04 z kanonického tvaru, předejte název URN pro `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Pro obrázek s parametry plánu nákupu, jako je RabbitMQ certifikovaný pomocí Bitnami image, předáte název URN pro `--image` a také zadáte parametry plánu nákupu:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Pokud se zobrazí zpráva o přijetí podmínek v imagi, přečtěte si část věnované [přijetí podmínek](#accept-the-terms). Ujistěte se, že výstup `az vm image accept-terms` vrátí hodnotu `"accepted": true,` , která ukazuje, že jste přijali podmínek obrázku.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Použití existujícího virtuálního pevného disku s informacemi o plánu nákupu

Pokud máte existující virtuální pevný disk z virtuálního počítače, který byl vytvořen pomocí placené Azure Marketplace image, může být nutné při vytváření nového virtuálního počítače z tohoto virtuálního pevného disku poskytnout informace o plánu nákupu. 

Pokud máte i nadále původní virtuální počítač nebo jiný virtuální počítač vytvořený pomocí stejné image na webu Marketplace, můžete z něj získat název plánu, vydavatele a informace o produktu pomocí [příkaz AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view). Tento příklad načte virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* a pak zobrazí informace o plánu nákupu.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Pokud jste neobdrželi informace o plánu před odstraněním původního virtuálního počítače, můžete [požádat o podporu](https://ms.portal.azure.com/#create/Microsoft.Support). Budou potřebovat název virtuálního počítače, ID předplatného a časové razítko operace odstranění.

Jakmile budete mít informace o plánu, můžete vytvořit nový virtuální počítač pomocí `--attach-os-disk` parametru a zadat virtuální pevný disk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Další kroky
Pokud chcete rychle vytvořit virtuální počítač pomocí informací o imagi, přečtěte si téma [Vytvoření a správa virtuálních počítačů se systémem Linux pomocí Azure CLI](tutorial-manage-vm.md).
