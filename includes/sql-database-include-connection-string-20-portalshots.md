---
title: Získání připojovacího řetězce z Azure Portal
description: Získání připojovacího řetězce z Azure Portal
keywords: připojení SQL, připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175087"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Získání připojovacího řetězce z Azure Portal
Použijte [Azure Portal](https://portal.azure.com/) k získání připojovacího řetězce, který je nezbytný pro komunikaci vašeho klientského programu s Azure SQL Database.

1. Vyberte **všechny služby**  >  **databáze SQL**.

2. Do textového pole Filtr v blízkosti levého horního rohu okna **databáze SQL** zadejte název vaší databáze.

3. Vyberte řádek pro vaši databázi.

4. Po zobrazení okna pro vaši databázi můžete pro vizuální usnadnění vybrat tlačítka **minimalizovat** a sbalit tak okna, která jste použili pro procházení a filtrování databáze.

5. V okně databáze vyberte **Zobrazit databázové připojovací řetězce**.

6. Zkopírujte příslušný připojovací řetězec. To znamená, že pokud máte v úmyslu použít knihovnu připojení ADO.NET, zkopírujte příslušný řetězec z karty **ADO.NET** .

    ![Zkopírování připojovacího řetězce ADO pro vaši databázi][20-CopyAdoConnectionString]

7. Podle potřeby upravte připojovací řetězec. To znamená, že vložte heslo do připojovacího řetězce nebo odeberte "@ &lt; servername &gt; " z uživatelského jména, pokud je název uživatelského jména nebo serveru moc dlouhý.

8. V jednom nebo jiném formátu vložte do kódu klientského programu informace o připojovacím řetězci.

Další informace najdete v tématu [připojovací řetězce a konfigurační soubory](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
