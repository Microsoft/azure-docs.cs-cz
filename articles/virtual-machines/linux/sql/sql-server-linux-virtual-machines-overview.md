---
title: Přehled SQL Serveru na virtuálních počítačích Azure s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak spouštět úplné edice SQL Serveru na virtuálních počítačích Azure s Linuxem. Získáte přímé odkazy na všechny image virtuálních počítačů s Linuxem a SQL Serverem a související obsah.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Přehled SQL Serveru na virtuálních počítačích Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Toto téma popisuje možnosti spouštění SQL Serveru na virtuálních počítačích Azure s Linuxem a uvádí [odkazy na image na portálu](#create).

> [!NOTE]
> Pokud jste již obeznámeni s SQL Serverem a chcete jenom zjistit, jak nasadit virtuální počítač s Linuxem a SQL Serverem, přečtěte si téma [Zřízení virtuálního počítače s Linuxem a SQL Serverem v Azure](provision-sql-server-linux-virtual-machine.md). Případně pokud chcete vytvořit virtuální počítač s Windows a SQL Serverem, přečtěte si téma [Zřízení virtuálního počítače s Windows a SQL Serverem v Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Pokud jste správce databáze nebo vývojář, nabízejí virtuální počítače Azure způsob, jak lokální úlohy aplikace SQL Serveru přesunout do cloudu.

## <a name="scenarios"></a>Scénáře

Existuje mnoho důvodů, kvůli kterým byste se mohli rozhodnout, že jako hostitele dat budete využívat Azure. Pokud vyvíjíte nebo migrujete svou aplikaci do Azure, zlepší se také výkon pro vyhledávání back-endových dat v Azure. Budete mít automaticky přístup k většímu počtu datových center pro dosažení globální působnosti a zotavení po havárii. Data také získají vysoké zabezpečení a odolnost.

SQL Server běžící na virtuálních počítačích Azure je jednou z možností, jak ukládat relační data v Azure. Můžete také použít službu Azure SQL Database. Další informace o výběru mezi SQL Serverem na virtuálních počítačích a službou Azure SQL Database najdete v tématu [Výběr možnosti cloudového SQL Serveru: Azure SQL Database (PaaS) nebo SQL Server na virtuálních počítačích Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a>Vytvoření nového virtuálního počítače s SQL Serverem

Podrobné pokyny k vytvoření nového virtuálního počítače SQL najdete v kurzu [Zřízení virtuálního počítače s Linuxem a SQL Serverem v Azure](provision-sql-server-linux-virtual-machine.md).

Následující tabulka obsahuje matici nejnovějších imagí SQL Serveru v galerii virtuálních počítačů. Po kliknutí na odkaz se začne vytvářet nový virtuální počítač SQL se zadanou verzí, edicí a operačním systémem.

> [!TIP]
> Pokud chcete lépe porozumět cenám virtuálních počítačů a SQL pro tyto image, podívejte se na [stránku s cenami virtuálních počítačů s Linuxem a SQL Serverem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Dostupné image virtuálních počítačů s Windows a SQL Serverem najdete v tématu [Přehled SQL Serveru na virtuálních počítačích Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a>Nainstalované balíčky

Při konfiguraci SQL Serveru v Linuxu nainstalujete balíček databázového stroje a následně několik volitelných balíčků podle vašich potřeb. Image virtuálních počítačů s Linuxem pro SQL Server automaticky nainstalují většinu balíčků za vás. Následující tabulka uvádí, které balíčky se nainstalují pro jednotlivé distribuce.

| Distribuce | [Databázový stroj](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Nástroje](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agent SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Fulltextové vyhledávání](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Doplněk pro vysokou dostupnost](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ne](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ne](./media/sql-server-linux-virtual-machines-overview/no.png) | ![ne](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ano](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci a používání SQL Serveru v Linuxu najdete v tématu [Přehled SQL Serveru v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
