---
title: Vizualizace Průzkumníka dat Azure pomocí funkce Redash
description: V tomto článku se dozvíte, jak vizualizovat data v Průzkumníku dat Azure pomocí nativního konektoru Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773959"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Vizualizace dat z Průzkumníka dat Azure v redashu

[Redash](https://redash.io/) propojuje a dotazuje vaše zdroje dat, vytváří řídicí panely pro vizualizaci dat a jejich sdílení s kolegy. V tomto článku se dozvíte, jak nastavit Azure Data Explorer jako zdroj dat pro Redash a pak vizualizovat data.

## <a name="prerequisites"></a>Požadavky

1. [Vytvořte cluster a databázi](create-cluster-database-portal.md).
1. Ingestujte data, jak je vysvětleno v [ingestování ukázkových dat do Průzkumníka dat Azure](ingest-sample-data.md). Další možnosti požití naleznete v [tématu přehled požití](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Vytvoření konektoru Průzkumníka dat Azure v redashu 

1. Přihlaste se k [Redash](https://www.redash.io/). Chcete-li vytvořit účet, vyberte **Možnost Začínáme.**
1. V části **Začínáme**vyberte **Připojit zdroj dat**.

    ![Připojit zdroj dat](media/redash/connect-data-source.png)

1. V **okně Vytvořit nový zdroj dat** vyberte Průzkumník dat Azure **(Kusto) a**pak vyberte **Vytvořit**. 

    ![Výběr zdroje dat Průzkumníka dat Azure](media/redash/select-adx-data-source.png)

1. V okně **Průzkumníka dat Azure (Kusto)** vyplňte následující formulář a vyberte **Vytvořit**.

    ![Okno nastavení Průzkumníka dat Azure (Kusto)](media/redash/adx-settings-window.png)

1. V okně **Nastavení** vyberte **Uložit** a **otestovat připojení** k testování připojení zdroje dat Aplikace **Azure (Kusto).**

## <a name="create-queries-in-redash"></a>Vytváření dotazů v pochodce

1. Vlevo nahoře na Redashu vyberte **Vytvořit** > **dotaz**. Klikněte na **Nový dotaz** a přejmenujte dotaz.

    ![Vytvořit dotaz](media/redash/create-query.png)

1. Zadejte dotaz do horního podokna úprav a vyberte **Uložit** a **spustit**. Vyberte **Publikovat,** chcete-li publikovat dotaz pro budoucí použití.

    ![Uložení a spuštění dotazu](media/redash/save-and-execute-query.png)

    V levém podokně uvidíte název připojení zdroje dat **(konektor Github** v našem toku) v rozevírací nabídce a tabulky ve vybrané databázi. 

1. Zobrazení výsledků dotazu v dolním centrálním podokně Vytvořte vizualizaci, která se bude s dotazem přikládat, a to tak, že vyberete tlačítko **Nová vizualizace.**

    ![Nová vizualizace](media/redash/new-visualization.png)

1. Na obrazovce vizualizace vyberte **typ vizualizace** a příslušná pole, například **Sloupec X** a **Sloupec Y**. **Uložte** vizualizaci.

    ![Konfigurace a uložení vizualizace](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Vytvoření dotazu pomocí parametru

1. **Vytvořte** > **dotaz** a vytvořte nový dotaz. Přidejte parametr pomocí{}{ } složených závorek. Výběrem **možnosti {{}}** otevřete okno Přidat **parametr.** Můžete také vybrat *ikonu nastavení* a upravit atributy existujícího parametru a otevřít okno<parameter_name **>.** 

    ![vložit parametr](media/redash/insert-parameter.png)

1. Pojmenujte svůj parametr. V rozevírací nabídce vyberte **Typ**: **Rozevírací seznam založený na dotazu.** Vybrat **OK**

    ![rozevírací seznam založený na dotazech](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Dotaz používá více hodnot, proto je `| where Type in ((split('{{Type}}', ',')))`nutné zahrnout následující syntaxi . Další informace naleznete [v tématu operátor](/azure/kusto/query/inoperator). Výsledkem [je více možností parametrů dotazu v aplikaci redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Vytvoření řídicího panelu v redashu

1. Chcete-li vytvořit řídicí panel, **vytvořte** > **řídicí panel**. Případně vyberte existující řídicí panel, **řídicí panely** > vyberte řídicí panel ze seznamu.

    ![Vytvořit řídicí panel](media/redash/create-dashboard.png)

1. V okně **Nový řídicí panel** pojmenujte řídicí panel a vyberte **Uložit**. V **okně<Dashboard_name>** vyberte Přidat **widget,** abyste vytvořili nový widget. 

1. V okně **Přidat widget** vyberte název dotazu, **Zvolte vizualizaci**a **Parametry**. Vybrat **přidat na řídicí panel**

   ![Výběr vizualizací a přidání na řídicí panel](media/redash/add-widget-window.png)

1. Chcete-li dokončit vytvoření řídicího panelu, vyberte **Hotovo úpravy.**

1.  V režimu úprav řídicího panelu vyberte **Použít filtry úrovně řídicího panelu,** chcete-li použít dříve definovaný parametr **Typ.**

    ![Kompletní vytvoření řídicího panelu](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)


