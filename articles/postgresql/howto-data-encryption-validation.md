---
title: Jak zajistit ověřování Azure Database for PostgreSQLho šifrování dat
description: Naučte se, jak ověřit šifrování Azure Database for PostgreSQLho šifrování dat pomocí klíče spravovaného zákazníky.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 7ec27cc4f28151214ca97ffb5113607d6b60ee36
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240575"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Ověřování šifrování dat pro Azure Database for PostgreSQL

Tento článek vám pomůže ověřit, že šifrování dat pomocí spravovaného klíče zákazníka pro Azure Database for PostgreSQL pracuje podle očekávání.

## <a name="check-the-encryption-status"></a>Ověřte stav šifrování.

### <a name="from-portal"></a>Na portálu

1. Pokud chcete ověřit, jestli se klíč zákazníka používá k šifrování, postupujte podle těchto kroků:

    * V Azure Portal přejděte na **Azure Key Vault**  ->  **klíče** Azure Key Vault
    * Vyberte klíč, který se používá pro šifrování serveru.
    * Nastavte stav **povoleného** klíče na **ne** .
  
       Po určité době ( **~ 15 min** ) by měl být **stav** serveru Azure Database for PostgreSQL **nepřístupný** . Jakékoli vstupně-výstupní operace provedené na serveru selžou, což Ověří, jestli je server skutečně zašifrovaný s klíčem Customers a klíč není aktuálně platný.
    
        Aby byl server **dostupný** pro, můžete klíč znovu ověřit. 
    
    * Nastavte stav klíče v Key Vault na **Ano** .
    * V **šifrování dat** serveru vyberte znovu **Ověřit klíč** .
    * Po úspěšném ověření klíče se **stav** serveru změní na **k dispozici** .

2. Pokud se v Azure Portal můžete ujistit, že je šifrovací klíč nastavený, data se šifrují pomocí klíče Customers používaného v Azure Portal.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Přehled zásad přístupu":::

### <a name="from-cli"></a>Z CLI

1. Pomocí příkazu *AZ CLI* můžete ověřit klíčové prostředky používané pro server Azure Database for PostgreSQL.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    U serveru bez sady šifrování dat tento příkaz vyplní prázdnou sadu [].

### <a name="azure-audit-reports"></a>Sestavy služby Audit Azure

Můžete také zkontrolovat [sestavy auditu](https://servicetrust.microsoft.com) , které poskytují informace o kompatibilitě s normami ochrany dat a zákonnými požadavky.

## <a name="next-steps"></a>Další kroky

Další informace o šifrování dat najdete v tématu [Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem](concepts-data-encryption-postgresql.md).