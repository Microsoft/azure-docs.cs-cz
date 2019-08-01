---
title: Postup přesunutí prostředků Azure SQL Database do jiné oblasti | Microsoft Docs
description: Naučte se, jak přesunout Azure SQL Database, elastický fond Azure SQL nebo spravovanou instanci SQL Azure do jiné oblasti.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2158d4120445de4c62461fb89555a1b73bc1e2b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567171"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Jak přesunout prostředky SQL Azure do jiné oblasti

Tento článek vás seznámí s obecným pracovním postupem, jak přesunout jednu databázi, elastický fond a spravovanou instanci Azure SQL Database do nové oblasti. 

## <a name="overview"></a>Přehled

Existují různé scénáře, ve kterých byste chtěli přesunout existující prostředky Azure SQL z jedné oblasti do druhé. Například rozšíříte svou firmu do nové oblasti a chcete ji optimalizovat pro novou zákaznickou základnu. Nebo je nutné operace přesunout do jiné oblasti z důvodu dodržování předpisů. Nebo Azure vydala značku – novou oblast, která poskytuje lepší blízkost a zlepšuje prostředí pro zákazníky.  

Tento článek poskytuje obecný pracovní postup pro přesun prostředků do jiné oblasti. Pracovní postup se skládá z následujících kroků: 

- Ověření požadavků pro přesun 
- Příprava na přesunutí prostředků v oboru
- Monitorování procesu přípravy
- Test procesu přesunutí
- Iniciovat skutečný přesun 
- Odebrat prostředky ze zdrojové oblasti 


> [!NOTE]
> Tento článek se týká migrace ve veřejném cloudu Azure nebo v rámci stejného cloudového cloudu. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Přesunout izolovanou databázi

### <a name="verify-prerequisites"></a>Ověření požadavků 

