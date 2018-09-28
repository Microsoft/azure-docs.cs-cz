---
title: Použití Windows řešení potíží s virtuálního počítače na webu Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže virtuální počítač Windows v Azure s připojením disku s operačním systémem k obnovení virtuálního počítače pomocí webu Azure portal
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/013/2018
ms.author: genli
ms.openlocfilehash: 42494ef538fa9840afe5f489074934da3965d56f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413296"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem k obnovení virtuálního počítače pomocí webu Azure portal
Pokud váš Windows virtuální počítač (VM) v Azure dojde k chybě spouštění nebo disku, budete muset provést postup řešení potíží na samotném virtuálním pevném disku. Běžným příkladem může být aktualizace selhání aplikace, která brání virtuálního počítače moci úspěšně spustil. Tento článek podrobně popisuje, jak připojit virtuální pevný disk k jinému virtuálnímu počítači Windows opravte všechny chyby a pak znovu vytvořit původní virtuální počítač pomocí webu Azure portal.

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstranění virtuálního počítače k chybám, zachování virtuálních pevných disků.
2. Připojení a připojte virtuální pevný disk k jinému virtuálnímu počítači s Windows pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Upravení souborů nebo spuštění všech nástrojů k opravě potíží na původním virtuálním pevném disku.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače s použitím původního virtuálního pevného disku.

