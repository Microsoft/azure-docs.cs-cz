---
title: Nahrání souboru virtuálního pevného disku do Azure DevTest Labs pomocí Microsoft Azure Storage Explorer | Microsoft Docs
description: Nahrání souboru virtuálního pevného disku do testovacího prostředí na účtu úložiště pomocí Microsoft Azure Storage Explorer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cafb3ec2da335165a3b66c18d83930008c6d72e5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Nahrání souboru virtuálního pevného disku do testovacího prostředí na účtu úložiště pomocí Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs soubory virtuálního pevného disku lze vytvořit vlastní Image, které se používají ke zřízení virtuálních počítačů. Tento článek ukazuje, jak používat [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) chcete nahrát soubor virtuálního pevného disku do účtu úložiště testovacího prostředí. Jakmile jste odeslali souboru virtuálního pevného disku [další kroky části](#next-steps) jsou uvedeny některé články, které ukazují, jak vytvořit vlastní image z nahrávaný soubor virtuálního pevného disku. Další informace o disky a virtuální pevné disky v Azure najdete v tématu [o disky a virtuální pevné disky pro virtuální počítače](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou nahrání souboru virtuálního pevného disku do DevTest Labs pomocí [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Stáhněte a nainstalujte nejnovější verzi Microsoft Azure Storage Explorer](http://www.storageexplorer.com).

1. Získání názvu účtu úložiště v prostředí pomocí portálu Azure:

    1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
    
    1. Ze seznamu labs vyberte požadované testovací prostředí.  
    
    1. V okně v prostředí, vyberte **konfigurace**. 
    
    1. V testovacím prostředí **konfigurace** vyberte **vlastní Image (VHD)**.
    
    1. Na **vlastní image** okně vyberte **+ přidat**. 
    
    1. Na **vlastní image** vyberte **virtuálního pevného disku**.
    
    1. Na **virtuálního pevného disku** vyberte **nahrát virtuální pevný disk pomocí prostředí PowerShell**.
    
        ![Nahrání virtuálního pevného disku pomocí prostředí PowerShell][0]
    
    1. **Odeslat bitovou kopii pomocí prostředí PowerShell** zobrazuje volání **přidat AzureVhd** rutiny. První parametr (*cílové*) obsahuje název účtu úložiště pro testovací prostředí v následujícím formátu:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Poznamenejte si název účtu úložiště jako se používá v dalších krocích.
    
1. Připojte k účtu předplatného Azure, pomocí Průzkumníka úložiště.

    > [!TIP] 
    > 
    > Storage Explorer podporuje několik možností připojení. Tato část ukazuje připojení k účtu úložiště přidruženého k předplatnému Azure. Zobrazíte další možnosti připojení nepodporuje Storage Explorer naleznete v článku [Začínáme se Storage Explorerem](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otevřete Průzkumníka služby Storage.
    
    1. V Průzkumníku úložiště vyberte **nastavení účtu Azure**. 
    
        ![Nastavení účtu Azure][1]
    
    1. V levém podokně se zobrazí účty Microsoft, pod kterým jste přihlášení k. Pokud se chcete připojit k jinému účtu, vyberte položku **Add an account** (Přidat účet) a podle pokynů v dialogových oknech se přihlaste účtem Microsoft, který je přidružený minimálně k jednomu aktivnímu předplatnému Azure.
    
        ![Přidání účtu][2]
    
    1. Jakmile se úspěšně přihlásíte s účtem Microsoft, zobrazí se v levém podokně předplatná Azure přidružená k tomuto účtu. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Výběr **všechny odběry** přepíná výběr všech nebo žádných z uvedených předplatných Azure.)
    
        ![Výběr předplatných Azure][3]
    
    1. V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.
    
        ![Vybraná předplatná Azure][4]

1. Vyhledejte účet úložiště v prostředí:

    1. V levém podokně Storage Exploreru vyhledejte a rozbalte uzel pro předplatné Azure, který vlastní testovací prostředí.
    
    1. V uzlu odběru, rozbalte položku **účty úložiště**.

    1. Rozbalte uzel účet úložiště v prostředí a odhalit uzlů pro **kontejnery objektů Blob**, **sdílené složky**, **fronty**, a **tabulky**.
    
    1. Rozbalte **kontejnery objektů Blob** uzlu.
    
    1. Vyberte kontejner objektů blob nahrávání zobrazíte její obsah v pravém podokně.
        
        ![Nahrát adresáře][5]

1. Nahrajte soubor virtuálního pevného disku pomocí Průzkumníka úložiště:

    1. V pravém podokně Storage Exploreru, byste měli vidět seznam objektů BLOB v **odešle** kontejner objektů blob z účtu úložiště v prostředí. Na panelu nástrojů editoru objektů blob, vyberte **nahrát** 
        
        ![Tlačítko Nahrát][6]
    
    1. Z **nahrát** rozevírací nabídky vyberte **nahrání souborů...** .
    
    1. Na **nahrání souborů** dialogovém okně, vyberte se třemi tečkami.
        
        ![Výběr souboru][8]  

    1. Na **vyberte soubory, které chcete nahrát** dialogové okno, přejděte na požadovaný soubor virtuálního pevného disku, vyberte ho a potom vyberte **otevřete**.
    
    1. Pokud vrátí k **nahrání souborů** dialogové okno, změna **Blob typ** k **objekt Blob stránky**.
    
    1. Vyberte **Nahrát**.

        ![Výběr souboru][9]  
    
    1. Storage Explorer **protokol aktivit** podokně se zobrazí stav stahování (spolu s odkazy na Zrušit odesílání). Proces odesílání soubor virtuálního pevného disku může být náročná v závislosti na velikosti souboru virtuálního pevného disku a rychlost připojení. 

        ![Stav nahrávání souboru][10]  

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru virtuálního pevného disku pomocí portálu Azure](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru virtuálního pevného disku pomocí prostředí PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
