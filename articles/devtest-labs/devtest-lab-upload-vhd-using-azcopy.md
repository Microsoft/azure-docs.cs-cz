---
title: Nahrání souboru VHD do Azure DevTest Labs pomocí AzCopy | Microsoft Docs
description: Tento článek poskytuje návod k použití nástroje příkazového řádku AzCopy k nahrání souboru VHD do účtu úložiště testovacího prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650770"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs se soubory VHD dají použít k vytvoření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou použitím nástroje příkazového řádku AzCopy k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD se v [části Další kroky](#next-steps) zobrazí některé články, které ukazují, jak vytvořit vlastní image z nahraného souboru VHD. Další informace o discích a virtuálních pevných discích v Azure najdete v tématu [Úvod do Managed disks](../virtual-machines/managed-disks-overview.md) .

> [!NOTE] 
>  
> AzCopy je nástroj příkazového řádku pouze pro Windows.

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou odesláním souboru VHD do Azure DevTest Labs pomocí [AzCopy](https://aka.ms/downloadazcopy). 

1. Získejte název účtu úložiště testovacího prostředí pomocí Azure Portal:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. V okně testovacího prostředí vyberte **Konfigurace**. 

1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VHD)**.

1. V okně **vlastní image** vyberte **+ Přidat**. 

1. V okně **vlastní obrázek** vyberte **VHD**.

1. V okně **VHD** vyberte **nahrát VHD pomocí PowerShellu**.

    ![Nahrání VHD pomocí PowerShellu](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Okno **nahrát obrázek pomocí okna PowerShell** zobrazí volání rutiny **Add-AzureVhd** . První parametr (*cíl*) obsahuje identifikátor URI pro kontejner objektů BLOB (*nahrávání*) v následujícím formátu:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Poznamenejte si úplný identifikátor URI, protože se používá v pozdějších krocích.

1. Nahrajte soubor VHD pomocí AzCopy:
 
1. [Stáhněte a nainstalujte si nejnovější verzi AzCopy](https://aka.ms/downloadazcopy).

1. Otevřete příkazové okno a přejděte do instalačního adresáře AzCopy. Volitelně můžete přidat umístění instalace AzCopy do systémové cesty. Ve výchozím nastavení se AzCopy nainstaluje do následujícího adresáře:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Pomocí klíče účtu úložiště a identifikátoru URI kontejneru objektů BLOB spusťte na příkazovém řádku následující příkaz. Hodnota *vhdFileName* musí být v uvozovkách. Proces odeslání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí Azure Portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)