---
title: Nahrání souboru VHD do azure devtest labs pomocí AzCopy | Dokumenty společnosti Microsoft
description: Tento článek poskytuje návod k použití nástroje příkazového řádku AzCopy k nahrání souboru VHD do účtu úložiště testovacího prostředí v Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757418"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Nahrání souboru VHD do účtu úložiště laboratoře pomocí AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs, soubory Virtuálního pevného disku lze použít k vytvoření vlastní image, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou pomocí nástroje příkazového řádku AzCopy k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD uvádí [v části Další kroky](#next-steps) některé články, které ilustrují, jak vytvořit vlastní obrázek z nahraného souboru Virtuálního pevného disku. Další informace o discích a virtuálních počítačích v Azure najdete v [tématu Úvod ke spravovaným diskům.](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy je nástroj příkazového řádku pouze pro Windows.

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou nahráním souboru VHD do azure devtest labs pomocí [AzCopy](https://aka.ms/downloadazcopy). 

1. Získejte název účtu úložiště testovacího prostředí pomocí portálu Azure:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

1. Na okně testovacího prostředí vyberte **Možnost Konfigurace**. 

1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VD).**

1. V okně **Vlastní obrázky** vyberte **+Přidat**. 

1. V okně **Vlastní obrázek** vyberte **VHD**.

1. V okně **VHD** vyberte **Nahrát virtuální pevný disk pomocí prostředí PowerShell**.

    ![Nahrání virtuálního pevného disku pomocí PowerShellu](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Upload **bitové kopie pomocí** rozhraní PowerShell zobrazí volání **rutiny Add-AzureVhd.** První parametr (*Cíl*) obsahuje identifikátor URI pro kontejner objektů blob (*nahrávání*) v následujícím formátu:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Poznamenejte si úplný identifikátor URI při použití v pozdějších krocích.

1. Nahrajte soubor VHD pomocí AzCopy:
 
1. [Stáhněte a nainstalujte nejnovější verzi AzCopy](https://aka.ms/downloadazcopy).

1. Otevřete příkazové okno a přejděte do instalačního adresáře AzCopy. Volitelně můžete přidat umístění instalace AzCopy do systémové cesty. Ve výchozím nastavení je azCopy nainstalován do následujícího adresáře:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Pomocí klíče účtu úložiště a identifikátoru URI kontejneru objektů blob spusťte na příkazovém řádku následující příkaz. Hodnota *vhdFileName* musí být v uvozovkách. Proces nahrávání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru Virtuálního pevného disku pomocí portálu Azure](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)