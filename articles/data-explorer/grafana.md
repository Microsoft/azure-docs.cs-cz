---
title: Vizualizace dat v Průzkumníku dat Azure v Grafana
description: V tomto návodu se dozvíte, jak nastavit Průzkumník dat Azure jako zdroj dat pro Grafana, a pak vizualizovat data z ukázkové clusteru.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 5a9684605de9af1cd9006810d595ae846db01661
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975155"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Vizualizace dat v Průzkumníku dat Azure v Grafana

Grafana je analytická platforma, která umožňuje dotazování a vizualizace dat, pak vytvořit a sdílet řídicí panely založené na vašich vizualizací. Grafana poskytuje Průzkumníka dat Azure *modulu plug-in*, což vám umožní připojit k a vizualizace počítačových dat z Průzkumníku dat Azure. V tomto článku se dozvíte, jak nastavit Průzkumník dat Azure jako zdroj dat pro Grafana, a pak vizualizovat data z ukázkové clusteru.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující dokončete tento postup:

* [Grafana verze 5.3.0 nebo novější](http://docs.grafana.org/installation/) pro váš operační systém

* [Průzkumník dat Azure plugin](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pro Grafana

* Clusteru, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [rychlý start: vytvoření clusteru Průzkumník dat Azure a databáze](create-cluster-database-portal.md) a [Ingestování ukázková data do Průzkumníku dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Konfigurace zdroje dat

Provedete následující kroky konfigurace Průzkumník dat Azure jako zdroj dat pro Grafana. Tyto kroky v této části podrobně probereme:

1. Vytvoření instančního objektu služby Azure Active Directory (Azure AD). Služby, které používá Grafana přístup ke službě Průzkumník dat Azure.

1. Přidání instančního objektu služby Azure AD pro *prohlížeče* role databáze Průzkumník dat Azure.

1. Zadejte vlastnosti připojení Grafana na základě informací z instančního objektu služby Azure AD a potom otestujte připojení.

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Služby můžete vytvořit instanční objekt v [webu Azure portal](#azure-portal) nebo pomocí [rozhraní příkazového řádku Azure](#azure-cli) prostředí příkazového řádku. Bez ohledu na to, jakou metodu použijete, po vytvoření, který jste získali hodnoty pro čtyři vlastnosti připojení, které budete používat v dalších krocích.

#### <a name="azure-portal"></a>portál Azure

1. Pokud chcete vytvořit instanční objekt služby, postupujte podle pokynů [dokumentace k webu Azure portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. V [přiřazení aplikace k roli](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části, přiřazení role typu **čtečky** ke svému clusteru Průzkumník dat Azure.

    1. V [získání hodnot pro přihlášení](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) tématu, zkopírujte tři vlastnosti hodnoty uvedené v krocích: **ID adresáře** (ID tenanta), **ID aplikace**, a  **Heslo**.

1. Na webu Azure Portal, vyberte **předplatná** zkopírujte ID předplatného, ve kterém vytvoříte instanční objekt služby.

    ![ID předplatného – portál](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Vytvoření instančního objektu. Nastavte příslušeného oboru a typ role `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Další informace najdete v tématu [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Příkaz vrátí výsledek nastavte takto. Zkopírujte hodnoty tři vlastnosti: **appID**, **heslo**, a **tenanta**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Získá seznam vašich předplatných.

    ```azurecli
    az account list --output table
    ```

    Zkopírujte ID odpovídající předplatné.

    ![ID předplatného – rozhraní příkazového řádku](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Přidejte instanční objekt služby k roli prohlížeče

Teď, když máte instanční objekt služby, přidáte jej do *prohlížeče* role databáze Průzkumník dat Azure. Můžete provést tuto úlohu v rámci **oprávnění** na webu Azure Portal nebo v části **dotazu** příkazem správy.

#### <a name="azure-portal---permissions"></a>Azure portal – oprávnění

1. Na webu Azure Portal přejděte ke svému clusteru Průzkumník dat Azure.

1. V **přehled** vyberte databázi s ukázkovými daty StormEvents.

    ![Vyberte databázi](media/grafana/select-database.png)

1. Vyberte **oprávnění** pak **přidat**.

    ![Oprávnění k databázi](media/grafana/database-permissions.png)

1. V části **přidat databázi oprávnění**, vyberte **prohlížeč** role potom **vyberte objekty zabezpečení**.

    ![Přidejte oprávnění k databázi](media/grafana/add-permission.png)

1. Hledání pro instanční objekt služby, které jste vytvořili (příkladu je objekt zabezpečení **mb grafana**). Vybrat objekt zabezpečení, pak **vyberte**.

    ![Spravovat oprávnění na webu Azure Portal](media/grafana/new-principals.png)

1. Vyberte **Uložit**.

    ![Spravovat oprávnění na webu Azure Portal](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Příkaz správy – dotaz

1. Na webu Azure Portal, přejděte ke svému clusteru Průzkumník dat Azure a vyberte **dotazu**.

    ![Dotaz](media/grafana/query.png)

1. V okně dotazu spusťte následující příkaz. Pomocí ID aplikace a ID tenanta z webu Azure portal nebo rozhraní příkazového řádku.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Příkaz vrátí výsledek nastavte takto. V tomto příkladu první řádek je pro stávajícího uživatele v databázi a druhý řádek je pro instanční objekt, který byl právě přidali.

    ![Sada výsledků dotazu](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Zadejte vlastnosti a otestujte připojení

S instanční objekt služby, které jsou přiřazeny *prohlížeče* roli, můžete teď určit vlastnosti ve vaší instanci služby Grafana a otestujte připojení do Průzkumníku dat Azure.

1. V Grafana, v nabídce vlevo vyberte ikonu ozubeného kola pak **zdroje dat**.

    ![Zdroje dat](media/grafana/data-sources.png)

1. Vyberte **přidat zdroj dat**.

1. Na **zdroje dat / nová** stránky, zadejte název pro zdroj dat a pak vyberte typ **Datasource Průzkumník dat Azure**.

    ![Název připojení a typ](media/grafana/connection-name-type.png)

1. Zadejte název vašeho clusteru ve formě https://{ClusterName}. {Region}. kusto.windows.net. Zadejte jiné hodnoty z webu Azure portal nebo rozhraní příkazového řádku. V tabulce níže pro mapování na následujícím obrázku.

    ![Vlastnosti připojení](media/grafana/connection-properties.png)

    | Uživatelské rozhraní služby Grafana | portál Azure | Azure CLI |
    | --- | --- | --- |
    | ID předplatného | ID PŘEDPLATNÉHO | SubscriptionId |
    | ID tenanta | ID adresáře | tenant |
    | ID klienta | ID aplikace | appId |
    | Tajný klíč klienta | Heslo | heslo |
    | | | |

1. Vyberte **uložit a otestovat**.

    Je-li test úspěšný, přejděte k další části. Pokud budete mít nějaké problémy, zkontrolujte hodnoty, které jste zadali v Grafana a přezkoumání předchozí kroky.

## <a name="visualize-data"></a>Vizualizace dat

Nyní jste dokončili konfiguraci Průzkumník dat Azure jako zdroj dat pro Grafana, je čas, která bude vizualizovat data. Ukážeme základní příklad, ale dochází k mnoha více můžete provést. Doporučujeme, abyste prohlížení [psát dotazy pro Průzkumníka služby Azure Data](write-queries.md) Příklady dalších dotazů ke spuštění ukázkové datové sadě.

1. V Grafana, v nabídce vlevo vyberte ikonu se znaménkem plus pak **řídicí panel**.

    ![Vytvoření řídicího panelu](media/grafana/create-dashboard.png)

1. V části **přidat** kartu, vyberte možnost **grafu**.

    ![Přidat graf](media/grafana/add-graph.png)

1. Na panelu grafu vyberte **nadpis panelu** pak **upravit**.

    ![Upravit panel](media/grafana/edit-panel.png)

1. V dolní části panelu, vyberte **zdroj dat** vyberte zdroj dat, který jste nakonfigurovali.

    ![Výběr zdroje dat](media/grafana/select-data-source.png)

1. V podokně dotazu, zkopírujte do ní následující dotaz, pak vyberte **spustit**. Dotaz intervalů počet událostí za den pro ukázkové datové sadě.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Spuštění dotazu](media/grafana/run-query.png)

1. Grafu nezobrazí žádné výsledky, protože je zaměřen ve výchozím nastavení data z posledních 6 hodin. V horní nabídce vyberte **posledních 6 hodin**.

    ![Posledních 6 hodin](media/grafana/last-six-hours.png)

1. Zadejte vlastní rozsah, který zahrnuje 2007, roce zahrnutého v našich StormEvents ukázkovou datovou sadu. Vyberte **Použít**.

    ![Vlastní rozsah kalendářních dat](media/grafana/custom-date-range.png)

    Graf nyní zobrazuje data z 2007, kterých rozdělit podle dne.

    ![Dokončení grafu](media/grafana/finished-graph.png)

1. V horní nabídce vyberte uložení ikony: ![Ikonu Uložit](media/grafana/save-icon.png).

## <a name="next-steps"></a>Další postup

[Psaní dotazů pro Azure Data Explorer](write-queries.md)

[Kurz: Vizualizace počítačových dat z Průzkumníka služby Azure Data v Power BI](visualize-power-bi.md)