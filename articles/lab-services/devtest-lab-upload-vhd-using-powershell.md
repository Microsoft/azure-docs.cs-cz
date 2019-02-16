---
title: Nahrání souboru VHD pomocí Powershellu Azure DevTest Labs | Dokumentace Microsoftu
description: Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Powershellu
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
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326524"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Powershellu

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Ve službě Azure DevTest Labs soubory virtuálního pevného disku slouží k vytváření vlastních imagí, které se používají ke zřízení virtuálních počítačů. Následující kroky vás provedou použitím prostředí PowerShell k nahrání souboru VHD do účtu úložiště testovacího prostředí. Po nahrání souboru VHD [další kroky části](#next-steps) uvádí některé články, které ukazují, jak vytvořit vlastní image z virtuálního pevného disku uloženého souboru. Další informace o discích a virtuálních pevných disků v Azure najdete v tématu [Úvod do managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou po nahrání souboru VHD do Azure DevTest Labs pomocí Powershellu. 

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.  

1. V okně testovacího prostředí, vyberte **konfigurace**. 

1. V testovacím prostředí **konfigurace** okně vyberte **vlastních imagí (VHD)**.

1. Na **vlastních imagí** okno, vyberte **+ přidat**. 

1. Na **vlastní image** okně vyberte **virtuálního pevného disku**.

1. Na **virtuálního pevného disku** okně vyberte **nahrát VHD pomocí Powershellu**.

    ![Nahrát VHD pomocí Powershellu](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Na **nahrát image pomocí prostředí PowerShell** okno, zkopírujte vygenerovaný skript prostředí PowerShell do textového editoru.

1. Upravit **LocalFilePath** parametr **Add-AzureVhd** rutiny tak, aby odkazoval na umístění soubor virtuálního pevného disku, který chcete nahrát.

1. Na příkazový řádek Powershellu, spusťte **Add-AzureVhd** rutiny (s upravenou **LocalFilePath** parametr).

> [!WARNING] 
> 
> Proces nahrávání souboru virtuálního pevného disku může být zdlouhavé v závislosti na velikosti souboru virtuálního pevného disku a rychlost připojení.

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí webu Azure portal](devtest-lab-create-template.md)
- [Vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí Powershellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
