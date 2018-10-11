---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d59cb64e1499f703bf6913ab422d0f979caca324
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47008724"
---
Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Všechny služby**. Potom přejděte dolů do části **Úložiště** a zvolte **Účty úložiště**. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** klikněte na **Vytvořit nový**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](./media/storage-create-account-portal-include/create-resource-group.png)

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Tato pole nechte nastavená na výchozí hodnoty:
    - Pole **Model nasazení** má výchozí nastavení **Resource Manager**.
    - Pole **Výkon** má výchozí nastavení **Standardní**.
    - Pole **Druh účtu** má výchozí nastavení **StorageV2 (univerzální v2)**.
    - Pole **Replikace** má výchozí nastavení **Místně redundantní úložiště (LRS)**.
    - Pole **Úroveň přístupu** má výchozí nastavení **Horká**.

1. Kliknutím na **Zkontrolovat a vytvořit** si prohlédněte nastavení účtu úložiště a vytvořte účet.

Další informace o typech účtů úložiště a dalším nastavení účtu úložiště najdete v článku s [přehledem účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
