---
title: Místní změna edice SQL Server
description: Naučte se, jak změnit edici virtuálního počítače s SQL Server v Azure na downgrade za účelem snížení nákladů nebo upgradu za účelem povolení více funkcí.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358822"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Místní změna edice SQL Serveru na virtuálním počítači Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak změnit edici SQL Server na virtuálním počítači s Windows v Azure. 

Edice SQL Server je určena klíčovým produktem a je určena během procesu instalace pomocí instalačního média. Edice určuje, jaké [funkce](/sql/sql-server/editions-and-components-of-sql-server-2017) jsou k dispozici v SQL Server produktu. Můžete změnit edici SQL Server pomocí instalačního média a snížit náklady nebo upgradovat, aby se povolilo více funkcí.

Po interní změně edice SQL Server k virtuálnímu počítači SQL Server musíte v Azure Portal pro účely fakturace aktualizovat vlastnost edice SQL Server. 

## <a name="prerequisites"></a>Předpoklady

Chcete-li provést místní změnu edice SQL Server, budete potřebovat následující: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- [SQL Server virtuální počítač ve Windows](./create-sql-vm-portal.md) zaregistrovaný pomocí [rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).
- Nastavte médium s **požadovanou verzí** SQL Server. Zákazníci, kteří mají [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), můžou instalační médium získat z centra [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z Azure Marketplace SQL Server image virtuálního počítače, která má požadovanou edici (obvykle se nachází v části `C:\SQLServerFull` ). 


## <a name="upgrade-an-edition"></a>Upgrade edice

> [!WARNING]
> Upgrade edice SQL Server restartuje službu pro SQL Server společně s případnými přidruženými službami, jako jsou služby Analysis Services a R. 

Chcete-li upgradovat edici SQL Server, Získejte instalační médium SQL Server pro požadovanou edici SQL Server a proveďte následující kroky:

1. Z instalačního média SQL Server otevřete Setup.exe. 
1. Pokračujte na **Údržba** a vyberte možnost **upgradu edice** . 

   ![Výběr pro upgrade edice SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Vyberte **Další** , dokud se nedostanete na stránku **připraveno k upgradu** , a pak vyberte **upgradovat**. Když se změna projeví, může okno instalace přestat reagovat během několika minut. Po **dokončení** této stránky se ověří, že je upgrade edice dokončený. 

Po upgradu edice SQL Server upravte vlastnost edice SQL Server virtuálního počítače v Azure Portal. Tím se aktualizují metadata a fakturace přidružená k tomuto virtuálnímu počítači.

## <a name="downgrade-an-edition"></a>Downgrade edice

Chcete-li downgradovat edici SQL Server, je nutné zcela odinstalovat SQL Server a znovu ji znovu nainstalovat pomocí instalačního média požadovaného vydání. 

> [!WARNING]
> Odinstalace SQL Server může způsobit další výpadky. 

Verzi SQL Server můžete downgradovat pomocí následujících kroků:

1. Zálohujte všechny databáze, včetně systémových databází. 
1. Přesuňte systémové databáze (hlavní, model a msdb) do nového umístění. 
1. Kompletně odinstalujte SQL Server a všechny přidružené služby. 
1. Restartujte virtuální počítač. 
1. Nainstalujte SQL Server pomocí média s požadovanou verzí SQL Server.
1. Nainstalujte nejnovější aktualizace Service Pack a kumulativní aktualizace.  
1. Nahraďte nové systémové databáze, které byly vytvořeny během instalace, se systémovými databázemi, které jste dříve přesunuli do jiného umístění. 

Po downgradu edice SQL Server upravte vlastnost edice SQL Server virtuálního počítače v Azure Portal. Tím se aktualizují metadata a fakturace přidružená k tomuto virtuálnímu počítači.

## <a name="change-edition-in-portal"></a>Změna edice na portálu 

Pokud jste změnili edici SQL Server pomocí instalačního média a zaregistrovali jste SQL Server virtuální počítač pomocí [rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md), můžete pomocí Azure Portal změnit vlastnost edice virtuálního počítače SQL Server pro účely fakturace. To můžete provést pomocí těchto kroků: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
1. Přejít na prostředek SQL Server prostředku virtuálního počítače. 
1. V části **Nastavení** vyberte **Konfigurovat**. Pak v rozevíracím seznamu v části **edice** vyberte požadovanou edici SQL Server. 

   ![Změnit metadata edice](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Přečtěte si upozornění, které uvádí, že je nutné nejprve změnit edici SQL Server a zda vlastnost edice musí odpovídat edici SQL Server. 
1. Pokud chcete použít změny metadat edice, vyberte **použít** . 


## <a name="remarks"></a>Poznámky

- Vlastnost edice pro virtuální počítač s SQL Server musí odpovídat edici SQL Server nainstalované pro všechny SQL Server virtuální počítače, včetně průběžných plateb a typů licencí.
- Pokud přetáhnete prostředek virtuálního počítače SQL Server, vrátíte se do nastavení pevně zakódované verze image.
- Možnost změnit edici je funkcí rozšíření agenta SQL IaaS. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s rozšířením agenta SQL IaaS. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset ručně [zaregistrovat svůj SQL Server virtuální počítač](sql-agent-extension-manually-register-single-vm.md).
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. Všechny virtuální počítače přidané do skupiny dostupnosti se vrátí k výchozí edici a Tato edice bude muset být upravena znovu.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](doc-changes-updates-release-notes.md)