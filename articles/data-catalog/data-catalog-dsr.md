---
title: Podporované zdroje dat ve službě Azure Data Catalog
description: Tento článek uvádí specifikace zdrojů dat aktuálně podporované.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 0f7ee285e77b9e54e2ff720f10f0d24c5b764014
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042935"
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Podporované zdroje dat ve službě Azure Data Catalog

Metadata můžete publikovat pomocí veřejné rozhraní API nebo kliknutím-jednou registrační nástroj nebo informace o ručním zadáním přímo do služby Azure Data Catalog webového portálu. V následující tabulce najdete souhrn všech zdrojů dat, které jsou podporovány v katalogu ještě dnes a možnosti publikování pro každou. Externí data nástroje, které každý zdroj dat můžete spustit z našich zkušeností portál "open in" jsou také uvedeny. Druhá tabulka obsahuje další technické specifikace každé připojení vlastnosti zdroje dat.


## <a name="list-of-supported-data-sources"></a>Seznam podporovaných zdrojů dat

<table>
    <tr>
       <td><b>Objekt zdroje dat</b></td>
       <td><b>Rozhraní API</b></td>
       <td><b>Ruční zadání</b></td>
       <td><b>Nástroj pro registraci</b></td>
       <td><b>Otevřít v nástroji</b></td>
       <td><b>Poznámky</b></td>
    </tr>
    <tr>
      <td>Adresář Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Soubor Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Azure Blob Storage</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Adresář služby Azure Storage</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Azure Storage table</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2">
      </td>
      <td>
        <font size="2">
      </td>
    </tr>
    <tr>
      <td>Adresář HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Soubor HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka databáze Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení databáze Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Ostatní (obecný prostředek)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulky Azure SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Nástroje aplikace Excel, Power BI Desktopu, SQL Server data tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Nástroje aplikace Excel, Power BI Desktopu, SQL Server data tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Dimenze služby SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services klíčového ukazatele výkonu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Míra SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Aplikace Excel, Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Sestavy služby SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Prohlížeč</td>
      <td>Pouze servery v nativním režimu. Režim serveru SharePoint není podporován.</td>
    </tr>
    <tr>
      <td>Tabulka SQL Serveru</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Nástroje aplikace Excel, Power BI Desktopu, SQL Server data tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení systému SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Nástroje aplikace Excel, Power BI Desktopu, SQL Server data tools</td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení SAP HANA</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktopu</td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulky DB2.</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení DB2</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Soubor system</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Adresář serveru FTP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Soubor protokolu FTP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Sestavy HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Koncový bod HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Soubor protokolu HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Sada entit OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Funkci prostředí OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka PostgreSQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení PostgreSQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení SAP HANA</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td> Objektu Salesforce</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Seznam služby SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Kolekce Azure Cosmos DB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Obecná rozhraní ODBC tabulky</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Obecná rozhraní ODBC zobrazení</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka Cassandra</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Publikovat jako obecný prostředek ODBC</td>
    </tr>
    <tr>
      <td>Zobrazení Cassandra</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Publikovat jako obecný prostředek ODBC</td>
    </tr>
    <tr>
      <td>Tabulka Sybase</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Zobrazení Sybase</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Tabulka MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Publikovat jako obecný prostředek ODBC</td>
    </tr>
    <tr>
      <td>Zobrazení MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Publikovat jako obecný prostředek ODBC</td>
    </tr>
</table>

Pokud se chcete podívat na konkrétnímu zdroji dat nepodporuje, navrhněte ji (nebo hlasové vaši podporu, pokud již byla navržena) tak, že přejdete [zpětnou vazbu fóra Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).


## <a name="data-source-reference-specification"></a>Specifikace odkazu na zdroj dat
> [!NOTE]
> **DSL struktura** sloupce v následující tabulce jsou uvedeny pouze vlastnosti připojení pro "address" kontejner objektů a dat, které používají Azure Data Catalog. To znamená "address" kontejner objektů a dat může obsahovat jiné vlastnosti připojení zdroje dat, která přetrvává Azure Data Catalog, ale nepoužívá.

<table>
    <tr>
       <td><b>Typ zdroje</b></td>
       <td><b>Typ prostředku</b></td>
       <td><b>Typy objektů</b></td>
       <td><b>Struktura DSL<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Kontejner</td>
      <td>Data Lake</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Blob, directory</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Container</td>
      <td>Virtual cluster</td>
      <td>
        
            Protocol: cosmos
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Table</td>
      <td>Stream, stream set, view</td>
      <td>
        
            Protocol: cosmos
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>File system</td>
      <td>Table</td>
      <td>File</td>
      <td>
        
            Protocol: file
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
        
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: ftp
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Container</td>
      <td>Cluster</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Table</td>
      <td>Endpoint, file</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Container</td>
      <td>Entity container</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Table</td>
      <td>Entity set, function</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Data mashup</td>
      <td>
        
            Protocol: power-query
            <br>Authentication: {oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Table</td>
      <td>Object</td>
      <td>
        
            Protocol: salesforce-com
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Table</td>
      <td>View</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>List</td>
      <td>
        
            Protocol: sharepoint-list
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
          <br>Authentication: {protocol, windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entity</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Collection</td>
      <td>Collection</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; collection
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Other (none of the above)</td>
      <td>&#10033;</td>
      <td>&#10033;</td>
      <td>
        
            Protocol: generic-asset
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId
        
      </td>
    </tr>
</table>
