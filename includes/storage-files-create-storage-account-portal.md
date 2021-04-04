---
title: storage-files-create-storage-account-portal
description: Vytvoření účtu úložiště pro Azure Files
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509488"
---
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

Vytvoření účtu úložiště:

1. V nabídce vlevo vyberte **+** vytvořit prostředek.
1. Do vyhledávacího pole zadejte **storage account**, vyberte **Storage account - blob, file, table, queue** a potom vyberte **Vytvořit**.
    ![Snímek obrazovky ukazující, jak by měla vypadat položka účtu úložiště v dialogovém okně hledání prostředků](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. Do pole **Název** zadejte *mystorageacct* a několik náhodných čísel, dokud se nezobrazí zelená značka zaškrtnutí, která značí, že se jedná o jedinečný název. Název účtu úložiště musí být malými písmeny a globálně jedinečný. Název svého účtu úložiště si poznamenejte. Budete jej později potřebovat. 
1. V části **model nasazení** ponechte výchozí hodnotu **Správce prostředků**. Další informace o rozdílech mezi Azure Resource Managerem a klasickým modelem nasazení najdete v článku [Vysvětlení modelů nasazení a stavu prostředků](../articles/azure-resource-manager/management/deployment-models.md).
1. V části **výkon** ponechte výchozí hodnotu **Standard**.
    
    > [!NOTE]
    > V tomto rychlém startu se vytvoří standardní sdílená složka, ale pokud jste chtěli použít sdílené složky Premium, vyberte místo toho možnost **Premium** .

1. V části **Druh účtu** vyberte **StorageV2**. Další informace o různých druzích účtů úložiště najdete v článku [Vysvětlení účtů úložiště Azure](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

    > [!NOTE]
    > V tomto rychlém startu se vytvoří účet pro obecné účely v2. Pokud jste chtěli použít sdílené složky Premium, vyberte místo toho možnost **úložiště** souborů.

1. V části **Replikace** vyberte **Místně redundantní úložiště (LRS)**. 
1. V části **Vyžádání bezpečného přenosu** doporučujeme vždy vybrat **Povoleno**. Další informace o této možnosti najdete v článku [Vysvětlení šifrování během přenosu](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
1. V části **Předplatné** vyberte předplatné, které jste použili k vytvoření účtu úložiště. Pokud máte pouze jedno předplatné, mělo by být nastavené jako výchozí.
1. V poli **Skupina prostředků** vyberte **Vytvořit novou**. Jako název zadejte *myResourceGroup*.
1. V části **Umístění** vyberte **USA – východ**.
1. V části **Virtuální sítě** ponechte výchozí možnost **Zakázáno**. 
1. Vyberte **Připnout na řídicí panel**, abyste si usnadnili hledání účtu úložiště.
1. Až budete hotoví, kliknutím na **Vytvořit** spusťte nasazení.