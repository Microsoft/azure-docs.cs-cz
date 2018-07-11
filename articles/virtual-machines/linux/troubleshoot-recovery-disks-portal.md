---
title: Použít s Linuxem, řešení potíží s virtuálního počítače na webu Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže virtuálního počítače Linux s připojením disku s operačním systémem k obnovení virtuálního počítače pomocí webu Azure portal
services: virtual-machines-linux
documentationCenter: ''
authors: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: cynthn
ms.openlocfilehash: efa001a24be3fb646a2a10afe72cb9b4ebfbf836
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932004"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Řešení potíží s virtuálního počítače s Linuxem připojením disku s operačním systémem k obnovení virtuálního počítače pomocí webu Azure portal
Pokud virtuální počítač s Linuxem (VM), zaznamená chybám spouštění nebo disku, budete muset provést postup řešení potíží na samotném virtuálním pevném disku. Běžným příkladem by byla neplatná položka v `/etc/fstab` virtuální počítač, který brání tomu nebudou moct úspěšně spustil. Tento článek podrobně popisuje, jak připojit virtuální pevný disk k jinému virtuálnímu počítači Linux opravte všechny chyby a pak znovu vytvořit původní virtuální počítač pomocí webu Azure portal.

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstranění virtuálního počítače k chybám, zachování virtuálních pevných disků.
2. Připojení a připojte virtuální pevný disk k jinému virtuálnímu počítači s Linuxem pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Upravení souborů nebo spuštění všech nástrojů k opravě potíží na původním virtuálním pevném disku.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače s použitím původního virtuálního pevného disku.

