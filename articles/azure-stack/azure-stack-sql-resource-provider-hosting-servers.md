---
title: Hostitelské servery v zásobníku Azure SQL | Microsoft Docs
description: Postup přidání instance SQL pro zřizování prostřednictvím poskytovatele prostředků adaptér SQL.
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265224"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků SQL

Je možné hostovat v instanci SQL na virtuálním počítači (VM) [zásobník Azure](azure-stack-poc.md), nebo na virtuálním počítači mimo prostředí Azure zásobníku, pokud zprostředkovatel prostředků SQL můžete připojit k instanci.

## <a name="overview"></a>Přehled

Obecné požadavky pro hostování servery SQL jsou:

* SQL instance musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci SQL, který se používá další příjemce. Toto omezení platí i pro aplikační služby.
* Zprostředkovatel prostředků SQL virtuální počítač není připojený k doméně a lze připojit pouze pomocí ověřování SQL.
* Musíte nakonfigurovat účet s náležitými oprávněními pro použití poskytovatelem prostředků.
* Poskytovatel prostředků a uživatelé, třeba webové aplikace, využívají sítě uživatele, takže není vyžadována možnost připojení k instanci serveru SQL v této síti. Tento požadavek se obvykle znamená, že IP adresa pro vaše instance SQL musí být ve veřejné síti.
* Správa instance SQL a jejich hostitelů je na vás. Například nebude poskytovatele prostředků aktualizace, zpracování zálohy nebo zpracování pověření otočení.
* Můžete použít SKU, které podporují různé třídy dalo SQL, jako je například výkon a vysoké dostupnosti používají AlwaysOn.

### <a name="sql-server-virtual-machine-images"></a>Bitové kopie virtuálních počítačů systému SQL Server

Bitové kopie virtuálních počítačů SQL IaaS jsou k dispozici prostřednictvím funkce správy Marketplace. Tyto Image jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure.

Ověřte, že je vždy stáhnout nejnovější verzi **SQL IaaS rozšíření** před nasazením virtuálního počítače pomocí příslušné položky Marketplace.  Rozšíření IaaS a odpovídající portálu vylepšení poskytují další funkce, například automatické opravy a zálohování.

