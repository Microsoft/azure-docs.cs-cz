---
title: Nahrání souboru VHD pomocí AzCopy Azure DevTest Labs | Dokumentace Microsoftu
description: Nahrání souboru VHD do testovacího prostředí účtu úložiště pomocí AzCopy
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
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330847"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Nahrání souboru VHD do testovacího prostředí účtu úložiště pomocí AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Ve službě Azure DevTest Labs soubory virtuálního pevného disku slouží k vytváření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou použitím nástroje příkazového řádku azcopy k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD [další kroky části](#next-steps) uvádí některé články, které ukazují, jak vytvořit vlastní image z virtuálního pevného disku uloženého souboru. Další informace o discích a virtuálních pevných disků v Azure najdete v tématu [Úvod do managed disks](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy je nástroj příkazového řádku jen pro Windows.

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou po nahrání souboru VHD pomocí Azure DevTest Labs [AzCopy](https://aka.ms/downloadazcopy). 

1. Získejte název účtu úložiště testovacího prostředí pomocí webu Azure portal:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.  

1. V okně testovacího prostředí, vyberte **konfigurace**. 

1. V testovacím prostředí **konfigurace** okně vyberte **vlastních imagí (VHD)**.

1. Na **vlastních imagí** okno, vyberte **+ přidat**. 

1. Na **vlastní image** okně vyberte **virtuálního pevného disku**.

1. Na **virtuálního pevného disku** okně vyberte **nahrát VHD pomocí Powershellu**.

    ![Nahrát VHD pomocí Powershellu](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **Nahrát image pomocí prostředí PowerShell** volání zobrazí okno **Add-AzureVhd** rutiny. První parametr (*cílové*) obsahuje identifikátor URI pro kontejner objektů blob (*nahraje*) v následujícím formátu:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Poznamenejte si úplný identifikátor URI jako se používá v následujících krocích.

1. Nahrání souboru VHD pomocí AzCopy:
 
1. [Stáhněte a nainstalujte nejnovější verzi AzCopy](https://aka.ms/downloadazcopy).

1. Otevřete okno příkazového řádku a přejděte do instalačního adresáře nástroje AzCopy. Volitelně můžete přidat umístění instalace nástroje AzCopy do systémové cesty. Ve výchozím nastavení je nástroj AzCopy nainstalovaný na následující adresář:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Pomocí účtu klíč a objektů blob v kontejneru úložiště identifikátor URI, spusťte následující příkaz na příkazovém řádku. *VhdFileName* hodnota musí být v uvozovkách. Proces nahrávání souboru virtuálního pevného disku může být zdlouhavé v závislosti na velikosti souboru virtuálního pevného disku a rychlost připojení.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí webu Azure portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí Powershellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)