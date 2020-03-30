---
title: Připojení k Průzkumníku dat Azure pomocí rozhraní ODBC
description: V tomto článku se dozvíte, jak nastavit připojení k otevřené databázi (ODBC) k Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034022"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Připojení k Průzkumníku dat Azure pomocí rozhraní ODBC

Open Database Connectivity[(ODBC)](/sql/odbc/reference/odbc-overview)je široce přijímané aplikační programovací rozhraní (API) pro přístup k databázi. Pomocí rozhraní ODBC se můžete připojit k Průzkumníku dat Azure z aplikací, které nemají vyhrazený konektor.

Na pozadí aplikace volají funkce v rozhraní ODBC, které jsou implementovány v modulech specifických pro databázi nazývaných *ovladače*. Azure Data Explorer podporuje podmnožinu komunikačního protokolu SQL Server[(MS-TDS),](/azure/kusto/api/tds/)takže může použít ovladač ODBC pro SQL Server.

Pomocí následujícího videa se můžete naučit vytvořit připojení ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Případně můžete [nakonfigurovat zdroj dat ODBC,](#configure-the-odbc-data-source) jak je podrobně popsáno níže. 

V článku se dozvíte, jak používat ovladač SQL Server ODBC, takže se můžete připojit k Průzkumníku dat Azure z libovolné aplikace, která podporuje ROZHRANÍ ODBC. 

## <a name="prerequisites"></a>Požadavky

Potřebujete následující:

* [Ovladač OdBC společnosti Microsoft pro sql server verze 17.2.0.1 nebo novější](/sql/connect/odbc/download-odbc-driver-for-sql-server) pro váš operační systém.

## <a name="configure-the-odbc-data-source"></a>Konfigurace zdroje dat ODBC

Chcete-li nakonfigurovat zdroj dat odbc pomocí ovladače ODBC pro sql server, postupujte takto.

1. Ve Windows vyhledejte *zdroje dat ODBC*a otevřete desktopovou aplikaci ODBC.

1. Vyberte **Přidat**.

    ![Přidání zdroje dat](media/connect-odbc/add-data-source.png)

1. Vyberte **ovladač ODBC 17 pro sql server** a poté **dokončete**.

    ![Vybrat ovladač](media/connect-odbc/select-driver.png)

1. Zadejte název a popis připojení a clusteru, ke kterému se chcete připojit, a vyberte **další**. Adresa URL clusteru by měla být ve formuláři * \<Název_clusteru\>.\< Oblast\>.kusto.windows.net*.

    ![Vybrat server](media/connect-odbc/select-server.png)

1. Vyberte **položku Active Directory Integrated** then **Next**.

    ![Integrovaná služba Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Vyberte databázi s ukázkovými daty a pak **Další**.

    ![Změna výchozí databáze](media/connect-odbc/change-default-database.png)

1. Na další obrazovce ponechejte všechny možnosti jako výchozí a pak vyberte **Dokončit**.

1. Vyberte **možnost Testovat zdroj dat**.

    ![Testovací zdroj dat](media/connect-odbc/test-data-source.png)

1. Ověřte, zda byl test úspěšný, a pak vyberte **možnost OK**. Pokud test nebyl úspěšný, zkontrolujte hodnoty, které jste zadali v předchozích krocích, a ujistěte se, že máte dostatečná oprávnění pro připojení ke clusteru.

    ![Test byl úspěšný.](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Další kroky

* [Připojení k Průzkumníku dat Azure z Tableau](tableau.md)