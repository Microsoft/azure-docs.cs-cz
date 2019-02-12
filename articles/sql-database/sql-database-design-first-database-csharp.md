---
title: Návrh první databáze relační - C# – Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak navrhnout první relační databáze v jedné databázi ve službě Azure SQL Database s C# pomocí ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 02/08/2019
ms.openlocfilehash: ce46a6b8d4e2bc57625f9202349718dfbaedc660
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995676"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Kurz: Návrh relační databáze v jedné databázi v rámci Azure SQL Database C&#x23; a ADO.NET

Azure SQL Database je relační databáze as-a-service (DBaaS) v Microsoft cloudu (Azure). V tomto kurzu zjistíte, jak pomocí webu Azure Portal a ADO.NET v sadě Visual Studio provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření izolované databáze pomocí Azure portal *
> * Nastavit pravidlo firewallu protokolu IP úrovni serveru pomocí webu Azure portal
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET
> * Dotazovat data pomocí ADO.NET

* Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.

## <a name="prerequisites"></a>Požadavky

An installation of [Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="create-a-blank-single-database"></a>Vytvoření prázdné jedné databáze

Izolované databáze ve službě Azure SQL Database se vytvoří s definovanou sadou výpočetních a úložných prostředků. Vytvoření databáze v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a spravuje se pomocí [databázový server](sql-database-servers.md).

Postupujte podle těchto kroků můžete vytvořit prázdné jedné databáze.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

3. Vyplňte **SQL Database** formuláře následující informace, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
    | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Klikněte na tlačítko **Server** použít existující databázový server nebo vytvořte a nakonfigurujte nový server databáze. Vyberte existující server, nebo klikněte na tlačítko **vytvořit nový server** a vyplňte **nový server** formuláře následující informace:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klikněte na **Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Může prozkoumejte možnosti počtu Dtu nebo virtuálních jader a úložiště, které je k dispozici na jednotlivých úrovních služby.

    Po výběru úrovně služby, počet Dtu nebo virtuálních jader a velikost úložiště, klikněte na tlačítko **použít**.

7. Zadejte **kolace** pro prázdnou databázi (pro účely tohoto kurzu použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste dokončili **SQL Database** formuláře, klikněte na tlačítko **vytvořit** ke zřízení izolované databáze. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![oznámení](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall úrovni serveru IP

Služba SQL Database vytvoří bránu firewall IP na úrovni serveru. Tato brána firewall brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru, pokud pravidlo brány firewall umožňuje jejich IP přes bránu firewall. Pokud chcete povolit externí připojení k izolované databáze, musíte nejprve přidat pravidlo IP brány firewall pro IP adresu (nebo rozsah IP adres). Postupujte podle těchto kroků můžete vytvořit [pravidlo firewallu protokolu IP serveru SQL Database](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Služba SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit k této službě z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k izolované databáze, dokud správce otevře port 1433.

1. Jakmile se nasazení dokončí, klikněte na tlačítko **databází SQL** z nabídky na levé straně a pak klikněte na tlačítko *yourDatabase* na **databází SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, zobrazí plně kvalifikovaný **název serveru** (například *yourserver.database.windows.net*) a poskytne vám možnosti další konfigurace.

2. Zkopírujte tento plně kvalifikovaný název serveru použít pro připojení k vašemu serveru a databáze z SQL Server Management Studio.

   ![název serveru](./media/sql-database-design-first-database/server-name.png)

3. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall na úrovni serveru IP](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall protokolu IP. Pravidla brány firewall protokolu IP můžete otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Pravidlo firewallu protokolu IP úrovni serveru se vytvoří pro vaši aktuální IP adresu, které otevře port 1433 na serveru SQL Database.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaši IP adresu můžete nyní procházet brány firewall protokolu IP. Teď můžete připojit k vaší izolované databáze pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Nezapomeňte použít účet správce serveru, který jste vytvořili dříve.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup přes bránu firewall služby SQL Database IP povolené pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, že běžných úloh, jako je například vytvoření databáze a tabulky, připojení k databázi, načtení dat a spouštění dotazů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí sady [Visual Studio a jazyka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Vytvoření tabulek
> * INSERT, update, delete a dotazování dat

Přejděte k dalšímu kurzu, kde Další informace o migraci dat.

> [!div class="nextstepaction"]
> [Offline migrace SQL Serveru do služby Azure SQL Database pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md)