Pro virtuální počítač, který používá spravovaný disk, najdete v článku [vyřešit spravovaný virtuální počítač Disk připojit nový disk s operačním systémem](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Určete spouštěcí problémy
Podívejte se na diagnostiky spouštění a snímek obrazovky virtuálního počítače k určení, proč váš virtuální počítač není možné správně spustit. Běžným příkladem by byla neplatná položka v `/etc/fstab`, nebo základní virtuální pevný disk se odstraní nebo přesune.

Vyberte svůj virtuální počítač na portálu a potom přejděte dolů k položce **podpora a řešení potíží** oddílu. Klikněte na tlačítko **Diagnostika spouštění** zobrazení konzoly zpráv streamovaných ze svého virtuálního počítače. Zkontrolujte protokoly konzoly a zobrazit, pokud můžete určit, proč tento virtuální počítač se vyskytnout problém. Následující příklad ukazuje, že se že zablokuje a virtuální počítač v režimu údržby, která vyžaduje ruční zásah:

![Virtuální počítač zobrazení diagnostiky spouštění protokoly konzoly](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Můžete také kliknout na **snímek obrazovky** v horní části protokolu diagnostiky spouštění stáhnout zachycení snímku obrazovky virtuálního počítače.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazit podrobnosti o stávající virtuální pevný disk
Než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači, musíte určit název virtuálního pevného disku (VHD). 

Vyberte skupinu prostředků z portálu a pak vyberte svůj účet úložiště. Klikněte na tlačítko **objekty BLOB**, jako v následujícím příkladu:

![Vyberte úložiště objektů BLOB](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Obvykle máte kontejner s názvem **virtuální pevné disky** , který ukládá virtuální pevné disky. Vyberte kontejner, chcete-li zobrazit seznam virtuálních pevných disků. Poznamenejte si název vašeho virtuálního pevného disku (předpona, která je obvykle název vašeho virtuálního počítače):

![Identifikujte virtuální pevný disk v kontejneru úložiště](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Vyberte existující virtuální pevný disk ze seznamu a zkopírujte adresu URL pro použití v následujících krocích:

![Zkopírujte adresu URL existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Odstranění existujícího virtuálního počítače
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikací a konfigurace. Virtuální počítač je jenom metadata, která definují velikost nebo umístění a odkazuje na prostředky, jako jsou virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má při připojení k virtuálnímu počítači přiřadí zapůjčení. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení se nadále přidružuje disk s operačním systémem v případě virtuálních počítačů i v případě, že je virtuální počítač v zastaveném a uvolněném stavu.

Prvním krokem k obnovení vašeho virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače, připojit virtuální pevný disk k jinému virtuálnímu počítači odstraňovat potíže a řešit chyby.

Vyberte svůj virtuální počítač na portálu a potom klikněte na **odstranit**:

![Virtuální počítač snímek obrazovky diagnostiky spouštění došlo k chybě spuštění](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Počkejte, dokud virtuální počítač má bylo dokončeno odstraňování než připojit virtuální pevný disk k jinému virtuálnímu počítači. Zapůjčený virtuální pevný disk, který se přidruží k němu virtuální počítač je potřeba uvolnit, než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jinému virtuálnímu počítači
Pro několik dalších kroků použijte jiný virtuální počítač pro účely odstraňování potíží. Připojení existujícího virtuálního pevného disku do tohoto řešení potíží virtuálního počítače moci procházet a upravovat obsah na disku. Tento proces umožňuje opravte případné chyby v konfiguraci, případně si můžete přečíst další aplikace nebo systému souborů protokolu, např. Zvolte nebo vytvořte jiný virtuální počítač určený pro účely odstraňování potíží.

1. Vyberte skupinu prostředků z portálu a potom vyberte řešení potíží virtuální počítač. Vyberte **disky** a potom klikněte na tlačítko **připojit existující**:

    ![Připojit stávající disk na portálu](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Klikněte na **Soubor VHD** a vyberte váš existující virtuální pevný disk:

    ![Vyhledání existujícího VHD procházením](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Vyberte účet úložiště a kontejner a pak klikněte na existujícím virtuálním pevném disku. Klikněte na tlačítko **vyberte** tlačítko potvrďte výběr:

    ![Výběr existujícího VHD](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Se teď vybrán váš VHD, klikněte na tlačítko **OK** připojit existující virtuální pevný disk:

    ![Ověřte připojení existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Po několika sekundách **disky** podokno pro váš virtuální počítač obsahuje existující virtuální pevný disk připojený jako datový disk:

    ![Existující virtuální pevný disk připojený jako datový disk](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Připojte přídavný datový disk

> [!NOTE]
> Následující příklady jsou podrobně popsané kroky potřebné na virtuální počítač s Ubuntu. Pokud používáte jiné distribuce Linuxu, jako je Red Hat Enterprise Linux nebo SUSE, umístění souborů protokolu a `mount` příkazy mohou být mírně liší. Naleznete v dokumentaci pro vaše konkrétní distribuce pro odpovídající změny v příkazech.

1. Připojte přes SSH k virtuálnímu počítači poradce při potížích s pomocí příslušných přihlašovacích údajů. Pokud tento disk je první datový disk připojený k řešení potíží virtuální počítač, je pravděpodobně připojené k `/dev/sdc`. Použití `dmseg` pro výpis připojených disků:

    ```bash
    dmesg | grep SCSI
    ```
    Výstup se podobá následujícímu příkladu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    V předchozím příkladu je disk s operačním systémem na `/dev/sda` a dočasný disk k dispozici pro každý virtuální počítač je v `/dev/sdb`. Pokud jste měli více datových disků, měly by být na `/dev/sdd`, `/dev/sde`, a tak dále.

2. Vytvořte adresář připojte existující virtuální pevný disk. Následující příklad vytvoří adresář s názvem `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte více oddílů na existující virtuální pevný disk, připojte oddíl povinný. Následující příklad připojí na první primární oddíl `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Osvědčeným postupem je připojit datové disky na virtuálních počítačích v Azure s využitím univerzálně jedinečným identifikátorem (UUID) virtuálního pevného disku. Pro tento krátký Poradce při potížích scénář připojení virtuálního pevného disku pomocí identifikátoru UUID je nezbytné. Ale při normálním používání úpravy `/etc/fstab` připojit virtuální pevné disky pomocí místo UUID název zařízení může způsobit selhání spuštění virtuálního počítače.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Pomocí existující virtuální pevný disk připojený můžete teď provádět údržbu a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Po vyřešení chyby odpojte existující virtuální pevný disk z vašeho řešení potíží virtuálního počítače. Virtuální pevný disk s jakýkoli jiný virtuální počítač nelze použít, dokud se neuvolní zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z relace SSH k řešení potíží virtuální počítač odpojte existující virtuální pevný disk. Nejprve změňte mimo nadřazený adresář pro přípojného bodu:

    ```bash
    cd /
    ```

    Nyní odpojte existující virtuální pevný disk. Následující příklad odpojí zařízení `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nyní Odpojte virtuální pevný disk z virtuálního počítače. Vyberte svůj virtuální počítač na portálu a klikněte na **disky**. Vyberte existující virtuální pevný disk a potom klikněte na tlačítko **odpojit**:

    ![Odpojte existující virtuální pevný disk](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Počkejte, dokud virtuální počítač úspěšně odpojit datový disk než budete pokračovat.

## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Vytvoření virtuálního počítače z původního virtuálního pevného disku, použijte [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Šablona nasadí virtuální počítač do existující virtuální sítě pomocí adresy URL virtuálního pevného disku z předchozích příkazu. Klikněte na tlačítko **nasadit do Azure** tlačítko následujícím způsobem:

![Nasazení virtuálního počítače ze šablony z Githubu](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Šablona se načtou do portálu Azure pro nasazení. Zadejte název pro nový virtuální počítač a stávající prostředky Azure a vložte adresu URL na existující virtuální pevný disk. Chcete-li zahájit nasazení, klikněte na tlačítko **nákupní**:

![Nasazení virtuálního počítače ze šablony](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení diagnostiky spouštění
Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí Diagnostika spouštění automaticky povolená. Chcete-li zkontrolovat stav Diagnostika spouštění a zapnutí v případě potřeby, vyberte svůj virtuální počítač na portálu. V části **monitorování**, klikněte na tlačítko **nastavení diagnostiky**. Zkontrolujte, že stav je **na**a na značku zaškrtnutí vedle **Diagnostika spouštění** je vybrána. Pokud provedete změny, klikněte na tlačítko **Uložit**:

![Aktualizovat nastavení diagnostiky spouštění](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Řešení potíží s spravovaný virtuální počítač Disk připojením nový disk operačního systému
1. Zastavte dotčených spravovaného disku Windows virtuální počítač.
2. [Vytvoření snímku spravovaného disku](../windows/snapshot-copy-managed-disk.md) z disku s operačním systémem virtuálního počítače spravovaného disku.
3. [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Připojení spravovaného disku jako datového disku virtuálního počítače](../windows/attach-disk-ps.md).
5. [Změňte datový disk na disk s operačním systémem z kroku 4](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [řešení potíží s připojení SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuální počítač s Linuxem](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Další informace o použití Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
