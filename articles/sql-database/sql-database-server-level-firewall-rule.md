---
title: Vytvoření pravidla brány firewall na úrovni serveru
description: Vytvoření pravidla brány firewall na úrovni serveru SQL Database pro jednu a sdruženou databázi
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
ms.openlocfilehash: ff2508952b75bad88ff8ff92388c20ba52f50f42
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73818257"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Úvodní příručka: Vytvoření pravidla brány firewall na úrovni serveru pro jednu a sdruženou databázi pomocí portálu Azure

Tento rychlý start vás provede vytvořením [pravidla brány firewall na úrovni serveru](sql-database-firewall-configure.md) pro jednu a sdruženou databázi v Azure SQL Database pomocí portálu Azure, který vám umožní připojit se k databázovým serverům, jednotlivým databázím a elastickým fondům a jejich databázím. Pravidlo brány firewall je nutné pro připojení z jiných prostředků Azure a z místních prostředků.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start používá prostředky vytvořené v [vytvořit jednu databázi pomocí portálu Azure](sql-database-single-database-get-started.md) jako výchozí bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall IP na úrovni serveru

Služba SQL Database vytvoří bránu firewall na úrovni databázového serveru pro jednu a sdružené databáze. Tato brána firewall zabraňuje klientským aplikacím v připojení k serveru nebo některé z jeho jedné nebo sdružené databáze, pokud nevytvoříte pravidlo brány firewall IP pro otevření brány firewall. Pro připojení z IP adresy mimo Azure vytvořte pravidlo brány firewall pro konkrétní IP adresu nebo rozsah adres, které se chcete připojit. Další informace o pravidlech brány firewall IP na úrovni serveru a databáze naleznete v tématu [SQL Database server-level a database-level IP firewall rules](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, vaše brána firewall možná nepovoluje odchozí přenosy přes port 1433. Pokud ano, nemůžete se připojit k serveru Azure SQL Database, pokud vaše IT oddělení neotevře port 1433.
> [!IMPORTANT]
> Pravidlo brány firewall 0.0.0.0 umožňuje všem službám Azure projít pravidlem brány firewall na úrovni serveru a pokusit se připojit k jedné nebo sdružené databázi prostřednictvím serveru. 

Následujícím postupem vytvořte pravidlo brány firewall IP na úrovni serveru pro ip adresu vašeho klienta a povolte externí připojení přes bránu firewall databáze SQL pouze pro vaši IP adresu.

1. Po dokončení [požadovaného](#prerequisites) nasazení databáze Azure SQL vyberte **databáze SQL** z nabídky na levé straně a pak zvolte **mySampleDatabase** na stránce **databáze SQL.** Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který chcete použít při připojování k serveru a jeho databázím v jiných rychlých startech.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Na panelu nástrojů vyberte **Nastavit serverovou bránu firewall.** Otevře se stránka **Nastavení brány firewall** pro databázový server.

   ![Pravidlo brány firewall IP na úrovni serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Chcete-li přidat aktuální adresu IP na novém pravidle brány firewall IP na úrovni serveru, zvolte přidat IP adresu **klienta** na panelu nástrojů. Pravidlo brány firewall IP na úrovni serveru může otevřít port 1433 pro jednu adresu IP nebo rozsah adres IP.

   > [!IMPORTANT]
   > Ve výchozím nastavení je přístup přes bránu firewall databáze SQL pro všechny služby Azure zakázán. Pokud chcete povolit přístup pro všechny služby Azure, zvolte na této stránce možnost **ZAPNUTO.**
   >

5. Vyberte **Uložit**. Pro aktuální port pro otevření ip adresy 1433 na serveru SQL Database je vytvořeno pravidlo brány firewall IP na úrovni serveru.

6. Zavřete stránku **nastavení brány firewall.**

Pomocí sql server management studio nebo jiný nástroj podle vašeho výběru, nyní můžete připojit k serveru SQL Database a jeho databází z této adresy IP pomocí účtu správce serveru vytvořené dříve.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud však chcete odstranit prostředky, které jste vytvořili v tomto rychlém startu, použijte následující kroky.

1. V levé nabídce na portálu Azure vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, zadejte do textového pole **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Když máte databázi, můžete se k ní [připojit a vytvářet dotazy](sql-database-connect-query.md) v některém z oblíbených nástrojů nebo jazyků, například:
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
  - [Návrh první jediné databáze v Azure SQL Database pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh jedné databáze v Azure SQL Database a připojení s C# a ADO.NET](sql-database-design-first-database-csharp.md)
