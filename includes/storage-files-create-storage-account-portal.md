---
title: storage-files-create-storage-account-portal
description: Návod k vytvoření účtu úložiště pro Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až do omezení kapacity účtu úložiště.

Vytvoření účtu úložiště:

1. V nabídce vlevo klikněte na **+** a vytvořte prostředek.
2. Do vyhledávacího pole (1) zadejte **účet úložiště**, vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta** (2) a pak klikněte na **Vytvořit**.
    ![Snímek obrazovky ukazující, jak by měla vypadat položka účtu úložiště v dialogovém okně hledání prostředků](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Do pole **Název** zadejte *mystorageacct* a několik náhodných čísel, dokud se nezobrazí zelená značka zaškrtnutí, která značí, že se jedná o jedinečný název. Název účtu úložiště musí být malými písmeny a globálně jedinečný. Poznamenejte si název svého účtu úložiště pro pozdější použití. 
4. V části **Model nasazení** ponechte výchozí hodnotu **Resource Manager**. Další informace o rozdílech mezi modely nasazení Azure Resource Manager a Classic najdete v článku [Vysvětlení modelů nasazení a stavu prostředků](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. V části **Druh účtu** vyberte **StorageV2**. Další informace o různých druzích účtů úložiště najdete v článku [Vysvětlení účtů úložiště Azure](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. V části **Výkon** ponechte výchozí hodnotu **Storage úrovně Standard**. Služba Soubory Azure aktuálně podporuje pouze službu Storage úrovně Standard. I když vyberete službu Storage úrovně Premium, vaše sdílená složka se uloží ve službě Storage úrovně Standard.
7. V části **Replikace** vyberte *Místně redundantní úložiště (LRS)*. 
8. V části **Vyžádání bezpečného přenosu** doporučujeme vždy vybrat **Povoleno**. Další informace o této možnosti najdete v článku [Vysvětlení šifrování během přenosu](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. V části **Předplatné** vyberte předplatné, ve kterém chcete účet úložiště vytvořit. Pokud máte pouze jedno předplatné, mělo by být nastavené jako výchozí.
10. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název *myResourceGroup*.
11. V části **Umístění** vyberte **Východní USA**.
12. V části **Virtuální sítě** ponechte výchozí možnost *Zakázáno*. 
13. Vyberte **Připnout na řídicí panel**, abyste si usnadnili hledání účtu úložiště.
14. Jakmile budete hotovi, kliknutím na **Vytvořit** spusťte nasazení.