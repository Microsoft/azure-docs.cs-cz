---
title: Kurz – Vytváření a správa virtuálních počítačů s Linuxem pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI 2.0 vytvářet a spravovat virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e8be3af81ce74b033b2a15ceaf857540c1d9a6e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192199"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli-20"></a>Kurz: Vytváření a správa virtuálních počítačů s Linuxem pomocí Azure CLI 2.0

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se zaměřuje na základní kroky při nasazování virtuálních počítačů, jako jsou výběr velikosti virtuálního počítače, výběr image virtuálního počítače a nasazení virtuálního počítače. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření a připojení virtuálního počítače
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Změna velikosti virtuálního počítače
> * Zobrazení a pochopení stavu virtuálního počítače

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroupVM* v oblasti *eastus*. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Skupinu prostředků je třeba zadat při vytváření nebo úpravách virtuálního počítače, což uvidíte dále v tomto kurzu.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). 

Při vytváření virtuálního počítače máte k dispozici několik možností, jako jsou image operačního systému, velikost disku a přihlašovací údaje pro správu. Následující příklad vytvoří virtuální počítač s názvem *myVM*, na kterém poběží Ubuntu Server. Ve virtuálním počítači se vytvoří uživatelský účet s názvem *azureuser* a vygenerují se klíče SSH, pokud neexistují ve výchozím umístění klíčů (*~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače může několik minut trvat. Po vytvoření virtuálního počítače o něm Azure CLI vypíše informace. Poznamenejte si hodnotu `publicIpAddress`. Pomocí této adresy je možné získat přístup k virtuálnímu počítači. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Připojení k virtuálnímu počítači

Nyní se můžete k virtuálnímu počítači připojit pomocí protokolu SSH ve službě Azure Cloud Shell nebo z místního počítače. Ukázkovou IP adresu nahraďte hodnotou `publicIpAddress`, kterou jste si poznamenali v předchozím kroku.

```bash
ssh azureuser@52.174.34.95
```

Po přihlášení k virtuálnímu počítači můžete nainstalovat a nakonfigurovat aplikace. Po dokončení zavřete relaci SSH obvyklým způsobem:

```bash
exit
```

## <a name="understand-vm-images"></a>Vysvětlení imagí virtuálních počítačů

Azure Marketplace obsahuje celou řadu imagí, které jde využít k vytváření virtuálních počítačů. V předchozích krocích jsme vytvořili virtuální počítač pomocí image Ubuntu. V tomto kroku pomocí Azure CLI vyhledáme na webu Marketplace image CentOS, kterou pak použijeme k nasazení druhého virtuálního počítače. 

Pokud chcete zobrazit seznam nejčastěji používaných imagí, použijte příkaz [az vm image list](/cli/azure/vm/image#az_vm_image_list).

```azurecli-interactive 
az vm image list --output table
```

Příkaz vrátí nejoblíbenější image virtuálních počítačů v Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Úplný seznam můžete zobrazit tak, že přidáte argument `--all`. Seznam imagí je také možné filtrovat podle parametrů `--publisher` nebo `–-offer`. V tomto příkladu se v seznamu vyfiltrují všechny image, jejichž nabídka (parametr offer) je *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Částečný výstup:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Pokud chcete nasadit virtuální počítač s použitím konkrétní image, poznamenejte si hodnotu ve sloupci *Urn*, která se skládá z vydavatele, nabídky, skladové položky a volitelně čísla verze pro [identifikaci](cli-ps-findimage.md#terminology) image. Při zadávání image můžete její číslo verze nahradit klíčovým slovem latest. To zajistí, aby se vybrala nejnovější verze dané distribuce. V tomto příkladu pomocí argumentu `--image` určíme nejnovější verzi image CentOS 6.5.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Vysvětlení velikostí virtuálních počítačů

Velikost virtuálního počítače určuje množství výpočetních prostředků, jako jsou procesor, grafický procesor a paměť, které jsou pro virtuální počítač k dispozici. Velikost virtuálních počítačů je třeba správně určit podle očekávané pracovní zátěže. Pokud se pracovní zátěž zvýší, je možné velikost existujícího virtuálního počítače změnit.

### <a name="vm-sizes"></a>Velikosti virtuálních počítačů

V následující tabulce jsou velikosti rozdělené podle způsobů použití.  

| Typ                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)   | Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)      | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](sizes-hpc.md) | H, A8-11          | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 


