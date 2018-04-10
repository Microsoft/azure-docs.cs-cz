---
title: Vytvoření a dotazování datového skladu SQL Azure – Azure Portal | Microsoft Docs
description: Na webu Azure Portal vytvoříte službu Azure SQL Data Warehouse a pak ji budete dotazovat.
services: sql-data-warehouse
author: hirokib
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 03/28/2018
ms.author: elbutter
ms.reviewer: jrj
ms.openlocfilehash: 97738b37f81cac3e7eef263fecc2095c9ef4b7ff
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlé zprovoznění: Vytvoření a dotazování datového skladu SQL Azure na webu Azure Portal

Rychle vytvořte a dotazujte datový sklad SQL Azure pomocí webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Vytvoření datového skladu

Datový sklad SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků](performance-tiers.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru SQL Azure](../sql-database/sql-database-features.md). 

Podle následujícího postupu vytvořte datový sklad SQL, který obsahuje ukázková data AdventureWorksDW. 

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** vyberte **Databáze** a v části **Doporučené** na stránce **Nový** vyberte **SQL Data Warehouse**.

    ![vytvoření prázdného datového skladu](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Do formuláře služby SQL Data Warehouse zadejte následující informace:   

    | Nastavení | Navrhovaná hodnota | Popis | 
    | ------- | --------------- | ----------- | 
    | **Název databáze** | mySampleDataWarehouse | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). Poznámka: datový sklad je typem databáze.| 
    | **Předplatné** | Vaše předplatné  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
    | **Výběr zdroje** | Ukázka | Určuje, že se má načíst ukázková databáze. Poznámka: datový sklad je jedním z typů databáze. |
    | **Výběr ukázky** | AdventureWorksDW | Určuje, že se má načíst ukázková databáze AdventureWorksDW.  |

    ![vytvoření datového skladu](media/create-data-warehouse-portal/select-sample.png)

4. Klikněte na **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace: 

    | Nastavení | Navrhovaná hodnota | Popis | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![vytvoření databázového serveru](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klikněte na **Vybrat**.

6. Klikněte na **Úroveň výkonu** a zadejte konfiguraci výkonu pro datový sklad.

7. Pro účely tohoto kurzu vyberte úroveň výkonu **Optimalizováno pro elasticitu**. Posuvník je ve výchozím nastavení nastavený na hodnotu **DW400**.  Zkuste jeho posouváním hodnotu zvýšit a snížit a podívejte se, jak funguje. 

    ![konfigurace výkonu](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klikněte na tlačítko **Použít**.

9. Po vyplnění formuláře pro SQL Database klikněte na **Vytvořit** a databázi zřiďte. Zřizování trvá několik minut. 

    ![kliknutí na Vytvořit](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.
    
     ![oznámení](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Data Warehouse vytvoří bránu firewall na úrovni serveru, aby zabránila externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres.  Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta. 

> [!NOTE]
> SQL Data Warehouse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

1. Po dokončení nasazení klikněte na **Databáze SQL** z nabídky na levé straně a klikněte na **mySampleDatabase** na stránce **Databáze SQL**. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20171113.database.windows.net**) a možnosti pro další konfiguraci. 

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech. Pokud chcete otevřít nastavení serveru, klikněte na název serveru.

   ![vyhledání názvu serveru](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Pokud chcete otevřít nastavení serveru, 
4. klikněte na název serveru.

   ![nastavení serveru](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Klikněte na **Zobrazit nastavení brány firewall**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

   ![pravidlo brány firewall serveru](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Pokud chcete do nového pravidla brány firewall přidat svou aktuální IP adresu, klikněte na **Přidat IP adresu klienta** na panelu nástrojů. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Pomocí této IP adresy se teď můžete připojit k serveru SQL a jeho datovým skladům. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.  

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Pokud chcete bránu firewall zakázat pro všechny služby Azure, klikněte na této stránce na **VYPNUTO** pak klikněte na **Uložit**.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **SQL Database** a na stránce **Databáze SQL** klikněte na vaši databázi. 
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název mynewserver-20171113.database.windows.net. 

    ![informace o připojení](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Tady je příklad: **mynewserver-20171113.database.windows.net**. |
   | Authentication | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |

    ![Připojení k serveru](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Klikněte na **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

5. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

    ![databázové objekty](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Spuštění nějakých dotazů

SQL Data Warehouse jako dotazovací jazyk používá T-SQL. Pokud chcete otevřít okno dotazu a spustit nějaké dotazy T-SQL, použijte následující postup:

1. Klikněte pravým tlačítkem na **mySampleDataWarehouse** a vyberte **Nový dotaz**.  Otevře se nové okno dotazu.
2. V okně dotazu zadejte následující příkaz, který zobrazí seznam databází.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Klikněte na tlačítko **Spustit**.  Výsledky dotazu ukazují dvě databáze: **master** a **mySampleDataWarehouse**.

    ![Dotazování databází](media/create-data-warehouse-portal/query-databases.png)

4. Pokud se chcete podívat na nějaká data, pomocí následujícího příkazu zobrazte počet zákazníků s příjmením Adams, kteří mají tři děti. Seznam výsledků obsahuje šest zákazníků. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Dotaz dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a uložená data v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat pouze úložiště dat. Výpočetní prostředky můžete obnovit, kdykoli budete připraveni s daty pracovat.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na váš datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

2. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, klikněte na **Odstranit**.

3. Pokud chcete odstranit server SQL, který jste vytvořili, klikněte na **mynewserver-20171113.database.windows.net**, jak je znázorněno na předchozím obrázku, a pak klikněte na **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

4. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další kroky
Právě jste vytvořili datový sklad a pravidlo brány firewall, připojili jste se ke svému datovému skladu a spustili jste několik dotazů. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.
> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)