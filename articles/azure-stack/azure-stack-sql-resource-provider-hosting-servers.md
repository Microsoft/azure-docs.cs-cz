---
title: Hostitelské servery v zásobníku Azure SQL | Microsoft Docs
description: Postup přidání instance SQL pro zřizování prostřednictvím poskytovatele prostředků adaptér SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.openlocfilehash: a89e5bf48c24abf72f18ee98f2dcb0eda6db35cd
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků SQL
Instance SQL můžete použít na virtuální počítače uvnitř vaší [zásobník Azure](azure-stack-poc.md), nebo k nim mohla připojit instanci mimo prostředí Azure zásobníku, pokud poskytovatel prostředků. Obecné požadavky jsou:

* SQL instance musí být vyhrazená pro použití úlohami RP a uživatele. Nelze použít instanci SQL, který se používá jakékoli další příjemce, včetně aplikační služby.
* Zprostředkovatel prostředků SQL virtuální počítač není připojený k doméně a lze připojit pouze pomocí ověřování SQL.
* Musíte nakonfigurovat účet s náležitými oprávněními pro použití poskytovatelem prostředků.
* Poskytovatel prostředků a uživatelé, třeba webové aplikace, využívají sítě uživatele, takže není vyžadována možnost připojení k instanci serveru SQL v této síti. Tento požadavek se obvykle znamená, že IP adresa pro vaše instance SQL musí být ve veřejné síti.
* Správa instance SQL a jejich hostitelů závisí na vás; Zprostředkovatel prostředků neobsahuje provedení oprav, zálohování, přihlašovací údaje otočení atd.
* SKU lze použít k vytvoření různé třídy dalo SQL, jako je například výkonu, vždycky na atd.

Počet bitové kopie virtuálních počítačů SQL IaaS jsou k dispozici prostřednictvím funkce správy Marketplace. Ověřte, že je vždy stáhnout nejnovější verzi **SQL IaaS rozšíření** před nasazením virtuálního počítače pomocí příslušné položky Marketplace. Image s SQL serverem jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure. Pro virtuální počítače SQL vytvořené z těchto bitových kopií, rozšíření IaaS a odpovídající portálu vylepšení poskytují funkce, jako je automatické opravy a možnosti zálohování.

