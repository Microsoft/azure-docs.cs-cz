---
title: Přesunutí prostředků do nové oblasti
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si, jak přesunout databázi nebo spravovanou instanci do jiné oblasti.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: 0b78419f4fb37bb96e2c71c89f740a35914ccede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446381"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Přesunutí prostředků do nové oblasti – Azure SQL Database & spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tento článek vás seznámí s obecným pracovním postupem přesunutí databáze nebo spravované instance do nové oblasti.

## <a name="overview"></a>Přehled

Existují různé scénáře, ve kterých byste chtěli přesunout existující databázi nebo spravovanou instanci z jedné oblasti do druhé. Například rozšíříte svou firmu do nové oblasti a chcete ji optimalizovat pro novou zákaznickou základnu. Nebo je nutné operace přesunout do jiné oblasti z důvodu dodržování předpisů. Nebo Azure vydali novou oblast, která poskytuje lepší blízkost a zlepšuje prostředí pro zákazníky.  

Tento článek poskytuje obecný pracovní postup pro přesun prostředků do jiné oblasti. Pracovní postup se skládá z následujících kroků:

1. Ověřte požadavky pro přesun.
1. Připravte se na přesunutí prostředků v oboru.
1. Monitorujte proces přípravy.
1. Otestujte proces přesunutí.
1. Zahajte aktuální přesun.
1. Odeberte prostředky ze zdrojové oblasti.

> [!NOTE]
> Tento článek se týká migrací ve veřejném cloudu Azure nebo v rámci stejného cloudového cloudu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Přesunutí databáze

### <a name="verify-prerequisites"></a>Ověření požadavků

