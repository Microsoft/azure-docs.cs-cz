---
title: Připojit disk do virtuálního počítače s Linuxem v Azure | Microsoft Docs
description: Zjistěte, jak připojit datový disk do virtuálního počítače s Linuxem pomocí modelu nasazení Classic a inicializujte disk tak, aby byl připravený k použití
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 07b7f16cc019f993df90b217abd0c7ad16db75e1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Tom, jak připojit datový Disk pro virtuální počítač s Linuxem
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. V tématu Jak [připojit datový disk pomocí modelu nasazení Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Můžete připojit prázdné disky a disky, které obsahují data pro virtuální počítače Azure. Oba typy disků jsou soubory VHD, které jsou umístěny v účtu úložiště Azure. Jako přidávání každý disk, na počítač s Linuxem, jakmile připojíte disk musíte inicializovat a naformátovat ho tak, aby byl připravený k použití. Tento článek údaje prázdný disků i disků již obsahující data do virtuálních počítačů a také jak pak inicializace a formátování nový disk se připojuje.

> [!NOTE]
> Je vhodné použít jeden nebo více samostatných disků k ukládání dat virtuálního počítače. Když vytvoříte virtuální počítač Azure, je disk operačního systému a dočasný disk. **Nepoužívejte dočasné disku k uložení dat trvalé.** Jak již název napovídá, obsahuje pouze dočasné úložiště. Vzhledem k tomu, že není uložena v úložišti Azure nabízí žádné redundance nebo zálohování.
> Je obvykle spravuje Azure Linux Agent a automaticky připojit k dočasným diskovým **/mnt nebo prostředků** (nebo **/mnt** Ubuntu Image). Na druhé straně datový disk může být pojmenován jádrem Linux něco podobného jako `/dev/sdc`, a je třeba k oddílu, formátování a připojte tento prostředek. Najdete v článku [Azure Linux Agent uživatelská příručka] [ Agent] podrobnosti.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializace nový datový disk v systému Linux
1. SSH k virtuálnímu počítači. Další informace najdete v tématu [přihlášení do virtuálního počítače se systémem Linux][Logon].
2. Dále je třeba najít identifikátor zařízení pro datový disk k chybě při inicializaci. Existují dva způsoby, jak to udělat:
   
    (a) Grep pro zařízení SCSI v protokolech, například následující příkaz:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Pro poslední Ubuntu distribuce, budete muset použít `sudo grep SCSI /var/log/syslog` protože protokolování tak, aby `/var/log/messages` může ve výchozím nastavení zakázané.
   
    Můžete najít identifikátor poslední datový disk, která byla přidána do zpráv, které se zobrazují.
   
    ![Získávání zpráv disku](./media/attach-disk/scsidisklog.png)
   
    NEBO
   
    b) použití `lsscsi` příkazu zjistit id zařízení. `lsscsi` může být instalován buď `yum install lsscsi` (na Red Hat na základě distribuce) nebo `apt-get install lsscsi` (na Debian na základě distribuce). Můžete najít na disku, kterou hledáte podle jeho *lun* nebo **číslo logické jednotky**. Například *lun* pro disky můžete z snadno pohledu `azure vm disk list <virtual-machine>` jako:

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
   
    Tato data se výstup porovnání `lsscsi` pro stejné ukázkové virtuálního počítače:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Je poslední číslo v řazené kolekci členů v každém řádku *lun*. V tématu `man lsscsi` Další informace.
3. Do příkazového řádku zadejte následující příkaz k vytvoření vašeho zařízení:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Po zobrazení výzvy zadejte **n** k vytvoření oddílu.

    ![Vytvoření zařízení](./media/attach-disk/fdisknewpartition.png)

5. Po zobrazení výzvy zadejte **p** změnit primární oddíl na oddíl. Typ **1** na první oddíl a pak zadejte zadejte přijměte výchozí hodnotu cylindr. U některých systémů může zobrazit výchozí hodnoty, první a poslední sektory, místo cylindr. Můžete tak, aby přijímal tyto výchozí hodnoty.

    ![Vytvořit oddíl](./media/attach-disk/fdisknewpartdetails.png)


6. Typ **p** a zobrazit podrobnosti o disk, který je rozdělena na oddíly.

    ![Informace o disku seznamu](./media/attach-disk/fdiskpartitiondetails.png)


7. Typ **w** se zapsat nastavení disku.

    ![Zápis disku změny](./media/attach-disk/fdiskwritedisk.png)

