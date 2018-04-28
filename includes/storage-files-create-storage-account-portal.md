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
ms.openlocfilehash: a09e9206b3ecd23a6208572a88c16b149033f123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

Vytvoření účtu úložiště:

1. V nabídce vlevo vyberte **+** a vytvořte prostředek.
2. Do vyhledávacího pole zadejte **storage account**, vyberte **Storage account - blob, file, table, queue** a potom vyberte **Vytvořit**.
    ![Snímek obrazovky ukazující, jak by měla vypadat položka účtu úložiště v dialogovém okně hledání prostředků](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Do pole **Název** zadejte *mystorageacct* a několik náhodných čísel, dokud se nezobrazí zelená značka zaškrtnutí, která značí, že se jedná o jedinečný název. Název účtu úložiště musí být malými písmeny a globálně jedinečný. Název svého účtu úložiště si poznamenejte. Budete jej později potřebovat. 
4. V části **Model nasazení** ponechte výchozí hodnotu **Resource Manager**. Další informace o rozdílech mezi Azure Resource Managerem a klasickým modelem nasazení najdete v článku [Vysvětlení modelů nasazení a stavu prostředků](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. V části **Druh účtu** vyberte **StorageV2**. Další informace o různých druzích účtů úložiště najdete v článku [Vysvětlení účtů úložiště Azure](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. V části **Výkon** ponechte výchozí hodnotu **Storage úrovně Standard**. Služba Azure Files aktuálně podporuje pouze službu Storage úrovně Standard. I když vyberete službu Azure Premium Storage, vaše sdílená složka se uloží ve službě Storage úrovně Standard.
7. V části **Replikace** vyberte **Místně redundantní úložiště (LRS)**. 
8. V části **Vyžádání bezpečného přenosu** doporučujeme vždy vybrat **Povoleno**. Další informace o této možnosti najdete v článku [Vysvětlení šifrování během přenosu](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. V části **Předplatné** vyberte předplatné, které jste použili k vytvoření účtu úložiště. Pokud máte pouze jedno předplatné, mělo by být nastavené jako výchozí.
10. V poli **Skupina prostředků** vyberte **Vytvořit novou**. Jako název zadejte *myResourceGroup*.
11. V části **Umístění** vyberte **Východní USA**.
12. V části **Virtuální sítě** ponechte výchozí možnost **Zakázáno**. 
13. Vyberte **Připnout na řídicí panel**, abyste si usnadnili hledání účtu úložiště.
14. Až budete hotoví, kliknutím na **Vytvořit** spusťte nasazení.