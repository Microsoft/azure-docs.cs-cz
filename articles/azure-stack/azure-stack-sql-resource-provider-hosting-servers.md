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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.openlocfilehash: af820f90c5d8822dbdaa768b16360d534fd47828
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060038"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků SQL

Je možné hostovat v instanci SQL na virtuálním počítači (VM) [zásobník Azure](azure-stack-poc.md), nebo na virtuálním počítači mimo prostředí Azure zásobníku, pokud zprostředkovatel prostředků SQL můžete připojit k instanci.

## <a name="overview"></a>Přehled

Než přidáte SQL, který je hostitelem serveru, zkontrolujte následující povinné a obecné požadavky.

**Povinné požadavky**

* Povolte ověřování SQL na instanci serveru SQL. Vzhledem k tomu, že zprostředkovatel prostředků SQL virtuální počítač není připojený k doméně, se může připojit pouze k hostitelskému serveru pomocí ověřování SQL.
* Nakonfigurujte IP adresy pro instance SQL jako veřejné. Poskytovatel prostředků a uživatelé, třeba webové aplikace, komunikují přes síť uživatelem, třeba připojení k instanci serveru SQL v této síti.

**Obecné požadavky**

* Vyhradit instanci SQL pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci SQL, který se používá další příjemce. Toto omezení platí i pro aplikační služby.
* Nakonfigurujte účet pro zprostředkovatele prostředků s úrovní příslušná oprávnění.
* Jste zodpovědní za správu instance SQL a jejich hostitelů.  Například nebude poskytovatele prostředků aktualizace, zpracování zálohy nebo zpracování pověření otočení.

### <a name="sql-server-virtual-machine-images"></a>Bitové kopie virtuálních počítačů systému SQL Server

Bitové kopie virtuálních počítačů SQL IaaS jsou k dispozici prostřednictvím funkce správy Marketplace. Tyto Image jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure.

Ověřte, že je vždy stáhnout nejnovější verzi **SQL IaaS rozšíření** před nasazením virtuálního počítače s SQL pomocí položky Marketplace. Rozšíření IaaS a odpovídající portálu vylepšení poskytují další funkce, například automatické opravy a zálohování. Další informace o toto rozšíření najdete v tématu [automatizaci úloh správy ve virtuálních počítačích Azure s příponou SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Existují další možnosti pro nasazení virtuálních počítačů SQL, včetně šablon v [galerii pro rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Všechny hostitelské servery, které jsou nainstalované v zásobníku Azure několika uzly musí být vytvořeny z předplatného uživatele. Nemohou být vytvářeny ze předplatného výchozí zprostředkovatel. Musí být vytvořený z portálu pro uživatele nebo z relace prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci SQL. Správce služeb _můžete_ být vlastníkem daného předplatného.

### <a name="required-privileges"></a>Požadovaná oprávnění

Můžete vytvořit administrativní uživatel s oprávněními nižší než správce sysadmin systému SQL. Uživatel se musí oprávnění pro následující operace:

* Databáze: Vytvořit, Alter, s členství ve skupině (pro Always On pouze), vyřadit, zálohování
* Skupina dostupnosti: Alter, připojení, přidat nebo odebrat databáze
* Přihlášení: Vytvořit, vyberte, Alter, Drop, odvolání
* Vyberte operace: \[hlavní\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sloupec sys.availability_replicas (AlwaysOn), zobrazení sys.databases, \[hlavní\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[hlavní\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zadejte kapacitu připojením na samostatnou hostitelem SQL serveru

Můžete použít samostatné (bez-HA) servery SQL Server prostřednictvím jakékoli edici systému SQL Server 2014 nebo SQL Server 2016. Ujistěte se, že máte přihlašovací údaje pro účet s oprávněními správce systému.

Pokud chcete přidat samostatný hostitelský server, který je již nastavena, postupujte takto:

1. Přihlaste se k portálu Azure zásobníku operátor jako správce služeb.

2. Vyberte **Procházet** &gt; **prostředky pro správu** &gt; **hostitelské servery SQL**.

   ![Hostování serverů SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   V části **hostování servery SQL**, poskytovatel prostředků SQL můžete připojit k instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.

   ![Řídicí panel adaptér SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Na **přidání serveru SQL pro hostování**, zadejte podrobnosti připojení pro instanci serveru SQL.

   ![Přidat SQL, který je hostitelem serveru](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Volitelně zadejte název instance a zadejte číslo portu, pokud instance není přiřazen k výchozí port 1433.

   > [!NOTE]
   > Tak dlouho, dokud SQL instance může mít přístup uživatele a správce Azure Resource Manager, mohou být umístěny pod kontrolou zprostředkovatele prostředků. SQL instance __musí__ přidělit výhradně k poskytovateli prostředků.

4. Při přidávání serverů, musíte přiřadit stávající SKU nebo vytvořit nový SKU. V části **přidání serveru pro hostování**, vyberte **SKU**.

   * Chcete-li použít existující SKU, vyberte dostupné SKU a pak vyberte **vytvořit**.
   * Chcete-li vytvořit SKU, vyberte **+ vytvořit nové SKU**. V **vytvořit SKU**, zadejte požadované informace a potom vyberte **OK**.

     > [!IMPORTANT]
     > Speciální znaky, včetně mezery a tečky, nejsou podporovány v **název** pole. Zadejte hodnoty pro pomocí příklady v následující snímek obrazovky **rodiny**, **vrstvy**, a **edice** pole.

     ![Vytvoření SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Uživatele nelze vytvořit databázi, dokud nebude plně vytvořen verze SKU.

### <a name="sku-notes"></a>Poznámky k SKU

SKU slouží k odlišení nabídky služeb. Například můžete mít instanci SQL Enterprise, která má následující vlastnosti:
  
* vysoké kapacity
* vysoce výkonné
* Vysoká dostupnost

Můžete vytvořit SKU pro předchozí příklad, omezení přístupu do konkrétních skupin, které je třeba databázi vysoce výkonné.

>[!TIP]
>Použijte název SKU, který popisuje možnosti servery v SKU, jako je například kapacitu a výkon. Název slouží jako pomocný pomoci uživatelům nasadit své databáze do příslušné SKU.

Jako osvědčený postup musí všechny hostitelské servery SKU mají stejné vlastnosti prostředků a výkonu.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zajištění vysoké dostupnosti pomocí SQL skupin dostupnosti Always On

Instance SQL Always On konfigurace vyžaduje další kroky a vyžaduje tři virtuální počítače (nebo fyzické počítače.) Tento článek předpokládá, že už máte plnou Principy skupin dostupnosti Always On. Další informace najdete v následujících článcích:

* [Představení skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On skupiny dostupnosti (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Zprostředkovatel prostředků adaptér SQL _pouze_ podporuje SQL 2016 SP1 Enterprise nebo později instance pro Always On. Tato konfigurace adaptér vyžaduje nové funkce SQL, jako je například automatická synchronizace replik indexů.

Kromě toho je nutné povolit [Automatická synchronizace replik indexů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou skupinu dostupnosti pro každou instanci systému SQL Server.

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

   V části **hostování servery SQL**, poskytovatel prostředků SQL serveru můžete připojit k skutečné instance systému SQL Server, které slouží jako zprostředkovatel prostředků back-end.

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