8. Nyní můžete vytvořit systém souborů na nový oddíl. Připojit číslo oddílu k ID zařízení (v následujícím příkladu `/dev/sdc1`). Následující příklad vytvoří oddíl ext4 na /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Vytvořit systém souborů](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > Systémy SuSE Linux Enterprise 11 pro systémy souborů ext4 podporují pouze oprávnění jen pro čtení. Pro tyto systémy doporučujeme formátovat jako ext3, nikoli ext4 nový systém souborů.

9. Aby byl adresář připojit nový systém souborů, následujícím způsobem:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Nakonec můžete připojit jednotku, následujícím způsobem:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Datový disk je teď připravený k použití jako **/datadrive**.
   
    ![Vytvoření adresáře a připojit disk](./media/attach-disk/mkdirandmount.png)

11. Přidejte nový disk /etc/fstab:
   
    K zajištění, že jednotka je znovu připojeny automaticky po restartování systému musí být přidané do souboru/etc/fstab. Kromě toho je důrazně doporučujeme, aby identifikátor UUID (univerzálně jedinečný identifikátor) se používá v /etc/fstab k odkazování na jednotku, nikoli jen název zařízení (tj. /dev/sdc1). Pomocí identifikátoru UUID zabraňuje nesprávnou disku se připojí na určitém místě, když operačního systému zjistí chybu disk během spuštění a všechny zbývající datové disky poté přiřazeny ID těchto zařízení. Chcete-li najít identifikátor UUID nový disk, můžete použít **blkid** nástroj:
   
    ```bash
    sudo -i blkid
    ```
   
    Výstup bude vypadat podobně jako v následujícím příkladu:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nesprávně úpravy **/etc/fstab** soubor může mít za následek nelze spustit systém. Pokud jistí, naleznete distribuční dokumentaci informace o tom, jak správně upravit tento soubor. Dále je doporučeno, jestli je vytvořená záloha souboru /etc/fstab před úpravou.

    Dále otevřete **/etc/fstab** soubor v textovém editoru:

    ```bash
    sudo vi /etc/fstab
    ```

    V tomto příkladu používáme UUID hodnotu pro nové **/dev/sdc1** zařízení, který byl vytvořen v předchozích krocích a přípojný bod **/datadrive**. Přidejte následující řádek na konec **/etc/fstab** souboru:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Nebo na systémy založené na systému SuSE Linux budete muset použít mírně odlišný formát:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > `nofail` Možnost zajistí, že virtuální počítač spustí i v případě, že systém souborů je poškozený nebo disk neexistuje při spuštění. Bez této možnosti se můžete setkat chování jak je popsáno v [nelze SSH pro virtuální počítač s Linuxem z důvodu chyb FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Nyní můžete otestovat, že je připojený systém souborů správně odpojování a pak opakovanému připojení systému souborů, tj. pomocí příkladu přípojný bod `/datadrive` vytvořené v dřívějších krocích:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Pokud `mount` příkaz vytvořil chybu, zkontrolujte soubor fstab/etc/pro správnou syntaxi. Pokud budou vytvořeny další datové jednotky nebo oddíly, zadejte je do/etc/fstab také samostatně.

    Zkontrolujte jednotku s možností zápisu pomocí tohoto příkazu:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Následně odebrat datový disk bez úprav fstab může způsobit selhání spuštění virtuálního počítače. Pokud je to běžné v situaci, většina distribuce zadejte buď `nofail` nebo `nobootwait` dobou spuštění fstab možnosti, které umožňují spustit i v případě, že na disku se nepodaří připojit v systému. Další informace o těchto parametrů naleznete v dokumentaci vaší distribuce.

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora uvolnění dočasné paměti nebo UNMAP pro Linux v Azure
Některé Linux jádra podporovat operace TRIM/UNMAP vyřadí nepoužívané bloky na disku. Tyto operace jsou užitečné hlavně v standardní úložiště k informování Azure, které odstraněné stránky již nejsou platné a může být vymazány. Zahození stránky můžete uložit náklady, pokud chcete vytvořit velkých souborů a pak odstraňte je.

Existují dva způsoby, jak povolit TRIM podporují ve virtuálním počítačům s Linuxem. Obvyklým způsobem podívejte se distribuční o doporučený postup:

* Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` ručně příkaz z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spouštět:
  
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
Další informace o používání virtuálním počítačům s Linuxem v těchto článcích:

* [Jak se přihlásit do virtuálního počítače se systémem Linux][Logon]
* [Jak se odpojit disk z virtuálního počítače systému Linux](detach-disk-classic.md)
* [Pomocí rozhraní příkazového řádku Azure s modelem nasazení Classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Konfigurace RAID na virtuální počítač s Linuxem v Azure](../configure-raid.md)
* [Konfigurace LVM na virtuální počítač s Linuxem v Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
