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
ms.openlocfilehash: dab7623c86bea4e562313e618f238b9b33c0fdc5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117286"
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

Další informace najdete v tématu [připojovací řetězce a konfigurační soubory](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
