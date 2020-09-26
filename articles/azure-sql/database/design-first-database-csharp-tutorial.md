---
title: 'Návrh první relační databáze C #'
description: Naučte se navrhovat první relační databázi v Azure SQL Database pomocí jazyka C# pomocí ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 3cf73816b89d0a89ff8066fe45ceaece60c230b8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321726"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Kurz: návrh relační databáze v Azure SQL Database C&#x23; a ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database je relační databáze jako služba (DBaaS) v Microsoft Cloud (Azure). V tomto kurzu zjistíte, jak pomocí webu Azure Portal a ADO.NET v sadě Visual Studio provádět následující úlohy:

> [!div class="checklist"]
>
> * Vytvoření databáze pomocí Azure Portal
> * Nastavení pravidla brány firewall protokolu IP na úrovni serveru pomocí Azure Portal
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET
> * Dotazovat data pomocí ADO.NET

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!TIP]
> Následující Microsoft Learn modul vám pomůže naučit se zdarma, jak [vyvíjet a konfigurovat aplikaci ASP.NET, která se dotazuje na Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), včetně vytvoření jednoduché databáze.

## <a name="prerequisites"></a>Požadavky

Instalace sady [Visual Studio 2019](https://www.visualstudio.com/downloads/) nebo novější.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Vytvoření prázdné databáze v Azure SQL Database

Databáze v Azure SQL Database se vytvoří s definovanou sadou výpočetních prostředků a prostředků úložiště. Databáze se vytvoří v rámci [skupiny prostředků Azure](../../active-directory-b2c/overview.md) a spravuje se pomocí [logického SQL serveru](logical-servers.md).

Pomocí těchto kroků vytvořte prázdnou databázi.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. Do formuláře **SQL Database** zadejte následující informace, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Zvolit zdroj** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Pokud chcete použít existující server nebo vytvořit a nakonfigurovat nový server, klikněte na **Server** . Buď vyberte existující server, nebo klikněte na **vytvořit nový server** a vyplňte formulář **nového serveru** pomocí následujících informací:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít aspoň osm znaků a musí obsahovat znaky ze tří z těchto kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Klikněte na **Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Můžete prozkoumat možnosti pro počet DTU/virtuální jádra a úložiště, které máte k dispozici pro jednotlivé úrovně služeb.

    Po výběru úrovně služby, počtu DTU nebo virtuální jádra a velikosti úložiště klikněte na **použít**.

7. Zadejte **kolaci** pro prázdnou databázi (pro tento kurz použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste dokončili formulář **SQL Database** , klikněte na **vytvořit** a zřiďte databázi. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![oznámení](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall protokolu IP na úrovni serveru

SQL Database vytvoří bránu firewall protokolu IP na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru a libovolné databázi na serveru, pokud pravidlo brány firewall nepovoluje jejich IP adresu přes bránu firewall. Pokud chcete povolit externí připojení k vaší databázi, musíte nejdřív přidat pravidlo brány firewall protokolu IP pro vaši IP adresu (nebo rozsah IP adres). Pomocí těchto kroků vytvořte [pravidlo brány firewall protokolu IP na úrovni serveru](firewall-configure.md).

> [!IMPORTANT]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit k této službě v rámci podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k databázi, pokud správce neotevře port 1433.

1. Po dokončení nasazení klikněte na **databáze SQL** z nabídky na levé straně a pak na stránce **databáze SQL** klikněte na *yourDatabase* . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný **název serveru** (například *yourserver.Database.Windows.NET*), který poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a databázím z SQL Server Management Studio.

   ![název serveru](./media/design-first-database-csharp-tutorial/server-name.png)

3. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **nastavení brány firewall** pro server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Kliknutím na **Přidat IP adresu klienta** na panelu nástrojů přidejte svoji aktuální IP adresu do nového pravidla brány firewall protokolu IP. Pravidlo brány firewall protokolu IP může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaše IP adresa teď může projít bránou firewall protokolu IP. Nyní se můžete připojit k databázi pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Ujistěte se, že používáte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup prostřednictvím brány firewall SQL Database IP adres povolený pro všechny služby Azure. Kliknutím na **vypnout** na této stránce zakážete přístup pro všechny služby Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní databázové úlohy, jako je například vytvoření databáze a tabulek, připojení k databázi, načtení dat a spuštění dotazů. Naučili jste se:

> [!div class="checklist"]
>
> * Vytvoření databáze pomocí Azure Portal
> * Nastavení pravidla brány firewall protokolu IP na úrovni serveru pomocí Azure Portal
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET
> * Dotazovat data pomocí ADO.NET

Přejděte k dalšímu kurzu, kde se dozvíte o migraci dat.

> [!div class="nextstepaction"]
> [Offline migrace SQL Serveru do služby Azure SQL Database pomocí DMS](../../dms/tutorial-sql-server-to-azure-sql.md)
