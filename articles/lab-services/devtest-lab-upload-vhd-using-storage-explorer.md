---
title: Nahrání souboru VHD pomocí Průzkumníka služby Microsoft Azure Storage Azure DevTest Labs | Dokumentace Microsoftu
description: Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Průzkumníka služby Microsoft Azure Storage
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
ms.openlocfilehash: 55e8f536419f102f8f0fe69773ed486407c50cfb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328856"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Průzkumníka služby Microsoft Azure Storage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Ve službě Azure DevTest Labs soubory virtuálního pevného disku slouží k vytváření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Tento článek ukazuje, jak používat [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD [další kroky části](#next-steps) uvádí některé články, které ukazují, jak vytvořit vlastní image z virtuálního pevného disku uloženého souboru. Další informace o discích a virtuálních pevných disků v Azure najdete v tématu [Úvod do managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou po nahrání souboru VHD do DevTest Labs pomocí [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Stáhněte si a nainstalujte nejnovější verzi sady Microsoft Azure Storage Explorer](http://www.storageexplorer.com).

1. Získejte název účtu úložiště testovacího prostředí pomocí webu Azure portal:

    1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
    
    1. V seznamu testovacích prostředí vyberte požadované prostředí.  
    
    1. V okně testovacího prostředí, vyberte **konfigurace**. 
    
    1. V testovacím prostředí **konfigurace** okně vyberte **vlastních imagí (VHD)**.
    
    1. Na **vlastních imagí** okno, vyberte **+ přidat**. 
    
    1. Na **vlastní image** okně vyberte **virtuálního pevného disku**.
    
    1. Na **virtuálního pevného disku** okně vyberte **nahrát VHD pomocí Powershellu**.
    
        ![Nahrát VHD pomocí Powershellu][0]
    
    1. **Nahrát image pomocí prostředí PowerShell** volání zobrazí okno **Add-AzureVhd** rutiny. První parametr (*cílové*) obsahuje název účtu úložiště pro testovací prostředí v následujícím formátu:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Poznamenejte si název účtu úložiště jako se používá v následujících krocích.
    
1. Připojení k účtu předplatného Azure pomocí Průzkumníka služby Storage.

    > [!TIP] 
    > 
    > Průzkumník služby Storage podporuje několik možností připojení. Tato část ukazuje, připojení k účtu úložiště spojené s předplatným Azure. Pokud chcete zobrazit další možnosti připojení podporuje Průzkumník služby Storage, najdete v článku [Začínáme se Storage Explorerem](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otevřete Průzkumníka služby Storage.
    
    1. V Průzkumníku služby Storage vyberte **nastavení účtu Azure**. 
    
        ![Nastavení účtu Azure][1]
    
    1. V levém podokně zobrazí účty Microsoft, pod kterým jste přihlášení. Pokud se chcete připojit k jinému účtu, vyberte položku **Add an account** (Přidat účet) a podle pokynů v dialogových oknech se přihlaste účtem Microsoft, který je přidružený minimálně k jednomu aktivnímu předplatnému Azure.
    
        ![Přidání účtu][2]
    
    1. Jakmile se úspěšně přihlásíte s účtem Microsoft, zobrazí se v levém podokně předplatná Azure přidružená k tomuto účtu. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Výběr **Všechna předplatná** přepínáte výběr všech nebo žádných z uvedených předplatných Azure.)
    
        ![Výběr předplatných Azure][3]
    
    1. V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.
    
        ![Vybraná předplatná Azure][4]

1. Najděte účet úložiště testovacího prostředí:

    1. V levém podokně Storage Exploreru vyhledejte a rozbalte uzel pro předplatné Azure, který vlastní testovací prostředí.
    
    1. V uzlu předplatného, rozbalte **účty úložiště**.

    1. Rozbalte uzel účtu úložiště testovacího prostředí zobrazíte uzly, aby se **kontejnery objektů Blob**, **sdílené složky**, **fronty**, a **tabulky**.
    
    1. Rozbalte **kontejnery objektů Blob** uzlu.
    
    1. Vyberte kontejner objektů blob nahrávání zobrazíte v pravém podokně.
        
        ![Nahrát adresář][5]

1. Nahrání souboru VHD pomocí Průzkumníka služby Storage:

    1. V pravém podokně Průzkumníka služby Storage, byste měli vidět seznam objektů BLOB v **nahraje** kontejneru objektů blob v účtu úložiště testovacího prostředí. Na panelu nástrojů editoru objektů blob, vyberte **nahrát** 
        
        ![Tlačítko Nahrát][6]
    
    1. Z **nahrát** rozevírací nabídky vyberte **nahrát soubory...** .
    
    1. Na **nahrávání souborů** dialogového okna, vyberte tři tečky.
        
        ![Výběr souboru][8]  

    1. Na **vyberte soubory k nahrání** dialogové okno, přejděte na požadovaný soubor virtuálního pevného disku, vyberte ji a pak vyberte **otevřít**.
    
    1. Po vrácení do **nahrávání souborů** dialogové okno Změnit **typ blobu** k **objektů Blob stránky**.
    
    1. Vyberte **Nahrát**.

        ![Výběr souboru][9]  
    
    1. Storage Explorer **protokolu aktivit** podokně se zobrazí stav stahování (spolu s odkazy na Zrušit nahrávání). Proces nahrávání souboru virtuálního pevného disku může být zdlouhavé v závislosti na velikosti souboru virtuálního pevného disku a rychlost připojení. 

        ![Stav nahrávání souboru][10]  

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí webu Azure portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí Powershellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
