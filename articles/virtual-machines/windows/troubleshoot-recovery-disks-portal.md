---
title: Použití Windows řešení potíží s virtuálního počítače na portálu Azure | Microsoft Docs
description: Zjistěte, jak k řešení potíží virtuální počítač Windows v Azure připojením disk operačního systému k obnovení virtuálního počítače pomocí portálu Azure
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 523bf4055b4181715efd2c2d67e4dde086e7540e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Řešení potíží s virtuální počítač s Windows pomocí disk operačního systému se připojuje k obnovení virtuálního počítače pomocí portálu Azure
Pokud Windows virtuálního počítače (VM) v prostředí Azure dojde k chybě spouštěcí nebo disk, musíte provést na virtuálním pevném disku, sám sebe pro řešení potíží. Běžným příkladem bude aplikaci, která selhala aktualizace, která brání virtuálního počítače nebudou moct úspěšně spustil. Tento článek popisuje, jak připojit virtuální pevný disk na jiný virtuální počítač opravte případné chyby a pak znovu vytvořte původní virtuální počítač s Windows pomocí portálu Azure.

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstraňte virtuální počítač, na zjištění problémy, zachovat virtuální pevné disky.
2. Připojte a připojit virtuální pevný disk na jiný virtuální počítač s Windows pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Úpravy souborů nebo spustit žádné nástroje na opravte problémy v původní virtuální pevný disk.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače pomocí původní virtuální pevný disk.


## <a name="determine-boot-issues"></a>Určení spouštěcí problémy
Pokud chcete zjistit, proč váš virtuální počítač není možné správně spustit, zkontrolujte Diagnostika spouštění snímek virtuálního počítače. Běžným příkladem by být aktualizaci selhání aplikace nebo virtuální pevný disk, na kterém se odstranil nebo přesunul.

Vyberte virtuální počítač na portálu a poté přejděte dolů k **podporu + Poradce při potížích s** části. Klikněte na tlačítko **spouštění diagnostiky** zobrazíte na snímku obrazovky. Poznámka: všechny specifické chybové zprávy nebo kódy chyb, které vám pomohou určit, proč je virtuální počítač zjištění problému. Následující příklad ukazuje čekání na zastavení služeb virtuálního počítače:

