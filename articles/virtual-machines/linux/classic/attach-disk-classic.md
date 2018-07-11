---
title: Připojení disku k virtuálnímu počítači s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak připojit datový disk k virtuálnímu počítači s Linuxem pomocí modelu nasazení Classic a disk inicializovat, aby byl připraven k použití
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: b5bb3a9353f83cb569988a068f3ca02da85f739c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929147"
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Postup připojení datového disku virtuálního počítače s Linuxem
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. V tématu Jak [připojení datového disku pomocí modelu nasazení Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Můžete připojit prázdné disky a disky, které obsahují data pro virtuální počítače Azure. Oba typy disků jsou soubory .vhd, které se nacházejí v účtu služby Azure storage. Stejně jako u přidání žádný disk do počítače s Linuxem po připojení disku potřebujete inicializovat a naformátovat ho tak, aby byl připravený k použití. Tento článek podrobně popisuje připojení prázdné disky a disky už obsahující data o vašich virtuálních počítačů, jakož i jak potom inicializovat a naformátovat nový disk.

> [!NOTE]
> Je osvědčeným postupem je použití jednoho nebo více samostatných disků k ukládání dat virtuálního počítače. Když vytváříte virtuální počítač Azure, má disk s operačním systémem a dočasný disk. **Nepoužívejte dočasný disk k uložení trvalá data.** Jak již název napovídá, obsahuje jenom dočasné úložiště. Vzhledem k tomu, že není uložena ve službě Azure storage nabízí žádné zálohování nebo zálohování.
> Dočasný disk je obvykle spravuje pomocí agenta Azure Linux a automaticky připojit k **/mnt/prostředků** (nebo **/mnt** imagemi Ubuntu). Na druhé straně datový disk může být názvem jádro Linuxu podobným `/dev/sdc`, a je potřeba rozdělit, formátování a připojit tento prostředek. Zobrazit [uživatelská příručka agenta Azure Linux] [ Agent] podrobnosti.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializovat nový datový disk v Linuxu
1. Připojte přes SSH k virtuálnímu počítači. Další informace najdete v tématu [jak se přihlásit k virtuálnímu počítači s Linuxem][Logon].
2. Dál musíte najít identifikátor zařízení pro datový disk k inicializaci. Existují dva způsoby, jak to udělat:
   
    (a) Grep pro zařízení SCSI v protokolech, jako je například následující příkaz:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Pro distribuce poslední Ubuntu, budete muset použít `sudo grep SCSI /var/log/syslog` protože protokolování `/var/log/messages` můžou být ve výchozím nastavení zakázané.
   
    Můžete najít identifikátor poslední datový disk, který byl přidán do zpráv, které jsou zobrazeny.
   
    ![Získat disk zprávy](./media/attach-disk/scsidisklog.png)
   
    NEBO
   
    b), použijte `lsscsi` příkaz zjistit id zařízení. `lsscsi` můžete nainstalovat pomocí příkazu `yum install lsscsi` (v systému Red Hat na základě distribuce) nebo `apt-get install lsscsi` (distribucích založených na Debian). Můžete najít disk hledáte podle jeho *logickou jednotku* nebo **logickou jednotkou**. Například *logickou jednotku* pro jste připojili disky můžete snadno podívat z `azure vm disk list <virtual-machine>` jako:

    ```azurecli
    azure vm disk list myVM
    ```

    Výstup je podobný tomuto:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Porovnání těchto dat s výstupem `lsscsi` pro stejný ukázkový virtuální počítač:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Poslední číslo v řazené kolekci členů na každém řádku je *logickou jednotku*. Zobrazit `man lsscsi` Další informace.
3. Do příkazového řádku zadejte následující příkaz k vytvoření vašeho zařízení:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Po zobrazení výzvy zadejte **n** k vytvoření oddílu.

    ![Vytvoření zařízení](./media/attach-disk/fdisknewpartition.png)

5. Po zobrazení výzvy zadejte **p** změnit primární oddíl na oddíl. Typ **1** do prvního oddílu, a pak zadejte zadejte přijměte výchozí hodnotu pro válce. U některých systémů může zobrazit výchozí hodnoty, první a poslední sektory, namísto válce. Můžete tak, aby přijímal tyto výchozí hodnoty.

    ![Vytvoření oddílů](./media/attach-disk/fdisknewpartdetails.png)


6. Typ **p** zobrazíte podrobné informace o disku, který je právě rozdělit na oddíly.

    ![Informace o seznamu disku](./media/attach-disk/fdiskpartitiondetails.png)


7. Typ **w** zapsat nastavení disku.

    ![Zapsat změny disku](./media/attach-disk/fdiskwritedisk.png)

