---
title: Použití virtuálního počítače pro řešení potíží s Windows v Azure Portal | Microsoft Docs
description: Naučte se řešit potíže s virtuálními počítači s Windows v Azure připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881162"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal
Pokud váš virtuální počítač s Windows v Azure najde chybu spuštění nebo disku, možná budete muset provést kroky pro řešení potíží na samotném virtuálním pevném disku. Běžným příkladem může být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí Azure Portal připojit virtuální pevný disk k jinému virtuálnímu počítači s Windows a opravit případné chyby a pak znovu vytvořit původní virtuální počítač. 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstraňte virtuální počítač, na kterém dochází k potížím, a zachováte virtuální pevné disky.
2. Připojte virtuální pevný disk k jinému virtuálnímu počítači s Windows pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Úpravou souborů nebo spuštěním libovolných nástrojů opravte potíže s původním virtuálním pevným diskem.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvořte virtuální počítač pomocí původního virtuálního pevného disku.

U virtuálního počítače, který používá spravovaný disk, teď můžeme použít Azure PowerShell ke změně disku s operačním systémem pro virtuální počítač. Už nepotřebujeme virtuální počítač odstranit a znovu vytvořit. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure PowerShell](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Tento článek se nevztahuje na virtuální počítač s nespravovaným diskem.

## <a name="determine-boot-issues"></a>Určení problémů se spouštěním
Pokud chcete zjistit, proč se váš virtuální počítač nemůže správně spustit, Projděte si snímek obrazovky virtuálního počítače diagnostiky spouštění. Běžným příkladem může být neúspěšná aktualizace aplikace nebo odstranění nebo přesunutí základního virtuálního pevného disku.

Na portálu vyberte svůj virtuální počítač a pak přejděte dolů k části **Podpora a řešení potíží** . Kliknutím na **Diagnostika spouštění** zobrazte snímek obrazovky. Poznamenejte si všechny konkrétní chybové zprávy nebo chybové kódy, které vám pomůžou zjistit, proč se u virtuálního počítače objevil problém.

![Zobrazení protokolů konzoly pro diagnostiku spouštění virtuálního počítače](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Můžete také kliknout na **Stáhnout snímek obrazovky** a stáhnout snímek obrazovky virtuálního počítače.

## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazit podrobnosti o existujícím virtuálním pevném disku
Než budete moci připojit virtuální pevný disk k jinému virtuálnímu počítači, je nutné určit název virtuálního pevného disku (VHD).

Vyberte virtuální počítač, který má problém, a pak vyberte **disky**. Poznamenejte si název disku s operačním systémem, jak je uvedeno v následujícím příkladu:

![Výběr objektů BLOB služby Storage](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Odstranit existující virtuální počítač
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je místo, kde se ukládají samotné operační systémy, aplikace a konfigurace. Samotný virtuální počítač je jenom metadata definující velikost nebo umístění a odkazuje na prostředky, jako je virtuální pevný disk nebo karta virtuální sítě (NIC). Pro každý virtuální pevný disk je zapůjčení přiřazeno při připojení k virtuálnímu počítači. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení nadále přidružuje disk s operačním systémem k virtuálnímu počítači i v případě, že je tento virtuální počítač ve stavu Zastaveno a zrušeno.

Prvním krokem při obnovení virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače připojíte virtuální pevný disk k jinému virtuálnímu počítači, abyste mohli řešit problémy a vyřešit chyby.

Na portálu vyberte svůj virtuální počítač a pak klikněte na **Odstranit**:

![Snímek obrazovky diagnostiky spouštění virtuálního počítače zobrazující chybu spuštění](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Než se virtuální pevný disk připojíte k jinému virtuálnímu počítači, počkejte, než se dokončí odstranění virtuálního počítače. Aby bylo možné připojit virtuální pevný disk k jinému virtuálnímu počítači, musí být zapůjčení virtuálního pevného disku, který ho přidružuje k virtuálnímu počítači, vydaný.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit stávající virtuální pevný disk k jinému virtuálnímu počítači
V následujících několika krocích použijete pro účely odstraňování potíží jiný virtuální počítač. K tomuto virtuálnímu počítači pro řešení potíží připojíte existující virtuální pevný disk, abyste mohli procházet a upravovat obsah disku. Tento proces umožňuje opravit chyby v konfiguraci nebo zkontrolovat soubory protokolu aplikace nebo systému, například. Vyberte nebo vytvořte jiný virtuální počítač, který chcete použít pro účely řešení potíží.

1. Z portálu vyberte skupinu prostředků a potom vyberte virtuální počítač pro řešení potíží. Vyberte **disky**, vyberte **Upravit**a pak klikněte na **přidat datový disk**:

    ![Připojit existující disk na portálu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. V seznamu **datové disky** vyberte disk s operačním systémem virtuálního počítače, který jste identifikovali. Pokud se disk s operačním systémem nezobrazuje, ujistěte se, že je virtuální počítač pro řešení potíží a disk s operačním systémem ve stejné oblasti (umístění).
3. Kliknutím na **Uložit** změny aplikujte.

## <a name="mount-the-attached-data-disk"></a>Připojit připojený datový disk

1. Otevřete připojení ke vzdálené ploše virtuálního počítače. Na portálu vyberte svůj virtuální počítač a klikněte na **připojit**. Stáhněte a otevřete soubor připojení RDP. Zadejte přihlašovací údaje pro přihlášení k VIRTUÁLNÍmu počítači následujícím způsobem:

    ![Přihlaste se k VIRTUÁLNÍmu počítači pomocí vzdálené plochy](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Otevřete **Správce serveru**a pak vyberte **Souborová služba a služba úložiště**. 

    ![Vyberte Souborová služba a služba úložiště v Správce serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datový disk se automaticky detekuje a připojí. Pokud chcete zobrazit seznam připojených disků, vyberte **disky**. Můžete vybrat datový disk pro zobrazení informací o svazku, včetně písmene jednotky. Následující příklad ukazuje, že datový disk je připojený a používá **F:** :

    ![Informace o připojených discích a svazcích v Správce serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Opravit problémy s původním virtuálním pevným diskem
S připojeným virtuálním pevným diskem teď můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojte a odpojte původní virtuální pevný disk
Po vyřešení chyb odpojte stávající virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nemůžete použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení virtuálního pevného disku k virtuálnímu počítači pro řešení potíží.

1. Z relace RDP na svůj virtuální počítač otevřete **Správce serveru**a pak vyberte **Souborová služba a služba úložiště**:

    ![Vyberte Souborová služba a služba úložiště v Správce serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Vyberte **disky** a pak vyberte svůj datový disk. Pravým tlačítkem myši klikněte na datový disk a vyberte příkaz **převést do režimu offline**:

    ![Nastavte datový disk jako offline v Správce serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Teď odpojte virtuální pevný disk od virtuálního počítače. V Azure Portal vyberte svůj virtuální počítač a klikněte na **disky**. 
4. Vyberte **Upravit**, vyberte disk s operačním systémem, který jste připojili, a potom klikněte na **Odpojit**:

    ![Odpojit stávající virtuální pevný disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Než budete pokračovat, počkejte, než virtuální počítač úspěšně odpojí datový disk.

## <a name="create-vm-from-original-hard-disk"></a>Vytvořit virtuální počítač z původního pevného disku

### <a name="method-1-use-azure-resource-manager-template"></a>Metoda 1 použití šablony Azure Resource Manager
Pokud chcete vytvořit virtuální počítač z původního virtuálního pevného disku, použijte [tuto šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). Šablona nasadí virtuální počítač do existující nebo nové virtuální sítě pomocí adresy URL virtuálního pevného disku z předchozího příkazu. Klikněte na tlačítko **nasadit do Azure** následujícím způsobem:

![Nasazení virtuálního počítače ze šablony z GitHubu](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Šablona se načte do Azure Portal pro nasazení. Zadejte názvy pro svůj nový virtuální počítač a stávající prostředky Azure a vložte adresu URL na existující virtuální pevný disk. Pokud chcete zahájit nasazení, klikněte na **koupit**:

![Nasazení virtuálního počítače ze šablony](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>Metoda 2 – Vytvoření virtuálního počítače z disku

1. V Azure Portal na portálu vyberte skupinu prostředků a pak najděte disk s operačním systémem. Disk můžete také vyhledat pomocí názvu disku:

    ![Hledat disk z Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Vyberte **Přehled**a pak vyberte **vytvořit virtuální počítač**.
    ![Vytvoření virtuálního počítače z disku z Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. Postupujte podle pokynů průvodce a vytvořte virtuální počítač.

## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení diagnostiky spouštění
Při vytváření virtuálního počítače z existujícího virtuálního pevného disku nemusí být automaticky povolena Diagnostika spouštění. Pokud chcete zjistit stav diagnostiky spouštění a v případě potřeby ho zapnout, vyberte svůj virtuální počítač na portálu. V části **monitorování**klikněte na **nastavení diagnostiky**. Zajistěte, aby byl stav zapnutý a byl vybrán symbol zaškrtnutí vedle možnosti **Diagnostika spouštění** . Pokud provedete nějaké změny, klikněte na **Uložit**:

![Aktualizovat nastavení diagnostiky spouštění](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači s Windows](troubleshoot-app-connection.md).

Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/resource-group-overview.md).

