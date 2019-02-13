---
title: Vytvořit pravidlo brány firewall na úrovni serveru – Azure SQL Database | Dokumentace Microsoftu
description: Vytvořit pravidlo brány firewall na úrovni serveru služby SQL Database pro jednu databázi, databázi ve fondu
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
ms.date: 02/11/2019
ms.openlocfilehash: f708e5a3cd5bc0f11f8b0cfe79a791347c7a7a2b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108955"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Rychlý start: Vytvořit pravidlo brány firewall na úrovni serveru pro jeden a ve fondu databáze pomocí webu Azure portal

Tento rychlý start vás provede vytvořením [pravidlo brány firewall na úrovni serveru](sql-database-firewall-configure.md) jednoho a ve fondu databází ve službě Azure SQL Database pomocí webu Azure portal umožňuje připojení k databázové servery, jeden databází a elastických fondů a na jejich databáze. Pravidlo brány firewall, je potřeba připojit z jiných prostředcích Azure i z místních prostředků.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start používá prostředky vytvořené v rámci [vytvoření izolované databáze pomocí webu Azure portal](sql-database-single-database-get-started.md) jako výchozí bod.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall úrovni serveru IP

Služba SQL Database vytvoří bránu firewall na úrovni serveru databáze pro jednu databázi, databázi ve fondu. Tato brána firewall brání klientské aplikace v připojení k serveru nebo žádnou z jeho databází jeden, nebo součástí fondu, pokud vytvoříte pravidlo brány firewall protokolu IP k otevření brány firewall. Pro připojení z IP adresy mimo Azure vytvořte pravidlo brány firewall pro konkrétní IP adresu nebo rozsah adres, které mají být schopná připojit. Další informace o pravidlech brány firewall protokolu IP na úrovni serveru a databáze, najdete v části [pravidla brány firewall IP SQL Database na úrovni serveru a databáze](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
> [!IMPORTANT]
> Pravidlo brány firewall 0.0.0.0 povoluje všem službám Azure přes pravidlo brány firewall na úrovni serveru a zkuste se připojit k databázi jeden, nebo součástí fondu prostřednictvím serveru. Další informace o použití pravidel virtuální sítě, naleznete v tématu [pravidla virtuální sítě jako alternativy k pravidlům IP](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules).

Postupujte podle těchto kroků k vytvoření pravidla brány firewall pro IP úrovni serveru pro IP adresu vašeho klienta a umožněte externí připojení přes bránu firewall služby SQL Database pro vaši IP adresu.

1. Po [požadovaných součástí Azure SQL database](#prerequisites) nasazení dokončí, vyberte **databází SQL** z nabídky na levé straně a klikněte na tlačítko **mySampleDatabase** na  **Databáze SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru mají používat při připojování k serveru a jeho databázím v jiných rychlých startech.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Vyberte **nastavit bránu firewall serveru** na panelu nástrojů. **Nastavení brány Firewall** otevře se stránka pro databázový server.

   ![pravidlo brány firewall na úrovni serveru IP](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Zvolte **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall na úrovni serveru IP. Pravidlo firewallu protokolu IP úrovni serveru můžete otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   > [!IMPORTANT]
   > Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Zvolte **OFF** na této stránce provedete zákaz pro všechny služby Azure.
   >

5. Vyberte **Uložit**. Pravidlo firewallu protokolu IP úrovni serveru se vytvoří pro vaši aktuální IP adresu, které otevře port 1433 na serveru SQL Database.

6. Zavřít **nastavení brány Firewall** stránky.

Pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru, můžete teď připojit k serveru SQL Database a jeho databázím z této IP adresy, pomocí účtu správce serveru vytvořeného dříve.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uložte tyto prostředky, pokud chcete přejít na [Další kroky](#next-steps) a seznámit se s několika způsoby, jak se připojit k databázi a dotazovat ji. Pokud však chcete prostředky, které jste vytvořili v rámci tohoto rychlého startu odstranit, použijte následující postup.

1. V nabídce vlevo na webu Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků, vyberte **odstranit**, typ **myResourceGroup** v textovém poli a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

- Když máte databázi, můžete se k ní [připojit a vytvářet dotazy](sql-database-connect-query.md) v některém z oblíbených nástrojů nebo jazyků, například:
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
  - [Návrh první databáze jeden ve službě Azure SQL Database pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh izolované databáze ve službě Azure SQL Database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
