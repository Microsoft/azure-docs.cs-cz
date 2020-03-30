---
title: Správa virtuálních počítačů SQL Serveru v Azure pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak získat přístup k prostředkům virtuálního počítače SQL na webu Azure Portal pro virtuální počítač SQL Server hostovaný v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243208"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Správa virtuálních počítačů SQL Serveru v Azure pomocí portálu Azure

Na [webu Azure Portal](https://portal.azure.com)je prostředek **virtuálních počítačů SQL** nezávislou službou pro správu. Můžete ji použít k zobrazení všech virtuálních počítačů SQL Server současně a upravit nastavení vyhrazené pro SQL Server: 

![Prostředek virtuálních počítačů SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Poznámky

- Doporučujeme použít prostředek **virtuálních počítačů SQL** k zobrazení a správě virtuálních počítačů SQL Serveru v Azure. Ale v současné době prostředek **virtuálních počítačů SQL** nepodporuje správu virtuálních počítačů SQL Server [na konci podpory.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) Chcete-li spravovat nastavení virtuálních počítačů SQL Server s ukončenou podporou, použijte místo toho kartu konfigurace zastaralého [serveru SQL Server.](#access-the-sql-server-configuration-tab) 
- Prostředek **virtuálních počítačů SQL** je k dispozici pouze pro virtuální počítače SQL Server, které se [zaregistrovaly u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Přístup k prostředku virtuálních počítačů SQL
Chcete-li získat přístup k prostředku **virtuálních počítačů SQL,** postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Do vyhledávacího pole zadejte **virtuální počítače SQL.**
1. (Volitelné): Vyberte hvězdičku vedle **virtuálních počítačů SQL** a přidejte tuto možnost do nabídky **Oblíbené.** 
1. Vyberte **virtuální počítače SQL**. 

   ![Vyhledání virtuálních počítačů SQL Serveru ve všech službách](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portál obsahuje seznam všech virtuálních aplikací SQL Server, které jsou k dispozici v rámci předplatného. Vyberte ten, který chcete spravovat k otevření prostředku **virtuálních počítačů SQL.** Pokud se váš virtuální počítač SQL Server nezobrazuje, použijte vyhledávací pole. 

   ![Všechny dostupné virtuální servery SQL Server](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Výběrem virtuálního počítače SQL Server se otevře prostředek **virtuálních počítačů SQL:** 


   ![Prostředek virtuálních počítačů SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> Prostředek **virtuálních počítačů SQL** je určen pro vyhrazená nastavení serveru SQL Server. Vyberte název virtuálního počítače v poli **Virtuální počítač** otevřít nastavení, které jsou specifické pro virtuální počítač, ale není výhradní sql server. 

## <a name="access-the-sql-server-configuration-tab"></a>Přístup k kartě konfigurace serveru SQL Server
Karta **konfigurace serveru SQL Server** byla zastaralá. V tuto chvíli je to jediná metoda pro správu [virtuálních](virtual-machines-windows-sql-server-2008-eos-extend-support.md) stránek SQL Server na konci podpory a virtuálních ms SQL Server, které nebyly [zaregistrovány u poskytovatele prostředků virtuálního virtuálního připojení SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Chcete-li získat přístup k zastaralé **konfigurační** kartě SQL Server, přejděte na prostředek **virtuálních počítačů.** Použijte k tomu následující postup:

1. Otevřete [portál Azure](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Do vyhledávacího pole zadejte **virtuální počítače.**
1. (Volitelné): Vyberte hvězdičku vedle **virtuálních počítačů** a přidejte tuto možnost do nabídky **Oblíbené.** 
1. Vyberte **Virtuální počítače**. 

   ![Hledání virtuálních počítačů](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portál uvádí všechny virtuální počítače v předplatném. Vyberte ten, který chcete spravovat k otevření prostředku **virtuálních počítačů.** Pokud se váš virtuální počítač SQL Server nezobrazuje, použijte vyhledávací pole. 
1. Vyberte **konfiguraci serveru SQL Server** v podokně **Nastavení** a spravujte virtuální počítač SQL Server. 

   ![Konfigurace SQL Serveru](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


