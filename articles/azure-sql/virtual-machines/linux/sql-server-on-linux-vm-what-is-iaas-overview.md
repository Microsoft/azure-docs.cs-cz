---
title: Přehled SQL Server v Azure Virtual Machines pro Linux | Microsoft Docs
description: Přečtěte si, jak spouštět plné edice SQL Server v Azure Virtual Machines pro Linux. Získáte přímé odkazy na všechny image virtuálních počítačů s Linuxem a SQL Serverem a související obsah.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c394fe2aa7639d32e5d79bcb22a01151f7666f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96324614"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Přehled SQL Serveru na virtuálních počítačích Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server v Azure Virtual Machines umožňují používat v cloudu úplné verze SQL Server, aniž byste museli spravovat jakýkoli místní hardware. Pokud platíte průběžně, pro virtuální počítače s SQL Serverem se také zjednoduší náklady na licencování.

Virtuální počítače Azure běží v mnoha různých [geografických oblastech](https://azure.microsoft.com/regions/) po celém světě. Také nabízejí celou řadu [velikostí počítačů](../../../virtual-machines/sizes.md). Galerie imagí virtuálních počítačů umožňuje vytvoření virtuálního počítače s SQL Serverem, který má správnou verzi, vydání i operační systém. Díky tomu jsou virtuální počítače vhodné pro mnoho různých úloh SQL Serveru. 

Pokud s Azure SQL začínáte, podívejte se na video s *přehledem SQL Server na virtuálním počítači Azure* z naší podrobné [série videí SQL Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Začínáme s SQL Servermi virtuálními počítači

Chcete-li začít, zvolte image virtuálního počítače s SQL Serverem s požadovanou verzí, vydáním a operačním systémem. Následující oddíly poskytují přímé odkazy na image v galerii virtuálních počítačů s SQL Serverem na webu Azure Portal.

> [!TIP]
> Další informace o tom, jak pochopit ceny SQL Server imagí, najdete [na stránce s cenami pro virtuální počítače se systémem Linux se spuštěným SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Pokud chcete zobrazit dostupné image virtuálních počítačů s SQL Server pro Windows, přečtěte si téma [přehled SQL Server v Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a>Nainstalované balíčky

Při konfiguraci SQL Server on Linux nainstalujete balíček databázového stroje a pak několik volitelných balíčků v závislosti na vašich požadavcích. Image virtuálních počítačů s Linuxem pro SQL Server automaticky nainstalují většinu balíčků za vás. Následující tabulka uvádí, které balíčky se nainstalují pro jednotlivé distribuce.

| Distribuce | [Databázový stroj](/sql/linux/sql-server-linux-setup) | [Nástroje](/sql/linux/sql-server-linux-setup-tools) | [Agent SQL Server](/sql/linux/sql-server-linux-setup-sql-agent) | [Fulltextové vyhledávání](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [Doplněk pro vysokou dostupnost](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL a databázový stroj](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL a nástroje](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL a Agent SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL a fulltextové vyhledávání](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL a SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Doplněk RHEL a HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES a databázový stroj](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES a nástroje](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES a Agent SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES a fulltextové vyhledávání](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES a SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![Doplněk SLES a HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu a databázový stroj](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu a nástroje](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu a Agent SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu a fulltextové vyhledávání](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu a SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Doplněk Ubuntu a HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Související produkty a služby

### <a name="linux-virtual-machines"></a>Virtuální počítače s Linuxem

* [Přehled služby Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Sítě

* [Přehled Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../../virtual-network/public-ip-addresses.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [Dokumentace k SQL Server on Linux](/sql/linux)
* [Porovnání Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Další kroky

Začínáme s SQL Server on Linux virtuálních počítačů:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](sql-vm-create-portal-quickstart.md)

Získejte odpovědi na nejčastější dotazy týkající se SQL Server virtuálních počítačů v systému Linux:

* [SQL Server na Azure Virtual Machines – nejčastější dotazy](frequently-asked-questions-faq.md)