### <a name="find-available-vm-sizes"></a>Zjištění dostupných velikostí virtuálních počítačů

Pokud chcete zobrazit seznam velikostí virtuálních počítačů dostupných v konkrétní oblasti, použijte příkaz [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Částečný výstup:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Vytvoření virtuálního počítače s konkrétní velikostí

V předchozím příkladu vytvoření virtuálního počítače jsme nezadali velikost, takže se použila výchozí velikost. Velikost virtuálního počítače je možné při vytváření vybrat pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) a argumentu `--size`. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače

Po nasazení virtuálního počítače můžete jeho velikost změnit, čímž se zvýší nebo sníží přidělení prostředků. Aktuální velikost virtuálního počítače je možné zobrazit pomocí příkazu [az vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Před změnou velikosti virtuálního počítače zkontrolujte, jestli je požadovaná velikost dostupná v aktuálním clusteru Azure. Seznam velikostí můžete získat pomocí příkazu [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Pokud je požadovaná velikost dostupná, dá se velikost virtuálního počítače změnit i za provozu, ale během této operace se bude vyžadovat restartování. Ke změně velikosti použijte příkaz [az vm resize]( /cli/azure/vm#az_vm_resize).

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Pokud požadovaná velikost není v aktuálním clusteru, je třeba napřed zrušit přidělení virtuálního počítače, aby mohla změna velikosti proběhnout. K zastavení a zrušení přidělení virtuálního počítače použijte příkaz [az vm deallocate]( /cli/azure/vm#az_vm_deallocate). Upozorňujeme, že až se virtuální počítač zase zapne, můžou na něm být odebraná všechna data na dočasném disku. Změní se také jeho veřejná IP adresa, pokud nepoužíváte statickou IP adresu. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Po zrušení přidělení může proběhnout změna velikosti. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Po změně velikosti se může virtuální počítač zase spustit.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Stavy napájení virtuálního počítače

Virtuální počítač Azure může mít jeden z mnoha stavů napájení. Tento stav představuje aktuální stav virtuálního počítače z pohledu hypervisoru. 

### <a name="power-states"></a>Stavy napájení

| Stav napájení | Popis
|----|----|
| Spouštění | Označuje, že virtuální počítač se právě spouští. |
| Spuštěno | Označuje, že virtuální počítač běží (je spuštěný). |
| Zastavování | Označuje, že virtuální počítač se právě zastavuje. | 
| Zastaveno | Označuje, že virtuální počítač je zastavený. Poplatky za výpočetní výkon se účtují i za virtuální počítače v zastaveném stavu.  |
| Rušení přidělení | Označuje, že se právě ruší přidělení virtuálního počítače. |
| Přidělení zrušeno | Označuje, že virtuální počítač je odebraný z hypervisoru, ale stále je dostupný v rovině řízení. Za virtuální počítače ve stavu zrušeného přidělení se poplatky za výpočetní výkon neúčtují. |
| - | Označuje, že stav napájení virtuálního počítače není známý. |

### <a name="find-power-state"></a>Zjištění stavu napájení

Pokud chcete zjistit stav konkrétního virtuálního počítače, použijte příkaz [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). Nezapomeňte zadat platný název virtuálního počítače a skupiny prostředků. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Výstup:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Úlohy správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Možná také budete chtít vytvořit skripty pro automatizaci opakovaných nebo komplexních úloh. Pomocí Azure CLI se dá mnoho běžných úloh správy spustit z příkazového řádku nebo ve skriptech. 

### <a name="get-ip-address"></a>Zjištění IP adresy

Tento příkaz vrátí privátní a veřejnou IP adresu virtuálního počítače.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Zastavení virtuálního počítače

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Spuštění virtuálního počítače

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Odstranění skupiny prostředků

Odstraněním skupiny prostředků se také odstraní všechny prostředky v ní obsažené, například virtuální počítač, virtuální síť a disk. Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě virtuálních počítačů, jako jsou:

> [!div class="checklist"]
> * Vytvoření a připojení virtuálního počítače
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Změna velikosti virtuálního počítače
> * Zobrazení a pochopení stavu virtuálního počítače

V dalším kurzu se dozvíte něco o discích virtuálních počítačů.  

> [!div class="nextstepaction"]
> [Vytvoření a správa disků virtuálního počítače](./tutorial-manage-disks.md)
