---
title: Kurz – Vytvoření a správa škálovací sady virtuálních počítačů Azure | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI vytvořit škálovací sadu virtuálních počítačů a provádět několik běžných úloh správy, jako je spuštění a zastavení instance nebo změna kapacity škálovací sady.
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 263a2ddd1cf42348678488a02ed0b97a7ed1304c
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466133"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kurz: Vytvoření a správa škálovací sady virtuálních počítačů pomocí Azure CLI
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření škálovací sady virtuálních počítačů a připojení k ní
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí instancí virtuálních počítačů
> * Ruční škálování škálovací sady
> * Provádění běžných úloh správy škálovací sady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením škálovací sady virtuálních počítačů. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Název skupiny prostředků zadáte při vytváření nebo úpravě škálovací sady v rámci tohoto kurzu.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Škálovací sadu virtuálních počítačů vytvoříte pomocí příkazu [az vmss create](/cli/azure/vmss#az_vmss_create). Následující příklad vytvoří škálovací sadu *myScaleSet* a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut. Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Zobrazení instancí virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) následujícím způsobem:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Následující příklad výstupu ukazuje dvě instance virtuálních počítačů ve škálovací sadě:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


První sloupec ve výstupu se ukazuje *InstanceId* (ID instance). Pokud chcete zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte k příkazu [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) parametr `--instance-id`. Následující příklad zobrazí informace o instanci virtuálního počítače *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Výpis informací o připojení
K nástroji pro vyrovnávání zatížení, který směruje provoz do jednotlivých instancí virtuálních počítačů, se přiřadí veřejná IP adresa. Ve výchozím nastavení se k nástroji pro vyrovnávání zatížení Azure, který směruje provoz vzdáleného připojení do jednotlivých virtuálních počítačů na daném portu, přidají pravidla překladu adres (NAT). Pokud se chcete připojit k instancím virtuálních počítačů ve škálovací sadě, vytvořte vzdálené připojení k přiřazené veřejné IP adrese a číslu portu.

Pokud chcete vypsat adresu a porty pro připojení k instancím virtuálních počítačů ve škálovací, použijte příkaz [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Následující příklad výstupu ukazuje název instance, veřejnou IP adresu nástroje pro vyrovnávání zatížení a číslo portu, na které pravidla překladu adres směrují provoz:

```bash
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Připojte se přes SSH k první instanci virtuálního počítače. Pomocí parametru `-p` zadejte veřejnou IP adresu a číslo portu uvedené ve výstupu předchozího příkazu:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Po přihlášení k instanci virtuálního počítače můžete podle potřeby provést ruční změny konfigurace. Prozatím relaci SSH normálně ukončete:

```bash
exit
```


## <a name="understand-vm-instance-images"></a>Vysvětlení imagí instancí virtuálních počítačů
Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali parametr `--image` s hodnotou *UbuntuLTS*. Azure Marketplace obsahuje celou řadu imagí, které je možné využít k vytváření instancí virtuálních počítačů. Pokud chcete zobrazit seznam nejčastěji používaných imagí, použijte příkaz [az vm image list](/cli/azure/vm/image#az_vm_image_list).

```azurecli-interactive
az vm image list --output table
```

Následující příklad výstupu ukazuje nejběžnější image virtuálních počítačů v Azure. K zadání některé z těchto běžných imagí při vytváření škálovací sady je možné použít *UrnAlias*.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Pokud chcete zobrazit úplný seznam, přidejte argument `--all`. Seznam imagí je také možné filtrovat podle parametrů `--publisher` nebo `–-offer`. V následujícím příkladu se v seznamu vyfiltrují všechny image, jejichž nabídka (parametr offer) je *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

Následující zkrácený výstup ukazuje několik dostupných imagí CentOS 7.3:

```azurecli-interactive 
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Pokud chcete nasadit škálovací sadu používající konkrétní image, použijte hodnotu ve sloupci *Urn*. Při zadávání image můžete její číslo verze nahradit klíčovým slovem *latest*. To zajistí, aby se vybrala nejnovější verze dané distribuce. V následujícím příkladu pomocí argumentu `--image` určíme nejnovější verzi image CentOS 7.3.

Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Vysvětlení velikostí instancí virtuálních počítačů
Velikost instance virtuálního počítače neboli *skladová položka* určuje množství výpočetních prostředků, jako jsou procesor, grafický procesor a paměť, které jsou pro instanci virtuálního počítače k dispozici. Velikost instancí virtuálních počítačů ve škálovací sadě je třeba správně určit podle očekávané pracovní zátěže.

### <a name="vm-instance-sizes"></a>Velikosti instancí virtuálních počítačů
V následující tabulce jsou běžné velikosti virtuálních počítačů rozdělené podle způsobů použití.

| Typ                     | Běžné velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](../virtual-machines/linux/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/linux/sizes-compute.md)   | Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované z hlediska paměti](../virtual-machines/linux/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../virtual-machines/linux/sizes-storage.md)      | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](../virtual-machines/linux/sizes-gpu.md)          | NV, NC            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](../virtual-machines/linux/sizes-hpc.md) | H, A8-11          | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Vyhledání dostupných velikostí instancí virtuálních počítačů
Pokud chcete zobrazit seznam velikostí instancí virtuálních počítačů dostupných v konkrétní oblasti, použijte příkaz [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

Výstup se podobá následujícímu zhuštěnému příkladu, který ukazuje prostředky přiřazené k jednotlivým velikostem virtuálních počítačů:

```azurecli-interactive
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Vytvoření škálovací sady s konkrétní velikostí instancí virtuálních počítačů
Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali výchozí skladovou položku virtuálního počítače *Standard_D1_v2*. Na základě výstupu příkazu [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes) můžete zadat jinou velikost instancí virtuálních počítačů. Následující příklad vytvoří škálovací sadu s použitím parametru `--vm-sku`, který určí velikost instancí virtuálních počítačů *Standard_F1*. Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Při vytváření škálovací sady na začátku kurzu se ve výchozím nastavení nasadily dvě instance virtuálních počítačů. Počet instancí vytvořených se škálovací sadou můžete změnit zadáním parametru `--instance-count` u příkazu [az vmss create](/cli/azure/vmss#az_vmss_create). Pokud chcete zvýšit nebo snížit počet instancí virtuálních počítačů v existující škálovací sadě, můžete ručně změnit kapacitu. Škálovací sada vytvoří nebo odebere požadovaný počet instancí virtuálních počítačů a pak nakonfiguruje nástroj pro vyrovnávání zatížení pro distribuci provozu.

Pokud chcete ručně zvýšit nebo snížit počet instancí virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss scale](/cli/azure/vmss#az_vmss_scale). Následující příklad nastaví počet instancí virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud chcete zobrazit počet instancí, které teď máte ve škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss#az_vmss_show) s dotazem na *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Běžné úlohy správy
Teď můžete vytvořit škálovací sadu, vypsat informace o připojení a připojit se k instancím virtuálních počítačů. Zjistili jste, jak pro instance virtuálních počítačů použít jinou image operačního systému, vybrat jinou velikost virtuálních počítačů nebo ručně škálovat počet instancí. V rámci každodenní správy můžete potřebovat zastavit, spustit nebo restartovat instance virtuálních počítačů ve své škálovací sadě.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Zastavení a uvolnění instancí virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jednu nebo několik instancí virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss stop](/cli/azure/vmss#az_vmss_stop). Pomocí parametru `--instance-ids` můžete zadat jednu nebo několik instancí virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny instance virtuálních počítačů ve škálovací sadě. Následující příklad zastaví instanci *1*:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

Zastavené instance virtuálních počítačů zůstanou přidělené a nadále se u nich účtují poplatky za výpočty. Pokud místo toho chcete instance virtuálních počítačů uvolnit, aby se vám účtovaly pouze poplatky za úložiště, použijte příkaz [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Následující příklad zastaví a uvolní instanci *1*:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Spuštění instancí virtuálních počítačů ve škálovací sadě
Pokud chcete spustit jednu nebo několik instancí virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss start](/cli/azure/vmss#az_vmss_start). Pomocí parametru `--instance-ids` můžete zadat jednu nebo několik instancí virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny instance virtuálních počítačů ve škálovací sadě. Následující příklad spustí instanci *1*:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Restartování instancí virtuálních počítačů ve škálovací sadě
Pokud chcete restartovat jednu nebo několik instancí virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss restart](/cli/azure/vmss#az_vm_restart). Pomocí parametru `--instance-ids` můžete zadat jednu nebo několik instancí virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny instance virtuálních počítačů ve škálovací sadě. Následující příklad restartuje instanci *1*:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Když odstraníte skupinu prostředků, odstraní se také všechny prostředky v ní obsažené, například instance virtuálních počítačů, virtuální síť a disky. Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak provádět několik běžných úloh vytvoření a správy škálovací sady pomocí Azure CLI:

> [!div class="checklist"]
> * Vytvoření škálovací sady virtuálních počítačů a připojení k ní
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Ruční škálování škálovací sady
> * Provádění běžných úloh správy škálovací sady

V dalším kurzu se dozvíte něco o discích škálovacích sad.

> [!div class="nextstepaction"]
> [Použití datových disků se škálovacími sadami](tutorial-use-disks-cli.md)
