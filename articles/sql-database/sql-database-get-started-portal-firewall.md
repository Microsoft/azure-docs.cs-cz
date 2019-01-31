---
title: 'Azure portal: Vytvořte pravidlo brány firwall SQL Database | Dokumentace Microsoftu'
description: Vytvoření pravidla brány firewall na úrovni serveru databáze pro databázi SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 589d3fa8c0ee8c8f374cd4f34f17401caa46d265
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462252"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Rychlý start: Vytvoření pravidla brány firewall na úrovni serveru pro databázi SQL na webu Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit pravidlo brány firewall na úrovni serveru pro databázi Azure SQL, abyste se k ní mohli připojit z místního prostředku.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start používá prostředky vytvořené v rámci [vytvořit databázi Azure SQL na webu Azure Portal](sql-database-get-started-portal.md) jako výchozí bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Database vytvoří bránu firewall na úrovni serveru. Tato brána firewall brání aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru, pokud vytvoříte pravidlo brány firewall k otevření brány firewall. Pro připojení z IP adresy mimo Azure vytvořte pravidlo brány firewall pro konkrétní IP adresu nebo rozsah adres. Další informace o pravidlech brány firewall najdete v tématu [pravidlo brány firewall na úrovni serveru služby SQL Database](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

Postupujte podle těchto kroků vytvořte pravidlo brány firewall na úrovni serveru pro IP adresu vašeho klienta a umožněte externí připojení přes bránu firewall služby SQL Database pro vaši IP adresu.

1. Po [požadovaných součástí Azure SQL database](#prerequisites) nasazení dokončí, vyberte **databází SQL** z nabídky na levé straně a klikněte na tlačítko **mySampleDatabase** na  **Databáze SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru mají používat při připojování k serveru a jeho databázím v jiných rychlých startech.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Vyberte **nastavit bránu firewall serveru** na panelu nástrojů. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Zvolte **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   > [!IMPORTANT]
   > Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Zvolte **OFF** na této stránce provedete zákaz pro všechny služby Azure.
   >

5. Vyberte **Uložit**. Pro vaši aktuální IP adresu, které otevře port 1433 na databáze SQL serveru se vytvoří pravidlo brány firewall na úrovni serveru.

6. Zavřít **nastavení brány Firewall** stránky.

Pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru, můžete teď připojit k serveru SQL Database a jeho databázím z této IP adresy, pomocí účtu správce serveru vytvořeného dříve.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud však chcete prostředky, které jste vytvořili v rámci tohoto rychlého startu odstranit, použijte následující postup.

1. V nabídce vlevo na webu Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků, vyberte **odstranit**, typ **myResourceGroup** v textovém poli a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

- Když máte databázi, můžete se k ní [připojit a vytvářet dotazy](sql-database-connect-query.md) v některém z oblíbených nástrojů nebo jazyků, například:
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
  - [Návrh první databáze SQL Azure pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh databáze SQL Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