![Zobrazení virtuálních počítačů Diagnostika spouštění protokoly konzoly](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Můžete také kliknout na **– snímek obrazovky** ke stažení zachycení snímek virtuálního počítače.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazení podrobností existující virtuální pevný disk
Než k jiným virtuálním Počítačem můžete připojit virtuální pevný disk, musíte určit název virtuálního pevného disku (VHD). 

Vyberte skupinu prostředků z portálu a potom vyberte účet úložiště. Klikněte na tlačítko **objekty BLOB**, jako v následujícím příkladu:

![Vyberte úložiště objektů BLOB](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Obvykle mají kontejner s názvem **virtuální pevné disky** , ukládá virtuální pevné disky. Vyberte kontejner zobrazení seznamu virtuálních pevných disků. Poznamenejte si název vašeho virtuálního pevného disku (předpona, která je obvykle název vašeho virtuálního počítače):

![Identifikovat virtuální pevný disk v kontejneru úložiště](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Vyberte ze seznamu existující virtuální pevný disk a zkopírujte adresu URL pro použití v následujících krocích:

![Zkopírujte adresu URL existující virtuální pevný disk](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Odstraňte existující virtuální počítač
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikace a konfigurace. Virtuální počítač je jenom metadata, která definuje velikosti či umístění a odkazuje na prostředky, jako je virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má zapůjčení přiřazen při připojen k virtuálnímu počítači. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení i nadále i v případě, že tento virtuální počítač je ve stavu Zastaveno a deallocated přidružení disk operačního systému virtuálního počítače.

Prvním krokem k obnovení virtuálního počítače je odstranit samotné prostředků virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače připojit virtuální pevný disk k jiným virtuálním Počítačem vyřešit chyby.

Vyberte virtuální počítač na portálu a pak klikněte na tlačítko **odstranit**:

![Virtuální počítač spouštěcí diagnostiky snímek obrazovky zobrazující chyby spouštěcí](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Počkejte, dokud je virtuální počítač dokončí odstraňování před připojit virtuální pevný disk k jiným virtuálním Počítačem. Zapůjčení na virtuální pevný disk, který přidruží k němu virtuální počítač je nutné uvolnit předtím, než k jiným virtuálním Počítačem můžete připojit virtuální pevný disk.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jiným virtuálním Počítačem
Pro několika dalších krocích použijete jiný počítač pro účely odstraňování potíží. Existující virtuální pevný disk se připojit k řešení potíží VM být schopni prohlížet a upravovat obsah na disk. Tento proces umožňuje opravte všechny chyby konfigurace nebo zkontrolujte další aplikace nebo systému souborů protokolu, např. Vyberte nebo vytvořte jiným virtuálním Počítačem používat pro účely odstraňování potíží.

1. Vyberte skupinu prostředků z portálu a potom vyberte řešení potíží virtuálního počítače. Vyberte **disky** a pak klikněte na **připojit existující**:

    ![Připojit stávající disk na portálu](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Klikněte na **Soubor VHD** a vyberte váš existující virtuální pevný disk:

    ![Vyhledání existujícího VHD procházením](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Vyberte účet úložiště a kontejneru a pak klikněte na existující virtuální pevný disk. Klikněte **vyberte** tlačítko potvrďte svou volbu:

    ![Výběr existujícího VHD](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. S svůj disk VHD nyní vybraný, klikněte na tlačítko **OK** připojit existující virtuální pevný disk:

    ![Zkontrolujte připojení existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Za několik sekund **disky** podokno pro virtuální počítač obsahuje existující virtuální pevný disk připojený jako datový disk:

    ![Existující virtuální pevný disk připojený jako datový disk](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Připojit disk připojená data

1. Otevřete připojení vzdálené plochy k virtuálnímu počítači. Vyberte virtuální počítač v portálu a klikněte na **Connect**. Stáhněte a otevřete soubor RDP připojení. Zadejte své přihlašovací údaje pro přihlášení k virtuálnímu počítači následujícím způsobem:

    ![Přihlaste se k virtuálnímu počítači pomocí vzdálené plochy](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Otevřete **správce serveru**, pak vyberte **Souborová služba a služba úložiště**. 

    ![Vyberte Souborová služba a služba úložiště ve Správci serveru](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. Datový disk je automaticky zjistil a připojené. Pokud chcete zobrazit seznam připojených disků, vyberte **disky**. Můžete vybrat datový disk k zobrazení informací o svazku, včetně písmeno jednotky. Následující příklad ukazuje datový disk připojený a pomocí **F:**:

    ![Disk připojený a informace o svazku ve Správci serveru](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Vyřešte problémy na původní virtuální pevný disk
S existující virtuální pevný disk připojit teď můžete dělat žádné údržby a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojte Image a odpojit původní virtuální pevný disk
Jakmile jsou vaše chyby vyřešeny, odpojte existující virtuální pevný disk z virtuálního počítače řešení potíží. Virtuální pevný disk s jiných virtuálních počítačů nelze používat, dokud vydání zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z relace protokolu RDP pro virtuální počítač, otevřete **správce serveru**, pak vyberte **Souborová služba a služba úložiště**:

    ![Vyberte Souborová služba a služba úložiště ve Správci serveru](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Vyberte **disky** a pak vyberte datový disk. Klikněte pravým tlačítkem na datový disk a vyberte **převést do offline režimu**:

    ![Nastavte datový disk jako offline ve Správci serveru](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Nyní Odpojte virtuální pevný disk z virtuálního počítače. Vyberte virtuální počítač na portálu Azure a klikněte na **disky**. Vyberte existující virtuální pevný disk a potom klikněte na **odpojení**:

    ![Odpojte existující virtuální pevný disk](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Počkejte, dokud virtuálního počítače úspěšně odpojil datový disk než budete pokračovat.

## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Chcete-li vytvořit virtuální počítač z původní virtuální pevný disk, použijte [této šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Šablona nasadí virtuální počítač do existující virtuální síť pomocí adresy URL virtuálního pevného disku z dřívějších příkazu. Klikněte **nasadit do Azure** tlačítko následujícím způsobem:

![Nasazení virtuálních počítačů z šablony z Githubu](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Šablona je načten do portálu Azure pro nasazení. Zadejte názvy pro nový virtuální počítač a existující prostředky Azure a vložte adresu URL na existující virtuální pevný disk. Chcete-li zahájit nasazení, klikněte na tlačítko **nákupu**:

![Nasazení virtuálního počítače ze šablony](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení Diagnostika spouštění
Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí být Diagnostika spouštění automaticky povolené. Zkontrolujte stav Diagnostika spouštění a v případě potřeby zapnout, vyberte virtuální počítač na portálu. V části **monitorování**, klikněte na tlačítko **nastavení diagnostiky**. Ujistěte se, že stav je **na**a zaškrtnutí políčka vedle **spouštění diagnostiky** je vybrána. Pokud provedete změny, klikněte na tlačítko **Uložit**:

![Aktualizovat nastavení diagnostiky spouštění](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP na virtuální počítač Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete v tématu [problémů s připojením aplikace na virtuálním počítači Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o používání správce prostředků najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
