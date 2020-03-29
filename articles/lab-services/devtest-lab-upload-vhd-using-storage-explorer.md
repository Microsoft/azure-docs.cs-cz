---
title: Nahrání souboru Virtuálního pevného disku do laboratoře Azure DevTest pomocí Průzkumníka úložiště
description: Nahrání souboru Virtuálního pevného disku do účtu úložiště testovacího prostředí pomocí Průzkumníka úložiště Microsoft Azure
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170376"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Nahrání souboru Virtuálního pevného disku do účtu úložiště testovacího prostředí pomocí Průzkumníka úložiště Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs, soubory Virtuálního pevného disku lze použít k vytvoření vlastní image, které se používají ke zřízení virtuálních počítačů. Tento článek ukazuje, jak pomocí [Průzkumníka úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) nahrát soubor Virtuálního pevného disku do účtu úložiště testovacího prostředí. Po nahrání souboru VHD uvádí [v části Další kroky](#next-steps) některé články, které ilustrují, jak vytvořit vlastní obrázek z nahraného souboru Virtuálního pevného disku. Další informace o discích a virtuálních počítačích v Azure najdete v [tématu Úvod ke spravovaným diskům.](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou nahráním souboru Virtuálního pevného disku do devTest Labs pomocí [Průzkumníka úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Stáhněte a nainstalujte nejnovější verzi Průzkumníka úložiště Microsoft Azure](https://www.storageexplorer.com).

1. Získejte název účtu úložiště testovacího prostředí pomocí portálu Azure:

    1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
    
    1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  
    
    1. Na okně testovacího prostředí vyberte **Možnost Konfigurace**. 
    
    1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VD).**
    
    1. V okně **Vlastní obrázky** vyberte **+Přidat**. 
    
    1. V okně **Vlastní obrázek** vyberte **VHD**.
    
    1. V okně **VHD** vyberte **Nahrát virtuální pevný disk pomocí prostředí PowerShell**.
    
        ![Nahrání virtuálního pevného disku pomocí PowerShellu][0]
    
    1. Upload **bitové kopie pomocí** rozhraní PowerShell zobrazí volání **rutiny Add-AzureVhd.** První parametr (*Cíl*) obsahuje název účtu úložiště pro testovací prostředí v následujícím formátu:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Poznamenejte si název účtu úložiště, jak se používá v pozdějších krocích.
    
1. Připojte se k účtu předplatného Azure pomocí Průzkumníka úložiště.

    > [!TIP] 
    > 
    > Průzkumník úložiště podporuje několik možností připojení. Tato část znázorňuje připojení k účtu úložiště přidruženému k vašemu předplatnému Azure. Další možnosti připojení podporované Průzkumníkem úložišť naleznete v článku [Začínáme s Průzkumníkem úložišť](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otevřete Průzkumníka služby Storage.
    
    1. V Průzkumníkovi úložiště vyberte **nastavení účtu Azure**. 
    
        ![Nastavení účtu Azure][1]
    
    1. V levém podokně se zobrazí účty Microsoft, ke které jste se přihlásili. Pokud se chcete připojit k jinému účtu, vyberte položku **Add an account** (Přidat účet) a podle pokynů v dialogových oknech se přihlaste účtem Microsoft, který je přidružený minimálně k jednomu aktivnímu předplatnému Azure.
    
        ![Přidání účtu][2]
    
    1. Jakmile se úspěšně přihlásíte s účtem Microsoft, zobrazí se v levém podokně předplatná Azure přidružená k tomuto účtu. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Výběrvšech **předplatných** přepíná výběr všech nebo žádných z uvedených předplatných Azure.)
    
        ![Výběr předplatných Azure][3]
    
    1. V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.
    
        ![Vybraná předplatná Azure][4]

1. Vyhledejte účet úložiště v testovacím prostředí:

    1. V levém podokně Průzkumníka úložiště vyhledejte a rozbalte uzel pro předplatné Azure, které vlastní testovací prostředí.
    
    1. V uzlu předplatného rozbalte **položku Účty úložiště**.

    1. Rozbalte uzel účtu úložiště testovacího prostředí a odhalte uzly pro **kontejnery objektů BLOB**, **sdílené složky**, **fronty**a **tabulky**.
    
    1. Rozbalte uzel **kontejnerů objektů blob.**
    
    1. Vyberte kontejner objektů blob pro nahrávání, chcete-li zobrazit jeho obsah v pravém podokně.
        
        ![Nahrát adresář][5]

1. Nahrajte soubor VHD pomocí Průzkumníka úložiště:

    1. V pravém podokně Průzkumníka úložiště byste měli vidět výpis objektů BLOB v kontejneru objektů blob **odesílání** účtu úložiště účtu testovacího prostředí. Na panelu nástrojů Editoru objektů blob vyberte **Nahrát** 
        
        ![Tlačítko Nahrát][6]
    
    1. V rozevírací nabídce **Nahrát** vyberte **Nahrát soubory...**.
    
    1. V dialogovém okně **Nahrát soubory** vyberte tři tečky.
        
        ![Vybrat soubor][8]  

    1. V dialogovém okně **Vybrat soubory k nahrání** vyhledejte požadovaný soubor VHD, vyberte ho a pak vyberte **Otevřít**.
    
    1. Po návratu do dialogového okna **Nahrát soubory** změňte **typ objektu blob** na **Objekt blob stránky**.
    
    1. Vyberte **Nahrát**.

        ![Vybrat soubor][9]  
    
    1. Podokno **Protokol aktivit** Průzkumníka úložiště zobrazuje stav stahování (spolu s odkazy na zrušení nahrávání). Proces nahrávání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení. 

        ![Stav odeslat soubor][10]  

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru Virtuálního pevného disku pomocí portálu Azure](devtest-lab-create-template.md)
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
