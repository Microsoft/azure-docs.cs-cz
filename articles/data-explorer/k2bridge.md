---
title: Vizualizace dat z Průzkumníka dat Azure pomocí Kibany
description: V tomto článku se dozvíte, jak nastavit Azure Data Explorer jako zdroj dat pro Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065615"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Vizualizace dat z Průzkumníka dat Azure v Kibaně pomocí open source konektoru K2Bridge

K2Bridge (Kibana-Kusto Bridge) umožňuje používat Azure Data Explorer jako zdroj dat a vizualizovat tato data v Kibana. K2Bridge je [kontejnerizovaná aplikace s otevřeným zdrojovým kódem,](https://github.com/microsoft/K2Bridge) která funguje jako proxy mezi instancí Kibana a clusterem Průzkumníka dat Azure. Tento článek popisuje, jak pomocí K2Bridge vytvořit toto připojení.

K2Bridge překládá dotazy Kibana do kusto dotazovacího jazyka (KQL) a odešle výsledky Průzkumníka dat Azure zpět do Kibany. 

   ![graf](media/k2bridge/k2bridge-chart.png)

K2Bridge podporuje kartu Discover společnosti Kibana, kde můžete:
* Vyhledávání a zkoumání dat
* Filtrování výsledků
* Přidání nebo odebrání polí v mřížce výsledků
* Zobrazit obsah záznamu
* Uložení a sdílení vyhledávání

Na obrázku níže je zobrazena instance Kibana vázaná na Azure Data Explorer pomocí K2Bridge. Uživatelské prostředí v Kibaně se nemění.

   [![Stránka Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Požadavky

Než budete moci vizualizovat data z Průzkumníka dat Azure v Kibaně, připravte si následující:

* [Helm V3](https://github.com/helm/helm#install), správce balíčků Kubernetes
* Cluster Azure Kubernetes Service (AKS) nebo jakýkoli jiný cluster Kubernetes (verze 1.14 až verze 1.16 byly testovány a ověřeny). Pokud potřebujete cluster AKS, přečtěte si informace o nasazení clusteru AKS [pomocí azure cli](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) nebo [pomocí portálu Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Cluster Průzkumníka dat Azure](create-cluster-database-portal.md), včetně:
    * Adresa URL clusteru Průzkumníka dat Azure 
    * Název databáze
    
* Instanční objekt Azure AD oprávněný k zobrazení dat v Průzkumníku dat Azure, včetně:
    * ID klienta 
    * Tajný klíč klienta

    Je doporučen instanční objekt s oprávněním Prohlížeč. Je nedoporučuje používat vyšší oprávnění.

    * [Nastavte oprávnění zobrazení clusteru pro instanční objekt služby Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Další informace o instančním objektu služby Azure AD najdete v [tématu Vytvoření instančního objektu služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Spuštění služby K2Bridge ve službě Azure Kubernetes (AKS)

Ve výchozím nastavení graf Helm společnosti K2Bridges odkazuje na veřejně dostupnou bitovou kopii umístěnou v registru kontejnerů společnosti Microsoft (MCR). MCR nevyžaduje žádné přihlašovací údaje a funguje out-of-the-box.

1. Stáhněte si požadované grafy Helm.

1. Přidejte závislost Elasticsearch do helmu. 
    Důvodem pro elasticsearch závislost je, že K2Bridge používá vnitřní malé Elasticsearch instance pro služby požadavky související s metadaty (například index vzory a uložené dotazy). V této interní instanci nejsou uložena žádná obchodní data a lze je považovat za podrobnosti implementace. 

    1. Přidání závislosti Elasticsearch do helmy:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Jak získat graf K2Bridge z adresáře grafů v úložišti GitHub:
        1. Klonovat úložiště z [GitHubu](https://github.com/microsoft/K2Bridge).
        1. Přejděte do adresáře kořenového úložiště K2Bridges.
        1. Spuštěním příkazu

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Nasazení K2Bridge:

    1. Nastavte proměnné se správnými hodnotami pro vaše prostředí:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Nepovinné) Povolte telemetrii Azure Application Insights. 
        Pokud používáte Azure Application Insights poprvé, měli byste nejprve [vytvořit prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Klíč [instrumentace](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) bude nutné zkopírovat do proměnné: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Nainstalujte graf K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        V [konfiguraci](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) najdete kompletní sadu možností konfigurace.

    1. Výstup příkazu navrhne další příkaz Helmu ke spuštění k nasazení Kibany. Volitelně spustit:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Použití předávání portů pro přístup ke kibana na localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Připojte se ke Kibaně procházením aplikace http://127.0.0.1:5601.

    1. Vystavit Kibana koncovým uživatelům. Existuje několik metod, jak to udělat. Metoda, kterou používáte, závisí do značné míry na případu použití.

        Například:

        Vystavit službu jako službu LoadBalancer. Chcete-li tak `--set service.type=LoadBalancer` učinit, přidejte parametr do příkazu instalace kormidelníka K2Bridge ([výše](#install-k2bridge-chart)).        
    
        Potom následujícím příkazem:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        Výstup by měl vypadat takto: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Potom můžete použít vygenerovanou externí IP adresu, která se zobrazí, a `<EXTERNAL-IP>:5601`použít ji k přístupu ke kibaně otevřením prohlížeče do aplikace .

1. Konfigurace vzorů rejstříku pro přístup k datům:  
V nové instanci Kibana:
     1. Otevřete Kibanu.
     1. Přejděte do managementu.
     1. Vyberte **indexové vzorky**. 
     1. Vytvořte vzorek indexu.
Název indexu se musí přesně shodovat s názvem tabulky nebo název funkce bez hvězdičky. Příslušný řádek můžete zkopírovat ze seznamu.

> [!Note]
> Chcete-li spustit na jiných zprostředkovatelů Kubernetes, `values.yaml` změňte Elasticsearch storageClassName v tak, aby se vešly ten navržený zprostředkovatelem.

## <a name="visualize-data"></a>Vizualizace dat

Když je Azure Data Explorer nakonfigurovaný jako zdroj dat pro Kibana, můžete použít Kibana k prozkoumání dat. 

1. V Kibaně v yberte v levé nabídce na kartu **Objevit.**

1. V levém rozevíracím seznamu vyberte vzorek indexu (v tomto případě tabulku Průzkumníka dat Azure), která definuje zdroj dat, který chcete prozkoumat.
    
   ![Výběr vzorku rejstříku](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Pokud data mají pole časového filtru, můžete určit časový rozsah. V pravém horním bodě stránky nastavte časový filtr. Ve výchozím nastavení funkce Discover zobrazuje data za posledních 15 minut.

   ![Časový filtr](media/k2bridge/k2bridge-time-filter.png)
    
1. V tabulce výsledků jsou uvedeny prvních 500 záznamů. Dokument můžete rozbalit a prozkoumat jeho data pole ve formátu JSON nebo tabulky.

   ![Rozšíření záznamu](media/k2bridge/k2bridge-expand-record.png)

1. Ve výchozím nastavení obsahuje tabulka výsledků sloupce pro dokument _source a časové pole (pokud existuje). Konkrétní sloupce, které chcete přidat do tabulky výsledků, můžete vybrat tak, že vyberete **přidat** vedle názvu pole v levém postranním panelu.

   ![Konkrétní sloupce](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na panelu dotazů můžete data prohledávat podle:
    * Zadání hledaného výrazu
    * Pomocí syntaxe dotazu Lucene. 
    Například:
        * Vyhledejte všechny záznamy, které tuto hodnotu obsahují, hledáním chyby. 
        * Vyhledejte "stav: 200", chcete-li získat všechny záznamy s hodnotou stavu 200. 
    * Použití logických operátorů (AND, OR, NOT)
    * Použití zástupných znaků (hvězdička " \* " nebo otazník "?") Například:
        * Dotaz `"destination_city: L*"` bude odpovídat záznamům, kde hodnota cílového města začíná na "l" (K2Bridge nerozlišuje malá a velká písmena).

    ![Spuštění dotazu](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > V [hledání](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)najdete další pravidla vyhledávání a logiku.

1. Chcete-li filtrovat výsledky hledání, použijte **seznam polí** na pravém postranním panelu stránky. 
    Seznam polí je místo, kde můžete vidět:
    * Pět nejvyšších hodnot pro pole
    * Počet záznamů obsahujících pole
    * Procento záznamů, které obsahují každou hodnotu. 
    
    >[!Tip]
    > Pomocí ikony lupy slouží k vyhledání všech záznamů, které mají určitou hodnotu.
    
    ![Seznam polí](media/k2bridge/k2bridge-field-list.png)
   
    Výsledky můžete také filtrovat pomocí ikony lupy (+) v zobrazení formátu tabulky výsledků každého záznamu v tabulce výsledků.
    
     ![Seznam tabulek](media/k2bridge/k2bridge-table-list.png)
    
1. Vyberte možnost **Uložit** nebo **Sdílet** hledání.

     ![Uložit hledání](media/k2bridge/k2bridge-save-search.png)
