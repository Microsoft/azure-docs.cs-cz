---
title: Změna edice serveru SQL Server na místě
description: Přečtěte si, jak změnit edici virtuálního počítače SQL Serveru v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605455"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Změna edice SQL Serveru na místě na virtuálním počítači Azure

Tento článek popisuje, jak změnit edici SQL Serveru na virtuálním počítači s Windows v Azure. 

Edice serveru SQL Server je určena kódem Product Key a je určena během procesu instalace pomocí instalačního média. Vydání určuje, jaké [funkce](/sql/sql-server/editions-and-components-of-sql-server-2017) jsou k dispozici v produktu SQL Server. Můžete změnit sql server vydání s instalačnímédia a buď downgrade snížit náklady nebo upgrade povolit další funkce.

Po změně edice SQL Serveru interně na virtuální počítač SQL Server, pak je nutné aktualizovat edition vlastnost SQL Server na webu Azure portal pro účely fakturace. 

## <a name="prerequisites"></a>Požadavky

Chcete-li provést změnu na místě vydání sql serveru, budete potřebovat následující: 

- Předplatné [Azure](https://azure.microsoft.com/free/).
- [Virtuální počítač SQL Server v systému Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrovaný u poskytovatele prostředků [virtuálního počítači SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Instalační médium s **požadovanou edicí** serveru SQL Server. Zákazníci, kteří mají [program Software Assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mohou získat instalační médium v [Centru multilicenčních zařízení](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají software assurance můžete použít instalační médium z image virtuálního počítače Azure `C:\SQLServerFull`Marketplace SQL Server, který má požadovanou edici (obvykle umístěné v ). 


## <a name="upgrade-an-edition"></a>Upgrade edice

> [!WARNING]
> Inovace edice SQL Server restartuje službu pro SQL Server, spolu se všemi přidruženými službami, jako je například Analysis Services a R Services. 

Chcete-li inovovat edici serveru SQL Server, získejte instalační médium serveru SQL Server pro požadovanou edici serveru SQL Server a proveďte následující kroky:

1. Sem setup.exe z instalačního média serveru SQL Server. 
1. Přejděte na **Možnost Údržba** a zvolte možnost **Upgrade edice.** 

   ![Výběr pro upgrade edice sql serveru](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Vyberte **Další,** dokud se nedostanete na stránku **Edice připraveno k upgradu,** a pak vyberte **Možnost Upgradovat**. Okno nastavení může přestat reagovat na několik minut, zatímco změna se projeví. Kompletní **Complete** stránka potvrdí, že upgrade edice je dokončen. 

Po upgradu edice SQL Serveru upravte vlastnost edition virtuálního počítače SQL Server na webu Azure Portal. Tím se aktualizují metadata a fakturace přidružené k tomuto virtuálnímu jevu.

## <a name="downgrade-an-edition"></a>Downgrade vydání

Chcete-li downgrade edice SQL Server, je třeba zcela odinstalovat SQL Server a znovu jej nainstalovat pomocí požadované edice instalačního média. 

> [!WARNING]
> Při odinstalaci serveru SQL Server mohou dojít k dalším prostojům. 

Můžete downgrade edice SQL Server podle následujících kroků:

1. Záloha všechny databáze, včetně systémových databází. 
1. Přesuňte systémové databáze (hlavní, model a msdb) do nového umístění. 
1. Úplně odinstalujte SQL Server a všechny související služby. 
1. Restartujte virtuální počítač. 
1. Nainstalujte SQL Server pomocí média s požadovanou edicí SERVERU SQL Server.
1. Nainstalujte nejnovější aktualizace Service Pack a kumulativní aktualizace.  
1. Nahraďte nové systémové databáze, které byly vytvořeny během instalace, systémovými databázemi, které jste dříve přesunuli do jiného umístění. 

Po downgraded sql server vydání, upravit edition vlastnost virtuálního počítače SQL Server na webu Azure Portal. Tím se aktualizují metadata a fakturace přidružené k tomuto virtuálnímu jevu.

## <a name="change-edition-in-portal"></a>Změna edice na portálu 

Po změně edice SQL Serveru pomocí instalačního média a registrace virtuálního počítače SQL Server u [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md), pak můžete použít portál Azure k úpravě edition vlastnost SQL Server Virtuální počítač pro účely fakturace. Postup je následující: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Přejděte na prostředek virtuálního počítače serveru SQL Server. 
1. V části **Nastavení**vyberte **Konfigurovat**. Potom vyberte požadovanou edici serveru SQL Server z rozevíracího seznamu v části **Edice**. 

   ![Změna metadat edice](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Zkontrolujte upozornění, že je nutné nejprve změnit edici serveru SQL Server a že vlastnost edition musí odpovídat edici SERVERU SQL Server. 
1. Vyberte **Použít,** chcete-li použít změny metadat edice. 


## <a name="remarks"></a>Poznámky

- Vlastnost edition pro virtuální počítač SQL Server musí odpovídat edici instance SERVERU SQL Server nainstalované pro všechny virtuální počítače SQL Serveru, včetně typů licencí s průběžnými platbami a vlastnílicence.
- Pokud přehodíte prostředek virtuálního počítače SQL Server, vrátíte se k pevně zakódované mušce nastavení bitové kopie.
- Možnost změnit edici je funkce poskytovatele prostředků virtuálního virtuálního připojení SQL. Nasazení image Azure Marketplace prostřednictvím portálu Azure automaticky zaregistruje virtuální počítač SQL Server u poskytovatele prostředků. Zákazníci, kteří jsou však vlastní instalaci SQL Server bude muset ručně [zaregistrovat jejich SQL Server Virtuální hod](virtual-machines-windows-sql-register-with-resource-provider.md).
- Přidání virtuálního virtuálního serveru SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního soudu. Všechny virtuální virtuální měna přidané do skupiny dostupnosti se vrátí na výchozí edici a edice bude nutné ji znovu upravit.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