Pro virtuální počítač používá spravovaný disk jsme nyní pomocí prostředí Azure PowerShell můžete změnit disk s operačním systémem pro virtuální počítač. Už musíme odstranit a znovu vytvořte virtuální počítač. Další informace najdete v tématu [odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem k obnovení virtuálního počítače pomocí Azure Powershellu](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Určete spouštěcí problémy
Pokud chcete zjistit, proč váš virtuální počítač není schopen spuštění systému správně, zkontrolujte diagnostiku spouštění snímek obrazovky virtuálního počítače. Běžným příkladem by se aktualizace aplikace, která selhala, nebo základní virtuální pevný disk se odstraní nebo přesune.

Vyberte svůj virtuální počítač na portálu a potom přejděte dolů k položce **podpora a řešení potíží** oddílu. Klikněte na tlačítko **Diagnostika spouštění** zobrazíte na snímku obrazovky. Poznámka: všechny specifické chybové zprávy nebo kódy chyb, k určení toho, proč tento virtuální počítač se vyskytnout problém. Následující příklad ukazuje čekání na zastavení služby virtuálního počítače:

![Virtuální počítač zobrazení diagnostiky spouštění protokoly konzoly](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Můžete také kliknout na **snímek obrazovky** stáhnout zachycení snímku obrazovky virtuálního počítače.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazit podrobnosti o stávající virtuální pevný disk
Než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači, musíte určit název virtuálního pevného disku (VHD). 

Vyberte skupinu prostředků z portálu a pak vyberte svůj účet úložiště. Klikněte na tlačítko **objekty BLOB**, jako v následujícím příkladu:

![Vyberte úložiště objektů BLOB](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

Obvykle máte kontejner s názvem **virtuální pevné disky** , který ukládá virtuální pevné disky. Vyberte kontejner, chcete-li zobrazit seznam virtuálních pevných disků. Poznamenejte si název vašeho virtuálního pevného disku (předpona, která je obvykle název vašeho virtuálního počítače):

![Identifikujte virtuální pevný disk v kontejneru úložiště](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Vyberte existující virtuální pevný disk ze seznamu a zkopírujte adresu URL pro použití v následujících krocích:

![Zkopírujte adresu URL existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Odstranění existujícího virtuálního počítače
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikací a konfigurace. Virtuální počítač je jenom metadata, která definují velikost nebo umístění a odkazuje na prostředky, jako jsou virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má při připojení k virtuálnímu počítači přiřadí zapůjčení. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení se nadále přidružuje disk s operačním systémem v případě virtuálních počítačů i v případě, že je virtuální počítač v zastaveném a uvolněném stavu.

Prvním krokem k obnovení vašeho virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače, připojit virtuální pevný disk k jinému virtuálnímu počítači odstraňovat potíže a řešit chyby.

Vyberte svůj virtuální počítač na portálu a potom klikněte na **odstranit**:

![Virtuální počítač snímek obrazovky diagnostiky spouštění došlo k chybě spuštění](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Počkejte, dokud virtuální počítač má bylo dokončeno odstraňování než připojit virtuální pevný disk k jinému virtuálnímu počítači. Zapůjčený virtuální pevný disk, který se přidruží k němu virtuální počítač je potřeba uvolnit, než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jinému virtuálnímu počítači
Pro několik dalších kroků použijte jiný virtuální počítač pro účely odstraňování potíží. Připojení existujícího virtuálního pevného disku do tohoto řešení potíží virtuálního počítače moci procházet a upravovat obsah na disku. Tento proces umožňuje opravte případné chyby v konfiguraci, případně si můžete přečíst další aplikace nebo systému souborů protokolu, např. Zvolte nebo vytvořte jiný virtuální počítač určený pro účely odstraňování potíží.

1. Vyberte skupinu prostředků z portálu a potom vyberte řešení potíží virtuální počítač. Vyberte **disky** a potom klikněte na tlačítko **připojit existující**:

    ![Připojit stávající disk na portálu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Klikněte na **Soubor VHD** a vyberte váš existující virtuální pevný disk:

    ![Vyhledání existujícího VHD procházením](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Vyberte účet úložiště a kontejner a pak klikněte na existujícím virtuálním pevném disku. Klikněte na tlačítko **vyberte** tlačítko potvrďte výběr:

    ![Výběr existujícího VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Se teď vybrán váš VHD, klikněte na tlačítko **OK** připojit existující virtuální pevný disk:

    ![Ověřte připojení existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Po několika sekundách **disky** podokno pro váš virtuální počítač obsahuje existující virtuální pevný disk připojený jako datový disk:

    ![Existující virtuální pevný disk připojený jako datový disk](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Připojte přídavný datový disk

1. Otevřete připojení vzdálené plochy k virtuálnímu počítači. Vyberte svůj virtuální počítač na portálu a klikněte na **připojit**. Stáhněte a otevřete soubor připojení RDP. Zadejte svoje přihlašovací údaje pro přihlášení k virtuálnímu počítači následujícím způsobem:

    ![Přihlaste se ke svému virtuálnímu počítači pomocí vzdálené plochy](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Otevřít **správce serveru**a pak vyberte **Souborová služba a služba úložiště**. 

    ![Vyberte Souborová služba a služba úložiště ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datový disk je automaticky zjistil a připojené. Pokud chcete zobrazit seznam připojených disků, vyberte **disky**. Můžete vybrat váš datový disk, chcete-li zobrazit informace o svazku, včetně písmeno jednotky. Následující příklad ukazuje datový disk připojený a pomocí **F:**:

    ![Disk připojený a informace o svazku ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Pomocí existující virtuální pevný disk připojený můžete teď provádět údržbu a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Po vyřešení chyby odpojte existující virtuální pevný disk z vašeho řešení potíží virtuálního počítače. Virtuální pevný disk s jakýkoli jiný virtuální počítač nelze použít, dokud se neuvolní zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z relace protokolu RDP k virtuálnímu počítači, otevřete **správce serveru**a pak vyberte **Souborová služba a služba úložiště**:

    ![Vyberte Souborová služba a služba úložiště ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Vyberte **disky** a pak vyberte datový disk. Klikněte pravým tlačítkem na datový disk a vyberte **převést do offline režimu**:

    ![Nastavte datový disk jako offline ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Nyní Odpojte virtuální pevný disk z virtuálního počítače. Vyberte svůj virtuální počítač na webu Azure Portal a klikněte na tlačítko **disky**. Vyberte existující virtuální pevný disk a potom klikněte na tlačítko **odpojit**:

    ![Odpojte existující virtuální pevný disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Počkejte, dokud virtuální počítač úspěšně odpojit datový disk než budete pokračovat.

## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Vytvoření virtuálního počítače z původního virtuálního pevného disku, použijte [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Šablona nasadí virtuální počítač do existující virtuální sítě pomocí adresy URL virtuálního pevného disku z předchozích příkazu. Klikněte na tlačítko **nasadit do Azure** tlačítko následujícím způsobem:

![Nasazení virtuálního počítače ze šablony z Githubu](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Šablona se načtou do portálu Azure pro nasazení. Zadejte název pro nový virtuální počítač a stávající prostředky Azure a vložte adresu URL na existující virtuální pevný disk. Chcete-li zahájit nasazení, klikněte na tlačítko **nákupní**:

![Nasazení virtuálního počítače ze šablony](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení diagnostiky spouštění
Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí Diagnostika spouštění automaticky povolená. Chcete-li zkontrolovat stav Diagnostika spouštění a zapnutí v případě potřeby, vyberte svůj virtuální počítač na portálu. V části **monitorování**, klikněte na tlačítko **nastavení diagnostiky**. Zkontrolujte, že stav je **na**a na značku zaškrtnutí vedle **Diagnostika spouštění** je vybrána. Pokud provedete změny, klikněte na tlačítko **Uložit**:

![Aktualizovat nastavení diagnostiky spouštění](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuálním počítači s Windows](troubleshoot-app-connection.md).

Další informace o použití Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

