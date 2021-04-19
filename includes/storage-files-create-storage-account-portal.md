---
title: storage-files-create-storage-account-portal
description: Vytvoření účtu úložiště pro Azure Files
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717826"
---
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

Vytvoření účtu úložiště:

1. V nabídce vlevo vyberte **+** vytvořit prostředek.
1. Vyberte **účet úložiště** a vytvořte účet úložiště.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Snímek obrazovky s možností účtu úložiště v okně vytvořit prostředek" lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. Do pole **Název** zadejte *mystorageacct* a několik náhodných čísel, dokud se nezobrazí zelená značka zaškrtnutí, která značí, že se jedná o jedinečný název. Název účtu úložiště musí být malými písmeny a globálně jedinečný. Název svého účtu úložiště si poznamenejte. Budete jej později potřebovat. 
1. V části **výkon** ponechte výchozí hodnotu **Standard**.
1. V části **Replikace** vyberte **Místně redundantní úložiště (LRS)**.
1. V části **Předplatné** vyberte předplatné, které jste použili k vytvoření účtu úložiště. Pokud máte pouze jedno předplatné, mělo by být nastavené jako výchozí.
1. V poli **Skupina prostředků** vyberte **Vytvořit novou**. Jako název zadejte *myResourceGroup*.
1. V části **Umístění** vyberte **USA – východ**.
1. Až budete hotoví, kliknutím na **Vytvořit** spusťte nasazení.