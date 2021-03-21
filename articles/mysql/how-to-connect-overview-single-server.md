---
title: Připojení a dotazování – jeden server MySQL
description: Odkazy na rychlé starty, které ukazují, jak se připojit ke svému SQL Databasemu jednomu serveru Azure a spouštět dotazy.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546428"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Přehled připojení a dotazování pro Azure Database for MySQL – jeden server

Následující dokument obsahuje odkazy na příklady, které ukazují, jak se připojit a dotazovat se na Azure Database for MySQL jednom serveru. Tato příručka také obsahuje doporučení a knihovny TLS, pomocí kterých se můžete připojit k serveru v níže uvedených podporovaných jazycích.

## <a name="quickstarts"></a>Rychlé starty

| Rychlé zprovoznění | Description |
|---|---|
|[MySQL Workbench](connect-workbench.md)|Tento rychlý Start ukazuje, jak se pomocí klienta MySQL Workbench připojit k databázi. Příkazy MySQL pak můžete použít k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Tento článek ukazuje, jak spustit **mysql.exe** v [Azure Cloud Shell](../cloud-shell/overview.md) pro připojení k vašemu serveru a následné spuštění příkazů pro dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[MySQL se sadou Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|MySQL pro Visual Studio můžete použít pro připojení k serveru MySQL. MySQL pro Visual Studio se integruje přímo do Průzkumník serveru usnadňuje instalaci nových připojení a práci s databázovými objekty.|
|[PHP](connect-php.md)|Tento rychlý Start ukazuje použití PHP k vytvoření programu pro připojení k databázi a použití příkazů MySQL k dotazování dat.|
|[Java](connect-java.md)|Tento rychlý Start ukazuje použití jazyka Java pro připojení k databázi a následné použití příkazů MySQL k dotazování dat.|
|[Node.js](connect-nodejs.md)|Tento rychlý Start ukazuje, jak použít Node.js k vytvoření programu pro připojení k databázi a použití příkazů MySQL k dotazování dat.|
|[.NET (C#)](connect-csharp.md)|Tento rychlý Start ukazuje, jak use.NET (C#) vytvořit program v jazyce C# pro připojení k databázi a použití příkazů MySQL k dotazování dat.|
|[Přejít](connect-go.md)|V tomto rychlém startu se dozvíte, jak použít Přejít k připojení k databázi. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Python](connect-python.md)|Tento rychlý Start ukazuje použití Pythonu pro připojení k databázi a použití příkazů MySQL k dotazování dat. |
|[Ruby](connect-ruby.md)|Tento rychlý Start ukazuje použití Ruby k vytvoření programu pro připojení k databázi a použití příkazů MySQL k dotazování dat.|
|[C++](connect-cpp.md)|Tento rychlý Start ukazuje použití jazyka C++ + k vytvoření programu pro připojení k databázi a použití dat dotazů.|

## <a name="tls-considerations-for-database-connectivity"></a>Požadavky TLS pro připojení databáze

Protokol TLS (Transport Layer Security) se používá u všech ovladačů, které Microsoft poskytuje nebo podporuje pro připojení k databázím v Azure Database for MySQL. Není potřeba žádná zvláštní konfigurace, ale vynutila TLS 1,2 pro nově vytvořené servery. Doporučujeme, abyste při použití TLS 1,0 a 1,1 aktualizovali verzi TLS pro vaše servery. Viz [jak nakonfigurovat TLS](howto-tls-configurations.md) .

## <a name="libraries"></a>Knihovny

Azure Database for MySQL používá nejoblíbenější komunitní edici Community databáze MySQL. Proto je kompatibilní s širokou škálou programovacích jazyků a ovladačů. Cílem je podpořit tři nejnovější verze ovladačů MySQL a snahy s autory z komunity Open sources, aby neustále vylepšily funkčnost a použitelnost ovladačů MySQL.

Podívejte se na to, jaké [ovladače](concepts-compatibility.md) jsou kompatibilní s Azure Database for MySQLm jediným serverem.

## <a name="next-steps"></a>Další kroky

- [Migrace dat pomocí výpisu a obnovení](concepts-migrate-dump-restore.md)
- [Migrace dat pomocí importu a exportu](concepts-migrate-import-export.md)