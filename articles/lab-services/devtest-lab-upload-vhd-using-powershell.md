---
title: Nahrání souboru Virtuálního pevného disku do laboratoří Azure DevTest pomocí Prostředí PowerShell | Dokumenty společnosti Microsoft
description: Tento článek obsahuje návod, který ukazuje, jak nahrát soubor VHD do Azure DevTest Labs pomocí PowerShellu.
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
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759751"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí prostředí PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

V Azure DevTest Labs, soubory Virtuálního pevného disku lze použít k vytvoření vlastní image, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou pomocí prostředí PowerShell k nahrání souboru Virtuálního pevného disku do účtu úložiště testovacího prostředí. Po nahrání souboru VHD uvádí [v části Další kroky](#next-steps) některé články, které ilustrují, jak vytvořit vlastní obrázek z nahraného souboru Virtuálního pevného disku. Další informace o discích a virtuálních počítačích v Azure najdete v [tématu Úvod ke spravovaným diskům.](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou nahráním souboru Virtuálního pevného disku do azure devtest labs pomocí PowerShellu. 

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

1. Na okně testovacího prostředí vyberte **Možnost Konfigurace**. 

1. V okně **Konfigurace** testovacího prostředí vyberte **vlastní image (VD).**

1. V okně **Vlastní obrázky** vyberte **+Přidat**. 

1. V okně **Vlastní obrázek** vyberte **VHD**.

1. V okně **VHD** vyberte **Nahrát virtuální pevný disk pomocí prostředí PowerShell**.

    ![Nahrání virtuálního pevného disku pomocí PowerShellu](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. V okně **Nahrát obrázek pomocí** rozhraní PowerShell zkopírujte vygenerovaný skript PowerShellu do textového editoru.

1. Upravte parametr **LocalFilePath** rutiny **Add-AzureVhd** tak, aby ukazoval na umístění souboru VHD, který chcete nahrát.

1. Na výzvu prostředí PowerShell spusťte **rutinu Add-AzureVhd** (s upraveným parametrem **LocalFilePath).**

> [!WARNING] 
> 
> Proces nahrávání souboru VHD může být zdlouhavý v závislosti na velikosti souboru VHD a rychlosti připojení.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image v Azure DevTest Labs ze souboru Virtuálního pevného disku pomocí portálu Azure](devtest-lab-create-template.md)
- [Vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
