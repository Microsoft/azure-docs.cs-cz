---
title: Jak přesunout zdroje do jiné oblasti
description: Zjistěte, jak přesunout azure SQL database, elastický fond Azure SQL nebo spravanou instanci Azure SQL do jiné oblasti.
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
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821437"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Jak přesunout prostředky Azure SQL do jiné oblasti

Tento článek vás naučí obecný pracovní postup, jak přesunout jednu databázi Azure SQL Database, elastický fond a spravovanou instanci do nové oblasti. 

## <a name="overview"></a>Přehled

Existují různé scénáře, ve kterých byste chtěli přesunout existující prostředky Azure SQL z jedné oblasti do druhé. Například můžete rozšířit své podnikání do nové oblasti a chcete ji optimalizovat pro novou zákaznickou základnu. Nebo je třeba přesunout operace do jiné oblasti z důvodů dodržování předpisů. Nebo Azure vydala zbrusu novou oblast, která poskytuje lepší blízkost a zlepšuje zákaznickou zkušenost.  

Tento článek obsahuje obecný pracovní postup pro přesun prostředků do jiné oblasti. Pracovní postup se skládá z následujících kroků: 

- Ověření předpokladů pro přesun 
- Příprava na přesunutí zdrojů v rozsahu
- Sledování procesu přípravy
- Otestujte proces přesunu
- Zahájit skutečný tah 
- Odebrání prostředků ze zdrojové oblasti 


> [!NOTE]
> Tento článek se vztahuje na migrace v rámci veřejného cloudu Azure nebo ve stejném suverénním cloudu. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Přesunutí jedné databáze

### <a name="verify-prerequisites"></a>Ověření požadavků 

