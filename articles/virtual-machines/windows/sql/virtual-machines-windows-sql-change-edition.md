---
title: Jak provést místní upgrade z edice systému SQL Server na virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak změnit verzi virtuálního počítače s SQL serverem v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607543"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Jak provést místní upgrade z edice systému SQL Server na virtuálním počítači Azure

Tento článek popisuje, jak změnit edici systému SQL Server pro existující SQL Server na virtuálním počítači Windows v Azure. 

Edice systému SQL Server je určeno kód product key a je zadán s instalačním procesem. Edice Určuje, co [funkce](/sql/sql-server/editions-and-components-of-sql-server-2017) jsou k dispozici v rámci produktu SQL Server. Můžete změnit edici systému SQL Server pomocí instalačního média a buď downgradovat na snížení nákladů, nebo upgradujte na zapneme další funkce.

Pokud jste aktualizovali edici systému SQL Server pomocí instalačního média po registraci u poskytovatele prostředků virtuálního počítače s SQL, k aktualizaci Azure odpovídajícím způsobem fakturace by měla nastavte vlastnost edice SQL serveru prostředku virtuálního počítače SQL následujícím způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do prostředku vašeho virtuálního počítače systému SQL Server. 
1. V části **nastavení**vyberte **konfigurovat** a potom vyberte z rozevírací nabídky v rámci vaší požadovanou edici systému SQL Server **Edition**. 

   ![Změnu edice metadat](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Přečtěte si upozornění oznamující, že je nutné nejprve změnit edici systému SQL Server, a tuto vlastnost edition musí odpovídat edici systému SQL Server. 
1. Vyberte **použít** změny metadat edition. 


## <a name="prerequisites"></a>Požadavky

Provedete-li o místní změnu edice systému SQL Server, budete potřebovat následující: 

- [Předplatného Azure](https://azure.microsoft.com/free/).
- Windows [virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrované [poskytovatele prostředků SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- Instalační médium s požadovanou edici systému SQL Server. Zákazníci, kteří mají [programu software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) můžete získat z jejich instalačního média [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci nemají software assurance můžete pomocí instalačního média z marketplace image virtuálního počítače s SQL serverem, který má jejich požadovanou edici.


## <a name="upgrade-edition"></a>Upgrade edice

  > [!WARNING]
  > - **Upgrade edice systému SQL Server se restartuje službu pro SQL Server, jakož i všechny přidružené služby, jako jsou služby Analysis Services a R Services.** 

Upgrade edice systému SQL Server, získání instalačního média systému SQL Server pro požadovanou edici systému SQL Server a proveďte následující kroky:

1. Spusťte Setup.exe z instalačního média systému SQL Server. 
1. Přejděte do **údržby** a zvolte **Upgrade edice** možnost. 

   ![Upgrade edice systému SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Vyberte **Další** dokud se nedostanete **připraveno k upgradu edice** stránce a pak vyberte **upgradovat**. Okno nastavení může přestat reagovat na pár minut, zatímco tato změna se uplatní, a pak se zobrazí **Complete** stránka s potvrzením, že edice upgrade byl dokončen. 

Po dokončení upgradu edice SQL serveru byste měli upravit vlastnost "Edice" z virtuálního počítače Sql na webu Azure portal, jak je znázorněno výše; Tím se aktualizují metadata a fakturace přidružené k tomuto virtuálnímu počítači.

## <a name="downgrade-edition"></a>Přechod na starší verzi edice

  > [!WARNING]
  > - **Downgrade edici systému SQL Server vyžaduje zcela odinstalaci serveru SQL Server, což může způsobit další prostoje**. 


Přejít na nižší verzi edice systému SQL Server, je potřeba úplně odinstalujte systém SQL Server a znovu ji nainstalujte znovu s požadovanou edici instalačního média. 

Můžou provést downgrade edici systému SQL Server pomocí následujících kroků:

1. Zálohujte všechny databáze, včetně systémových databází. 
1. Systémové databáze (master, model a databázi msdb) přesuňte do nového umístění. 
1. Úplně odinstalujte systém SQL Server a všechny přidružené služby. 
1. Restartujte virtuální počítač. 
1. Nainstalujte SQL Server pomocí média s požadovanou edici systému SQL Server.
1. Nainstalujte nejnovější aktualizace service Pack a kumulativním aktualizacím.  
1. Nahraďte novou systémové databáze, které byly vytvořeny během instalace s systémové databáze, které jste přesunuli do jiného umístění. 

Po downgradovat je edice systému SQL Server, byste měli upravit vlastnost "Edice" virtuálního počítače SQL na webu Azure portal, jak je znázorněno výše; Tím se aktualizují metadata a fakturace přidružené k tomuto virtuálnímu počítači.

## <a name="remarks"></a>Poznámky

 - Vlastnost edition pro virtuální počítač SQL serveru musí odpovídat edici systému SQL Server nainstalovaná na virtuálním počítači pro všechny SQL virtuálních počítačů včetně průběžné platby a BYOL typy licencí.
 - Pokud odstraníte váš prostředek virtuálního počítače s SQL serverem, přejdete zpět na pevně zakódované edition nastavení bitové kopie.
  - Možnost měnit edici je funkce poskytovatele prostředků virtuálního počítače s SQL. Nasazení imagí marketplace na webu Azure portal automaticky zaregistruje poskytovatele prostředků virtuálního počítače s SQL serverem. Ale zákazníci, kteří jsou vlastní instalace systému SQL Server bude nutné ručně [registraci jejich virtuální počítač s SQL serverem](virtual-machines-windows-sql-register-with-resource-provider.md).
- Přidání virtuálního počítače s SQL serverem na skupinu dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. Jako takové, všechny virtuální počítače přidat do skupiny dostupnosti sady vrátí zpátky na výchozí verzi a edici bude potřeba znovu upravit.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


