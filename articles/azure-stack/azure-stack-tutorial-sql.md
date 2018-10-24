---
title: Nabízí vysoce dostupné databáze SQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit hostitelský počítač a vysoce dostupné databáze SQL AlwaysOn poskytovatele prostředků SQL serveru pomocí služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 50f5662fa574b512ab607e17dbdfcf1861e2f5c6
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954891"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Kurz: Nabízí vysoce dostupné databáze SQL

Jako operátor Azure stacku můžete nakonfigurovat virtuální počítače serveru pro hostování databází serveru SQL Server. Po SQL je hostitelský server úspěšně vytvořen a spravován společností Azure Stack, uživatelé, kteří odběru jste přihlášeni ke službě SQL services můžete snadno vytvořit databáze SQL.

Tento kurz ukazuje, jak vytvořit pomocí šablony rychlý start Azure Stack [skupiny dostupnosti AlwaysOn systému SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), přidejte ho jako Server služby Azure Stack SQL hostování a vytvořte vysoce dostupné databáze SQL.

Co se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti AlwaysOn systému SQL Server ze šablony
> * Vytvoření serveru hostování služby Azure Stack SQL
> * Vytvoření vysoce dostupné databáze SQL

V tomto kurzu dvě skupiny dostupnosti AlwaysOn SQL serveru virtuálních počítačů se vytvoří a nakonfigurovat pomocí položky marketplace k dispozici služby Azure Stack. 

Před zahájením těchto kroků v tomto kurzu, ujistěte se, že [poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md) byl úspěšně nainstalován a k dispozici následující položky marketplace služby Azure Stack:

> [!IMPORTANT]
> Všechny tyto funkce jsou požadovány pro šablonu pro rychlý start Azure Stack se použije.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) image z marketplace.
- SQL Server 2016 SP1 nebo SP2 (Enterprise, Standard nebo vývojář) na image serveru Windows Server 2016. Tento kurz používá [SQL Server 2016 SP2 Enterprise na Windows serveru 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) image z marketplace.
- [Rozšíření systému SQL Server IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) verze 1.2.30 nebo vyšší. Rozšíření SQL IaaS nainstaluje potřebné komponenty, které jsou vyžadované položky Marketplace SQL serveru pro všechny verze Windows. Umožňuje nastavení specifická pro SQL nakonfigurovat na virtuálních počítačích SQL. Pokud rozšíření není nainstalována na místní webu Marketplace, zřizování serveru SQL se nezdaří.
- [Rozšíření vlastních skriptů pro Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) verze 1.9.1 nebo vyšší. Rozšíření vlastních skriptů je nástroj, který je možné automaticky spustit po nasazení úkolů vlastního nastavení virtuálních počítačů.
- [Prostředí PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) verze 2.76.0.0 nebo vyšší. DSC je platforma pro správu v prostředí Windows PowerShell, který umožňuje nasazení a Správa konfigurační data pro softwarové služby a správu prostředí, ve které tyto služby jsou spuštěny.

