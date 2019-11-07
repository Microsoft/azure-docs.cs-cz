---
title: Vizualizace Průzkumník dat Azure s pomlčkou
description: V tomto článku se dozvíte, jak vizualizovat data v Azure Průzkumník dat pomocí přepřerušovaného nativního konektoru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588587"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Vizualizace dat z Azure Průzkumník dat v přerušování

[Pomlčky](https://www.redash.io/) se připojují k datovým zdrojům a dotazují se na ně, vytváří řídicí panely k vizualizaci dat a sdílí je s partnerskými složkami. V tomto článku se dozvíte, jak nastavit službu Azure Průzkumník dat jako zdroj dat pro přerušování a pak vizualizovat data.

## <a name="prerequisites"></a>Požadavky

1. [Vytvořte cluster a databázi](create-cluster-database-portal.md).
1. Ingestování dat, jak je vysvětleno v tématu příjem [ukázkových dat do Azure Průzkumník dat](ingest-sample-data.md) Další možnosti ingestování najdete v tématu [Přehled](ingest-data-overview.md)ingestování.

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Vytvořit konektor Azure Průzkumník dat v přerušování 

1. Přihlaste se a [přečárkujte](https://www.redash.io/)se. Vyberte **Začínáme** a vytvořte účet.
1. V části **Pojďme začít**vyberte **připojit zdroj dat**.

    ![Připojení zdroje dat](media/redash/connect-data-source.png)

1. V okně **vytvořit nový zdroj dat** vyberte **Azure Průzkumník dat (Kusto)** a pak vyberte **vytvořit**. 

    ![Vybrat zdroj dat Průzkumník dat Azure](media/redash/select-adx-data-source.png)

1. V okně **Azure Průzkumník dat (Kusto)** vyplňte následující formulář a vyberte **vytvořit**.

    ![Okno nastavení Azure Průzkumník dat (Kusto)](media/redash/adx-settings-window.png)

1. V okně **Nastavení** vyberte **Uložit** a **otestovat připojení** pro otestování připojení ke zdroji dat v **Azure Průzkumník dat (Kusto)** .

## <a name="create-queries-in-redash"></a>Vytváření dotazů v přerušování

1. V levém horním rohu přerušování vyberte **vytvořit** > **dotaz**. Klikněte na **Nový dotaz** a přejmenujte dotaz.

    ![Vytvořit dotaz](media/redash/create-query.png)

1. Zadejte dotaz v horním podokně úprav a vyberte **Uložit** a **Spustit**. Vyberte **publikovat** a publikujte dotaz pro budoucí použití.

    ![Uložit a spustit dotaz](media/redash/save-and-execute-query.png)

    V levém podokně uvidíte v rozevírací nabídce název připojení zdroje dat (**konektor GitHubu** v našem toku) a tabulky ve vybrané databázi. 

1. Zobrazení výsledků dotazu v dolním centrálním podokně. Vytvořte vizualizaci, kterou chcete použít pro dotaz, a to tak, že vyberete tlačítko **Nová vizualizace** .

    ![Nová vizualizace](media/redash/new-visualization.png)

1. Na obrazovce vizualizace vyberte **typ vizualizace** a příslušná pole, například sloupec **X** a **Y**. **Uložte** vizualizaci.

    ![Konfigurace a uložení vizualizace](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Vytvoření dotazu pomocí parametru

1. **Vytvořte** **dotaz** > pro vytvoření nového dotazu. Přidejte do něj parametr pomocí složených závorek {{}}. Vyberte **{{}}** pro otevření okna **Přidat parametr** . Můžete také vybrat *ikonu nastavení* pro úpravu atributů stávajícího parametru a otevření okna **< parameter_name >** . 

    ![vložit parametr](media/redash/insert-parameter.png)

1. Pojmenujte parametr. Vyberte **typ**: **rozevírací seznam založený na dotazu** z rozevírací nabídky. Vyberte **OK**.

    ![rozevírací seznam založený na dotazu](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Dotaz používá více hodnot, proto musíte zahrnout následující syntaxi `| where Type in ((split('{{Type}}', ',')))`. Další informace najdete v tématu [operátor in](/azure/kusto/query/inoperator). Výsledkem je [vícenásobné možnosti parametrů dotazu v aplikaci s pomlčkou](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) .

## <a name="create-a-dashboard-in-redash"></a>Vytvoření řídicího panelu v přerušování

1. Řídicí panel vytvoříte tak, že **vytvoříte** > **řídicí panel**. Případně vyberte existující řídicí panel, **řídicí panely** > v seznamu vyberte řídicí panel.

    ![Vytvořit řídicí panel](media/redash/create-dashboard.png)

1. V okně **nové řídicí panel** pojmenujte řídicí panel a vyberte **Uložit**. V okně **< Dashboard_name >** vyberte **Přidat widget** a vytvořte nový widget. 

1. V okně **Přidat widget** vyberte název dotazu, **Zvolte vizualizaci**a **parametry**. Vybrat **Přidat na řídicí panel**

   ![Výběr vizualizací a přidání na řídicí panel](media/redash/add-widget-window.png)

1. Kliknutím na **hotové úpravy** dokončíte vytváření řídicích panelů.

1.  V režimu úprav řídicího panelu vyberte **použít filtry na úrovni řídicího panelu** a použijte dříve definovaný parametr **typu** .

    ![Dokončit vytváření řídicího panelu](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)


