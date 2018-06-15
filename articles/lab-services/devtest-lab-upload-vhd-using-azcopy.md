---
title: Nahrání souboru virtuálního pevného disku do Azure DevTest Labs pomocí AzCopy | Microsoft Docs
description: Nahrání souboru virtuálního pevného disku do testovacího prostředí na účtu úložiště pomocí nástroje AzCopy
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
ms.openlocfilehash: e35686e7ba7c2e88d62930082d39856673a661b6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787980"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Nahrání souboru virtuálního pevného disku do testovacího prostředí na účtu úložiště pomocí nástroje AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs soubory virtuálního pevného disku lze vytvořit vlastní Image, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou pomocí příkazového řádku azcopy Pokud chcete nahrát soubor virtuálního pevného disku do účtu úložiště testovacího prostředí. Jakmile jste odeslali souboru virtuálního pevného disku [další kroky části](#next-steps) jsou uvedeny některé články, které ukazují, jak vytvořit vlastní image z nahrávaný soubor virtuálního pevného disku. Další informace o disky a virtuální pevné disky v Azure najdete v tématu [o disky a virtuální pevné disky pro virtuální počítače](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> AzCopy je nástroj příkazového řádku pouze pro systém Windows.

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou nahrání souboru virtuálního pevného disku do Azure DevTest Labs pomocí [AzCopy](http://aka.ms/downloadazcopy). 

1. Získání názvu účtu úložiště v prostředí pomocí portálu Azure:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.

1. Ze seznamu labs vyberte požadované testovací prostředí.  

1. V okně v prostředí, vyberte **konfigurace**. 

1. V testovacím prostředí **konfigurace** vyberte **vlastní Image (VHD)**.

1. Na **vlastní image** okně vyberte **+ přidat**. 

1. Na **vlastní image** vyberte **virtuálního pevného disku**.

1. Na **virtuálního pevného disku** vyberte **nahrát virtuální pevný disk pomocí prostředí PowerShell**.

    ![Nahrání virtuálního pevného disku pomocí prostředí PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **Odeslat bitovou kopii pomocí prostředí PowerShell** zobrazuje volání **přidat AzureVhd** rutiny. První parametr (*cílové*) obsahuje identifikátor URI pro kontejner objektů blob (*odešle*) v následujícím formátu:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Poznamenejte si úplný identifikátor URI jako se používá v dalších krocích.

1. Nahrajte soubor virtuálního pevného disku pomocí nástroje AzCopy:
 
1. [Stáhněte a nainstalujte nejnovější verzi AzCopy](http://aka.ms/downloadazcopy).

1. Otevřete okno příkazového řádku a přejděte do instalačního adresáře nástroje AzCopy. Umístění instalace AzCopy můžete volitelně přidat cestu v systému. Ve výchozím nastavení je AzCopy nainstalovat na následující adresář:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Pomocí účtu klíč a objektů blob kontejneru úložiště identifikátor URI, spusťte následující příkaz na příkazovém řádku. *VhdFileName* hodnota musí být v uvozovkách. Proces odesílání soubor virtuálního pevného disku může být náročná v závislosti na velikosti souboru virtuálního pevného disku a rychlost připojení.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru virtuálního pevného disku pomocí portálu Azure](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru virtuálního pevného disku pomocí prostředí PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)