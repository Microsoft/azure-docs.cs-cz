---
title: Vytvoření pravidla brány firewall na úrovni serveru
description: Vytvoření pravidla brány firewall na úrovni serveru
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: bef1e9bdafd91a51290fe84af074c8cece7cc371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486722"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Rychlý Start: vytvoření pravidla brány firewall na úrovni serveru pomocí Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto rychlém startu se dozvíte, jak vytvořit [pravidlo brány firewall na úrovni serveru](firewall-configure.md) v Azure SQL Database pomocí Azure Portal k tomu, abyste se mohli připojit k [logickým serverům SQL](logical-servers.md), izolovaným databázím a elastickým fondům a jejich databázím. Pro připojení z jiných prostředků Azure a z místních prostředků se vyžaduje pravidlo brány firewall. Pravidla brány firewall na úrovni serveru se nevztahují na spravovanou instanci Azure SQL.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start používá prostředky vytvořené v rámci [Vytvoření izolované databáze pomocí Azure Portal](single-database-create-quickstart.md) jako jejího počátečního bodu.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall protokolu IP na úrovni serveru

 SQL Database vytvoří bránu firewall na úrovni serveru pro databáze s jednou a ve fondu. Tato brána firewall zabraňuje klientským aplikacím v připojení k serveru nebo libovolné databázi, pokud nevytvoříte pravidlo brány firewall protokolu IP pro otevření brány firewall. Pro připojení z IP adresy mimo Azure vytvořte pravidlo brány firewall pro konkrétní IP adresu nebo rozsah adres, ze kterých chcete být schopni se připojit. Další informace o pravidlech brány firewall na úrovni serveru a databáze najdete v článku [pravidla brány firewall protokolu IP na úrovni serveru a databáze](firewall-configure.md).

> [!NOTE]
> Azure SQL Database používá ke komunikaci port 1433. Pokud se pokoušíte připojit z podnikové sítě, vaše brána firewall možná nepovoluje odchozí přenosy přes port 1433. Pokud ano, nemůžete se připojit k serveru, dokud vaše IT oddělení neotevře port 1433.
> [!IMPORTANT]
> Pravidlo brány firewall s hodnotou 0.0.0.0 umožňuje všem službám Azure projít pravidlo brány firewall na úrovni serveru a pokusit se připojit k databázi prostřednictvím serveru.

Postupujte podle těchto kroků a vytvořte pravidlo brány firewall protokolu IP na úrovni serveru pro IP adresu vašeho klienta a povolte externí připojení přes bránu Azure SQL Database firewall pouze pro vaši IP adresu.

1. Po dokončení nasazení [databáze](#prerequisites) vyberte v nabídce na levé straně možnost **databáze SQL** a na stránce **databáze SQL** zvolte **mySampleDatabase** . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který se má použít při připojování k serveru a jeho databázím v jiných rychlých startech.

   ![název serveru](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Na panelu nástrojů vyberte **nastavit bránu firewall serveru** . Otevře se stránka **nastavení brány firewall** pro server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Vyberte **Přidat IP adresu klienta** na panelu nástrojů a přidejte svou aktuální IP adresu k novému pravidlu brány firewall IP na úrovni serveru. Pravidlo brány firewall protokolu IP na úrovni serveru může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   > [!IMPORTANT]
   > Ve výchozím nastavení je přístup přes bránu Azure SQL Database firewall pro všechny služby Azure zakázaný. Pokud chcete povolit přístup pro všechny služby Azure, vyberte na této stránce **zapnuté** .
   >

5. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru.

6. Zavřete stránku **nastavení brány firewall** .

Pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru se teď můžete připojit k serveru a jeho databázím z této IP adresy pomocí dříve vytvořeného účtu správce serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud ale chcete odstranit prostředky, které jste vytvořili v rámci tohoto rychlého startu, použijte následující postup.

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Když máte databázi, můžete se k ní [připojit a vytvářet dotazy](connect-query-content-reference-guide.md) v některém z oblíbených nástrojů nebo jazyků, například:
  - [Připojení a dotazování pomocí SQL Server Management Studia](connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
  - [Návrh první izolované databáze v Azure SQL Database pomocí SSMS](design-first-database-tutorial.md)
  - [Návrh izolované databáze v Azure SQL Database a připojení k C# a ADO.NET](design-first-database-csharp-tutorial.md)