1. Vytvořte cílový logický Server pro každý zdrojový server. 
1. Nakonfigurujte bránu firewall pomocí správných výjimek pomocí [prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Nakonfigurujte logické servery se správnými přihlašovacími údaji. Pokud nejste správcem předplatného nebo správce systému SQL Server, ve spolupráci se správcem přiřaďte potřebná oprávnění. Další informace najdete v tématu [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč v Azure Key trezoru, zajistěte, aby byl v cílových oblastech zřízen správný šifrovací materiál. Další informace najdete v tématu [Azure SQL transparentní šifrování dat s použitím klíčů spravovaných zákazníkem v Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Pokud je povolený audit na úrovni databáze, zakažte ho a místo toho povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat přenos mezi jednotlivými oblastmi, což nebude po přesunutí žádoucí ani možné. 
1. Pro audity na úrovni serveru zajistěte, aby:
   - Kontejner úložiště, Log Analytics nebo centrum událostí s existujícími protokoly auditu se přesunou do cílové oblasti. 
   - Auditování je konfigurováno na cílovém serveru. Další informace najdete v tématu [Začínáme s auditováním služby SQL Database](sql-database-auditing.md). 
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuálním serverem. Vzhledem k tomu, že cílový server je jiný, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, i když se server odstraní. 

  > [!NOTE]
  > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR na cílový server, který se v tuto chvíli nepodporuje. 

### <a name="prepare-resources"></a>Příprava prostředků

1. Vytvořte [skupinu převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) mezi logickým serverem zdroje na logický Server cíle.  
1. Přidejte databáze, které chcete přesunout do skupiny převzetí služeb při selhání. 
    - Replikace všech přidaných databází se iniciuje automaticky. Další informace najdete v tématu [osvědčené postupy pro používání skupin převzetí služeb při selhání s](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)izolovanými databázemi. 
 
### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) znamená, že databáze je synchronizovaná a je možné ji bezpečně převzít při selhání. 
   - **ReplicationState = 0** (OSAZENí) znamená, že databáze ještě není dosazený a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Synchronizace testů

Jakmile je `2`ReplicationState, připojte se ke každé databázi nebo podmnožinu databází pomocí sekundárního `<fog-name>.secondary.database.windows.net` koncového bodu a proveďte jakékoli dotazy k databázím, abyste zajistili připojení, správnou konfiguraci zabezpečení a data. umístění. 

### <a name="initiate-the-move"></a>Zahájit přesun

1. Připojte se k cílovému serveru pomocí sekundárního `<fog-name>.secondary.database.windows.net`koncového bodu.
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně, nebo se vrátí zpět. 
1. Ověřte, že se příkaz úspěšně dokončil pomocí `nslook up <fog-name>.secondary.database.windows.net` příkazu, aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud příkaz switch selže, záznam CNAME se nebude aktualizovat. 

### <a name="remove-the-source-databases"></a>Odebrat zdrojové databáze

Až se přesun dokončí, odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Odstraňte každou zdrojovou databázi pomocí [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) pro každou databázi na zdrojovém serveru. Tím se automaticky ukončí odkazy geografické replikace. 
1. Odstraňte zdrojový server pomocí [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Odeberte Trezor klíčů, auditujte kontejnery úložiště, centrum událostí, instanci AAD a další závislé prostředky a zastavte se jejich účtování. 

## <a name="move-elastic-pools"></a>Přesunout elastické fondy

### <a name="verify-prerequisites"></a>Ověření požadavků 

1. Vytvořte cílový logický Server pro každý zdrojový server. 
1. Nakonfigurujte bránu firewall pomocí správných výjimek pomocí [prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Nakonfigurujte logické servery se správnými přihlašovacími údaji. Pokud nejste správcem předplatného nebo správce systému SQL Server, ve spolupráci se správcem přiřaďte potřebná oprávnění. Další informace najdete v tématu [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč ve službě Azure Key trezor, ujistěte se, že je v cílové oblasti zřízen správný šifrovací materiál.
1. Vytvořte cílový elastický fond pro každý zdrojový elastický fond a ujistěte se, že se fond vytvoří ve stejné úrovni služby se stejným názvem a stejnou velikostí. 
1. Pokud je povolený audit na úrovni databáze, zakažte ho a místo toho povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat provoz mezi jednotlivými oblastmi, což není po přesunu žádoucí nebo možné. 
1. Pro audity na úrovni serveru zajistěte, aby:
    - Kontejner úložiště, Log Analytics nebo centrum událostí s existujícími protokoly auditu se přesunou do cílové oblasti.
    - Konfigurace auditu je nakonfigurovaná na cílovém serveru. Další informace najdete v tématu [auditování služby SQL Database](sql-database-auditing.md).
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuálním serverem. Vzhledem k tomu, že cílový server je jiný, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, i když se server odstraní. 

  > [!NOTE]
  > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR na cílový server, který se v tuto chvíli nepodporuje. 

### <a name="prepare-to-move"></a>Příprava na přesunutí
 
1.  Vytvořte samostatnou [skupinu převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) mezi každým elastickým fondem na zdrojovém logickém serveru a elastickým fondem jeho protějšků na cílovém serveru. 
1.  Přidejte všechny databáze ve fondu do skupiny převzetí služeb při selhání. 
    - Replikace přidaných databází se iniciuje automaticky. Další informace najdete v tématu [osvědčené postupy pro skupiny převzetí služeb při selhání s elastickými fondy](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > I když je možné vytvořit skupinu převzetí služeb při selhání, která zahrnuje více elastických fondů, důrazně doporučujeme vytvořit samostatnou skupinu převzetí služeb při selhání pro každý fond. Pokud máte velký počet databází v rámci více elastických fondů, které potřebujete přesunout, můžete postup přípravy spustit paralelně a pak paralelně zahájit krok přesunutí. Tento proces bude lépe škálovatelný a bude mít méně času v porovnání s více elastickými fondy ve stejné skupině převzetí služeb při selhání. 

### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) znamená, že databáze je synchronizovaná a je možné ji bezpečně převzít při selhání. 
   - **ReplicationState = 0** (OSAZENí) znamená, že databáze ještě není dosazený a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Synchronizace testů
 
Jakmile je `2`ReplicationState, připojte se ke každé databázi nebo podmnožinu databází pomocí sekundárního `<fog-name>.secondary.database.windows.net` koncového bodu a proveďte jakékoli dotazy k databázím, abyste zajistili připojení, správnou konfiguraci zabezpečení a data. umístění. 

### <a name="initiate-the-move"></a>Zahájit přesun
 
1. Připojte se k cílovému serveru pomocí sekundárního `<fog-name>.secondary.database.windows.net`koncového bodu.
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně, nebo se vrátí zpět. 
1. Ověřte, že se příkaz úspěšně dokončil pomocí `nslook up <fog-name>.secondary.database.windows.net` příkazu, aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud příkaz switch selže, záznam CNAME se nebude aktualizovat. 

### <a name="remove-the-source-elastic-pools"></a>Odebrat zdrojové elastické fondy
 
Až se přesun dokončí, odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Odstraňte jednotlivé zdrojové elastické fondy na zdrojovém serveru pomocí [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Odstraňte zdrojový server pomocí [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Odeberte Trezor klíčů, auditujte kontejnery úložiště, centrum událostí, instanci AAD a další závislé prostředky a zastavte se jejich účtování. 

## <a name="move-managed-instance"></a>Přesunout spravovanou instanci

### <a name="verify-prerequisites"></a>Ověření požadavků
 
1. Pro každou zdrojovou spravovanou instanci vytvořte cílovou spravovanou instanci stejné velikosti v cílové oblasti.  
1. Nakonfigurujte síť pro spravovanou instanci. Další informace najdete v tématu [Konfigurace sítě](sql-database-howto-managed-instance.md#network-configuration).
1. Nakonfigurujte cílovou hlavní databázi se správnými přihlašovacími údaji. Pokud nejste správcem předplatného nebo správce systému SQL Server, ve spolupráci se správcem přiřaďte potřebná oprávnění. 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč v trezoru klíčů Azure, zajistěte, aby integrace se stejnými šifrovacími klíči existovaly ve zdrojové i cílové oblasti. Další informace najdete v tématu [TDE s klíči spravovanými zákazníky v Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Pokud je audit pro instanci povolený, ujistěte se, že:
    - Kontejner úložiště nebo centrum událostí s existujícími protokoly se přesune do cílové oblasti. 
    - V cílové instanci je nakonfigurovaný audit. Další informace najdete v tématu [auditování pomocí spravované instance](sql-database-managed-instance-auditing.md).
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuálním serverem. Vzhledem k tomu, že cílový server je jiný, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, i když se server odstraní. 

  > [!NOTE]
  > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR na cílový server, který se v tuto chvíli nepodporuje. 

### <a name="prepare-resources"></a>Příprava prostředků

Vytvořte skupinu převzetí služeb při selhání mezi jednotlivými zdrojovými instancemi a odpovídající cílovou instancí.
    - Replikace všech databází na každé instanci se iniciuje automaticky. Další informace najdete v tématu [skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) znamená, že databáze je synchronizovaná a je možné ji bezpečně převzít při selhání. 
   - **ReplicationState = 0** (OSAZENí) znamená, že databáze ještě není dosazený a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Synchronizace testů

Jakmile je `2`ReplicationState, připojte se ke každé databázi nebo podmnožinu databází pomocí sekundárního `<fog-name>.secondary.database.windows.net` koncového bodu a proveďte jakékoli dotazy k databázím, abyste zajistili připojení, správnou konfiguraci zabezpečení a data. umístění. 

### <a name="initiate-the-move"></a>Zahájit přesun 

1. Připojte se k cílovému serveru pomocí sekundárního `<fog-name>.secondary.database.windows.net`koncového bodu.
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně, nebo se vrátí zpět. 
1. Ověřte, že se příkaz úspěšně dokončil pomocí `nslook up <fog-name>.secondary.database.windows.net` příkazu, aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud příkaz switch selže, záznam CNAME se nebude aktualizovat. 

### <a name="remove-the-source-managed-instances"></a>Odebrat zdrojové spravované instance
Až se přesun dokončí, odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Tím se odeberou konfigurace skupiny převzetí služeb při selhání a ukončí se propojení geografické replikace mezi těmito dvěma instancemi. 
1. Odstraňte zdrojovou spravovanou instanci pomocí [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Odeberte všechny další prostředky ve skupině prostředků, jako je třeba virtuální cluster, virtuální síť a skupina zabezpečení. 

## <a name="next-steps"></a>Další postup 

Po migraci Azure SQL Database [Spravovat](sql-database-manage-after-migration.md) . 

