---
title: Přehled SQL Serveru na virtuálních počítačích Azure s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak spouštět úplné edice SQL Serveru na virtuálních počítačích Azure s Linuxem. Získáte přímé odkazy na všechny image virtuálních počítačů s Linuxem a SQL Serverem a související obsah.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 18f9dd5318c6aecff8819178168469d9334e5e29
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332400"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Přehled SQL Serveru na virtuálních počítačích Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

SQL Server na virtuálních počítačích Azure umožňuje používat plné verze SQL Serveru v cloudu, aniž by bylo potřeba spravovat jakýkoli místní hardware. Pokud platíte průběžně, pro virtuální počítače s SQL Serverem se také zjednoduší náklady na licencování.

Virtuální počítače Azure běží v mnoha různých [geografických oblastech](https://azure.microsoft.com/regions/) po celém světě. Také nabízejí celou řadu [velikostí počítačů](../sizes.md). Galerie imagí virtuálních počítačů umožňuje vytvoření virtuálního počítače s SQL Serverem, který má správnou verzi, vydání i operační systém. Díky tomu jsou virtuální počítače vhodné pro mnoho různých úloh SQL Serveru.

## <a id="create"></a> Začínáme s virtuálními počítači SQL

Chcete-li začít, zvolte image virtuálního počítače s SQL Serverem s požadovanou verzí, vydáním a operačním systémem. Následující oddíly poskytují přímé odkazy na image v galerii virtuálních počítačů s SQL Serverem na webu Azure Portal.

> [!TIP]
> Další informace pro lepší pochopení cen imagí SQL najdete na [stránce s cenami virtuálních počítačů s Linuxem a SQL Serverem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

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

## <a name="related-products-and-services"></a>Související produkty a služby

### <a name="linux-virtual-machines"></a>Linux Virtual Machines

* [Přehled služby Virtual Machines](../overview.md)

### <a name="storage"></a>Storage

* [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Sítě

* [Přehled služby Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux)
* [Porovnání Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Další postup

Začínáme s SQL Serverem na virtuálních počítačích Azure s Linuxem:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](provision-sql-server-linux-virtual-machine.md)

Odpovědi na nejčastější dotazy o virtuálních počítačích SQL s Linuxem:

* [SQL Server na Azure Virtual Machines s Linuxem – nejčastější dotazy](sql-server-linux-faq.md)