1. Vytvořte cílový server pro každý zdrojový server.
1. Nakonfigurujte bránu firewall správnými výjimkami pomocí [prostředí PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Nakonfigurujte servery se správnými přihlašovacími údaji. Pokud nejste správcem předplatného nebo správce systému SQL Server, ve spolupráci se správcem přiřaďte potřebná oprávnění. Další informace najdete v tématu [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md).
1. Pokud jsou vaše databáze šifrované pomocí transparentního šifrování dat a v Azure Key Vault používat vlastní šifrovací klíč, zajistěte, aby byl v cílových oblastech zřízen správný šifrovací materiál. Další informace najdete v tématu [transparentní šifrování dat Azure SQL pomocí klíčů spravovaných zákazníkem v Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Pokud je povolený audit na úrovni databáze, zakažte ho a místo toho povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat provoz mezi jednotlivými oblastmi, který po přesunu není žádoucí ani možné.
1. Pro audity na úrovni serveru zajistěte, aby:
   - Kontejner úložiště, Log Analytics nebo centrum událostí s existujícími protokoly auditu se přesunou do cílové oblasti.
   - Auditování je konfigurováno na cílovém serveru. Další informace najdete v tématu [Začínáme s auditováním SQL Database](../../azure-sql/database/auditing-overview.md).
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuálním serverem. Vzhledem k tomu, že cílový server je jiný, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, i když se server odstraní.

      > [!NOTE]
      > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR na cílový server, který se v tuto chvíli nepodporuje.

### <a name="prepare-resources"></a>Příprava prostředků

1. Vytvořte [skupinu převzetí služeb při selhání](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) mezi serverem zdrojového kódu a serverem cíle.  
1. Přidejte databáze, které chcete přesunout do skupiny převzetí služeb při selhání.
  
    Replikace všech přidaných databází se iniciuje automaticky. Další informace najdete v tématu [osvědčené postupy pro používání skupin převzetí služeb při selhání s izolovanými databázemi](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) znamená, že se databáze synchronizuje a že je možné provést její bezpečné převzetí služeb při selhání.
- **ReplicationState = 0** (osazení) znamená, že databáze ještě není osazená, a pokus o převzetí služeb při selhání se nezdaří.

### <a name="test-synchronization"></a>Synchronizace testů

Po **ReplicationState** je `2` připojte ke každé databázi nebo podmnožině databází pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` a proveďte jakékoli dotazy proti databázím, aby se zajistilo připojení, správná konfigurace zabezpečení a replikace dat.

### <a name="initiate-the-move"></a>Zahájit přesun

1. Připojte se k cílovému serveru pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` .
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně nebo se vrátí zpět.
1. Ověřte, že se příkaz úspěšně dokončil pomocí příkazu `nslook up <fog-name>.secondary.database.windows.net` , aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud se příkaz switch nezdařil, záznam CNAME nebude aktualizován.

### <a name="remove-the-source-databases"></a>Odebrat zdrojové databáze

Až se přesun dokončí, odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům.

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Odstraňte každou zdrojovou databázi pomocí [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) pro každou databázi na zdrojovém serveru. Tím se automaticky ukončí odkazy geografické replikace.
1. Odstraňte zdrojový server pomocí [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Odebrání trezoru klíčů, auditu kontejnerů úložiště, služby Event hub, Azure Active Directory (Azure AD) a dalších závislých prostředků, které se budou zastavovat, se jim budou účtovat.

## <a name="move-elastic-pools"></a>Přesunout elastické fondy

### <a name="verify-prerequisites"></a>Ověření požadavků

1. Vytvořte cílový server pro každý zdrojový server.
1. Nakonfigurujte bránu firewall pomocí správných výjimek pomocí [prostředí PowerShell](scripts/create-and-configure-database-powershell.md).
1. Nakonfigurujte servery se správnými přihlašovacími údaji. Pokud nejste správcem předplatného nebo správcem serveru, spolupracujte se správcem a přiřaďte potřebná oprávnění. Další informace najdete v tématu [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md).
1. Pokud jsou vaše databáze šifrované pomocí transparentního šifrování dat a v Azure Key Vault používat vlastní šifrovací klíč, ujistěte se, že je v cílové oblasti zřízen správný šifrovací materiál.
1. Vytvořte cílový elastický fond pro každý zdrojový elastický fond a ujistěte se, že se fond vytvoří ve stejné úrovni služby se stejným názvem a stejnou velikostí.
1. Pokud je povolený audit na úrovni databáze, zakažte ho a místo toho povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat provoz mezi jednotlivými oblastmi, což není po přesunu žádoucí nebo možné.
1. Pro audity na úrovni serveru zajistěte, aby:
    - Kontejner úložiště, Log Analytics nebo centrum událostí s existujícími protokoly auditu se přesunou do cílové oblasti.
    - Konfigurace auditu je nakonfigurovaná na cílovém serveru. Další informace najdete v tématu [auditování SQL Database](../../azure-sql/database/auditing-overview.md).
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuálním serverem. Vzhledem k tomu, že cílový server je jiný, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, i když se server odstraní.

      > [!NOTE]
      > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR na cílový server, který se v tuto chvíli nepodporuje.

### <a name="prepare-to-move"></a>Příprava na přesunutí

1. Vytvořte samostatnou [skupinu převzetí služeb při selhání](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) mezi každým elastickým fondem na zdrojovém serveru a elastickým fondem protějšků na cílovém serveru.
1. Přidejte všechny databáze ve fondu do skupiny převzetí služeb při selhání.

    Replikace přidaných databází se iniciuje automaticky. Další informace najdete v tématu [osvědčené postupy pro skupiny převzetí služeb při selhání s elastickými fondy](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > I když je možné vytvořit skupinu převzetí služeb při selhání, která zahrnuje více elastických fondů, důrazně doporučujeme vytvořit samostatnou skupinu převzetí služeb při selhání pro každý fond. Pokud máte velký počet databází v rámci více elastických fondů, které potřebujete přesunout, můžete postup přípravy spustit paralelně a pak paralelně zahájit krok přesunutí. Tento proces bude lépe škálovatelný a bude mít méně času v porovnání s více elastickými fondy ve stejné skupině převzetí služeb při selhání.

### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) znamená, že se databáze synchronizuje a že je možné provést její bezpečné převzetí služeb při selhání.
- **ReplicationState = 0** (osazení) znamená, že databáze ještě není osazená, a pokus o převzetí služeb při selhání se nezdaří.

### <a name="test-synchronization"></a>Synchronizace testů

Jakmile **ReplicationState** je ReplicationState `2` , připojte se ke každé databázi nebo podmnožině databází pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` a proveďte jakýkoli dotaz na databáze, abyste zajistili dostupnost připojení, správnou konfiguraci zabezpečení a replikaci dat.

### <a name="initiate-the-move"></a>Zahájit přesun

1. Připojte se k cílovému serveru pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` .
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně, nebo se vrátí zpět.
1. Ověřte, že se příkaz úspěšně dokončil pomocí příkazu `nslook up <fog-name>.secondary.database.windows.net` , aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud se příkaz switch nezdařil, záznam CNAME nebude aktualizován.

### <a name="remove-the-source-elastic-pools"></a>Odebrat zdrojové elastické fondy

Až se přesun dokončí, odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům.

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Odstraňte jednotlivé zdrojové elastické fondy na zdrojovém serveru pomocí [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Odstraňte zdrojový server pomocí [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Odeberte Trezor klíčů, auditujte kontejnery úložiště, centrum událostí, instance Azure AD a další závislé prostředky, aby se za ně neúčtují.

## <a name="move-a-managed-instance"></a>Přesunutí spravované instance

### <a name="verify-prerequisites"></a>Ověření požadavků

1. Pro každou zdrojovou spravovanou instanci vytvořte cílovou instanci spravované instance SQL se stejnou velikostí v cílové oblasti.  
1. Nakonfigurujte síť pro spravovanou instanci. Další informace najdete v tématu [Konfigurace sítě](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Nakonfigurujte cílovou hlavní databázi se správnými přihlašovacími údaji. Pokud nejste předplatné nebo správce spravované instance SQL, ve spolupráci se správcem přiřaďte potřebná oprávnění.
1. Pokud jsou vaše databáze šifrované pomocí transparentního šifrování dat a používají v Azure Key Vault vlastní šifrovací klíč, ujistěte se, že Azure Key Vault se stejnými šifrovacími klíči existují ve zdrojové i cílové oblasti. Další informace najdete v tématu [transparentní šifrování dat pomocí klíčů spravovaných zákazníkem v Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Pokud je audit pro spravovanou instanci povolený, ujistěte se, že:
    - Kontejner úložiště nebo centrum událostí s existujícími protokoly se přesune do cílové oblasti.
    - V cílové instanci je nakonfigurovaný audit. Další informace najdete v tématu [auditování pomocí spravované instance SQL](../managed-instance/auditing-configure.md).
1. Pokud má vaše instance dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou spojené s aktuální instancí. Vzhledem k tomu, že je cílová instance odlišná, budete mít přístup k starším zálohám LTR ve zdrojové oblasti pomocí zdrojové instance, a to i v případě, že dojde k odstranění instance.

  > [!NOTE]
  > To bude pro přesun mezi svrchovaným cloudem a veřejnou oblastí nedostatečné. Tato migrace bude vyžadovat přesunutí záloh LTR do cílové instance, která se v tuto chvíli nepodporuje.

### <a name="prepare-resources"></a>Příprava prostředků

Vytvořte skupinu převzetí služeb při selhání mezi každou zdrojovou spravovanou instancí a odpovídající cílovou instancí spravované instance SQL.

Replikace všech databází na každé instanci se iniciuje automaticky. Další informace najdete v tématu [skupiny s automatickým převzetím služeb při selhání](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Monitorování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) a monitorovat replikaci vašich databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost pro **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) znamená, že se databáze synchronizuje a že je možné provést její bezpečné převzetí služeb při selhání.
- **ReplicationState = 0** (osazení) znamená, že databáze ještě není osazená, a pokus o převzetí služeb při selhání se nezdaří.

### <a name="test-synchronization"></a>Synchronizace testů

Jakmile **ReplicationState** je ReplicationState `2` , připojte se ke každé databázi nebo podmnožinu databází pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` a proveďte jakékoli dotazy k databázím, abyste zajistili dostupnost připojení, správnou konfiguraci zabezpečení a replikaci dat.

### <a name="initiate-the-move"></a>Zahájit přesun

1. Připojte se k cílové spravované instanci pomocí sekundárního koncového bodu `<fog-name>.secondary.database.windows.net` .
1. Pomocí [přepínače-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepněte sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace proběhne úspěšně, nebo se vrátí zpět.
1. Ověřte, že se příkaz úspěšně dokončil pomocí příkazu `nslook up <fog-name>.secondary.database.windows.net` , aby se ověřilo, že záznam DNS CNAME odkazuje na IP adresu cílové oblasti. Pokud se příkaz switch nezdařil, záznam CNAME nebude aktualizován.

### <a name="remove-the-source-managed-instances"></a>Odebrat zdrojové spravované instance

Po dokončení přesunu odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům.

1. Skupinu převzetí služeb při selhání odstraňte pomocí [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Tím se odeberou konfigurace skupiny převzetí služeb při selhání a ukončí se propojení geografické replikace mezi těmito dvěma instancemi.
1. Odstraňte zdrojovou spravovanou instanci pomocí [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Odeberte všechny další prostředky ve skupině prostředků, jako je třeba virtuální cluster, virtuální síť a skupina zabezpečení.

## <a name="next-steps"></a>Další kroky

[Spravujte](manage-data-after-migrating-to-database.md) databázi po jejím migraci.
