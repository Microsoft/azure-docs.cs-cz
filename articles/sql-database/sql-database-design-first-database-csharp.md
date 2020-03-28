---
title: 'Navrhněte si svou první relační databázi C #'
description: Naučte se navrhnout první relační databázi v jedné databázi v Azure SQL Database s C# pomocí ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 0f1140bbefc7508666e763fcd4f1a04ba48cdfdd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75354944"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Kurz: Návrh relační databáze v jedné databázi v rámci Azure SQL Database C&#x23; a ADO.NET

Azure SQL Database je relační databáze jako služba (DBaaS) v Microsoft Cloudu (Azure). V tomto kurzu zjistíte, jak pomocí webu Azure Portal a ADO.NET v sadě Visual Studio provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření jedné databáze pomocí portálu Azure*
> * Nastavení pravidla brány firewall IP na úrovni serveru pomocí portálu Azure
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET
> * Dotazovat data pomocí ADO.NET

*Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

> [!TIP]
> Následující modul Microsoft Learn vám pomůže zdarma se naučit [vyvíjet a konfigurovat ASP.NET aplikaci, která se dotazuje na Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), včetně vytvoření jednoduché databáze.

## <a name="prerequisites"></a>Požadavky

Instalace [Visual Studia 2019](https://www.visualstudio.com/downloads/) nebo novější.

## <a name="create-a-blank-single-database"></a>Vytvoření prázdné jedné databáze

Jedna databáze v Azure SQL Database se vytvoří s definovanou sadou výpočetních prostředků a prostředků úložiště. Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) a spravuje se pomocí [databázového serveru](sql-database-servers.md).

Chcete-li vytvořit prázdnou jednu databázi, postupujte takto.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

3. Vyplňte formulář **databáze SQL** následujícími informacemi, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *vaše databáze* | Platné názvy databází naleznete v [tématu Identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *vaše předplatné*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *vašeskupina zdrojů* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Vybrat zdroj** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Chcete-li použít existující databázový server nebo vytvořit a nakonfigurovat nový databázový server, klepněte na **tlačítko Server.** Vyberte existující server nebo **klepněte** na tlačítko Vytvořit nový server a vyplňte formulář **Nový server** následujícími informacemi:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Přihlášení správce serveru** | Libovolné platné jméno | Platné přihlašovací názvy naleznete v [tématu Identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, čísla a nealfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klepněte na **tlačítko Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Můžete prozkoumat možnosti pro počet DTU/virtuálních jader a úložiště, které jsou k dispozici pro každou úroveň služby.

    Po výběru úrovně služby, počtu DTU nebo virtuálních jader a velikosti úložiště klikněte na **použít**.

7. Zadejte **kolaci** pro prázdnou databázi (pro tento kurz použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste vyplnili formulář **databáze SQL,** klikněte na **Vytvořit** a zřídit jednu databázi. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![oznámení](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall IP na úrovni serveru

Služba SQL Database vytvoří bránu firewall IP na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru a všem databázím na serveru, pokud pravidlo brány firewall neumožňuje jejich adresu IP přes bránu firewall. Chcete-li povolit externí připojení k jedné databázi, musíte nejprve přidat pravidlo brány firewall IP pro vaši IP adresu (nebo rozsah IP adres). Následujícím postupem vytvořte [pravidlo brány firewall na úrovni serveru SQL Database .](sql-database-firewall-configure.md)

> [!IMPORTANT]
> Služba SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit k této službě z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall sítě povolen. Pokud ano, nelze se připojit k jedné databázi, pokud správce neotevře port 1433.

1. Po dokončení nasazení klikněte na **databáze SQL** z nabídky na levé straně a potom klikněte na *yourDatabase* na stránce **databáze SQL.** Otevře se stránka s přehledem databáze, která zobrazuje plně kvalifikovaný **název serveru** (například *yourserver.database.windows.net)* a poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru pro připojení k serveru a databázím ze sady SQL Server Management Studio.

   ![název serveru](./media/sql-database-design-first-database/server-name.png)

3. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![Pravidlo brány firewall IP na úrovni serveru](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klepnutím na **tlačítko Přidat IP adresu klienta** na panelu nástrojů přidejte aktuální adresu IP do nového pravidla brány firewall IP. Pravidlo brány firewall IP může otevřít port 1433 pro jednu adresu IP nebo rozsah adres IP.

5. Klikněte na **Uložit**. Pro aktuální port pro otevření ip adresy 1433 na serveru SQL Database je vytvořeno pravidlo brány firewall IP na úrovni serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaše IP adresa může nyní projít ip firewallem. Nyní se můžete připojit k jedné databázi pomocí sql server management studio nebo jiný nástroj dle vašeho výběru. Nezapomeňte použít účet správce serveru, který jste vytvořili dříve.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup přes bránu firewall IP databáze SQL povolen pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní databázové úlohy, jako je například vytvoření databáze a tabulek, připojení k databázi, načtení dat a spouštění dotazů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí sady [Visual Studio a jazyka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Vytvoření tabulek
> * Vložení, aktualizace, odstranění a dotazování dat

Přejdete k dalšímu kurzu, kde se dozvíte o migraci dat.

> [!div class="nextstepaction"]
> [Offline migrace SQL Serveru do služby Azure SQL Database pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md)