1. Vytvořte cílový logický server pro každý zdrojový server. 
1. Nakonfigurujte bránu firewall s pravými výjimkami pomocí [prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Nakonfigurujte logické servery se správnými přihlášeními. Pokud nejste správce předplatného nebo správce serveru SQL, spolupracujte se správcem a přiřaďte oprávnění, která potřebujete. Další informace najdete v tématu [Jak spravovat zabezpečení databáze Azure SQL po zotavení po havárii](sql-database-geo-replication-security-config.md). 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč v trezoru klíčů Azure, ujistěte se, že správný šifrovací materiál je zřízen v cílových oblastech. Další informace najdete v tématu [Azure SQL Transparent Data Encryption s klíči spravovanými zákazníky v Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Pokud je povolen audit na úrovni databáze, zakažte jej a povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat přenos mezi oblastmi, který nebude žádoucí nebo možný po přesunutí. 
1. U auditů na úrovni serveru zajistěte, aby:
   - Kontejner úložiště, Analýza protokolů nebo centrum událostí s existujícími protokoly auditu se přesune do cílové oblasti. 
   - Auditování je konfigurováno na cílovém serveru. Další informace naleznete [v tématu Začínáme s auditováním databáze SQL](sql-database-auditing.md). 
1. Pokud vaše instance má dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou přidruženy k aktuálnímu serveru. Vzhledem k tomu, že cílový server je jiný, budete mít přístup ke starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, a to i v případě, že je server odstraněn. 

  > [!NOTE]
  > To bude stačit pro přechod mezi suverénním cloudem a veřejným regionem. Taková migrace bude vyžadovat přesunutí záloh LTR na cílový server, který není aktuálně podporován. 

### <a name="prepare-resources"></a>Příprava zdrojů

1. Vytvořte [skupinu převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) mezi logickým serverem zdroje na logický server cíle.  
1. Přidejte databáze, které chcete přesunout do skupiny převzetí služeb při selhání. 
    - Replikace všech přidaných databází bude zahájena automaticky. Další informace naleznete v [tématu Doporučené postupy pro použití skupin s podporou převzetí služeb při selhání s jednotlivými databázemi](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Sledování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) sledovat replikaci databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost **pro ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) označuje, že databáze je synchronizována a může být bezpečně převzetí služby při selhání. 
   - **ReplicationState = 0** (SEEDING) označuje, že databáze ještě není nasazena a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Testovací synchronizace

Jakmile **ReplicationState** je `2`, připojit ke každé databázi nebo podmnožinu databází pomocí sekundární koncový bod `<fog-name>.secondary.database.windows.net` a provádět jakýkoli dotaz proti databázím k zajištění připojení, správné konfigurace zabezpečení a replikace dat. 

### <a name="initiate-the-move"></a>Zahájit přesun

1. Připojte se k cílovému serveru `<fog-name>.secondary.database.windows.net`pomocí sekundárního koncového bodu .
1. Pomocí [switch-azSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepnete sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace bude buď úspěšná, nebo se vrátí zpět. 
1. Ověřte, zda byl příkaz `nslook up <fog-name>.secondary.database.windows.net` úspěšně dokončen, pomocí ověření, zda položka DNS CNAME odkazuje na adresu IP cílové oblasti. Pokud se příkaz přepínače nezdaří, CNAME nebude aktualizován. 

### <a name="remove-the-source-databases"></a>Odebrání zdrojových databází

Po dokončení přesunu odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Odstraňte skupinu převzetí služeb při selhání pomocí [skupiny Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Odstraňte každou zdrojovou databázi pomocí [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) pro každou z databází na zdrojovém serveru. Tím se automaticky ukončí propojení geografické replikace. 
1. Odstraňte zdrojový server pomocí [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Odeberte trezor klíčů, kontejnery úložiště auditu, centrum událostí, instanci AAD a další závislé prostředky, abyste za ně přestali účtovat. 

## <a name="move-elastic-pools"></a>Přesunutí elastických fondů

### <a name="verify-prerequisites"></a>Ověření požadavků 

1. Vytvořte cílový logický server pro každý zdrojový server. 
1. Nakonfigurujte bránu firewall s pravými výjimkami pomocí [prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Nakonfigurujte logické servery se správnými přihlášeními. Pokud nejste správce předplatného nebo správce serveru SQL, spolupracujte se správcem a přiřaďte oprávnění, která potřebujete. Další informace najdete v tématu [Jak spravovat zabezpečení databáze Azure SQL po zotavení po havárii](sql-database-geo-replication-security-config.md). 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč v trezoru klíčů Azure, ujistěte se, že je v cílové oblasti zřízen správný šifrovací materiál.
1. Vytvořte cílový elastický fond pro každý zdrojový elastický fond a ujistěte se, že fond je vytvořen ve stejné vrstvě služby se stejným názvem a stejnou velikostí. 
1. Pokud je povolen audit na úrovni databáze, zakažte jej a povolte auditování na úrovni serveru. Po převzetí služeb při selhání bude auditování na úrovni databáze vyžadovat přenos mezi oblastmi, což není žádoucí nebo možné po přesunutí. 
1. U auditů na úrovni serveru zajistěte, aby:
    - Kontejner úložiště, Analýza protokolů nebo centrum událostí s existujícími protokoly auditu se přesune do cílové oblasti.
    - Konfigurace auditu je konfigurována na cílovém serveru. Další informace naleznete v [tématu auditování databáze SQL](sql-database-auditing.md).
1. Pokud vaše instance má dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou přidruženy k aktuálnímu serveru. Vzhledem k tomu, že cílový server je jiný, budete mít přístup ke starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, a to i v případě, že je server odstraněn. 

  > [!NOTE]
  > To bude stačit pro přechod mezi suverénním cloudem a veřejným regionem. Taková migrace bude vyžadovat přesunutí záloh LTR na cílový server, který není aktuálně podporován. 

### <a name="prepare-to-move"></a>Připravte se na přesun
 
1.  Vytvořte samostatnou [skupinu převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) mezi jednotlivými elastickými fondy na zdrojovém logickém serveru a jeho protějškem elastického fondu na cílovém serveru. 
1.  Přidejte všechny databáze ve fondu do skupiny převzetí služeb při selhání. 
    - Replikace přidaných databází bude zahájena automaticky. Další informace naleznete v [doporučených postupech pro skupiny s podporou převzetí služeb při selhání s elastické fondy](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > I když je možné vytvořit skupinu převzetí služeb při selhání, která obsahuje více elastických fondů, důrazně doporučujeme vytvořit samostatnou skupinu převzetí služeb při selhání pro každý fond. Pokud máte velký počet databází napříč více elastických fondů, které je třeba přesunout, můžete spustit kroky přípravy paralelně a potom zahájit krok přesunutí paralelně. Tento proces bude škálovat lépe a bude trvat méně času ve srovnání s více elastické fondy ve stejné skupině převzetí služeb při selhání. 

### <a name="monitor-the-preparation-process"></a>Sledování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) sledovat replikaci databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost **pro ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) označuje, že databáze je synchronizována a může být bezpečně převzetí služby při selhání. 
   - **ReplicationState = 0** (SEEDING) označuje, že databáze ještě není nasazena a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Testovací synchronizace
 
Jakmile **ReplicationState** je `2`, připojit ke každé databázi nebo podmnožinu databází pomocí sekundární koncový bod `<fog-name>.secondary.database.windows.net` a provádět jakýkoli dotaz proti databázím k zajištění připojení, správné konfigurace zabezpečení a replikace dat. 

### <a name="initiate-the-move"></a>Zahájit přesun
 
1. Připojte se k cílovému serveru `<fog-name>.secondary.database.windows.net`pomocí sekundárního koncového bodu .
1. Pomocí [switch-azSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) přepnete sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace bude buď úspěšná, nebo se vrátí zpět. 
1. Ověřte, zda byl příkaz `nslook up <fog-name>.secondary.database.windows.net` úspěšně dokončen, pomocí ověření, zda položka DNS CNAME odkazuje na adresu IP cílové oblasti. Pokud se příkaz přepínače nezdaří, CNAME nebude aktualizován. 

### <a name="remove-the-source-elastic-pools"></a>Odebrání zdrojových elastických fondů
 
Po dokončení přesunu odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Odstraňte skupinu převzetí služeb při selhání pomocí [skupiny Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Odstraňte každý zdrojový elastický fond na zdrojovém serveru pomocí [nástroje Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Odstraňte zdrojový server pomocí [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Odeberte trezor klíčů, kontejnery úložiště auditu, centrum událostí, instanci AAD a další závislé prostředky, abyste za ně přestali účtovat. 

## <a name="move-managed-instance"></a>Přesunutí spravované instance

### <a name="verify-prerequisites"></a>Ověření požadavků
 
1. Pro každou zdrojovou spravovanou instanci vytvořte cílovou spravovanou instanci stejné velikosti v cílové oblasti.  
1. Nakonfigurujte síť pro spravovanou instanci. Další informace naleznete v [tématu konfigurace sítě](sql-database-howto-managed-instance.md#network-configuration).
1. Nakonfigurujte cílovou hlavní databázi se správnými přihlášeními. Pokud nejste správce předplatného nebo správce serveru SQL, spolupracujte se správcem a přiřaďte oprávnění, která potřebujete. 
1. Pokud jsou vaše databáze šifrované pomocí TDE a používají vlastní šifrovací klíč v trezoru klíčů Azure, ujistěte se, že AKV s identickými šifrovacími klíči existuje ve zdrojových i cílových oblastech. Další informace naleznete [v tématu TDE s klíči spravovanými zákazníky v azure key vault](transparent-data-encryption-byok-azure-sql.md).
1. Pokud je pro instanci povolen audit, ujistěte se, že:
    - Kontejner úložiště nebo centrum událostí s existujícíprotokoly se přesune do cílové oblasti. 
    - Audit je nakonfigurován pro cílovou instanci. Další informace naleznete v [tématu auditování se spravovanou instancí](sql-database-managed-instance-auditing.md).
1. Pokud vaše instance má dlouhodobé zásady uchovávání informací (LTR), existující zálohy LTR zůstanou přidruženy k aktuálnímu serveru. Vzhledem k tomu, že cílový server je jiný, budete mít přístup ke starším zálohám LTR ve zdrojové oblasti pomocí zdrojového serveru, a to i v případě, že je server odstraněn. 

  > [!NOTE]
  > To bude stačit pro přechod mezi suverénním cloudem a veřejným regionem. Taková migrace bude vyžadovat přesunutí záloh LTR na cílový server, který není aktuálně podporován. 

### <a name="prepare-resources"></a>Příprava zdrojů

Vytvořte skupinu převzetí služeb při selhání mezi každou zdrojovou instanci a odpovídající cílovou instancí.
    - Replikace všech databází v každé instanci bude zahájena automaticky. Další informace naleznete [v tématu Skupiny automatického převzetí služeb při selhání.](sql-database-auto-failover-group.md)

 
### <a name="monitor-the-preparation-process"></a>Sledování procesu přípravy

Můžete pravidelně volat [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) sledovat replikaci databází ze zdroje do cíle. Výstupní objekt `Get-AzSqlDatabaseFailoverGroup` obsahuje vlastnost **pro ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) označuje, že databáze je synchronizována a může být bezpečně převzetí služby při selhání. 
   - **ReplicationState = 0** (SEEDING) označuje, že databáze ještě není nasazena a pokus o převzetí služeb při selhání se nezdaří. 

### <a name="test-synchronization"></a>Testovací synchronizace

Jakmile **ReplicationState** je `2`, připojit ke každé databázi nebo podmnožinu databází pomocí sekundární koncový bod `<fog-name>.secondary.database.windows.net` a provádět jakýkoli dotaz proti databázím k zajištění připojení, správné konfigurace zabezpečení a replikace dat. 

### <a name="initiate-the-move"></a>Zahájit přesun 

1. Připojte se k cílovému serveru `<fog-name>.secondary.database.windows.net`pomocí sekundárního koncového bodu .
1. Pomocí [switch-azSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) přepnete sekundární spravovanou instanci na primární s úplnou synchronizací. Tato operace bude buď úspěšná, nebo se vrátí zpět. 
1. Ověřte, zda byl příkaz `nslook up <fog-name>.secondary.database.windows.net` úspěšně dokončen, pomocí ověření, zda položka DNS CNAME odkazuje na adresu IP cílové oblasti. Pokud se příkaz přepínače nezdaří, CNAME nebude aktualizován. 

### <a name="remove-the-source-managed-instances"></a>Odebrání zdrojových spravovaných instancí
Po dokončení přesunu odeberte prostředky ve zdrojové oblasti, abyste se vyhnuli zbytečným poplatkům. 

1. Odstraňte skupinu převzetí služeb při selhání pomocí [skupiny Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Tím dojde k přetažení konfigurace skupiny převzetí služeb při selhání a ukončit geografické replikace propojení mezi dvěma instancemi. 
1. Odstraňte zdrojovou spravovanou instanci pomocí [aplikace Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Odeberte všechny další prostředky ve skupině prostředků, jako je například virtuální cluster, virtuální síť a skupina zabezpečení. 

## <a name="next-steps"></a>Další kroky 

[Spravujte](sql-database-manage-after-migration.md) azure sql database po migraci. 

