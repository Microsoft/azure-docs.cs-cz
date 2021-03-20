---
title: 'Kurz: Začínáme s Azure synapse Analytics – vizualizace dat pracovního prostoru pomocí Power BI'
description: V tomto kurzu se naučíte, jak vytvořit pracovní prostor Power BI, propojíte svůj pracovní prostor Azure synapse a vytvoříte Power BI datovou sadu, která využívá data v pracovním prostoru Azure synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98208960"
---
# <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI

V tomto kurzu se naučíte, jak vytvořit pracovní prostor Power BI, propojíte svůj pracovní prostor Azure synapse a vytvoříte Power BI datovou sadu, která využívá data v pracovním prostoru Azure synapse. 

> [!NOTE]
> K dokončení tohoto kurzu [nainstalujte Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="overview"></a>Přehled

Z dat NYC taxislužby jsme vytvořili agregované datové sady ve dvou tabulkách:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Pracovní prostor Power BI můžete propojit s pracovním prostorem Azure synapse. Tato funkce umožňuje snadno získat data do svého pracovního prostoru Power BI. Sestavy Power BI můžete upravovat přímo v pracovním prostoru Azure synapse. 

### <a name="create-a-power-bi-workspace"></a>Vytvoření pracovního prostoru Power BI

1. Přihlaste se k [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Klikněte na **pracovní prostory** a pak vyberte **vytvořit pracovní prostor**. Vytvoří nový pracovní prostor Power BI s názvem **NYCTaxiWorkspace1** nebo podobný, protože tento název musí být jedinečný.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Propojení pracovního prostoru Azure synapse s novým pracovním prostorem Power BI

1. V synapse studiu si přečtěte, jak **Spravovat**  >  **propojené služby**.
1. Vyberte **Nový**  >  **připojit k Power BI**.
1. Nastavte **název** na **NYCTaxiWorkspace1**.
1. Nastavte **název pracovního prostoru** na pracovní prostor Power BI, který jste vytvořili výše, podobně jako **NYCTaxiWorkspace1**.
1. Vyberte **Vytvořit**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Vytvoření datové sady Power BI, která používá data v pracovním prostoru Azure synapse

1. V synapse studiu si Projděte **vývoj**  >  **Power BI**.
1. Přejít na **NYCTaxiWorkspace1**  >  **Power BI datových sad** a vyberte **Nový Power BI datová sada**. Klikněte na tlačítko **Start**.
1. Vyberte zdroj dat **SQLPOOL1** , klikněte na **pokračovat**.
1. Kliknutím na **Stáhnout** Stáhněte soubor. pbids pro soubor **NYCTaxiWorkspace1SQLPOOL1. pbids** . Klikněte na **Pokračovat**.
1. Otevřete stažený soubor **. pbids** . Power BI Desktop se otevře a automaticky se připojí k **SQLDB1** v pracovním prostoru Azure synapse.
1. Pokud se zobrazí dialogové okno s názvem **SQL Server Database**:
    1. Vyberte **účet Microsoft**.
    1. Vyberte **Přihlásit** se a přihlaste se ke svému účtu.
    1. Vyberte **Connect** (Připojit).
1. Po otevření dialogového okna **navigátor** ověřte tabulku **PassengerCountStats** a vyberte **načíst**.
1. Po zobrazení dialogového okna **nastavení připojení** vyberte **DirectQuery**  >  **OK**.
1. Na levé straně vyberte tlačítko **Sestava** .
1. V části **vizualizace** klikněte na ikonu spojnicového grafu a přidejte do sestavy **Spojnicový graf** .
    1. V části **pole** přetáhněte sloupec **PassengerCount** na osu **vizualizace**  >  .
    1. Přetáhněte sloupce **SumTripDistance** a **AvgTripDistance** na hodnoty **vizualizace**  >  .
1. Na kartě **Domů** vyberte **publikovat**.
1. Vyberte **Uložit** a uložte tak provedené změny.
1. Zvolte název souboru **PassengerAnalysis. pbix** a pak vyberte Save ( **Uložit**).
1. V okně **publikovat do Power BI** v části **vybrat cíl** zvolte **NYCTaxiWorkspace1** a potom klikněte na **Vybrat**.
1. Počkejte na dokončení publikování. 

### <a name="configure-authentication-for-your-dataset"></a>Konfigurace ověřování pro datovou sadu

1. Otevřete [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) a **Přihlaste se**.
1. Na levé straně v části **pracovní prostory** vyberte pracovní prostor **NYCTaxiWorkspace1** .
1. V tomto pracovním prostoru Najděte datovou sadu nazvanou **Analýza osobního** prostředí a sestavu s názvem **Analýza osobních** dat.
1. Najeďte myší na **PassengerAnalysis** datovou sadu, vyberte tlačítko se třemi tečkami (...) a pak vyberte **Nastavení**.
1. V části **přihlašovací údaje ke zdroji dat** klikněte na **Upravit**, nastavte **metodu ověřování** na **OAuth2** a pak vyberte **Přihlásit** se.

### <a name="edit-a-report-in-synapse-studio"></a>Úprava sestavy v synapse studiu

1. Vraťte se na synapse Studio a vyberte **Zavřít a aktualizovat**.
1. Přejít do centra pro **vývoj** .
1. Napravo od **Power BI** vrstvy, tlačítko se třemi tečkami (...) a kliknutím na **aktualizovat** aktualizujte uzel **Power BI sestavy** .
1. V části **Power BI** byste měli vidět:
    * V **NYCTaxiWorkspace1**  >  **Power BI datové sady** se vytvoří nová datová sada s názvem **PassengerAnalysis**.
    * V části **NYCTaxiWorkspace1**  >  **Power BI sestavy** se zobrazí nová sestava s názvem **PassengerAnalysis**.
1. Vyberte sestavu **PassengerAnalysis** . Sestava se otevře a můžete ji přímo v synapse studiu upravovat.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování](get-started-monitor.md)
                                 

