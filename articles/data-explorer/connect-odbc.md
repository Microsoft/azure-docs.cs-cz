---
title: Připojení k Azure Průzkumník dat pomocí rozhraní ODBC
description: V tomto článku se dozvíte, jak nastavit připojení rozhraní ODBC (Open Database Connectivity) k Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034022"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Připojení k Azure Průzkumník dat pomocí rozhraní ODBC

Rozhraní[ODBC](/sql/odbc/reference/odbc-overview)(Open Database Connectivity) představuje široce přijímané rozhraní API (Application Programming Interface) pro přístup k databázi. Pomocí rozhraní ODBC se můžete připojit k Azure Průzkumník dat z aplikací, které nemají vyhrazený konektor.

Na pozadí aplikace volají funkce v rozhraní ODBC, které jsou implementovány v modulech specifických pro databázi s názvem *ovladače*. Azure Průzkumník dat podporuje podmnožinu protokolu[MS-TDS](/azure/kusto/api/tds/)(SQL Server Communications Protocol), aby mohl používat ovladač ODBC pro SQL Server.

Pomocí následujícího videa se naučíte vytvořit připojení ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Případně můžete [nakonfigurovat zdroj dat rozhraní ODBC](#configure-the-odbc-data-source) , jak je popsáno níže. 

V článku se dozvíte, jak používat SQL Server ovladač ODBC, abyste se mohli připojit k Azure Průzkumník dat z jakékoli aplikace podporující rozhraní ODBC. 

## <a name="prerequisites"></a>Požadavky

Potřebujete následující:

* [Ovladač Microsoft ODBC pro SQL Server verze 17.2.0.1 nebo novější](/sql/connect/odbc/download-odbc-driver-for-sql-server) pro váš operační systém.

## <a name="configure-the-odbc-data-source"></a>Konfigurace zdroje dat ODBC

Postupujte podle těchto kroků a nakonfigurujte zdroj dat rozhraní ODBC pomocí ovladače ODBC pro SQL Server.

1. Ve Windows vyhledejte *zdroje dat ODBC*a otevřete desktopové aplikace ODBC zdroje dat.

1. Vyberte **Přidat**.

    ![Přidání zdroje dat](media/connect-odbc/add-data-source.png)

1. Vyberte **pro SQL Server ovladač ODBC Driver 17** a pak **dokončete**.

    ![Vybrat ovladač](media/connect-odbc/select-driver.png)

1. Zadejte název a popis připojení a clusteru, ke kterému se chcete připojit, a pak vyberte **Další**. Adresa URL clusteru by měla být ve tvaru *\<název_clusteru\>.\<Region\>. kusto.Windows.NET*.

    ![Vybrat server](media/connect-odbc/select-server.png)

1. Vyberte **Active Directory integrováno** a pak **Další**.

    ![Integrovaná služba Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Vyberte databázi s ukázkovými daty a pak klikněte na **Další**.

    ![Změnit výchozí databázi](media/connect-odbc/change-default-database.png)

1. Na další obrazovce ponechte všechny možnosti jako výchozí a pak vyberte **Dokončit**.

1. Vyberte **zdroj testovacích dat**.

    ![Zdroj testovacích dat](media/connect-odbc/test-data-source.png)

1. Ověřte, že test proběhl úspěšně, a pak vyberte **OK**. Pokud test neproběhl úspěšně, zkontrolujte hodnoty, které jste zadali v předchozích krocích, a ujistěte se, že máte dostatečná oprávnění pro připojení ke clusteru.

    ![Test byl úspěšný](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Další kroky

* [Připojení k Azure Průzkumník dat z Tableau](tableau.md)