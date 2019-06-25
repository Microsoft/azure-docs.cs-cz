---
title: Rozšíření podpory pro SQL Server 2008 a SQL Server 2008 R2 s Azure
description: Zjistěte, jak rozšíření podpory pro SQL Server 2008 a SQL Server 2008 R2 migrací vaší instanci SQL serveru do Azure nebo zakoupením rozšířenou podporu zachovat instance místní.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ecb7030fa3652525a36ce15d66ea6e5daf9c3296
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304228"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozšíření podpory pro SQL Server 2008 a SQL Server 2008 R2 s Azure

SQL Server 2008 a SQL Server 2008 R2 jsou obě blíží [konci svého životního cyklu podpory (SESTAVENÁ)](https://www.microsoft.com/sql-server/sql-server-2008). Protože řada našich zákazníků stále používají obě verze, společnost Microsoft poskytuje několik možností, jak pokračovat v uplatňování podpory. Můžete migrovat vaše místní instance systému SQL Server na virtuálních počítačích Azure (VM), migrace do Azure SQL Database, nebo zůstat v místním a koupit aktualizace rozšířené zabezpečení.

Na rozdíl od s managed instance, migrace na Virtuálním počítači Azure nevyžaduje recertifying vašich aplikací. A na rozdíl od s zůstávají místně, zobrazí se opravy rozšířené zabezpečení úrovně free migrací na Virtuálním počítači Azure.

Zbývající část tohoto článku poskytuje důležité informace týkající se migrace vaší instanci SQL serveru na Virtuálním počítači Azure.

## <a name="provisioning"></a>Zřizování

Je s průběžnými platbami `SQL Server 2008 R2 on Windows Server 2008 R2` image na Azure marketplace.

Zákazníci, kteří jsou v systému SQL Server 2008 buď potřebovat pro vlastní instalaci nebo upgrade na SQL Server 2008 R2. Podobně zákazníci ve Windows serveru 2008 buď potřebovat nasadit své virtuální počítač z vlastního virtuálního pevného disku, nebo upgradovat na Windows Server 2008 R2.

Image nasazené prostřednictvím webu Marketplace dodávají s předinstalovaným rozšíření SQL IaaS. Rozšíření SQL IaaS představuje požadavek pro flexibilní licencování a automatické opravy. Zákazníci, kteří nasazují samostatně nainstalovaný virtuálních počítačů bude nutné ručně nainstalovat rozšíření SQL IaaS. Rozšíření SQL IaaS není podporováno ve Windows 2008.

  > [!NOTE]
  > Zatímco se SQL Server `Create` a `Manage` oken bude fungovat s bitovou kopii systému SQL Server 2008 R2 na webu Azure Portal, jsou následující funkce _nepodporuje_: Automatické zálohování, integrace Azure Key Vault, R Services a konfiguraci úložiště.

## <a name="licensing"></a>Licencování
Nasazení s průběžnými platbami SQL serveru 2008 R2 můžete převést na [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Licenci na Software Assurance (SA) na základě převést na průběžné platby, zákazníci měli zaregistrovat s virtuálním Počítačem SQL [poskytovatele prostředků](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Jakmile zaregistrované u poskytovatele prostředků SQL VM, bude typ licence SQL zaměnitelné mezi AHB a s průběžnými platbami.

Samoobslužné nainstalované instance systému SQL Server 2008 nebo SQL Server 2008 R2 na virtuálním počítači Azure můžete zaregistrovat poskytovatele prostředků SQL a jejich typ licence přejděte k průběžným platbám.

## <a name="migration"></a>Migrace
Je možné migrovat instance SESTAVENÁ SQL serveru na Virtuálním počítači Azure pomocí metody Ruční zálohování a obnovení; Toto je nejběžnější způsob migrace z místního na Virtuálním počítači Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pro hromadné migrace doporučujeme [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) služby. Pomocí služby Azure Site Recovery můžete replikovat zákazníkům celý virtuální počítač, včetně SQL serveru z místního na Virtuálním počítači Azure.

Snímky konzistentní s Azure Site Recovery k zajištění obnovení; vyžaduje SQL Server a Azure Site Recovery podporuje snímky konzistentní s minimální interval 1 hodina. Minimální možné pro SQL Server s Azure Site Recovery migrací cíle bodu obnovení je 1 hodinu a RTO je 2 hodin a doba obnovení systému SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) možnost je určená pro zákazníky, pokud migrujete z místního Azure VM díky upgradu SQL serveru na SQL Server 2012 nebo novější.

## <a name="disaster-recovery"></a>Zotavení po havárii

Řešení zotavení po havárii pro SESTAVENÁ SQL Server na virtuálním počítači Azure jsou následující:

- **Zálohování systému SQL Server**: Použití Azure Backup k ochraně SQL serveru SESTAVENÁ proti ransomwaru, před poškozením a náhodným odstraněním. Toto řešení je momentálně ve verzi preview pro SQL Server SESTAVENÁ a podporuje SQL Server 2008 a 2008 R2 a systémem Windows 2008 R2 SP1. Další podrobnosti najdete na tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)
- **Přesouvání protokolu**: Můžete vytvořit repliku přesouvání protokolu v jiné zóně nebo oblasti Azure pomocí průběžného obnoví ke snížení RTO. Zákazníci budou muset ručně konfigurovat přesouvání protokolu.
- **Azure Site Recovery**: Mezi zónami a oblastí prostřednictvím replikace Azure Site Recovery můžete replikovat virtuální počítač. SQL Server vyžaduje snímky konzistentní s aplikací pro zajištění obnovení v případě havárie. Azure Site Recovery nabízí minimální cíle bodu obnovení za 1 hodinu a dvouhodinový + doba obnovení systému SQL Server RTO pro SESTAVENÁ SQL Server zotavení po Havárii.

## <a name="security-patching"></a>Opravy chyb zabezpečení
Aktualizace rozšířené zabezpečení pro virtuální počítače s SQL serverem budou doručeny prostřednictvím kanálů Microsoft Update, jakmile se virtuální počítač SQL Server byl zaregistrován u SQL [poskytovatele prostředků](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Opravy si můžete stáhnout buď ručně nebo automaticky.

**Automatizované opravy** jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Den v týdnu, čas a dobu trvání časového období údržby můžete určit, pokud je nainstalované rozšíření SQL IaaS. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů.  Další informace najdete v tématu [Automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Další postup

Migrace virtuálního počítače SQL serveru do Azure

* [Migrace databáze SQL Serveru na SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)

Začínáme s SQL Serverem na virtuálních počítačích Azure:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](quickstart-sql-vm-create-portal.md)

Odpovědi na nejčastější dotazy o virtuálních počítačích SQL:

* [SQL Server na Azure Virtual Machines – nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md)
