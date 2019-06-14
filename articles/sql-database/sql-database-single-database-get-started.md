---
title: 'Azure portal: Vytvoření izolované databáze – Azure SQL Database | Dokumentace Microsoftu'
description: Vytvoření a dotazování izolované databáze ve službě Azure SQL Database pomocí webu Azure portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 85bbd14c8d2bcd8e3699e5f5efdae9f52acd63e5
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808548"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí webu Azure portal

Vytváření [izolované databáze](sql-database-single-database.md) je možnost nejrychlejší a nejjednodušší nasazení pro vytvoření databáze ve službě Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit a potom dotazování izolované databáze pomocí webu Azure portal.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Pro všechny kroky v tomto rychlém startu, přihlaste se k [webu Azure portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Izolované databáze můžete vytvořit buď v zřízená nebo bez serveru úrovně výpočetních (preview).

- Izolované databáze na úrovni zřízených výpočetních má definovaný počet předběžně přidělit výpočetní prostředky a sadu prostředky paměti a úložiště pomocí jednoho ze dvou [zakoupení modely](sql-database-purchase-models.md).
- Izolované databáze na úrovni výpočetní prostředí má celou řadu výpočetní prostředky, které jsou automaticky škálovaných plus zadanou velikost paměti na jádro a zadaného množství prostředků úložiště a je dostupná jenom [založený na virtuálních jádrech zakoupení modelů ](sql-database-service-tiers-vcore.md).

Při vytvoření izolované databáze také definovat [serveru služby SQL Database](sql-database-servers.md) ho spravovat a umístěte ji v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v určité oblasti.

> [!NOTE]
> Tento rychlý start využívá [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) a [bez serveru](sql-database-serverless.md) výpočetní úroveň, ale [nákupní model založený na DTU](sql-database-service-tiers-DTU.md) je také k dispozici.

Vytvoření izolované databáze s ukázkovými daty AdventureWorksLT:

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
2. Vyberte **databází** a pak vyberte **SQL Database** otevřít **vytvořit databázi SQL** stránky.

   ![Vytvoření izolované databáze](./media/sql-database-get-started-portal/create-database-1.png)

3. Na **Základy** kartě **Project Details** části, zadejte nebo vyberte následující hodnoty:

   - **Předplatné**: Rozevírací seznam a vyberte správné předplatné, pokud se nezobrazí.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, typ `myResourceGroup`a vyberte **OK**.

     ![Nová databáze SQL – základní kartu](media/sql-database-get-started-portal/new-sql-database-basics.png)

4. V **podrobnosti databáze** části, zadejte nebo vyberte následující hodnoty:

   - **Název databáze**: Zadejte `mySampleDatabase`.
   - **Server**: Vyberte **vytvořit nový** a zadejte následující hodnoty a pak vyberte **vyberte**.
       - **Název serveru**: Typ `mysqlserver`; spolu s některá čísla jedinečný.
       - **Přihlašovací jméno správce serveru**: Zadejte `azureuser`.
       - **Heslo**: Zadejte složité heslo, které splňuje požadavky na heslo.
       - **Umístění**: Zvolte umístění z rozevírací nabídky, jako například `West US 2`.

         ![Nový server](media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Nezapomeňte si poznamenat přihlašovací jméno správce serveru a heslo, takže se můžete přihlásit k serveru a databáze pro tuto a další rychlé starty. Pokud zapomenete přihlašovací jméno nebo heslo, můžete získat přihlašovací jméno nebo heslo na **systému SQL server** stránky. Chcete-li otevřít **systému SQL server** vyberte název serveru v databázi, **přehled** stránku po vytvoření databáze.

        ![Podrobnosti o SQL Database](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Chcete použít elastický fond SQL**: Vyberte **ne** možnost.
   - **Výpočetní prostředky a úložiště**: Vyberte **databáze konfigurovat** a pro účely tohoto rychlého startu vyberte **založený na virtuálních jádrech možnosti nákupu**

     ![Možnosti nákupu na základě virtuálních jader](media/sql-database-get-started-portal/create-database-vcore.png)

   - Vyberte **bez serveru**.

     ![úroveň výpočetní prostředí](media/sql-database-get-started-portal/create-database-serverless.png)

   - Zkontrolujte nastavení pro **maximálního počtu virtuálních jader**, **virtuálních jader Min**, **automatického pozastavení zpoždění**, a **Data maximální velikost**. Tyto podle potřeby změňte.
   - Přijměte podmínky verze preview a klikněte na tlačítko **OK**.
   - Vyberte **Použít**.

5. Vyberte **další nastavení** kartu. 
6. V **zdroj dat** pod **využívat existující data**vyberte `Sample`. 

   ![Další nastavení SQL DB](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Je nutné vybrat **ukázka (AdventureWorksLT)** dat, takže můžete postupovat podle snadno tato a další rychlé starty Azure SQL Database, která tato data použít.

7. Ponechejte zbývající hodnoty jako výchozí a vyberte **revize + vytvořit** v dolní části formuláře.
8. Zkontrolujte poslední nastavení a vyberte **vytvořit**.

9. Na **SQL Database** formuláře, vyberte **vytvořit** k nasazení a zřizování skupiny prostředků, server a databáze.

## <a name="query-the-database"></a>Dotaz na databázi

Teď, když jste vytvořili databázi, použijte integrovaný dotazovací nástroj na webu Azure Portal pro připojení k databázi a zadávat dotazy na data.

1. Na **SQL Database** stránce pro vaši databázi, vyberte **editor dotazů (preview)** v levé nabídce.

   ![Přihlaste se k dotazování editoru](./media/sql-database-get-started-portal/query-editor-login.png)

2. Zadejte přihlašovací údaje a vyberte **OK**.
3. Zadejte následující dotaz **editoru dotazů** podokně.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Vyberte **spustit**a pak zkontrolujte výsledky dotazu v **výsledky** podokně.

   ![výsledky editoru dotazů](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zavřít **editoru dotazů** stránku a vybrat **OK** po zobrazení výzvy zahoďte neuložené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít na zachovat tuto skupinu prostředků, serveru databáze a izolované databáze [další kroky](#next-steps). Následující kroky ukazují, jak k připojení a dotazování databáze pomocí různých metod.

Jakmile budete hotovi, tyto prostředky používá, můžete je odstranit následujícím způsobem:

1. V nabídce vlevo na webu Azure Portal vyberte **skupiny prostředků**a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků, vyberte **odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* v poli a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databáze z místní nebo vzdálené nástroje. Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru, [připojení a dotazování](sql-database-connect-query.md) vaší databáze pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Vytvoření izolované databáze na úrovni zřízených výpočetních pomocí Azure CLI najdete v tématu [ukázky v Azure CLI](sql-database-cli-samples.md).
- Vytvoření izolované databáze na úrovni zřízených výpočetních pomocí Azure Powershellu najdete v tématu [ukázky Azure Powershellu](sql-database-powershell-samples.md).
- Vytvoření izolované databáze na úrovni výpočetní prostředí pomocí Azure Powershellu najdete v tématu [vytvořit databázi bez serveru pomocí prostředí PowerShell](sql-database-serverless.md#create-new-database-in-serverless-compute-tier-using-powershell)