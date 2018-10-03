---
title: SQL, který je hostitelem serverů v Azure stacku | Dokumentace Microsoftu
description: Postup přidání instancí SQL pro zřizování prostřednictvím poskytovatele prostředků SQL adaptéru.
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
ms.date: 09/27/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 57033de3224b5966b2dfa80dd1cb45fafd83b26b
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238713"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků SQL

Instance SQL na virtuálním počítači (VM), můžete hostovat v [Azure Stack](azure-stack-poc.md), nebo mimo prostředí Azure Stack, dokud poskytovatele prostředků SQL se může připojit k instanci virtuálního počítače.

> [!NOTE]
> Databáze SQL musí být vytvořené na poskytovatele prostředků SQL serveru. Poskytovatele prostředků SQL musí být vytvořené v výchozí předplatné poskytovatele při hostitelské servery SQL by měl být vytvořen v fakturovatelná, předplatné uživatele. Server zprostředkovatele prostředků není vhodné používat pro hostování databází uživatelů.

## <a name="overview"></a>Přehled

Předtím, než přidáte SQL, který je hostitelem serveru, zkontrolujte následující povinné a obecné požadavky.

### <a name="mandatory-requirements"></a>Povinné požadavky

* Povolte ověřování SQL Server v instanci systému SQL Server. Protože poskytovatele prostředků SQL virtuálního počítače k doméně připojený není, můžete připojit jenom k hostování serveru pomocí ověřování SQL.
* Nakonfigurujte IP adresy pro instance SQL jako veřejné při instalaci ve službě Azure Stack. Poskytovatel prostředků a uživatelů, jako jsou například webové aplikace, komunikují přes síť uživatelem, proto není vyžadována možnost připojení k instanci serveru SQL v této síti.

### <a name="general-requirements"></a>Obecné požadavky

* Instance serveru SQL pro použití vyhradíte pomocí zprostředkovatele a uživatelského zatížení prostředků. Nelze použít instanci SQL, který se používá v jiných uživatelů. Toto omezení platí také pro App Services.
* Konfigurace účtu s úrovní odpovídající oprávnění pro poskytovatele prostředků (popsaných níže).
* Jste odpovědní za správu instancí SQL a jejich hostitelů.  Například nebude poskytovatele prostředků aktualizace, zpracování zálohy nebo zpracování přihlašovacích údajů otočení.

### <a name="sql-server-virtual-machine-images"></a>Image virtuálního počítače SQL serveru

Image virtuálních počítačů SQL IaaS jsou dostupné prostřednictvím funkce pro správu webu Marketplace. Tyto Image jsou stejné jako virtuální počítače SQL, které jsou dostupné v Azure.

