---
title: 'Azure Portal: Vytvoření databáze SQL | Dokumentace Microsoftu'
description: Vytvořte logický server služby SQL Database, pravidlo brány firewall na úrovni serveru a databázi na webu Azure Portal a dotazujte ji.
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
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165003"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Vytvoření databáze SQL Azure na webu Azure Portal

Tento rychlý start vás provede vytvořením databáze SQL v Azure s využitím [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md). Azure SQL Database je nabídka „databáze jako služby“, která umožňuje spouštění a škálování vysoce dostupné databáze SQL Serveru v cloudu. Tento rychlý start ukazuje, jak začít tím, že vytvoříte a pak dotazujete databázi SQL pomocí portálu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

  >[!NOTE]
  >Tento kurz používá model nakupování založený na DTU, ale k dispozici je i [model nakupování založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

Databáze SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků a prostředků úložiště](sql-database-service-tiers-dtu.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru Azure SQL Database](sql-database-features.md).

Postupujte podle následujících kroků a vytvořte databázi SQL obsahující ukázková data Adventure Works LT.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** vyberte **Databáze** a v části **Databáze SQL** na stránce **Nový** vyberte **Vytvořit**.

   ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Vyplňte formulář databáze SQL pomocí následujících informací, jak je vidět na předchozím obrázku:   

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název databáze** | mySampleDatabase | Platné názvy databází najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Předplatné** | Vaše předplatné  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků**  | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Výběr zdroje** | Ukázka (AdventureWorksLT) | Načte schéma a data AdventureWorksLT do vaší nové databáze. |

   > [!IMPORTANT]
   > V tomto formuláři je nutné vybrat ukázkovou databázi, protože se používá ve zbývající části tohoto rychlého startu.
   >

4. V části **Server** klikněte na **Konfigurovat požadovaná nastavení** a vyplňte formulář serveru SQL (logický server) pomocí následujících informací, jak je vidět na tomto obrázku:   

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň 8 znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
   | **Předplatné** | Vaše předplatné | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Pokud jste formulář vyplnili, klikněte na **Vybrat**.

6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU a velikost úložiště. Prozkoumejte možnosti pro množství DTU a velikost úložiště, které máte k dispozici na jednotlivých úrovních služby.

   > [!IMPORTANT]
   > Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou následujících: Velká Británie – sever, USA – středozápad, Velká Británie – jih 2, Čína – východ, USDoD – střed, Německo – střed, USDoD – východ, US Gov – jihozápad, US Gov (střed) – jih, Německo – severovýchod, Čína – sever, USGov – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Pro účely tohoto rychlého startu vyberte úroveň služby **Standard** a pak pomocí posuvníku vyberte **10 DTU (S0)** a **1** GB úložiště.

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Přijměte podmínky verze Preview pro použití možnosti **Doplňkové úložiště**.

   > [!IMPORTANT]
   > Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou následujících: USA – středozápad, Čína – východ, USDoD – střed, US Gov – Iowa, Německo – střed, USDoD – východ, US Gov – jihozápad, Německo – severovýchod, Čína – sever. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Po výběru úrovně služby, počtu DTU a velikosti úložiště klikněte na **Použít**.  

10. Po vyplnění formuláře pro SQL Database klikněte na **Vytvořit** a databázi zřiďte. Zřizování trvá několik minut.

11. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

     ![oznámení](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Dotazování databáze SQL

Teď, když jste vytvořili ukázkovou databázi v Azure, můžete použít integrovaný dotazovací nástroj na webu Azure Portal k potvrzení, že se můžete připojit k databázi a zadávat dotazy na data.

1. Na stránce služby SQL Database pro vaši databázi klikněte v nabídce vlevo na **Editor dotazů (Preview)** a pak klikněte na **Přihlášení**.

   ![přihlášení](./media/sql-database-get-started-portal/query-editor-login.png)

2. Vyberte Ověřování přes server SQL, zadejte požadované přihlašovací údaje a pak se přihlaste kliknutím na tlačítko **OK**.

3. Jakmile budete ověřeni jako **Správce serveru**, do podokna editoru dotazů zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Klikněte na **Spustit** a pak zkontrolujte výsledky dotazu v podokně **Výsledky**.

   ![výsledky editoru dotazů](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zavřete stránku **Editor dotazů** a kliknutím na **OK** zahoďte neuložené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud však chcete prostředky vytvořené v rámci tohoto rychlého startu odstranit, použijte následující postup.


1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Teď, když máte databázi, musíte vytvořit pravidlo brány firewall na úrovni serveru pro připojení k bráně z místních nástrojů. Viz [Vytvoření pravidla brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md)
- Pokud vytvoříte pravidlo brány firewall na úrovni serveru, můžete se [připojit a dotazovat](sql-database-connect-query.md) pomocí některého z vašich oblíbených nástrojů nebo jazyků, včetně
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o vytvoření databází pomocí Azure CLI najdete v [ukázkách Azure CLI](sql-database-cli-samples.md)
- Informace o vytvoření databází pomocí Azure PowerShellu najdete v [ukázkách Azure PowerShellu](sql-database-powershell-samples.md)