---
title: Vizualizace dat z Azure Data Exploreru pomocí Sisense
description: V tomto článku se dozvíte, jak nastavit Azure Data Explorer jako zdroj dat pro Sisense a vizualizovat data.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358180"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Vizualizace dat z Azure Data Exploreru v Sisense

Sisense je analytická platforma business intelligence, která vám umožňuje vytvářet analytické aplikace, které poskytují vysoce interaktivní uživatelské prostředí. Software business intelligence a sestav řídicího panelu umožňuje přístup k datům a jejich kombinování několika kliknutími. Můžete se připojit ke strukturovaným a nestrukturovaným zdrojům dat, spojit tabulky z více zdrojů s minimálním skriptováním a kódováním a vytvářet interaktivní webové řídicí panely a sestavy. V tomto článku se dozvíte, jak nastavit Azure Data Explorer jako zdroj dat pro Sisense a vizualizovat data z ukázkového clusteru.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* [Stažení a instalace aplikace Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Vytvořte cluster a databázi, která obsahuje ukázková data StormEvents. Další informace najdete [v tématu Úvodní příručka: Vytvoření clusteru A databáze Azure A databáze](create-cluster-database-portal.md) a [ukázková data ingestování do Průzkumníka dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Připojení k řídicím panelům Sisense pomocí konektoru JDBC aplikace Azure Data Explorer

1. Stáhněte a zkopírujte nejnovější verze následujících souborů jar do *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * aktivace-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-anotace-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Otevřete aplikaci **Sisense.**
1. Vyberte **kartu Data** a vyberte **+ElastiCube,** chcete-li vytvořit nový model ElastiCube.
    
    ![Vybrat ElastiCube](media/sisense/data-select-elasticube.png)

1. V **přidat nový model ElastiCube**pojmenujte model ElastiCube a **uložte**.
   
    ![Přidání nového modelu ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Vyberte **+ Data**.

    ![Tlačítko Vybrat data](media/sisense/select-data.png)

1. Na kartě **Vybrat konektor** vyberte obecný konektor **JDBC.**

    ![Zvolte konektor JDBC](media/sisense/select-connector.png)

1. Na kartě **Připojit** vyplňte následující pole pro obecný konektor **JDBC** a vyberte **další**.

    ![Nastavení konektoru JDBC](media/sisense/jdbc-connector.png)

    |Pole |Popis |
    |---------|---------|
    |Připojovací řetězec     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Složka JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Název třídy řidiče    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Uživatelské jméno   |    Uživatelské jméno AAD     |
    |Heslo     |   Uživatelské heslo AAD      |

1. Na kartě Vybrat data **vyberte** příslušnou databázi, ke které máte oprávnění, na kartě **Vybrat data.** V tomto příkladu vyberte *test1*.

    ![vybrat databázi](media/sisense/select-database.png)

1. V podokně *Test* (název databáze):
    1. Vyberte název tabulky, chcete-li zobrazit náhled tabulky, a podívejte se na názvy sloupců tabulky. Nepotřebné sloupce můžete odebrat.
    1. Zaškrtněte políčko příslušné tabulky a tuto tabulku vyberte. 
    1. Vyberte **Done** (Hotovo).

    ![výběr tabulky](media/sisense/select-table-see-columns.png)    

1. Vyberte **Sestavení,** chcete-li vytvořit datovou sadu. 

    * V okně **Sestavení** vyberte **Build**.

      ![Okno sestavení](media/sisense/build-window.png)

    * Počkejte na dokončení procesu sestavení a pak vyberte **možnost Sestavení proběhlo úspěšně**.

      ![Sestavení proběhlo úspěšně.](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Vytvoření řídicích panelů Sisense

1. Na kartě **Analytics** vyberte **+**  >  **Nový řídicí panel** a vytvořte řídicí panely v této datové sadě.

    ![Nový řídicí panel](media/sisense/new-dashboard.png)

1. Vyberte řídicí panel a vyberte **Vytvořit**. 

    ![Vytvořit řídicí panel](media/sisense/create-dashboard.png)

1. V části **Nový widget**vyberte **+ Vyberte data,** chcete-li vytvořit nový widget. 

    ![Přidání polí na řídicí panel StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Výběrem **možnosti + Přidat další data** přidáte do grafu další sloupce. 

    ![Přidání dalších dat do grafu](media/sisense/add-more-data.png)

1. Výběrem **možnosti + widget** vytvořte další widget. Přetažením widgetů změňte uspořádání řídicího panelu.

    ![Řídicí panel Storm](media/sisense/final-dashboard.png)

Nyní můžete data prozkoumat pomocí vizuální analýzy, vytvořit další řídicí panely a transformovat data do užitečných přehledů, abyste měli dopad na vaši firmu.

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)

