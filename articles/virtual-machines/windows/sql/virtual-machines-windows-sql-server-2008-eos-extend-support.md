---
title: Rozšiřování podpory SQL Server 2008 a SQL Server 2008 R2 s Azure
description: Naučte se rozšířit podporu SQL Server 2008 a SQL Server 2008 R2 tím, že migrujete instanci služby SQL Server do Azure nebo si koupíte rozšířenou podporu, abyste zachovali instance v místním prostředí.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 93e0032cd283eda034519ca29a0e1cf501b5cde6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100458"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozšiřování podpory SQL Server 2008 a SQL Server 2008 R2 s Azure

SQL Server 2008 a SQL Server 2008 R2 se přiblíží ke [konci životního cyklu podpory (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Vzhledem k tomu, že mnoho zákazníků stále používá obě verze, nabízíme několik možností, jak pokračovat v získávání podpory. Místní instance SQL Server můžete migrovat na virtuální počítače Azure, migrovat na Azure SQL Database nebo zachovat místní a koupit rozšířené aktualizace zabezpečení.

Na rozdíl od spravované instance není migrace na virtuální počítač Azure vyžadovat recertifikaci vašich aplikací. A na rozdíl od udržování místního prostředí se při migraci na virtuální počítač Azure vám budou zobrazovat bezplatné rozšířené opravy zabezpečení.

Ve zbývající části tohoto článku najdete pokyny k migraci instance SQL Server na virtuální počítač Azure.

## <a name="provisioning"></a>Zřizování

K dispozici je SQL Server 2008 R2 s průběžnými platbami **v systému Windows Server 2008 R2** , který je k dispozici na Azure Marketplace.

Zákazníci, kteří jsou v SQL Server 2008, budou muset buď provést samoobslužnou instalaci, nebo upgradovat na SQL Server 2008 R2. Podobně zákazníci v systému Windows Server 2008 budou muset buď nasadit svůj virtuální počítač z vlastního virtuálního pevného disku, nebo upgradovat na Windows Server 2008 R2.

Image nasazené prostřednictvím Azure Marketplace se dodávají s předinstalovaným rozšířením SQL IaaS. Rozšíření SQL IaaS je požadavek flexibilního licencování a automatizované opravy. Zákazníci, kteří nasazují sami instalované virtuální počítače, budou muset ručně nainstalovat rozšíření SQL IaaS. Rozšíření SQL IaaS není podporováno v systému Windows Server 2008.

> [!NOTE]
> I když SQL Server **vytvořit** a **Spravovat** , budou v Azure Portal fungovat s imagí SQL Server 2008 R2, nejsou _podporovány_následující funkce: Automatické zálohování, Azure Key Vault integrace, služby R a konfigurace úložiště.

## <a name="licensing"></a>Licencování
Nasazení s průběžnými platbami SQL Server 2008 R2 se dá převést na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Pokud chcete převést licenci na základě programu Software Assurance (SA) na průběžné platby, měli by se zákazníci zaregistrovat u [poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md)virtuálního počítače SQL. Po této registraci se typ licence SQL zamění mezi Zvýhodněné hybridní využití Azure a průběžnými platbami.

Vlastní instalace SQL Server 2008 nebo SQL Server 2008 R2 instance na virtuálním počítači Azure se může zaregistrovat u poskytovatele prostředků virtuálního počítače SQL a převést jejich typ licence na průběžné platby.

## <a name="migration"></a>Migrace
Instance EOS SQL Server můžete migrovat do virtuálního počítače Azure pomocí ručních metod zálohování nebo obnovení. Toto je nejběžnější způsob migrace z místního prostředí do virtuálního počítače Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pro hromadné migrace doporučujeme službu [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) . Díky Azure Site Recovery můžou zákazníci replikovat celý virtuální počítač včetně SQL Server z místního prostředí do virtuálního počítače Azure.

SQL Server vyžaduje pro zajištění obnovení Azure Site Recovery snímky konzistentní vzhledem k aplikacím. Azure Site Recovery podporuje snímky konzistentní vzhledem k aplikacím s minimálním intervalem 1 hodiny. Minimální cíl bodu obnovení (RPO), který je možné SQL Server s Azure Site Recovery migracemi, je 1 hodina. Plánovaná doba obnovení (RTO) je 2 hodiny plus SQL Server doba obnovení.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) je možnost pro zákazníky, pokud se migrují z místního prostředí do virtuálního počítače Azure upgradem SQL Server na verzi 2012 nebo novější.

## <a name="disaster-recovery"></a>Zotavení po havárii

Řešení zotavení po havárii pro EOS SQL Server na virtuálním počítači Azure jsou následující:

- **Zálohy SQL Server**: Použijte Azure Backup k ochraně SQL Server EOS proti ransomwarem, náhodnému odstranění a poškození. Řešení je momentálně ve verzi Preview pro EOS SQL Server a podporuje SQL Server 2008 a 2008 R2 spuštěné v systému Windows 2008 R2 SP1. Další podrobnosti najdete v [tomto článku](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2).
- **Přenos protokolu**: Můžete vytvořit repliku přenosu protokolů v jiné zóně nebo oblasti Azure s průběžným obnovením, abyste snížili RTO. Je nutné ručně nakonfigurovat přesouvání protokolu.
- **Azure Site Recovery**: Můžete replikovat virtuální počítač mezi zónami a oblastmi prostřednictvím replikace Azure Site Recovery. SQL Server vyžaduje, aby se snímky konzistentní vzhledem k aplikacím zaručily v případě havárie. Azure Site Recovery nabízí minimální 1 hodinu RPO a 2 hodinu (plus SQL Server čas obnovení) RTO pro zotavení po havárii EOS SQL Server.

## <a name="security-patching"></a>Opravy zabezpečení
Rozšířené aktualizace zabezpečení pro SQL Server virtuálních počítačů se doručí prostřednictvím kanálů Microsoft Update po registraci SQL Server virtuálního počítače u [poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md)virtuálního počítače SQL. Opravy lze stáhnout ručně nebo automaticky.

*Automatizované opravy* jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Pokud je nainstalováno rozšíření SQL Server IaaS, můžete zadat den v týdnu, čas a dobu trvání okna údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů.  Další informace najdete v tématu [automatizované opravy pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Další postup

Migrace SQL Server virtuálního počítače do Azure:

* [Migrace databáze SQL Serveru na SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)

Začínáme s SQL Server v Azure Virtual Machines:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](quickstart-sql-vm-create-portal.md)

Získejte odpovědi na nejčastější dotazy týkající se SQL Server virtuálních počítačů:

* [Nejčastější dotazy k SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
