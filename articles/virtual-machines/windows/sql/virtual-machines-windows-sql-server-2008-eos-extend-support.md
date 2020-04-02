---
title: Rozšíření podpory pro SQL Server 2008 & 2008 R2
description: Rozšiřte podporu pro SQL Server 2008 a SQL Server 2008 R2 migrací instance SQL Serveru do Azure nebo zakoupením rozšířené podpory, abyste instance udrželi místně.
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
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548405"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozšíření podpory pro SQL Server 2008 a SQL Server 2008 R2 s Azure

SQL Server 2008 a SQL Server 2008 R2 dosáhly [konce svého životního cyklu podpory (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Vzhledem k tomu, že mnoho zákazníků stále používá obě verze, poskytujeme několik možností, jak pokračovat v získávání podpory. Místní instance SQL Serveru můžete migrovat do virtuálních počítačů (virtuálnípočítače) Azure, migrovat do Azure SQL Database nebo zůstat místně a nakupovat rozšířené aktualizace zabezpečení.

Na rozdíl od spravované instance migrace na virtuální počítač Azure nevyžaduje opětovné potvrzení vašich aplikací. A na rozdíl od toho, že zůstanete v místním prostředí, budete migrací na virtuální počítač Azure dostávat bezplatné rozšířené opravy zabezpečení.

Zbývající část tohoto článku obsahuje důležité informace pro migraci instance SQL Server do virtuálního počítače Azure.

Další informace o možnostech ukončení podpory naleznete v [tématu Konec podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Zřizování

Na webu Windows Server 2008 R2 je k dispozici průběžná sql **server 2008 R2,** která je k dispozici na webu Azure Marketplace.

Zákazníci, kteří jsou na SQL Server 2008 bude muset buď vlastní instalaci nebo upgrade na SQL Server 2008 R2. Podobně zákazníci v systému Windows Server 2008 budou muset nasadit svůj virtuální počítač z vlastního virtuálního pevného disku nebo upgradovat na systém Windows Server 2008 R2.

Image nasazené přes Azure Marketplace jsou dodávány s předinstalovaným rozšířením SQL IaaS. Rozšíření SQL IaaS je požadavek na flexibilní licencování a automatické opravy. Zákazníci, kteří nasazují vlastní nainstalované virtuální servery, budou muset ručně nainstalovat rozšíření SQL IaaS. Rozšíření SQL IaaS není v systému Windows Server 2008 podporováno.

> [!NOTE]
> Přestože okno SQL Server **Create** and **Manage** bude fungovat s bitovou kopií SQL Server 2008 R2 na webu Azure Portal, následující funkce _nejsou podporovány:_ automatické zálohování, integrace úložiště klíčů Azure, služby R a konfigurace úložiště.

## <a name="licensing"></a>Licencování
Nasazení SQL Serveru 2008 R2 s průběžným platbou lze převést na [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Chcete-li převést licenci založenou na programu Software Assurance (SA) na průběžnou platbu, měli by se zákazníci zaregistrovat u [poskytovatele prostředků virtuálního počítače](virtual-machines-windows-sql-register-with-resource-provider.md)SQL . Po této registraci bude typ licence SQL zaměnitelný mezi hybridní výhodou Azure a průběžně otáčených plateb.

Samoinstalované instance SQL Serveru 2008 nebo SQL Server 2008 R2 na virtuálním počítači Azure se můžou zaregistrovat u poskytovatele prostředků virtuálního počítače SQL a převést jejich typ licence na průběžný platby.

## <a name="migration"></a>Migrace
Instance EOS SQL Serveru můžete migrovat do virtuálního počítače Azure pomocí metod ručního zálohování a obnovení. Toto je nejběžnější metoda migrace z místního virtuálního počítače do virtuálního počítače Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pro hromadné migrace doporučujeme službu [Azure Site Recovery.](/azure/site-recovery/site-recovery-overview) Díky Azure Site Recovery můžou zákazníci replikovat celý virtuální počítač, včetně SQL Serveru z místního do virtuálního počítače Azure.

SQL Server vyžaduje snímky Azure Site Recovery konzistentní s aplikacemi, aby bylo zaručeno obnovení. Azure Site Recovery podporuje snímky konzistentní s aplikacemi s minimálním 1hodinovým intervalem. Minimální cíl bodu obnovení (RPO) možné pro SQL Server s migrace azure site recovery je 1 hodinu. Cíl doby obnovení (RTO) je 2 hodiny plus sql server doba obnovení.

### <a name="database-migration-service"></a>Database Migration Service

[Služba migrace databáze](/azure/dms/dms-overview) je možnost pro zákazníky, pokud migrují z místního na virtuální počítač Azure upgradem SQL Serveru na verzi 2012 nebo novější.

## <a name="disaster-recovery"></a>Zotavení po havárii

Řešení zotavení po havárii pro EOS SQL Server na virtuálním počítači Azure jsou následující:

- **Zálohy SQL Serveru:** Pomocí azure backupu můžete chránit eOS SQL Server 2008 a 2008 R2 před ransomwarem, náhodným odstraněním a poškozením pomocí 15minutového rpo a obnovení bodu v čase. Další podrobnosti naleznete v [tomto článku](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Přesouvání protokolu**: Můžete vytvořit repliku přesouvání protokolu v jiné zóně nebo oblasti Azure s nepřetržitým obnovením, abyste snížili rto. Je třeba ručně nakonfigurovat přesouvání protokolu.
- **Azure Site Recovery**: Virtuální počítač můžete replikovat mezi zónami a oblastmi prostřednictvím replikace obnovení webu Azure. SQL Server vyžaduje snímky konzistentní s aplikací zaručit obnovení v případě havárie. Azure Site Recovery nabízí minimálně 1 hodinu RPO a 2 hodiny (plus SQL Server doba obnovení) RTO pro eOS SQL Server zotavení po havárii.

## <a name="security-patching"></a>Opravy zabezpečení
Rozšířené aktualizace zabezpečení pro virtuální server SQL Server jsou doručovány prostřednictvím kanálů Microsoft Update poté, co byl virtuální modul SQL Server zaregistrován u [poskytovatele prostředků virtuálního virtuálního připojení](virtual-machines-windows-sql-register-with-resource-provider.md)SQL . Záplaty lze stáhnout ručně nebo automaticky.

*Automatizované opravy* jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Můžete zadat den v týdnu, čas a trvání pro okno údržby, pokud je nainstalováno rozšíření SQL Server IaaS. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů.  Další informace naleznete [v tématu Automated patching for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Další kroky

Migrace virtuálního počítače SQL Serveru do Azure:

* [Migrace databáze SQL Serveru na SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)

Začínáme s SQL Serverem na virtuálních počítačích Azure:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](quickstart-sql-vm-create-portal.md)

Získejte odpovědi na nejčastější dotazy týkající se virtuálních připojení SQL Server:

* [Nejčastější dotazy pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Přečtěte si další informace o možnostech ukončení podpory a rozšířených aktualizacích zabezpečení:

* [Aktualizace rozšířeného](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [zabezpečení](/sql/sql-server/end-of-support/sql-server-extended-security-updates) ukončení podpory