Ujistěte se, že můžete kdykoli stáhnout nejnovější verzi **rozšíření SQL IaaS** před nasazením virtuálního počítače SQL pomocí položky Marketplace. Rozšíření IaaS a odpovídající portál vylepšení poskytují další funkce, jako jsou automatické opravy a zálohování. Další informace o tomto rozšíření najdete v tématu [automatizace úloh správy v Azure Virtual Machines pomocí rozšíření agenta SQL serveru](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Rozšíření SQL IaaS je _požadované_ pro všechny SQL na imagích Windows na webu Marketplace; se nepodaří nasadit, když jste se nepodařilo stáhnout rozšíření virtuálního počítače. Není použit s imagí virtuálních počítačů založených na Linuxu SQL.

Existují další možnosti pro virtuální počítače s SQL, včetně šablon v nasazení [galerii pro rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Všechny hostitelské servery, které jsou nainstalované ve službě Azure Stack několika uzly musí být vytvořené z předplatné uživatele a nikoli výchozí předplatné poskytovatele. Musí se vytvořit na portálu user portal nebo v relaci Powershellu s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelná virtuální počítače a musí mít příslušné licence SQL. Správce služeb _můžete_ být vlastníkem daného předplatného.

### <a name="required-privileges"></a>Požadovaná oprávnění

Administrativní uživatel můžete vytvořit s oprávněními nižší než správce sysadmin systému SQL. Uživatel se musí oprávnění pro tyto operace:

* Databáze: Vytvořit, Alter, se členství ve skupině (pro Always On pouze), Drop, zálohování
* Skupiny dostupnosti: Alter, připojení, přidání nebo odebrání databáze
* Přihlášení: Vytvořit, vyberte, Alter, Drop, odvolání
* Vyberte operace: \[hlavní\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sloupec sys.availability_replicas (AlwaysOn), zobrazení sys.databases, \[hlavní\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[hlavní\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files

### <a name="additional-security-information"></a>Další informace o zabezpečení

Následující informace poskytují další bezpečnostní pokyny:

* Veškeré součásti úložiště služby Azure Stack se šifrují pomocí nástroje BitLocker, takže jakoukoli instanci SQL ve službě Azure Stack používat zašifrovaný objekt blob úložiště.
* Poskytovatele prostředků SQL plně podporuje TLS 1.2. Ujistěte se, že SQL Server, který se spravuje prostřednictvím poskytovatele prostředků SQL je nakonfigurované pro protokol TLS 1.2 _pouze_ a RP se ve výchozím nastavení, která. Všechny podporované verze systému SQL Server podporu protokolu TLS 1.2, najdete v článku [podpora protokolu TLS 1.2 pro Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Správce konfigurace SQL serveru použijte k nastavení **ForceEncryption** možnosti zajistíte veškerá komunikace se serverem SQL se vždy šifrují. Zobrazit [konfigurace serveru pro vynutit šifrovaná připojení](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Ujistěte se, že všechny klientské aplikace je také komunikaci přes šifrované připojení.
* RP je nakonfigurována tak certifikátů používaných instancí SQL serveru.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zadejte kapacitu propojíte samostatný hostitelem systému SQL server

Můžete použít samostatné (bez-HA) SQL serverů využívající kterákoli edice systému SQL Server 2014 nebo SQL Server 2016. Ujistěte se, že máte přihlašovací údaje pro účet s oprávněními správce systému.

Chcete-li přidat samostatný server hostingu, který je už nastavený, postupujte takto:

1. Přihlaste se k portálu Azure Stack – operátor jako správce služeb.

2. Vyberte **všechny služby** &gt; **prostředky pro správu** &gt; **servery hostující SQL**.

   ![Hostování servery SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   V části **servery hostující SQL**, poskytovatele prostředků SQL se můžete připojit k instance systému SQL Server, který bude sloužit jako back-endu poskytovatele prostředků.

   ![Řídicí panel adaptéru pro SQL](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Klikněte na tlačítko **přidat** a pak zadejte podrobnosti o připojení pro vaši instanci SQL serveru na **přidat SQL Server pro hostování** okno.

   ![Přidat SQL, který je hostitelem serveru](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Volitelně můžete zadat název instance a zadejte číslo portu, pokud instance není přiřazen výchozí port 1433.

   > [!NOTE]
   > Tak dlouho, dokud SQL instance je přístupný podle uživatele a správce Azure Resource Manageru, může být umístěn pod kontrolou zprostředkovatele prostředků. SQL instance __musí__ přidělit jenom pro poskytovatele prostředků.

4. Jak budete přidávat servery, musíte je přiřadit k existující skladové položky nebo vytvořit novou skladovou Položku. V části **přidat Server pro hostování**vyberte **SKU**.

   * Pokud chcete použít existující skladové položky, zvolte dostupné skladové položky a pak vyberte **vytvořit**.
   * Chcete-li vytvořit skladovou jednotku, vyberte **+ vytvořit novou skladovou Položku**. V **vytvořit SKU**, zadejte požadované informace a pak vyberte **OK**.

     ![Vytvoří skladová jednotka](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zajištění vysoké dostupnosti pomocí SQL skupin dostupnosti Always On

Konfigurace instance SQL Always On vyžaduje další kroky a vyžaduje tři virtuální počítače (nebo fyzických počítačích.) Tento článek předpokládá, že už máte důkladného porozumění skupin dostupnosti Always On. Další informace najdete v následujících článcích:

* [Představení skupin dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Skupiny nepřetržité dostupnosti (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Poskytovatele prostředků SQL adaptér _pouze_ podporuje SQL 2016 SP1 Enterprise nebo později instance pro skupiny dostupnosti Always On. Tato konfigurace adaptéru vyžaduje nové funkce SQL, jako je například automatická synchronizace replik indexů.

### <a name="automatic-seeding"></a>Automatická synchronizace replik indexů

Je nutné povolit [Automatická synchronizace replik indexů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou skupinu dostupnosti pro každou instanci systému SQL Server.

Pokud chcete povolit, automatická synchronizace replik indexů na všechny instance, upravit a pak spusťte následující příkaz SQL na primární replice pro každou sekundární instanci:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Skupina dostupnosti musí být uzavřeny do hranatých závorek.

Na sekundární uzly spusťte následující příkaz SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Konfigurace ověřování databáze s omezením

Před přidáním do skupiny dostupnosti databáze s omezením, ujistěte se, že možnost serveru ověřování databáze s omezením je nastavená na 1 na každou instanci serveru, který je hostitelem replika dostupnosti pro skupinu dostupnosti. Další informace najdete v tématu [obsažené ověřování databáze možnosti konfigurace serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Nastavení možnosti serveru ověřování databáze s omezením pro každou instanci použijte tyto příkazy:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Chcete-li přidat SQL Always On hostitelské servery

1. Přihlaste se k portálu pro správu Azure Stack jako správce služby.

2. Vyberte **Procházet** &gt; **prostředky pro správu** &gt; **hostitelské servery SQL** &gt; **+ přidat**.

   V části **servery hostující SQL**, poskytovatele prostředků SQL serveru se můžete připojit k skutečné instance systému SQL Server, které bude sloužit jako back-endu poskytovatele prostředků.

3. Vyplňte formulář s podrobnostmi o připojení pro vaši instanci SQL serveru. Ujistěte se, že používáte adresu plně kvalifikovaného názvu domény vždy na naslouchací proces (a volitelně také portu číslo a instance name). Zadejte informace pro účet, který jste nakonfigurovali s oprávněními správce systému.

4. Zaškrtněte políčko skupiny dostupnosti Always On k povolení podpory pro instance SQL skupiny dostupnosti Always On.

   ![Vždy povolit na](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Přidání instance SQL Always On do skladové položky.

   > [!IMPORTANT]
   > Samostatné servery nejde kombinovat s instancemi Always On v rámci stejné SKU. Probíhá pokus o různé typy po přidání první výsledky hostování serveru k chybě.

## <a name="sku-notes"></a>Skladová položka poznámky

SKU můžete použít k rozlišení nabídek služeb. Například můžete mít instanci SQL Enterprise, která má následující vlastnosti:
  
* vysoké kapacity
* vysoce výkonné
* Vysoká dostupnost

Skladové položky nelze přiřadit konkrétní uživatelé nebo skupiny v této verzi.

 SKU může trvat až hodinu, uvidí na portálu. Uživatelé nemůžou vytvářet databáze, dokud SKU je zcela vytvořen.

>[!TIP]
>Použijte název SKU, která odráží popisuje možnosti servery ve skladové Položce, jako je například kapacitu a výkon. Název slouží jako vodítko k poskytování pomoci uživatelům nasadit své databáze do příslušné SKU.

Jako osvědčený postup musí všechny hostitelské servery v SKU mají stejné vlastnosti prostředků a výkonu.

## <a name="make-the-sql-databases-available-to-users"></a>Databáze SQL zpřístupnit uživatelům

Vytvořte plány a nabídky, aby databáze SQL k dispozici pro uživatele. Přidat **Microsoft.SqlAdapter** služby v plánu a vytvořit novou kvótu.

## <a name="next-steps"></a>Další postup

[Přidat databáze](azure-stack-sql-resource-provider-databases.md)