Další informace o přidávání položek do Tržiště Azure Stack, najdete v článku [přehled Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Vytvoření skupiny dostupnosti AlwaysOn systému SQL Server
Postupujte podle kroků v této části nasadíte skupiny dostupnosti AlwaysOn systému SQL Server pomocí [šablonu pro rychlý start Azure Stack AlwaysOn serveru sql 2016](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Tato šablona nasadí dvě instance SQL serveru Enterprise, Standard nebo pro vývojáře v skupiny dostupnosti Always On. Vytvoří následující prostředky:

- Skupina zabezpečení sítě
- Virtuální síť
- Čtyři účty úložiště (jeden pro Active Directory (AD), jeden pro SQL, jeden pro určující sdílenou složku a jeden pro diagnostiku virtuálních počítačů)
- Čtyři veřejné adresy IP (jeden na AD, dvou pro každý virtuální počítač SQL a jeden veřejný load balancer vázán na naslouchací proces SQL AlwaysOn)
- Jedna externí nástroj pro vyrovnávání zatížení virtuálních počítačů SQL s veřejnou IP adresu vázán k naslouchacímu procesu SQL AlwaysOn
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
- Dva virtuální počítače (Windows Server 2016) nakonfigurovat SQL Server 2016 SP1 nebo SP2 Enterprise, Standard nebo edice Developer a v clusteru. Musí se jednat Image z marketplace.
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako určující sdílené složky souborů pro cluster
- Jedna skupina dostupnosti obsahující SQL a soubor sdílené složky s kopií clusteru virtuálních počítačů  

1. 
[!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **vlastní**a potom **nasazení šablony**.

   ![Nasazení vlastní šablony](media/azure-stack-tutorial-sqlrp/1.png)


3. Na **vlastní nasazení** okně vyberte **úpravy šablony** > **šablonu pro rychlý Start** a pak použijte rozevírací seznam dostupných vlastních šablon pro Vyberte **AlwaysOn serveru sql 2016** šablony, klikněte na tlačítko **OK**a potom **Uložit**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Vyberte šablonu pro rychlý start")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Na **vlastní nasazení** okně vyberte **upravit parametry** a zkontrolujte výchozí hodnoty. Upravte hodnoty podle potřeby poskytují všechny informace o povinný parametr a potom klikněte na **OK**.<br><br> Minimálně:

    - Zadejte parametry ADMINPASSWORD SQLSERVERSERVICEACCOUNTPASSWORD a SQLAUTHPASSWORD složitá hesla.
    - Zadejte příponu DNS pro zpětného vyhledávání v jenom malá písmena. pro parametr příponu DNS (**azurestack.external** pro instalace ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Upravit parametry vlastní nasazení")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Na **vlastní nasazení** okna, vyberte předplatné, které chcete používat a vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků pro vlastní nasazení.<br><br> Potom vyberte umístění skupiny prostředků (**místní** pro instalace ASDK) a potom klikněte na tlačítko **vytvořit**. Nasazení vlastního nastavení budou ověřena a poté se nasazení spustí.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Vytvoření vlastního nasazení")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. V portálu pro správu, vyberte **skupiny prostředků** a klikněte na název skupiny prostředků vytvořené pro vlastní nasazení (**; resource-group** v tomto příkladu). Zobrazte stav nasazení tak, aby Ujistěte se, že všechna nasazení byly úspěšně dokončeny.<br><br>Dále zkontrolujte položky skupiny prostředků a vyberte **SQLPIPsql\<název skupiny prostředků\>**  položky veřejné IP adresy. Zaznamenejte veřejné IP adresy a úplný plně kvalifikovaný název domény veřejné IP nástroje pro vyrovnávání zatížení. Je potřeba zadat operátor Azure stacku, aby mohli vytvářet hostitelského serveru SQL využití této skupiny dostupnosti SQL AlwaysOn.

   > [!NOTE]
   > Nasazení šablony bude trvat několik hodin.

   ![Vlastní nasazení dokončeno](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Povolit automatické synchronizace replik indexů
Poté, co šablona se úspěšně nasazení a konfiguraci skupiny dostupnosti SQL AlwaysON, je nutné povolit [Automatická synchronizace replik indexů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou instanci serveru SQL Server ve skupině dostupnosti. 

Když vytvoříte skupinu dostupnosti s Automatická synchronizace replik indexů, SQL Server automaticky vytvoří sekundární repliky pro každou databázi ve skupině bez dalších zásahů nezbytné k zajištění vysoké dostupnosti databází AlwaysOn.

Pomocí těchto příkazů SQL nakonfigurovat Automatická synchronizace replik indexů pro skupinu dostupnosti AlwaysOn. Nahraďte \<InstanceName\> s primární instance název SQL serveru a < availability_group_name > s názvem skupiny dostupnosti AlwaysOn podle potřeby. 

Na primární instance SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Primární instance skript SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

Na sekundární instance SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Sekundární instance skript SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurace ověřování databáze s omezením
Před přidáním do skupiny dostupnosti databáze s omezením, ujistěte se, že možnost serveru ověřování databáze s omezením je nastavená na 1 na každou instanci serveru, který je hostitelem replika dostupnosti pro skupinu dostupnosti. Další informace najdete v tématu [ověřování databáze s omezením](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Nastavení možnosti serveru ověřování databáze s omezením pro každou instanci serveru SQL Server ve skupině dostupnosti použijte tyto příkazy:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Ověřování databáze sady obsažené](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Vytvoření serveru hostování služby Azure Stack SQL
Po skupiny dostupnosti SQL Server AlwayOn byla vytvořena a správně nakonfigurované, musí operátor Azure stacku vytvořit Server služby Azure Stack SQL hostování zpřístupnit další kapacitu pro uživatele k vytváření databází. 

Použijte veřejnou IP adresu nebo úplný plně kvalifikovaný název domény pro veřejnou IP adresu nástroje pro vyrovnávání zatížení SQL zaznamenané dříve při vytvoření skupiny dostupnosti SQL AlwaysOn, skupiny prostředků (**SQLPIPsql\<název skupiny prostředků\>** ). Kromě toho musíte znát přihlašovací údaje pro ověřování používá pro přístup k instancím SQL ve skupině dostupnosti AlwaysOn serveru SQL Server.

> [!NOTE]
> Tento krok se musí spouštět z portálu pro správu služby Azure Stack nezavřel operátor Azure stacku.

Skupiny dostupnosti SQL AlwaysOn zatížení vyrovnávání naslouchací proces přihlašovací údaje pro veřejné IP adresy a SQL ověřování operátor Azure stacku umožní nyní [vytvoření SQL serveru hostující použití skupiny dostupnosti SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Také se ujistěte, že jste vytvořili plány a nabízí pro vytvoření databáze SQL AlwaysOn zpřístupnit uživatelům. Operátor, který se bude muset přidat **Microsoft.SqlAdapter** služby k plánu a vytvořit novou kvótu speciálně pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [plán, nabídky, kvót a předplatného přehled](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> **Microsoft.SqlAdapter** služba nebude k dispozici pro přidání do plánů až [nasazení poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Vytvoření vysoce dostupné databáze SQL
Po SQL AlwaysOn, skupiny dostupnosti byla vytvořena, nakonfigurovat a přidat jako Server služby Azure Stack SQL hostování operátorem Azure Stack uživatel tenanta s předplatným, včetně schopnosti databáze systému SQL Server vytvořit SQL Database podporuje Funkci AlwaysOn pomocí následujících kroků v této části. 

> [!NOTE]
> Projít tyto kroky na portálu user portal Azure Stack jako uživatel tenanta s předplatným nabízí funkce SQL serveru (Microsoft.SQLAdapter služby).

1. 
[!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **Data \+ úložiště**a potom **SQL Database**.<br><br>Zadejte informace o vlastnosti databáze, včetně názvu, kolace, maximální velikost a předplatné, skupinu prostředků a umístění pro nasazení. 

   ![Vytvoření databáze SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte příslušné SQL hostující Server SKU používat. V tomto příkladu je operátor Azure stacku vytvořil **Enterprise-HA** SKU pro podporu vysoké dostupnosti pro skupiny dostupnosti SQL AlwaysOn.

   ![Vyberte SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Vyberte **přihlášení** > **vytvořte nové přihlašovací údaje** a pak zadejte přihlašovací údaje ověřování SQL chcete použít pro novou databázi. Až budete hotovi, klikněte na tlačítko **OK** a potom **vytvořit** zahájíte proces nasazení databáze.

   ![Vytvořte přihlašovací údaje](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Po úspěšném dokončení nasazení databáze SQL zkontrolujte vlastnosti databáze ke zjištění připojovací řetězec pro připojení k nové databázi s vysokou dostupností. 

   ![Zobrazení připojovacího řetězce](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti AlwaysOn systému SQL Server ze šablony
> * Vytvoření serveru hostování služby Azure Stack SQL
> * Vytvoření vysoce dostupné databáze SQL

Pokračujte k dalším kurzu se dozvíte, jak:
> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupné databáze MySQL](azure-stack-tutorial-mysql.md)