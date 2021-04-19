---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718157"
---
Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

1. V nabídce webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Účty úložiště**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **účty úložiště**.
1. V okně **účty úložiště** , které se zobrazí, vyberte **+ Nová**.
1. V okně **základy** vyberte předplatné, ve kterém chcete účet úložiště vytvořit.
1. V poli **Skupina prostředků** vyberte požadovanou skupinu prostředků nebo vytvořte novou skupinu prostředků.  Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název musí mít také délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte oblast pro svůj účet úložiště nebo použijte výchozí oblast.
1. Vyberte úroveň výkonu. Výchozí úroveň je *Standard*.
1. Určete, jak se bude účet úložiště replikovat. Výchozí možnost redundance je *geograficky redundantní úložiště (GRS)*. Další informace o dostupných možnostech replikace najdete v tématu [Azure Storage redundance](../articles/storage/common/storage-redundancy.md).
1. Další možnosti jsou k dispozici v okně **Pokročilé**, **sítě**, **Ochrana dat** a **značky** . Chcete-li použít Azure Data Lake Storage, zvolte okno **Upřesnit** a pak nastavte **hierarchický obor názvů** na **Enabled**. Další informace najdete v tématu [Azure Data Lake Storage Gen2 Úvod](../articles/storage/blobs/data-lake-storage-introduction.md) .
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Následující obrázek ukazuje nastavení v okně **základy** pro nový účet úložiště:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Snímek obrazovky ukazující, jak vytvořit účet úložiště v Azure Portal." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
