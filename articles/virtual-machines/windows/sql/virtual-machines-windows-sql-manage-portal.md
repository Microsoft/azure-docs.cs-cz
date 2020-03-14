---
title: Správa virtuálních počítačů s SQL Server v Azure pomocí Azure Portal | Microsoft Docs
description: Naučte se, jak získat přístup k prostředku virtuálního počítače SQL v Azure Portal pro SQL Server virtuální počítač hostovaný v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243208"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Správa virtuálních počítačů s SQL Server v Azure pomocí Azure Portal

V [Azure Portal](https://portal.azure.com)prostředek **virtuálního počítače SQL** je nezávislá služba pro správu. Můžete ji použít k zobrazení všech SQL Server virtuálních počítačů současně a upravit nastavení vyhrazená pro SQL Server: 

![Prostředek virtuálních počítačů SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Poznámky

- K zobrazení a správě vašich SQL Server virtuálních počítačů v Azure doporučujeme použít prostředek **SQL Virtual Machine** . Ale v současné době prostředek **virtuálních počítačů s SQL** nepodporuje správu SQL Server virtuálních počítačů na [konci podpory](virtual-machines-windows-sql-server-2008-eos-extend-support.md) . Pokud chcete spravovat nastavení pro SQL Server virtuálních počítačů na konci, použijte místo toho kartu nepoužívané [SQL Server konfigurace](#access-the-sql-server-configuration-tab) . 
- Prostředek **SQL Virtual Machine** je dostupný jenom pro SQL Server virtuální počítače, které jsou [zaregistrované u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Přístup k prostředku virtuálních počítačů SQL
Pokud chcete získat přístup k prostředku **virtuálních počítačů SQL** , udělejte toto:

1. Otevřete [portál Azure](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Do vyhledávacího pole zadejte **virtuální počítače SQL** .
1. (Volitelné): vyberte hvězdičku vedle možnosti **virtuální počítače SQL** a přidejte tuto možnost do nabídky **Oblíbené** . 
1. Vyberte **virtuální počítače SQL**. 

   ![Najít SQL Server virtuálních počítačů ve všech službách](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portál zobrazí seznam všech SQL Server virtuálních počítačů dostupných v rámci předplatného. Vyberte tu, kterou chcete spravovat, a otevřete prostředek **virtuální počítače SQL** . Pokud se váš SQL Server virtuální počítač nezobrazuje, použijte vyhledávací pole. 

   ![Všechny dostupné SQL Server virtuální počítače](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Výběrem SQL Server virtuálního počítače otevřete prostředek **virtuálních počítačů SQL** : 


   ![Prostředek virtuálních počítačů SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> Prostředek **SQL Virtual Machine** je pro vyhrazená nastavení SQL Server. Vyberte název virtuálního počítače v poli **virtuální počítač** a otevřete tak nastavení specifická pro daný virtuální počítač, ale ne výhradně pro SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Přístup k kartě Konfigurace SQL Server
Karta **konfigurace SQL Server** je zastaralá. V tuto chvíli je jedinou metodou pro správu SQL Server virtuálních počítačů na [konci podpory](virtual-machines-windows-sql-server-2008-eos-extend-support.md) a SQL Server virtuálních počítačů, které nejsou [zaregistrované u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Pokud chcete získat přístup k zastaralé **SQL Server kartě Konfigurace** , přejděte do prostředku **virtuální počítače** . Použijte k tomu následující postup:

1. Otevřete [portál Azure](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Do vyhledávacího pole zadejte **virtuální počítače** .
1. (Volitelné): Pokud chcete přidat tuto možnost do nabídky **Oblíbené** , vyberte hvězdičku vedle možnosti **virtuální počítače** . 
1. Vyberte **virtuální počítače**. 

   ![Hledání virtuálních počítačů](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portál zobrazí seznam všech virtuálních počítačů v rámci předplatného. Vyberte tu, kterou chcete spravovat, a otevřete prostředek **virtuálních počítačů** . Pokud se váš SQL Server virtuální počítač nezobrazuje, použijte vyhledávací pole. 
1. V podokně **Nastavení** vyberte **SQL Server konfigurace** , abyste mohli spravovat SQL Server virtuální počítač. 

   ![Konfigurace SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


