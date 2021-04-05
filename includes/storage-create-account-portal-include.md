---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98109548"
---
Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

1. V nabídce webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Účty úložiště**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **účty úložiště**.
1. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Na kartě **základy** vyberte předplatné, ve kterém chcete účet úložiště vytvořit.
1. V poli **Skupina prostředků** vyberte požadovanou skupinu prostředků nebo vytvořte novou skupinu prostředků.  Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název musí mít také délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Vyberte úroveň výkonu. Výchozí úroveň je *Standard*.
1. Nastavte pole **druh účtu** na *Storage v2 (Obecné-účel v2)*.
1. Určete, jak se bude účet úložiště replikovat. Výchozí možnost replikace je *geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)*. Další informace o dostupných možnostech replikace najdete v tématu [Azure Storage redundance](../articles/storage/common/storage-redundancy.md).
1. Další možnosti jsou k dispozici na kartách **sítě**, **Ochrana dat**, **Upřesnit** a **značky** . Chcete-li použít Azure Data Lake Storage, zvolte kartu **Upřesnit** a pak nastavte **hierarchický obor názvů** na **Enabled**. Další informace najdete v tématu [Azure Data Lake Storage Gen2 Úvod](../articles/storage/blobs/data-lake-storage-introduction.md) .
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Následující obrázek ukazuje nastavení na kartě **základy** pro nový účet úložiště:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Snímek obrazovky ukazující postup vytvoření účtu úložiště na webu Azure Portal":::
