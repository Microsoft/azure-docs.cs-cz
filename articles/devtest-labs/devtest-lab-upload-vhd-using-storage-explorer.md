---
title: Nahrání souboru VHD do Azure DevTest Labs pomocí Průzkumník služby Storage
description: Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Průzkumník služby Microsoft Azure Storage
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91282523"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Průzkumník služby Microsoft Azure Storage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs se soubory VHD dají použít k vytvoření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Tento článek ukazuje, jak použít [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD se v [části Další kroky](#next-steps) zobrazí některé články, které ukazují, jak vytvořit vlastní image z nahraného souboru VHD. Další informace o discích a virtuálních pevných discích v Azure najdete v tématu [Úvod do Managed disks](../virtual-machines/managed-disks-overview.md) .

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou odesláním souboru VHD do DevTest Labs pomocí [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Stáhněte a nainstalujte nejnovější verzi Průzkumník služby Microsoft Azure Storage](https://www.storageexplorer.com).

1. Získejte název účtu úložiště testovacího prostředí pomocí Azure Portal:

    1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
    
    1. V seznamu cvičení vyberte požadované testovací prostředí.  
    
    1. V okně testovacího prostředí vyberte **Konfigurace**. 
    
    1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VHD)**.
    
    1. V okně **vlastní image** vyberte **+ Přidat**. 
    
    1. V okně **vlastní obrázek** vyberte **VHD**.
    
    1. V okně **VHD** vyberte **nahrát VHD pomocí PowerShellu**.
    
        ![Nahrání VHD pomocí PowerShellu][0]
    
    1. Okno **nahrát obrázek pomocí okna PowerShell** zobrazí volání rutiny **Add-AzureVhd** . První parametr (*cíl*) obsahuje název účtu úložiště pro testovací prostředí v následujícím formátu:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Poznamenejte si název účtu úložiště tak, jak se používá v pozdějších krocích.
    
1. Připojte se k účtu předplatného Azure pomocí Průzkumník služby Storage.

    > [!TIP] 
    > 
    > Průzkumník služby Storage podporuje několik možností připojení. Tato část ukazuje připojení k účtu úložiště, který je přidružený k vašemu předplatnému Azure. Další možnosti připojení, které podporuje Průzkumník služby Storage, najdete v článku [Začínáme s Průzkumník služby Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otevřete Průzkumníka služby Storage.
    
    1. V Průzkumník služby Storage vyberte **Nastavení účtu Azure**. 
    
        ![Nastavení účtu Azure][1]
    
    1. V levém podokně se zobrazují účty Microsoft, ke kterým jste se přihlásili. Pokud se chcete připojit k jinému účtu, vyberte položku **Add an account** (Přidat účet) a podle pokynů v dialogových oknech se přihlaste účtem Microsoft, který je přidružený minimálně k jednomu aktivnímu předplatnému Azure.
    
        ![Přidání účtu][2]
    
    1. Jakmile se úspěšně přihlásíte s účtem Microsoft, zobrazí se v levém podokně předplatná Azure přidružená k tomuto účtu. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Výběr **všech předplatných** přepíná výběr všech nebo žádných z uvedených předplatných Azure.)
    
        ![Výběr předplatných Azure][3]
    
    1. V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.
    
        ![Vybraná předplatná Azure][4]

1. Vyhledejte účet úložiště testovacího prostředí:

    1. V levém podokně Průzkumník služby Storage Najděte a rozbalte uzel pro předplatné Azure, které vlastní testovací prostředí.
    
    1. V uzlu předplatného rozbalte položku **účty úložiště**.

    1. Rozbalte uzel účtu úložiště testovacího prostředí, který odhalí uzly pro **kontejnery objektů BLOB**, **sdílené složky**, **fronty** a **tabulky**.
    
    1. Rozbalte uzel **kontejnery objektů BLOB** .
    
    1. Vyberte kontejner objektů BLOB pro nahrávání, aby se zobrazil jeho obsah v pravém podokně.
        
        ![Odeslat adresář][5]

1. Nahrajte soubor VHD pomocí Průzkumník služby Storage:

    1. V pravém podokně Průzkumník služby Storage by se měl zobrazit seznam objektů BLOB v kontejneru objektů BLOB při **nahrávání** v účtu úložiště testovacího prostředí. Na panelu nástrojů editoru objektů BLOB vyberte **nahrát** . 
        
        ![Snímek obrazovky, který zobrazuje Průzkumník služby Storage pravé podokno s vybraným tlačítkem Odeslat.][6]
    
    1. V rozevírací nabídce **nahrát** vyberte **Odeslat soubory...**.
    
    1. V dialogovém okně **nahrát soubory** vyberte tři tečky.
        
        ![Snímek obrazovky, který zobrazuje dialogové okno nahrát soubory s vybranými třemi tečkami][8]  

    1. V dialogovém okně **Vyberte soubory k nahrání** přejděte na požadovaný soubor VHD, vyberte ho a pak vyberte **otevřít**.
    
    1. Po návratu do dialogového okna **nahrát soubory** změňte **typ objektu BLOB** na **objekt blob stránky**.
    
    1. Vyberte **Nahrát**.

        ![Vybrat soubor][9]  
    
    1. V podokně **protokolu aktivit** Průzkumník služby Storage se zobrazuje stav stahování (spolu s odkazy na zrušení nahrávání). Proces odeslání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení. 

        ![Nahrání – stav souboru][10]  

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí Azure Portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
