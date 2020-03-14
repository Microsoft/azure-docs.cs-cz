---
title: Vizualizace dat z Azure Průzkumník dat pomocí Kibana
description: V tomto článku se dozvíte, jak nastavit Azure Průzkumník dat jako zdroj dat pro Kibana.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164810"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Vizualizace dat z Azure Průzkumník dat v Kibana pomocí konektoru open source K2Bridge

K2Bridge (most Kibana-Kusto) umožňuje použít Azure Průzkumník dat jako zdroj dat a vizualizovat tato data v Kibana. K2Bridge je [Open Source](https://github.com/microsoft/K2Bridge) kontejnerová aplikace, která funguje jako proxy mezi instancí Kibana a clusterem Azure Průzkumník dat. Tento článek popisuje, jak pomocí K2Bridge vytvořit toto připojení.

K2Bridge překládá dotazy Kibana na Kusto dotazovací jazyk (KQL) a odesílá výsledky Azure Průzkumník dat zpět do Kibana. 

   ![graf](media/k2bridge/k2bridge-chart.png)

K2Bridge podporuje kartu vyhledat na Kibana, kde můžete:
* Hledání a zkoumání dat
* Filtrování výsledků
* Přidání nebo odebrání polí v mřížce výsledků
* Zobrazit obsah záznamu
* Ukládání a sdílení hledání

Následující obrázek ukazuje instanci Kibana, která je vázaná na Azure Průzkumník dat pomocí K2Bridge. Činnost koncového uživatele v Kibana je beze změny.

   [Stránka ![Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Požadavky

Než budete moct vizualizovat data z Azure Průzkumník dat v Kibana, připravte si následující:

* [Helm V3](https://github.com/helm/helm#install), správce balíčků Kubernetes
* Otestujete a ověřili jste cluster Azure Kubernetes Service (AKS) nebo jakýkoli jiný cluster Kubernetes (verze 1,14 až verze 1,16). Pokud potřebujete cluster AKS, přečtěte si téma nasazení clusteru AKS [pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) nebo [použití Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Cluster Azure Průzkumník dat](create-cluster-database-portal.md), včetně:
    * Adresa URL clusteru Azure Průzkumník dat 
    * Název databáze
    
* Instanční objekt služby Azure AD autorizovaný pro zobrazení dat ve službě Azure Průzkumník dat, včetně:
    * ID klienta 
    * Tajný kód klienta

    Doporučuje se instanční objekt s oprávněním čtenář. Doporučuje se používat vyšší oprávnění.

    * [Nastavte oprávnění pro zobrazení clusteru pro instanční objekt služby Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Další informace o instančním objektu služby Azure AD najdete v tématu [Vytvoření instančního objektu služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Spuštění K2Bridge ve službě Azure Kubernetes (AKS)

Ve výchozím nastavení se v grafu Helm K2Bridges's odkazuje na veřejně dostupný obrázek umístěný na Container Registry Microsoftu (MCR). MCR nevyžaduje žádné přihlašovací údaje a funguje předem.

1. Stáhněte si požadované grafy Helm.

1. Přidejte závislost Elasticsearch do Helm. 
    Důvodem závislosti Elasticsearch je, že K2Bridge používá interní malou Elasticsearch instanci pro obsluhu požadavků souvisejících s metadaty (jako jsou vzory indexů a uložených dotazů). V této interní instanci se neukládají žádná obchodní data a je možné ji považovat za podrobnosti implementace. 

    1. Přidání závislosti Elasticsearch do Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Chcete-li získat graf K2Bridge z adresáře grafů úložiště GitHub:
        1. Naklonujte úložiště z [GitHubu](https://github.com/microsoft/K2Bridge).
        1. Přejít do kořenového adresáře úložiště K2Bridges
        1. Spuštěním příkazu

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Nasadit K2Bridge:

    1. Nastavte proměnné se správnými hodnotami pro vaše prostředí:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Volitelné Povolte telemetrii Azure Application Insights. 
        Pokud používáte Azure Application Insights poprvé, měli byste nejdřív [vytvořit prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). [Klíč instrumentace](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) budete muset zkopírovat do proměnné: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Nainstalujte graf K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        V části [Konfigurace](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) můžete najít kompletní sadu možností konfigurace.

    1. Výstup příkazu bude navrhovat další příkaz Helm, který se spustí pro nasazení Kibana. Volitelně můžete spustit:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Pro přístup k Kibana na localhost použijte předávání portů: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Pokud se chcete připojit k Kibana, přejděte na http://127.0.0.1:5601.

    1. Zveřejněte Kibana koncovým uživatelům. K tomu je potřeba více metod. Metoda, kterou použijete hlavně, závisí na vašem případu použití.

        Příklad:

        Vystavte službu jako službu pro vyrovnávání zatížení. Provedete to tak, že do instalačního příkazu K2Bridge Helm ([výše](#install-k2bridge-chart)) přidáte následující parametr:

        `--set service.type=LoadBalancer`
    
        Potom následujícím příkazem:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        Výstup by měl vypadat takto: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Pak můžete použít vygenerovanou externí IP adresu a použít ji k přístupu k Kibana otevřením prohlížeče na: `\<EXTERNAL-IP>:5601`.

1. Nakonfigurujte vzory indexu pro přístup k datům:  
V nové instanci Kibana:
     1. Otevřete Kibana.
     1. Přejděte ke správě.
     1. Vyberte **vzory indexu**. 
     1. Vytvořte vzor indexu.
Název indexu musí přesně odpovídat názvu tabulky nebo názvu funkce bez hvězdičky. Příslušný řádek můžete zkopírovat ze seznamu.

> [!Note]
> Pokud chcete spustit na jiných poskytovatelích Kubernetes, změňte Elasticsearch storageClassName v `values.yaml` tak, aby odpovídala vašemu doporučení pro poskytovatele.

## <a name="visualize-data"></a>Vizualizace dat

Když je Azure Průzkumník dat nakonfigurovaný jako zdroj dat pro Kibana, můžete k prozkoumávání dat použít Kibana. 

1. V Kibana nabídce vlevo vyberte kartu **Vyhledat** .

1. V rozevíracím seznamu vlevo vyberte vzor indexu (v tomto případě tabulku Azure Průzkumník dat), která definuje zdroj dat, který chcete prozkoumat.
    
   ![Vybrat vzor indexu](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Pokud vaše data obsahují pole s časovým filtrem, můžete zadat časový rozsah. V pravém horním rohu stránky nastavte filtr času. Ve výchozím nastavení zjišťování zobrazuje data za posledních 15 minut.

   ![Filtr času](media/k2bridge/k2bridge-time-filter.png)
    
1. Tabulka Results zobrazuje prvních 500 záznamů. Můžete rozbalit dokument a prohlédnout si jeho data pole ve formátu JSON nebo tabulka.

   ![Rozbalení záznamu](media/k2bridge/k2bridge-expand-record.png)

1. Ve výchozím nastavení obsahuje tabulka výsledků sloupce pro _source dokumentu a pole čas (pokud existuje). Výběrem možnosti **Přidat** vedle názvu pole na levém bočním panelu můžete vybrat konkrétní sloupce, které chcete přidat do tabulky výsledků.

   ![Konkrétní sloupce](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na panelu dotazů můžete vyhledat data podle:
    * Zadání hledaného termínu
    * Pomocí syntaxe dotazů Lucene. 
    Příklad:
        * Pokud chcete najít všechny záznamy, které obsahují tuto hodnotu, vyhledejte "Chyba". 
        * Vyhledejte "status: 200", chcete-li získat všechny záznamy s hodnotou stavu 200. 
    * Použití logických operátorů (a)
    * Použití zástupných znaků (hvězdička "\*" nebo "otazník"? ") Například:
        * Dotaz `"destination_city: L*"` bude odpovídat záznamům, kde hodnota cílového města začíná řetězcem "l" (K2Bridge nerozlišuje velká a malá písmena).

    ![Spuštění dotazu](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > Při [hledání](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)můžete najít další pravidla vyhledávání a logiku.

1. Pokud chcete filtrovat výsledky hledání, použijte **seznam polí** na pravém bočním panelu stránky. 
    Seznam polí je místo, kde vidíte:
    * Horních pět hodnot pro pole
    * Počet záznamů, které obsahují pole
    * Procento záznamů, které obsahují jednotlivé hodnoty. 
    
    >[!Tip]
    > Použijte ikonu lupy (+) k vyhledání všech záznamů, které mají konkrétní hodnotu.
    
    ![Seznam polí](media/k2bridge/k2bridge-field-list.png)
   
    Výsledky můžete filtrovat také pomocí ikony lupy (+) v zobrazení formát tabulky výsledků každého záznamu v tabulce výsledků.
    
     ![Seznam tabulek](media/k2bridge/k2bridge-table-list.png)
    
1. Chcete-li hledání **Uložit** nebo **sdílet** , vyberte možnost.

     ![Uložit hledání](media/k2bridge/k2bridge-save-search.png)
