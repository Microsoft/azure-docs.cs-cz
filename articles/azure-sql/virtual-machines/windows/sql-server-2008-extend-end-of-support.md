---
title: Prodloužená podpora SQL Server 2008 & 2008 R2
description: Podporu SQL Server 2008 a SQL Server 2008 R2 můžete rozšířit tak, že migrujete instanci služby SQL Server do Azure nebo zakoupíte rozšířenou podporu, abyste zachovali instance v místním prostředí.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fbfc4619e8af86a89b82f32ff3bc9a39c92b355a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784860"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozšiřování podpory SQL Server 2008 a SQL Server 2008 R2 s Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 a SQL Server 2008 R2 dosáhli [konce životního cyklu podpory (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Vzhledem k tomu, že mnoho zákazníků stále používá obě verze, nabízíme několik možností, jak pokračovat v získávání podpory. Místní instance SQL Server můžete migrovat na virtuální počítače Azure, migrovat na Azure SQL Database nebo zachovat místní a koupit rozšířené aktualizace zabezpečení.

Na rozdíl od spravované instance není migrace na virtuální počítač Azure vyžadovat recertifikaci vašich aplikací. A na rozdíl od udržování místního prostředí se při migraci na virtuální počítač Azure vám budou zobrazovat bezplatné rozšířené opravy zabezpečení.

Ve zbývající části tohoto článku najdete pokyny k migraci instance SQL Server na virtuální počítač Azure.

Další informace o možnostech konce podpory najdete na [konci podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Zřizování

K dispozici je SQL Server 2008 R2 s průběžnými platbami **v systému Windows Server 2008 R2** , který je k dispozici na Azure Marketplace.

Zákazníci, kteří jsou v SQL Server 2008, budou muset buď provést samoobslužnou instalaci, nebo upgradovat na SQL Server 2008 R2. Podobně zákazníci v systému Windows Server 2008 budou muset buď nasadit svůj virtuální počítač z vlastního virtuálního pevného disku, nebo upgradovat na Windows Server 2008 R2.

Image nasazené prostřednictvím Azure Marketplace se dodávají s předinstalovaným rozšířením SQL IaaS. Rozšíření SQL IaaS je požadavek flexibilního licencování a automatizované opravy. Zákazníci, kteří nasazují sami instalované virtuální počítače, budou muset ručně nainstalovat rozšíření SQL IaaS. Rozšíření SQL IaaS není podporováno v systému Windows Server 2008.

> [!NOTE]
> I když se SQL Server **vytvářet** a **Spravovat** okna, budou v Azure Portal fungovat s imagí SQL Server 2008 R2, nejsou _podporovány_ následující funkce: automatické zálohování, Integrace Azure Key Vault, služby R a konfigurace úložiště.

## <a name="licensing"></a>Licencování
Nasazení s průběžnými platbami SQL Server 2008 R2 se dá převést na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Pokud chcete převést licenci na základě programu Software Assurance (SA) na průběžné platby, měli by se zákazníci zaregistrovat u [poskytovatele prostředků](sql-vm-resource-provider-register.md)virtuálního počítače SQL. Po této registraci se typ licence SQL zamění mezi Zvýhodněné hybridní využití Azure a průběžnými platbami.

Vlastní instalace SQL Server 2008 nebo SQL Server 2008 R2 instance na virtuálním počítači Azure se může zaregistrovat u poskytovatele prostředků virtuálního počítače SQL a převést jejich typ licence na průběžné platby.

## <a name="migration"></a>Migrace
Instance EOS SQL Server můžete migrovat do virtuálního počítače Azure pomocí ručních metod zálohování nebo obnovení. Toto je nejběžnější způsob migrace z místního prostředí do virtuálního počítače Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pro hromadné migrace doporučujeme službu [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) . Díky Azure Site Recovery můžou zákazníci replikovat celý virtuální počítač včetně SQL Server z místního prostředí do virtuálního počítače Azure.

SQL Server vyžaduje pro zajištění obnovení Azure Site Recovery snímky konzistentní vzhledem k aplikacím. Azure Site Recovery podporuje snímky konzistentní vzhledem k aplikacím s minimálním intervalem 1 hodiny. Minimální cíl bodu obnovení (RPO), který je možné SQL Server s Azure Site Recovery migracemi, je 1 hodina. Plánovaná doba obnovení (RTO) je 2 hodiny plus SQL Server doba obnovení.

### <a name="database-migration-service"></a>Database Migration Service

[Azure Database Migration Service](../../../dms/dms-overview.md) je možnost pro zákazníky, pokud se migrují z místního prostředí do virtuálního počítače Azure upgradem SQL Server na verzi 2012 nebo novější.

## <a name="disaster-recovery"></a>Zotavení po havárii

Řešení zotavení po havárii pro EOS SQL Server na virtuálním počítači Azure jsou následující:

- **SQL Server zálohy** : pomocí Azure Backup můžete chránit EOS SQL Server 2008 a 2008 R2 proti ransomwarem, náhodnému odstranění a poškození s 15-min. RPO a obnovením k bodu v čase. Další podrobnosti najdete v [tomto článku](../../../backup/sql-support-matrix.md#scenario-support).
- **Přesouvání protokolu** : můžete vytvořit repliku přenosu protokolů v jiné zóně nebo oblasti Azure s průběžným obnovením, abyste snížili RTO. Je nutné ručně nakonfigurovat přesouvání protokolu.
- **Azure Site Recovery** : virtuální počítač můžete replikovat mezi zónami a oblastmi prostřednictvím replikace Azure Site Recovery. SQL Server vyžaduje, aby se snímky konzistentní vzhledem k aplikacím zaručily v případě havárie. Azure Site Recovery nabízí minimální 1 hodinu RPO a 2 hodinu (plus SQL Server čas obnovení) RTO pro zotavení po havárii EOS SQL Server.

## <a name="security-patching"></a>Opravy zabezpečení
Rozšířené aktualizace zabezpečení pro SQL Server virtuálních počítačů se doručí prostřednictvím kanálů Microsoft Update po registraci SQL Server virtuálního počítače u [poskytovatele prostředků](sql-vm-resource-provider-register.md)virtuálního počítače SQL. Opravy lze stáhnout ručně nebo automaticky.

*Automatizované opravy* jsou ve výchozím nastavení povolené. Automatizované opravy umožňují na platformě Azure automaticky opravovat SQL Server a operační systém. Pokud je nainstalováno rozšíření SQL Server IaaS, můžete zadat den v týdnu, čas a dobu trvání okna údržby. V té době pak Azure nainstaluje potřebné opravy. V rámci plánování intervalu údržby se pro čas používá národní prostředí virtuálních počítačů. Další informace najdete v tématu [automatizované opravy pro SQL Server v Azure Virtual Machines](automated-patching.md).


## <a name="next-steps"></a>Další kroky

Migrace SQL Server virtuálního počítače do Azure:

* [Migrace databáze SQL Serveru na SQL Server ve virtuálním počítači Azure](migrate-to-vm-from-sql-server.md)

Začínáme s SQL Server v Azure Virtual Machines:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](sql-vm-create-portal-quickstart.md)

Získejte odpovědi na nejčastější dotazy týkající se SQL Server virtuálních počítačů:

* [Nejčastější dotazy k SQL Server v Azure Virtual Machines](frequently-asked-questions-faq.md)

Přečtěte si další informace o možnostech konce podpory a rozšířených aktualizacích zabezpečení:

* [Konec podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Rozšířené aktualizace zabezpečení](/sql/sql-server/end-of-support/sql-server-extended-security-updates)