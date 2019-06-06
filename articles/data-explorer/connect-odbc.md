---
title: Připojit do Průzkumníku dat Azure s rozhraním ODBC
description: V tomto článku se dozvíte, jak nastavit připojení k připojení ODBC (Open Database) do Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494852"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Připojit do Průzkumníku dat Azure s rozhraním ODBC

Otevřete připojení k databázi ([ODBC](/sql/odbc/reference/odbc-overview)) je široce přijaté aplikačního programovacího rozhraní (API) pro přístup k databázi. Použití rozhraní ODBC pro připojení do Průzkumníku dat Azure z aplikací, které nemají vyhrazené konektoru.

Na pozadí aplikace volat funkce v rozhraní ODBC, které jsou implementovány v modulů specifické pro databázi s názvem *ovladače*. Průzkumník služby Azure Data podporuje podmnožinu komunikační protokol serveru SQL Server ([MS-TDS](/azure/kusto/api/tds/)), takže ho můžete použít ovladač ODBC pro SQL Server.

V tomto článku se dozvíte, jak použít ovladač ODBC systému SQL Server, abyste se mohli připojit do Průzkumníku dat Azure z libovolné aplikace, která podporuje rozhraní ODBC.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující:

* [Ovladač Microsoft ODBC pro SQL Server verze 17.2.0.1 nebo novější](/sql/connect/odbc/download-odbc-driver-for-sql-server) pro váš operační systém.

## <a name="configure-the-odbc-data-source"></a>Konfigurace zdroje dat ODBC

Postupujte podle těchto kroků a nakonfigurujte pomocí ovladače ODBC pro SQL Server zdroje dat ODBC.

1. Ve Windows, vyhledejte *zdroje dat ODBC*a otevřete aplikaci klasické pracovní plochy zdroje dat ODBC.

1. Vyberte **Přidat**.

    ![Přidání zdroje dat](media/connect-odbc/add-data-source.png)

1. Vyberte **ovladač ODBC Driver 17 pro SQL Server** pak **Dokončit**.

    ![Vyberte ovladač](media/connect-odbc/select-driver.png)

1. Zadejte název a popis pro připojení a clusteru, kterou chcete připojit, vyberte **Další**. Adresa URL by měla být ve tvaru clusteru  *\<ClusterName\>.\< Oblast\>. kusto.windows.net*.

    ![Výběr serveru](media/connect-odbc/select-server.png)

1. Vyberte **integrace služby Active Directory** pak **Další**.

    ![Integrované se službou Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Potom vyberte databázi s ukázkovými daty **Další**.

    ![Změnit výchozí databázi](media/connect-odbc/change-default-database.png)

1. Na další obrazovce všech možností ponechte jako výchozí vyberte **Dokončit**.

1. Vyberte **zdroj dat otestovat**.

    ![Zdroj testovacích dat.](media/connect-odbc/test-data-source.png)

1. Ověřte, že test úspěšně vyberte **OK**. Pokud test nebylo úspěšné, zkontrolujte hodnoty, které jste zadali v předchozích krocích a ujistěte se, že máte dostatečná oprávnění k připojení ke clusteru.

    ![Test byla úspěšná](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Další postup

* [Připojení z Tableau do Průzkumníku dat Azure](tableau.md)