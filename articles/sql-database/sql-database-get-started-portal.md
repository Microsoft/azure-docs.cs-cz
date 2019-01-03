---
title: Vytvoření Azure SQL database pomocí portálu | Dokumentace Microsoftu
description: Vytvoření logického serveru Azure SQL Database a databázi na webu Azure Portal a dotazujte ji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: b8ff482f2aec406ef4c1c545db7844a861317518
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994415"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Rychlý start: Vytvoření databáze SQL Azure na webu Azure Portal

Azure SQL Database je *Database-as-a-Service* , který umožňuje spouštět a škálovat vysoce dostupné databáze SQL serveru v cloudu. V tomto rychlém startu se dozvíte, jak začít vytvořením a potom dotazování Azure SQL database pomocí webu Azure portal. 

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pro všechny kroky v tomto rychlém startu, přihlaste se k [webu Azure portal](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

Azure SQL database má definovanou sadu [výpočetních a úložných kapacit](sql-database-service-tiers-dtu.md). Vytvoření databáze v [logického serveru Azure SQL Database](sql-database-features.md) v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md).

Chcete-li vytvořit databázi SQL obsahující ukázková data AdventureWorksLT:

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
   
1. Vyberte **databází**a pak vyberte **SQL Database**.
   
1. V **SQL Database** formuláři zadejte nebo vyberte následující hodnoty: 
   
   - **Název databáze**: Typ *mySampleDatabase*.
   - **Předplatné**: Rozevírací seznam a vyberte správné předplatné, pokud se nezobrazí.  
   - **Skupina prostředků**: Vyberte **vytvořit nový**, typ *myResourceGroup*a vyberte **OK**. 
   - **Výběr zdroje**: Rozevírací seznam a vyberte **ukázka (AdventureWorksLT)**. 
   
   >[!IMPORTANT]
   >Je nutné vybrat **ukázka (AdventureWorksLT)** dat, takže můžete postupovat podle tato a další rychlé starty Azure SQL Database, která tato data použít. 
   
   ![Vytvoření databáze Azure SQL](./media/sql-database-get-started-portal/create-database-1.png)
   
1. Vyberte **Server**a pak vyberte **vytvořit nový server**. 
   
1. V **nový server** formuláři zadejte nebo vyberte následující hodnoty: 
   
   - **Název serveru**: Typ *můjsqlserver*.
   - **Přihlašovací jméno správce serveru**: Typ *azureuser*. 
   - **Heslo**: Typ *Azure1234567*. 
   - **Potvrzení hesla**: Zadejte heslo znovu.
   - **Umístění**: Rozevírací seznam a vyberte jakékoli platné umístění.  
   
   >[!IMPORTANT]
   >Si zapamatujte nebo poznamenejte přihlašovací jméno správce serveru a heslo, takže se můžete přihlásit k serveru a databáze pro tuto a další rychlé starty. Pokud zapomenete přihlašovací jméno nebo heslo, můžete získat přihlašovací jméno nebo heslo na **systému SQL server** stránky. Chcete-li otevřít **systému SQL server** vyberte název serveru v databázi, **přehled** stránku po vytvoření databáze.
   
1. Vyberte **vyberte**.
   
   ![Vytvoření serveru](./media/sql-database-get-started-portal/create-database-server.png)
   
1. Na **SQL Database** formuláře, vyberte **cenová úroveň**. Prozkoumejte množství Dtu a velikosti úložiště k dispozici pro jednotlivé úrovně služby.
   
   >[!NOTE]
   >Tento rychlý start využívá [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), ale [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) je také k dispozici.
   
   >[!NOTE]
   >Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou: Velká Británie – sever, střed USA – Západ, Velká Británie South2, Čína – východ, USDoDCentral, Německo – střed, USDoDEast, USA (gov) – jihozápad, USA (gov) – jih – střed, Německo – severovýchod, Čína – sever a státní správu USA – východ. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB. Další informace najdete v tématu [aktuálních omezení pro P11 – P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   
1. Pro účely tohoto rychlého startu vyberte **standardní** úroveň služby a pak pomocí posuvníku vyberte **10 Dtu (S0)** a **1** GB úložiště.
   
1. Vyberte **Použít**.  
   
   ![Vyberte ceny.](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. Na **SQL Database** formuláře, vyberte **vytvořit** k nasazení a zřizování skupiny prostředků, server a databáze. 
   
   Nasazení trvá několik minut. Můžete vybrat **oznámení** na panelu nástrojů můžete sledovat průběh nasazení.

   ![Oznámení](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Dotazování databáze SQL

Teď, když vytvoříte databázi Azure SQL, můžete použijte integrovaný dotazovací nástroj na webu Azure Portal pro připojení k databázi a zadávat dotazy na data.

1. Na **SQL Database** stránce pro vaši databázi, vyberte **editor dotazů (preview)** v levé nabídce. 
   
   ![Přihlaste se k editoru dotazů](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. Zadejte přihlašovací údaje a vyberte **OK**.
   
1. Zadejte následující dotaz **editoru dotazů** podokně.
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. Vyberte **spustit**a pak zkontrolujte výsledky dotazu v **výsledky** podokně.

   ![výsledky editoru dotazů](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. Zavřít **editoru dotazů** stránku a vybrat **OK** po zobrazení výzvy zahoďte neuložené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Zachovat tuto skupinu prostředků, SQL server a SQL database, pokud chcete přejít na [další kroky](#next-steps) a informace o připojení a dotazování databáze pomocí několika způsoby. 

Jakmile budete hotovi, tyto prostředky používá, můžete je odstranit následujícím způsobem:

1. V nabídce vlevo na webu Azure Portal vyberte **skupiny prostředků**a pak vyberte **myResourceGroup**.
1. Na stránce skupiny prostředků, vyberte **odstranit skupinu prostředků**. 
1. Typ *myResourceGroup* v poli a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

- Musíte vytvořit pravidlo brány firewall na úrovni serveru pro připojení k databázi Azure SQL z místní nebo vzdálené nástroje. Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md).
- Po vytvoření pravidla brány firewall na úrovni serveru můžete [připojení a dotazování](sql-database-connect-query.md) vaší databáze pomocí několika různých nástrojů a jazyků. 
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Vytvoření databáze Azure SQL pomocí Azure CLI najdete v tématu [ukázky v Azure CLI](sql-database-cli-samples.md).
- Vytvoření databáze Azure SQL pomocí Azure Powershellu najdete v tématu [ukázky Azure Powershellu](sql-database-powershell-samples.md).
