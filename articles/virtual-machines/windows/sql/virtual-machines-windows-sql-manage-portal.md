---
title: Spravovat virtuální počítače s SQL serverem v Azure pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak získat přístup k prostředku virtuálního počítače SQL na webu Azure Portal pro virtuální počítač SQL serveru hostované na Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607212"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Spravovat virtuální počítače s SQL serverem v Azure pomocí webu Azure portal

Existuje nový přístupový bod ke správě virtuálního počítače s SQL serverem na Azure s využitím [webu Azure portal](https://portal.azure.com). 

**Virtuálních počítačů SQL** prostředků je nyní služba nezávislé správy, který vám umožní zobrazit všechny virtuální počítače s SQL serverem současně a změnit nastavení, které jsou vyhrazené SQL serveru: 

![Prostředek virtuální počítače SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Poznámky

- **Virtuálních počítačů SQL** prostředků je doporučená metoda můžete zobrazit a spravovat virtuální počítače s SQL serverem. Nicméně, v současné době **virtuálních počítačů SQL** prostředek nepodporuje správu [konec podpory (SESTAVENÁ)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) virtuální počítače s SQL serverem. Ke správě nastavení pro virtuální počítače SQL serveru SESTAVENÁ použijte zastaralá [karta Konfigurace systému SQL Server](#access-sql-server-configuration-tab) místo. 
- **Virtuálních počítačů SQL** prostředků je dostupná jenom pro virtuální počítače SQL serveru, který jste [zaregistrované u poskytovatele prostředků SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Přístup k prostředku virtuálního počítače SQL
Pro přístup k prostředku virtuálního počítače SQL, postupujte takto:

1. Otevřete web [Azure Portal](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Typ `SQL virtual machines` do vyhledávacího pole.
1. (Volitelné): Vyberte hvězdičku vedle **virtuálních počítačů SQL** si tuto možnost přidejte do oblíbených položek nabídky. 
1. Vyberte **virtuálních počítačů SQL**. 

   ![Nalezení virtuálních disků virtuálního počítače SQL ve všech služeb](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Tím se zobrazí seznam všech virtuálních počítačů SQL Server k dispozici v rámci předplatného. Vyberte předplatné, které chcete spravovat, spusťte **virtuálních počítačů SQL** prostředků. Pokud není virtuální počítač s SQL serverem snadno pozná, použijte vyhledávací pole. 

![Všechny dostupné virtuální počítače SQL](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Výběr virtuálního počítače s SQL serverem se otevře **virtuálních počítačů SQL** prostředků: 


![Prostředek virtuální počítače SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **Virtuálních počítačů SQL** prostředků je vyhrazené nastavení SQL serveru. Vybrat název virtuálního počítače v **virtuálního počítače** pole pro přechod na nastavení, která jsou specifická pro virtuální počítač, ale nikoli výhradně pro SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Karta Konfigurace přístupu k systému SQL Server
Na kartě Konfigurace systému SQL Server je zastaralá. V tuto chvíli je jedinou metodou, jak spravovat [konec podpory (SESTAVENÁ)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) virtuální počítače s SQL serverem a virtuální počítače SQL serveru, které nebyly [zaregistrované u poskytovatele prostředků SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).

Pro přístup k nepoužívané kartu konfigurace SQL serveru, budete muset přejít na **virtuálních počítačů** prostředků. Chcete-li to provést, postupujte takto:

1. Otevřete web [Azure Portal](https://portal.azure.com). 
1. Vyberte **všechny služby**. 
1. Typ `virtual machines` do vyhledávacího pole.
1. (Volitelné): Vyberte hvězdičku vedle **virtuálních počítačů** si tuto možnost přidejte do oblíbených položek nabídky. 
1. Vyberte **virtuálních počítačů**. 

   ![Vyhledávání pro virtuální počítače](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Tím se zobrazí seznam všech virtuálních počítačů v rámci předplatného. Vyberte předplatné, které chcete spravovat, spusťte **virtuálního počítače** prostředků. Pokud není virtuální počítač s SQL serverem snadno pozná, použijte vyhledávací pole. 
1. Vyberte **konfigurace systému SQL Server** v **nastavení** podokně pro správu SQL serveru. 

![Konfigurace systému SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


