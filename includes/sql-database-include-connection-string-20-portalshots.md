---
title: Získání připojovacího řetězce z webu Azure portal
description: Získání připojovacího řetězce z webu Azure portal
keywords: připojení k SQL, připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262790"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Získání připojovacího řetězce z webu Azure portal
Použití [webu Azure portal](https://portal.azure.com/) k získání připojovacího řetězce, které jsou nezbytné pro váš klientský program pro interakci s Azure SQL Database.

1. Vyberte **všechny služby** > **databází SQL**.

2. Zadejte název databáze do textového pole filtru v levém horním rohu **databází SQL** okno.

3. Vyberte řádek pro vaši databázi.

4. Jakmile se zobrazí v okně pro vaši databázi, vyberte visual pohodlí **minimalizovat** tlačítka sbalte oken, které jste použili pro procházení a filtrování databáze.

5. V okně pro vaši databázi, vyberte **zobrazit databázové připojovací řetězce**.

6. Zkopírujte příslušný připojovací řetězec. Například pokud máte v úmyslu používat knihovnu připojení ADO.NET, zkopírujte příslušným řetězcem z **ADO.NET** kartu.

    ![Zkopírujte připojovací řetězec pro vaši databázi ADO][20-CopyAdoConnectionString]

7. Podle potřeby upravte připojovací řetězec. tj. Vložte heslo do připojovacího řetězce, nebo odeberte "@&lt;servername&gt;" z uživatelské jméno, pokud uživatelské jméno nebo název serveru jsou příliš dlouhé.

8. V jeden formát nebo jiném vložte informace o připojovacím řetězci do váš klientský kód programu.

Další informace najdete v tématu [připojovací řetězce a konfigurační soubory](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
