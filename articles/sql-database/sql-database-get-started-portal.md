---
title: 'Azure Portal: Vytvoření databáze SQL | Dokumentace Microsoftu'
description: Vytvořte logický server služby SQL Database, pravidlo brány firewall na úrovni serveru a databázi na webu Azure Portal a dotazujte ji.
keywords: kurz k sql database, vytvoření databáze sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: carlrab
ms.openlocfilehash: b02c69f34c72fc89a4ae81fc1feb3641a7c8b06d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Vytvoření databáze SQL Azure na webu Azure Portal

Tento úvodní kurz vás provede postupy vytvoření databáze SQL v Azure. Azure SQL Database je nabídka „databáze jako služby“, která umožňuje spouštění a škálování vysoce dostupné databáze SQL Serveru v cloudu. Tento rychlý start ukazuje, jak začít tím, že vytvoříte databázi SQL pomocí webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

Databáze SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků a prostředků úložiště](sql-database-service-tiers.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru Azure SQL Database](sql-database-features.md).

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
   > Zde zadané jméno správce serveru a heslo se vyžadují k přihlášení na server a jeho databáze dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Pokud jste formulář vyplnili, klikněte na **Vybrat**.

6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU a velikost úložiště. Prozkoumejte možnosti pro množství DTU a velikost úložiště, které máte k dispozici na jednotlivých úrovních služby.

   > [!IMPORTANT]
   > \* Velikosti úložiště větší než velikost zahrnutého úložiště jsou ve verzi Preview a účtují se za ně další poplatky. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
   >
   >\* Na úrovni Premium je úložiště větší než 1 TB aktuálně dostupné v následujících oblastech: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, USA – střed, Francie – střed, Německo – střed, Japonsko – východ, Japonsko – západ, Korea – střed, Střed USA – sever, Severní Evropa, Střed USA – jih, Asie – jihovýchod, Velká Británie – jih, Velká Británie – západ, USA – východ 2, USA – západ, USA (Gov) – Virginia a Západní Evropa. Viz [Aktuální omezení pro P11–P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   >

7. Pro účely tohoto úvodního kurzu vyberte úroveň služby **Standard** a pak pomocí posuvníku vyberte **10 DTU (S0)** a **1** GB úložiště.

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Přijměte podmínky verze Preview pro použití možnosti **Doplňkové úložiště**.

   > [!IMPORTANT]
   > \* Velikosti úložiště větší než velikost zahrnutého úložiště jsou ve verzi Preview a účtují se za ně další poplatky. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
   >
   >\* Na úrovni Premium je úložiště větší než 1 TB aktuálně dostupné v následujících oblastech: Brazílie – jih, Kanada – střed, Kanada – východ, USA – střed, Francie – střed, Německo – střed, Japonsko – východ, Japonsko – západ, Korea – střed, Střed USA – sever, Severní Evropa, Střed USA – jih, Jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, USA – východ 2, USA – západ, USA (Gov) – Virginia a Západní Evropa. Viz [Aktuální omezení pro P11–P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   >

9. Po výběru úrovně služby, počtu DTU a velikosti úložiště klikněte na **Použít**.  

10. Po vyplnění formuláře pro SQL Database klikněte na **Vytvořit** a databázi zřiďte. Zřizování trvá několik minut.

11. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

     ![oznámení](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Database vytvoří bránu firewall na úrovni serveru, aby zabránila externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru, pokud není vytvořené pravidlo brány firewall k otevření brány firewall pro konkrétní IP adresy. Postupujte podle těchto kroků a vytvořte [pravidlo brány firewall na úrovni serveru služby SQL Database](sql-database-firewall-configure.md) pro vaši IP adresu klienta a umožněte externí připojení přes bránu firewall služby SQL Database pouze pro vaši IP adresu.

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

1. Po dokončení nasazení klikněte na **Databáze SQL** z nabídky na levé straně a klikněte na **mySampleDatabase** na stránce **Databáze SQL**. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Klikněte na **Nastavit bránu firewall serveru** na panelu nástrojů, jak je vidět na předchozím obrázku. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Klikněte na **Přidat IP adresu klienta** na panelu nástrojů a přidejte svoji aktuální IP adresu do nového pravidla brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Nyní se můžete z této IP adresy připojit k serveru SQL Database a jeho databázím pomocí aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru použitím účtu správce serveru vytvořeného dříve.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.
>

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

- Teď, když máte databázi, můžete se k ní [připojit a provádět dotazování](sql-database-connect-query.md) pomocí některého z vašich oblíbených nástrojů nebo jazyků. 
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
 - [Návrh první databáze SQL Azure pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh databáze SQL Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
