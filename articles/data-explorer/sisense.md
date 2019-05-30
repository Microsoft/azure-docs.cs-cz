---
title: Vizualizace dat v Průzkumníku dat Azure pomocí Sisense
description: V tomto článku se naučíte se nastavit Průzkumník dat Azure jako zdroj dat pro Sisense a vizualizovat data.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358180"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Vizualizace dat v Průzkumníku dat Azure v Sisense

Sisense je business intelligence platforma pro analýzu, která vám umožní sestavovat analýzy aplikací, které zajišťují vysoce interaktivní uživatelské prostředí. Business intelligence a vytváření sestav softwaru řídicí panel můžete přistupovat a kombinování dat stačí několik kliknutí. Můžete připojit k datům strukturovaných a nestrukturovaných zdrojů, spojení tabulek z více zdrojů s minimálními skriptování a psaní kódu a vytvářet interaktivní webové řídicí panely a sestavy. V tomto článku se dozvíte, jak nastavit Průzkumník dat Azure jako zdroj dat pro Sisense a vizualizace počítačových dat z ukázkové clusteru.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k dokončení tohoto článku:

* [Stáhněte a nainstalujte aplikaci Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Vytvoření clusteru a databáze, která obsahuje StormEvents ukázková data. Další informace najdete v tématu [rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze](create-cluster-database-portal.md) a [Ingestování ukázková data do Průzkumníku dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Připojte se k řídicím panelům Sisense pomocí konektoru JDBC Průzkumník dat Azure

1. Stáhněte si a zkopírujte následující soubory jar do nejnovější verze *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Otevřít **Sisense** aplikace.
1. Vyberte **Data** kartě a vyberte **+ ElastiCube** vytvořit nový model ElastiCube.
    
    ![Select ElastiCube](media/sisense/data-select-elasticube.png)

1. V **přidat nový ElastiCube Model**, název modelu ElastiCube a **Uložit**.
   
    ![Přidat nový model ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Vyberte **+ Data**.

    ![Vyberte tlačítko data](media/sisense/select-data.png)

1. V **vyberte konektor** kartu, vyberte **obecný JDBC** konektoru.

    ![Zvolte konektor JDBC](media/sisense/select-connector.png)

1. V **připojit** kartu, vyplňte následující pole pro **obecný JDBC** konektoru a vyberte **Další**.

    ![Nastavení konektoru JDBC](media/sisense/jdbc-connector.png)

    |Pole |Popis |
    |---------|---------|
    |Připojovací řetězec     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Složka JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Název třídy ovladače    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Uživatelské jméno   |    Uživatelské jméno AAD     |
    |Heslo     |   Heslo uživatele AAD      |

1. V **vybrat Data** kartu, Hledat **vybrat databázi** vyberte příslušnou databázi, ke kterému máte oprávnění. V tomto příkladu vyberte *test1*.

    ![Vyberte databázi](media/sisense/select-database.png)

1. V *testování* podokně (název databáze):
    1. Vyberte název tabulky k zobrazení náhledu tabulky a zobrazit názvy sloupců tabulky. Odstraněním nepotřebných sloupců.
    1. Zaškrtněte políčko příslušné tabulky a vyberte tuto tabulku. 
    1. Vyberte **Done** (Hotovo).

    ![Výběr tabulky](media/sisense/select-table-see-columns.png)    

1. Vyberte **sestavení** k vytvoření datové sady. 

    * V **sestavení** okně **sestavení**.

      ![Vytváření oken](media/sisense/build-window.png)

    * Počkejte, dokud proces sestavení je kompletní a pak vyberte **sestavení proběhlo úspěšně**.

      ![Sestavení proběhlo úspěšně.](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Vytváření řídicích panelů Sisense

1. V **Analytics** kartu, vyberte možnost **+**  >  **nový řídicí panel** vytváření řídicích panelů pro tuto datovou sadu.

    ![Nový řídicí panel](media/sisense/new-dashboard.png)

1. Vyberte řídicí panel a vyberte **vytvořit**. 

    ![Vytvoření řídicího panelu](media/sisense/create-dashboard.png)

1. V části **nový Widget**vyberte **+ vybrat Data** vytvořit nový widget. 

    ![Přidání pole na řídicí panel StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Vyberte **+ přidat více dat** do grafu přidat další sloupce. 

    ![Přidání dalších dat do grafu](media/sisense/add-more-data.png)

1. Vyberte **+ widgetu** vytvořit jiné pomůcce. Přetáhnout myší widgetů, chcete-li uspořádat řídicí panel.

    ![Řídicí panel Storm](media/sisense/final-dashboard.png)

Teď můžete zkoumat data pomocí analytických, vytvářet další řídicí panely a transformujte data na užitečné přehledy, aby se dopad na vaši firmu.

## <a name="next-steps"></a>Další postup

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)