Existují další možnosti pro nasazení virtuálních počítačů SQL, včetně šablon v [galerii pro rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Všechny hostitelské servery, které jsou nainstalované v zásobníku Azure několika uzly musí být vytvořeny z předplatného uživatele. Nemohou být vytvářeny ze předplatného výchozí zprostředkovatel. Musí být vytvořený z portálu pro uživatele nebo z relace prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci SQL. Správce služeb _můžete_ být vlastníkem daného předplatného.

### <a name="required-privileges"></a>Požadovaná oprávnění

Můžete vytvořit administrativní uživatel s oprávněními nižší která správce sysadmin systému SQL. Uživatel se musí oprávnění pro následující operace:

- Databáze: Vytvořit, Alter, s členství ve skupině (pro Always On pouze), vyřadit, zálohování
- Skupina dostupnosti: Alter, připojení, přidat nebo odebrat databáze
- Přihlášení: Vytvořit, vyberte, Alter, Drop, odvolání
- Vyberte operace: \[hlavní\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sloupec sys.availability_replicas (AlwaysOn), zobrazení sys.databases, \[hlavní\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[hlavní\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zadejte kapacitu připojením na samostatnou hostitelem SQL serveru

Můžete použít samostatné (bez-HA) servery SQL Server prostřednictvím jakékoli edici systému SQL Server 2014 nebo SQL Server 2016. Ujistěte se, že máte přihlašovací údaje pro účet s oprávněními správce systému.

Pokud chcete přidat samostatný hostitelský server, který je již nastavena, postupujte takto:

1. Přihlaste se k portálu Azure zásobníku operátor jako správce služeb.

2. Vyberte **Procházet** &gt; **prostředky pro správu** &gt; **hostitelské servery SQL**.

   ![Hostování serverů SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   V části **hostování servery SQL**, poskytovatel prostředků SQL můžete připojit k instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.

   ![Řídicí panel adaptér SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Vyplňte formulář Podrobnosti připojení instanci služby SQL Server.

   ![Přidat SQL, který je hostitelem serveru](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Volitelně můžete zahrnout název instance a zadejte číslo portu, pokud instance není přiřazen k výchozí port 1433.

   > [!NOTE]
   > Tak dlouho, dokud SQL instance může mít přístup uživatele a správce Azure Resource Manager, mohou být umístěny pod kontrolou zprostředkovatele prostředků. SQL instance __musí__ přidělit výhradně k poskytovateli prostředků.

4. Při přidávání serverů, musíte je přiřadit k nové nebo existující SKU k odlišení nabídky služeb. Například můžete mít instanci SQL Enterprise, která poskytuje:
  
   - kapacita databáze
   - Automatické zálohování
   - Rezervovat vysoce výkonné servery pro jednotlivá oddělení

   Všechny hostitelské servery SKU musí mít stejné funkce. **Název** odrážet vlastnosti verze SKU, mohou uživatelé nasadit své databáze příslušné SKU.

   > [!IMPORTANT]
   > Speciální znaky, včetně mezery a tečky, nejsou podporovány v **rodiny** nebo **vrstvy** názvy při vytváření SKU pro poskytovatele prostředků SQL a databáze MySQL.

   Příklad:

   ![Vytvoření SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Uživatele nelze vytvořit databázi, dokud nebude plně vytvořen verze SKU.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zajištění vysoké dostupnosti pomocí SQL skupin dostupnosti Always On

Instance SQL Always On konfigurace vyžaduje další kroky a vyžaduje minimálně tři virtuální počítače (nebo fyzické počítače.) Tento článek předpokládá, že už máte plnou Principy skupin dostupnosti Always On. Další informace naleznete v tématu:

* [Představení skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On skupiny dostupnosti (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Zprostředkovatel prostředků adaptér SQL _pouze_ podporuje SQL 2016 SP1 Enterprise nebo později instance pro Always On. Tato konfigurace adaptér vyžaduje nové funkce SQL, jako je například automatická synchronizace replik indexů.

Kromě předchozích seznam požadavků musíte povolit [Automatická synchronizace replik indexů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou skupinu dostupnosti pro každou instanci systému SQL Server.

Pokud chcete povolit, automatická synchronizace replik indexů na všech instancích, upravit a poté spusťte následující příkaz SQL pro každou instanci:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

V sekundární instance úpravy a potom spusťte následující příkaz SQL pro každou instanci:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Chcete-li přidat SQL Always On hostitelské servery

1. Přihlaste se k portálu pro správu zásobník Azure jako správce služby

2. Vyberte **Procházet** &gt; **prostředky pro správu** &gt; **SQL hostitelské servery** &gt; **+ přidat**.

   V části **hostování servery SQL** zprostředkovatele prostředků SQL serveru můžete připojit k skutečné instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.

3. Vyplňte formulář s podrobnostmi o připojení pro instanci serveru SQL. Ujistěte se, že používáte adresu plně kvalifikovaný název domény vždy na naslouchacího procesu (a volitelně také portu číslo.) Zadejte informace pro účet, který jste nakonfigurovali s oprávněními správce systému.

4. Zaškrtněte políčko vždy na skupiny dostupnosti povolení podpory pro instance vždy na skupiny dostupnosti systému SQL.

   ![Vždy zapnout na](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Přidáte k instanci SQL Always On SKU.

   > [!IMPORTANT]
   > Samostatné servery nelze kombinovat s instancí Always On ve stejné SKU. Probíhá pokus o různé typy po přidání první výsledky hostitelský server s chybou.

## <a name="make-the-sql-databases-available-to-users"></a>Databáze SQL zpřístupnit uživatelům

Vytvořte plány a nabízí chcete zpřístupnit databáze SQL pro uživatele. Přidat **Microsoft.SqlAdapter** služby k plánu a přidat výchozí kvótu nebo vytvořte novou kvótu.

![Vytvoření plánu a nabídky zahrnout databáze](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Další postup

[Přidat databáze](azure-stack-sql-resource-provider-databases.md)