Existují další možnosti pro nasazení virtuálních počítačů SQL, včetně šablon v [galerii pro rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Všechny hostitelské servery, které jsou nainstalované v zásobníku Azure několika uzly musí být vytvořeny z předplatného uživatele. Nemohou být vytvářeny ze předplatného výchozí zprostředkovatel. Musí být vytvořený z portálu pro uživatele nebo z relace prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci SQL. Správce služeb _můžete_ být vlastníkem daného předplatného.


### <a name="required-privileges"></a>Požadovaná oprávnění

Můžete vytvořit nového správce s oprávněními nižší než úplné sysadmin. Určité operace, které musí být povoleny jsou:

- Databáze: Vytvořit, změna, s členství ve skupině (Always On jenom), Drop, zálohování
- Skupina dostupnosti: Alter, připojení, přidat nebo odebrat databáze
- Přihlášení: Vytvořit, vyberte, Alter, Drop, odvolání
- Vyberte operace: \[hlavní\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sloupec sys.availability_replicas (AlwaysOn), zobrazení sys.databases, \[hlavní\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[hlavní\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zadejte kapacitu připojením na samostatnou hostitelem SQL serveru
Můžete použít samostatné (bez-HA) servery SQL Server prostřednictvím jakékoli edici systému SQL Server 2014 nebo SQL Server 2016. Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému.

Pokud chcete přidat samostatný server, který je již zřízeno hostování, postupujte takto:

1. Přihlaste se k portálu pro správu zásobník Azure jako správce služeb

2. Klikněte na tlačítko **Procházet** &gt; **prostředky pro správu** &gt; **hostitelské servery SQL**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  **Hostování servery SQL** okno je, kde můžete připojit zprostředkovatele prostředků SQL serveru k skutečné instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.

  ![Hostitelské servery](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Vyplňte formulář Podrobnosti připojení instanci služby SQL Server.

  ![Nové hostitelské serveru](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Volitelně můžete zahrnout název instance a číslo portu lze zadat, pokud instance není přiřazen k výchozí port 1433.

  > [!NOTE]
  > Tak dlouho, dokud SQL instance může mít přístup uživatele a správce Azure Resource Manager, mohou být umístěny pod kontrolou zprostředkovatele prostředků. SQL instance __musí__ přidělit výhradně RP.

4. Při přidávání serverů, musíte je přiřadit k nové nebo existující SKU k odlišení nabídky služeb. Například může mít instanci SQL Enterprise poskytuje:
  - kapacita databáze
  - Automatické zálohování
  - Rezervovat vysoce výkonné servery pro jednotlivá oddělení
  - a tak dále.

  Název SKU by měla odpovídat vlastnosti tak, aby uživatelé můžete umístit své databáze správně. Všechny hostitelské servery v SKU musí mít stejné funkce.

> [!IMPORTANT]
> Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** nebo **vrstvy** názvy při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL.

Příklad:

![Skladové položky](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Uživatele nelze vytvořit databázi, dokud nebude plně vytvořen verze SKU.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Zadejte kapacitu pomocí SQL skupin dostupnosti Always On
Instance SQL Always On konfigurace vyžaduje další kroky a zahrnuje minimálně tři virtuální počítače (nebo fyzického počítače).

> [!NOTE]
> Adaptér SQL RP _pouze_ podporuje SQL 2016 SP1 Enterprise nebo novější instance pro Always On, protože vyžaduje nové funkce SQL, jako je například automatická synchronizace replik indexů. Kromě předchozích seznam běžných požadavků:

* Je nutné zadat souborový server kromě počítačů SQL Always On. Je [šablony Azure Quickstart zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) toto prostředí, můžete vytvořit za vás. Také mohl sloužit jako vodítko k sestavení vlastní instanci.

* Je nutné nastavit servery SQL Server. Konkrétně je nutné povolit [Automatická synchronizace replik indexů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou skupinu dostupnosti pro každou instanci systému SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

Na sekundární instancí
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Pokud chcete přidat SQL Always On hostitelskými servery, postupujte takto:

1. Přihlaste se k portálu pro správu zásobník Azure jako správce služby

2. Klikněte na tlačítko **Procházet** &gt; **prostředky pro správu** &gt; **SQL hostitelské servery** &gt; **+ přidat**.

    **Hostování servery SQL** okno je, kde můžete připojit zprostředkovatele prostředků SQL serveru k skutečné instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.


3. Vyplňte formulář Podrobnosti připojení vaší instance systému SQL Server, nezapomeňte použít plně kvalifikovaný název domény nebo IPv4 adresu vždy na naslouchacího procesu (a volitelně také portu číslo). Zadejte informace o účtu pro účet, který jste nakonfigurovali s oprávněními správce systému.

4. Zaškrtnutím tohoto políčka Povolit podporu pro instance vždy na skupiny dostupnosti systému SQL.

    ![Hostitelské servery](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Přidáte k instanci SQL Always On SKU. Samostatné servery nelze kombinovat s instancí Always On ve stejné SKU. Který určí při přidávání první server pro hostování. Probíhá pokus o různé typy později, bude mít za následek chybu.


## <a name="making-sql-databases-available-to-users"></a>Zpřístupnění databáze SQL pro uživatele

Vytvořte plány a nabízí chcete zpřístupnit databáze SQL pro uživatele. Přidání služby Microsoft.SqlAdapter k plánu a přidat buď existující kvótu nebo vytvořte novou. Pokud vytvoříte kvótu, zadáte kapacitu uživatelům.

![Vytvoření plánu a nabídky zahrnout databáze](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>Další postup

[Přidat databáze](azure-stack-sql-resource-provider-databases.md)
