---
title: Získání připojovacího řetězce z webu Azure Portal
description: Získání připojovacího řetězce z webu Azure Portal
keywords: připojení SQL,připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175087"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Získání připojovacího řetězce z webu Azure Portal
Pomocí [portálu Azure](https://portal.azure.com/) získáte připojovací řetězec, který je nezbytný pro interakci vašeho klientského programu s Azure SQL Database.

1. Vyberte **všechny služby** > **SQL databáze**.

2. Zadejte název databáze do textového pole filtru v levém horním rohu okna **databáze SQL.**

3. Vyberte řádek databáze.

4. Po zobrazení okna pro vaši databázi vyberte pro vizuální pohodlí **minimalizovat** tlačítka sbalit okna, které jste použili pro procházení a filtrování databáze.

5. V okně pro databázi vyberte **Zobrazit připojovací řetězce databáze**.

6. Zkopírujte příslušný připojovací řetězec. Pokud chcete použít knihovnu připojení ADO.NET, zkopírujte příslušný řetězec z karty **ADO.NET.**

    ![Kopírování připojovacího řetězce ADO pro databázi][20-CopyAdoConnectionString]

7. Podle potřeby upravte připojovací řetězec. Vložte heslo do připojovacího řetězce nebo&lt;odeberte z uživatelského jména název&gt;@název_serveru, pokud je uživatelské jméno nebo název serveru příliš dlouhé.

8. V jednom nebo druhém formátu vložte informace o připojovacím řetězci do kódu klientského programu.

Další informace naleznete v tématu [Připojovací řetězce a konfigurační soubory](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
