---
title: Vytvoření pravidla brány firewall na úrovni serveru – Azure SQL Database | Microsoft Docs
description: Vytvoření pravidla brány firewall na úrovni serveru SQL Database pro databáze s jednou a ve fondu
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 27b554159fca2043fbd0c853e260a66294c10387
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162299"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Rychlý start: Vytvoření pravidla brány firewall na úrovni serveru pro databáze s jednou a ve fondu pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit [pravidlo brány firewall na úrovni serveru](sql-database-firewall-configure.md) pro databáze s jednou a ve fondu ve službě Azure SQL Database pomocí Azure Portal, abyste se mohli připojit k databázovým serverům, izolovaným databázím a elastickým fondům a jejich databázím. Pro připojení z jiných prostředků Azure a z místních prostředků se vyžaduje pravidlo brány firewall.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start používá prostředky vytvořené v rámci [Vytvoření izolované databáze pomocí Azure Portal](sql-database-single-database-get-started.md) jako jejího počátečního bodu.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall protokolu IP na úrovni serveru

Služba SQL Database vytvoří bránu firewall na úrovni databázového serveru pro databáze s jednou a ve fondu. Tato brána firewall zabraňuje klientským aplikacím v připojení k serveru nebo žádné z databází v jedné nebo sdružené databázi, pokud nevytvoříte pravidlo brány firewall protokolu IP pro otevření brány firewall. Pro připojení z IP adresy mimo Azure vytvořte pravidlo brány firewall pro konkrétní IP adresu nebo rozsah adres, ke kterým se chcete připojit. Další informace o pravidlech brány firewall na úrovni serveru a databáze najdete v tématu [SQL Database pravidla brány firewall protokolu IP na úrovni serveru a databáze](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
> [!IMPORTANT]
> Pravidlo brány firewall s hodnotou 0.0.0.0 umožňuje všem službám Azure projít pravidlo brány firewall na úrovni serveru a pokusit se o připojení k jedné nebo sdružené databázi prostřednictvím serveru. 

Postupujte podle těchto kroků a vytvořte pravidlo brány firewall protokolu IP na úrovni serveru pro IP adresu vašeho klienta a povolte externí připojení přes bránu SQL Database firewall pouze pro vaši IP adresu.

1. Po dokončení nasazení služby [Azure SQL Database](#prerequisites) v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** zvolte **mySampleDatabase** . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který se má použít při připojování k serveru a jeho databázím v jiných rychlých startech.

   ![Název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Na panelu nástrojů vyberte **nastavit bránu firewall serveru** . Otevře se stránka **nastavení brány firewall** pro databázový server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Vyberte **Přidat IP adresu klienta** na panelu nástrojů a přidejte svou aktuální IP adresu k novému pravidlu brány firewall IP na úrovni serveru. Pravidlo brány firewall protokolu IP na úrovni serveru může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   > [!IMPORTANT]
   > Ve výchozím nastavení je přístup přes bránu SQL Database firewall pro všechny služby Azure zakázaný. Pokud chcete povolit přístup pro všechny služby Azure, vyberte na této stránce **zapnuté** .
   >

5. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru SQL Database.

6. Zavřete stránku **nastavení brány firewall** .

Pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru se teď můžete připojit k serveru SQL Database a jeho databázím z této IP adresy pomocí dříve vytvořeného účtu správce serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud ale chcete odstranit prostředky, které jste vytvořili v rámci tohoto rychlého startu, použijte následující postup.

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Když máte databázi, můžete se k ní [připojit a vytvářet dotazy](sql-database-connect-query.md) v některém z oblíbených nástrojů nebo jazyků, například:
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
  - [Návrh první izolované databáze v Azure SQL Database pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh izolované databáze v Azure SQL Database a připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