8. Nyní můžete vytvořit systém souborů na nový oddíl. Připojí číslo oddílu k ID zařízení (v následujícím příkladu `/dev/sdc1`). Následující příklad vytvoří oddíl ext4 na /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Vytvořit systém souborů](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 systémy se podporují jenom přístup jen pro čtení pro ext4 souborové systémy. Pro tyto systémy se doporučuje formátu ext3 spíše než ext4 nový systém souborů.

9. Vytvořit adresář pro připojení nového systému souborů, následujícím způsobem:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Nakonec můžete připojit jednotku, následujícím způsobem:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Datový disk je teď připravený k použití jako **/datadrive**.
   
    ![Vytvořit adresář a připojte disk](./media/attach-disk/mkdirandmount.png)

11. Přidejte novou jednotku /etc/fstab:
   
    K zajištění, že že jednotka znovu připojí automaticky po restartování je nutné přidat do souboru/etc/fstab. Kromě toho je důrazně doporučujeme, aby UUID (univerzálně jedinečný identifikátor) se používá v /etc/fstab k odkazování na jednotce, nikoli jen název zařízení (tj. /dev/sdc1). Pomocí identifikátoru UUID předchází nesprávné disku, připojení na dané místo, pokud operační systém zjistí chyba disku během spouštění a všechny zbývající datové disky, pak se přiřadí tyto identifikátory zařízení. Chcete-li najít identifikátor UUID nové jednotky, můžete použít **blkid** nástroje:
   
    ```bash
    sudo -i blkid
    ```
   
    Výstup vypadá podobně jako v následujícím příkladu:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nesprávně úpravy **/etc/fstab** souboru by mohlo způsobit systém nelze spustit. Pokud nejste jisti, najdete v dokumentaci vaší distribuce pro informace o tom, jak správně upravit tento soubor. Doporučujeme také, že je záloha souboru /etc/fstab vytvořená před úpravou.

    Dále otevřete **/etc/fstab** souboru v textovém editoru:

    ```bash
    sudo vi /etc/fstab
    ```

    V tomto příkladu používáme pro novou hodnotu UUID **/dev/sdc1** zařízení, který byl vytvořen v předchozích krocích a přípojnému bodu **/datadrive**. Přidejte následující řádek na konec objektu **/etc/fstab** souboru:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Nebo na systémy založené na systému SuSE Linux budete muset použít trochu jiný formát:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > `nofail` Možnost zajišťuje, že virtuální počítač spustí i v případě systému souborů je poškozený nebo na disku v době spuštění neexistuje. Bez této možnosti může dojít chování, jak je popsáno v [nelze SSH k virtuálnímu počítači s Linuxem kvůli chybám FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Teď můžete otestovat, že systém souborů připojený správně odpojení a pak opakovanému připojení systému souborů, například pomocí příkladu přípojný bod `/datadrive` vytvořený v předchozích krocích:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Pokud `mount` příkazu dojde k chybě, / etc/fstab v souboru správnou syntaxi. Pokud se vytvoří další datové disky a oddíly, zadejte je do/etc/fstab také samostatně.

    Umožnit zápis disk pomocí tohoto příkazu:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Následně odebrání datového disku bez úprav fstab může způsobit selhání spuštění virtuálního počítače. Pokud je to společného výskytu, většině distribucí, zadejte buď `nofail` a/nebo `nobootwait` fstab možnosti systému ke spuštění i v případě, že disk se nepodařilo připojit na spuštění. Další informace o těchto parametrech naleznete v dokumentaci vaší distribuce.

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora uvolnění dočasné paměti/UNMAP pro Linux v Azure
Některé Linuxových jádrech podporovat operace TRIM/UNMAP zahodíte nepoužívané bloky na disku. Tyto operace jsou především užitečné ve standardním úložišti informovat Azure, které odstraní stránek už nejsou platné a mohou být zahozeny. Zahazuje se stránky můžete snížení nákladů, pokud vytvoříte velkých souborů a potom je odstraňte.

Existují dva způsoby, jak povolit TRIM podpory v virtuálního počítače s Linuxem. Obvyklým způsobem vaše distribuce najdete doporučený postup:

* Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` příkaz ručně z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spuštění:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Další kroky
Další informace o používání virtuálního počítače s Linuxem v následujících článcích:

* [Jak se přihlásit k virtuálnímu počítači s Linuxem][Logon]
* [Jak odpojit disk od virtuálního počítače s Linuxem](detach-disk-classic.md)
* [Model nasazení Classic pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Konfigurace RAID na virtuální počítač s Linuxem v Azure](../configure-raid.md)
* [Konfigurace LVM na virtuální počítač s Linuxem v Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
