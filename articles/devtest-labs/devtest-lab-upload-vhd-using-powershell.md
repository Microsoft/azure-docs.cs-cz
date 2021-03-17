---
title: Nahrání souboru VHD do Azure DevTest Labs pomocí PowerShellu | Microsoft Docs
description: Tento článek obsahuje návod, který vám ukáže, jak nahrát soubor VHD do Azure DevTest Labs pomocí PowerShellu.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650753"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí PowerShellu

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs se soubory VHD dají použít k vytvoření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou použitím prostředí PowerShell k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD se v [části Další kroky](#next-steps) zobrazí některé články, které ukazují, jak vytvořit vlastní image z nahraného souboru VHD. Další informace o discích a virtuálních pevných discích v Azure najdete v tématu [Úvod do Managed disks](../virtual-machines/managed-disks-overview.md) .

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou odesláním souboru VHD do Azure DevTest Labs pomocí prostředí PowerShell. 

1. Přihlaste se k [portálu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. V okně testovacího prostředí vyberte **Konfigurace**. 

1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VHD)**.

1. V okně **vlastní image** vyberte **+ Přidat**. 

1. V okně **vlastní obrázek** vyberte **VHD**.

1. V okně **VHD** vyberte **nahrát VHD pomocí PowerShellu**.

    ![Nahrání VHD pomocí PowerShellu](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. V okně **nahrát obrázek pomocí prostředí PowerShell** Zkopírujte vygenerovaný skript prostředí PowerShell do textového editoru.

1. Upravte parametr **LocalFilePath** rutiny **Add-AzureVhd** tak, aby ODKAZOVAL na umístění souboru VHD, který chcete nahrát.

1. Na příkazovém řádku PowerShellu spusťte rutinu **Add-AzureVhd** (s upraveným parametrem **LocalFilePath** ).

> [!WARNING] 
> 
> Proces odeslání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí Azure Portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
