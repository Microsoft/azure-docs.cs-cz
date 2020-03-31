---
title: Vizualizace protokolů toku nsg - elastický zásobník
titleSuffix: Azure Network Watcher
description: Spravujte a analyzujte protokoly toku skupiny zabezpečení sítě v Azure pomocí sledování sítě a elastického zásobníku.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840634"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Vizualizace protokolů toku skupin zabezpečení sítě, které jsou ve službě Azure Network Watcher, s použitím opensourcových nástrojů

Protokoly toku skupiny zabezpečení sítě poskytují informace, které lze použít k pochopení příchozího přenosu dat a odchozího přenosu ip ve skupinách zabezpečení sítě. Tyto protokoly toku zobrazují odchozí a příchozí toky na základě pravidla, nic toku se vztahuje na 5 řazené kolekce členů informace o toku (zdroj/cíl IP, zdrojový/cílový port, protokol), a pokud byl provoz povolen nebo odepřen.

Tyto protokoly toku může být obtížné ručně analyzovat a získat přehledy z. Existuje však několik nástrojů s otevřeným zdrojovým kódem, které mohou pomoci vizualizovat tato data. Tento článek bude poskytovat řešení pro vizualizaci těchto protokolů pomocí elastický zásobníku, který vám umožní rychle indexovat a vizualizovat protokoly toku na řídicím panelu Kibana.

> [!Warning]  
> Následující kroky pracují s protokoly toku verze 1. Podrobnosti naleznete [v tématu Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

## <a name="scenario"></a>Scénář

V tomto článku nastavíme řešení, které vám umožní vizualizovat protokoly toku skupiny zabezpečení sítě pomocí elastického zásobníku.  Modul plug-in pro vstup Logstash získá protokoly toku přímo z objektu blob úložiště nakonfigurovaného pro obsahující protokoly toku. Potom pomocí elastického zásobníku protokoly toku budou indexovány a slouží k vytvoření řídicího panelu Kibana vizualizovat informace.

![scénář][scenario]

## <a name="steps"></a>Kroky

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupiny zabezpečení sítě
V tomto scénáři musíte mít v účtu povoleno protokolování toku skupiny zabezpečení sítě alespoň v jedné skupině zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě naleznete v následujícím článku [Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Nastavení elastického zásobníku
Připojením protokolů toku skupiny NSG s elastickým zásobníkem můžeme vytvořit řídicí panel Kibana, který nám umožňuje vyhledávat, vytvářet grafy, analyzovat a odvozovat poznatky z našich protokolů.

#### <a name="install-elasticsearch"></a>Instalace elastického vyhledávání

1. Elastický zásobník z verze 5.0 a vyšší vyžaduje Java 8. Spusťte `java -version` příkaz a zkontrolujte svou verzi. Pokud nemáte nainstalovanou java, podívejte se do dokumentace na [Azure-suppored JDKs](https://aka.ms/azure-jdks).
2. Stáhněte si správný binární balíček pro váš systém:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Další způsoby instalace lze nalézt na [Elasticsearch Instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Ověřte, zda je pomocí příkazu spuštěno elastické hledání:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Měli byste vidět odpověď podobnou této:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Další pokyny k instalaci elastického vyhledávání naleznete [v pokynech k instalaci](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalace logstash

1. Chcete-li nainstalovat Logstash spustit následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Dále musíme nakonfigurovat Logstash pro přístup a analyzovat protokoly toku. Vytvořte soubor logstash.conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Přidejte do souboru následující obsah:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Další pokyny k instalaci logstash naleznete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalace vstupního modulu plug-in Logstash pro úložiště objektů blob Azure

Tento plugin Logstash vám umožní přímý přístup k protokolům toku z jejich určeného účtu úložiště. Chcete-li nainstalovat tento plugin, z výchozího instalačního adresáře Logstash (v tomto případě /usr/share/logstash/bin) spusťte příkaz:

```bash
logstash-plugin install logstash-input-azureblob
```

Spuštění příkazu Logstash:

```bash
sudo /etc/init.d/logstash start
```

Další informace o tomto pluginu naleznete v [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalace Kibany

1. Chcete-li nainstalovat kibanu, spusťte následující příkazy:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Chcete-li spustit Kibana, použijte příkazy:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Chcete-li zobrazit webové rozhraní kibana, přejděte na`http://localhost:5601`
4. V tomto scénáři je index vzor použitý pro protokoly toku je "nsg-flow-logs". Můžete změnit vzor indexu v části "výstup" souboru logstash.conf.
5. Chcete-li zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo nsg umožňující přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvoření řídicího panelu Kibana

Ukázkový řídicí panel pro zobrazení trendů a podrobností ve vašich výstrahách se zobrazí na následujícím obrázku:

![Obrázek 1][1]

Stáhněte si [soubor řídicího panelu](https://aka.ms/networkwatchernsgflowlogdashboard), [vizualizační soubor](https://aka.ms/networkwatchernsgflowlogvisualizations)a [uložený vyhledávací soubor](https://aka.ms/networkwatchernsgflowlogsearch).

Na kartě **Správa** v Kibaně přejděte na **Uložené objekty** a importujte všechny tři soubory. Potom můžete na kartě **Řídicí panel** otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely přizpůsobené metrikám vašeho vlastního zájmu. Přečtěte si více o vytváření vizualizací Kibana z [oficiální dokumentace](https://www.elastic.co/guide/en/kibana/current/visualize.html)Kibany .

### <a name="visualize-nsg-flow-logs"></a>Vizualizace protokolů toku nsg

Ukázkový řídicí panel poskytuje několik vizualizací protokolů toku:

1. Toky podle rozhodnutí/směru v průběhu času - grafy časových řad zobrazující počet toků za časové období. Jednotku času a rozpětí obou těchto vizualizací můžete upravit. Toky podle rozhodnutí zobrazuje podíl povolit nebo odepřít rozhodnutí, zatímco toky podle směru zobrazuje podíl příchozía odchozí provoz. Pomocí těchto vizuálů můžete prozkoumat trendy provozu v průběhu času a hledat nějaké špičky nebo neobvyklé vzory.

   ![Obrázek2][2]

2. Toky podle cílového/zdrojového portu – výsečové grafy zobrazující rozdělení toků na příslušné porty. V tomto zobrazení můžete vidět nejčastěji používané porty. Pokud kliknete na konkrétní port ve výsečovém grafu, zbytek řídicího panelu se bude filtrovat dolů na toky tohoto portu.

   ![Obrázek3][3]

3. Počet toků a nejbližší čas protokolu – metriky zobrazující počet zaznamenaných toků a datum nejstaršího záznamu.

   ![Obrázek4][4]

4. Toky podle nsg a pravidlo – pruhový graf zobrazující rozdělení toků v rámci každého nsg, stejně jako rozdělení pravidel v rámci každého nsg. Zde můžete vidět, které NSG a pravidla generované největší provoz.

   ![Obrázek5][5]

5. Top 10 Zdrojové/cílové IP adresy – pruhové grafy zobrazující 10 hlavních zdrojových a cílových IP adresy. Tyto grafy můžete upravit tak, aby zobrazovala více či méně horní chip. Odtud můžete vidět nejčastěji se vyskytující IP adresy, stejně jako dopravní rozhodnutí (povolit nebo popřít) se provádí na každé IP.

   ![obrázek6][6]

6. Kolekce dat toku – tato tabulka zobrazuje informace obsažené v každé řazené kolekce členů toku, stejně jako odpovídající NGS a pravidlo.

   ![obrázek7][7]

Pomocí panelu dotazů v horní části řídicího panelu můžete filtrovat řídicí panel na základě libovolného parametru toků, jako je ID předplatného, skupiny prostředků, pravidlo nebo jakákoli jiná proměnná, která vás zajímá. Další informace o dotazech a filtrech společnosti Kibana naleznete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Závěr

Kombinací protokolů toku skupiny zabezpečení sítě s elastickým zásobníkem jsme přišli s výkonným a přizpůsobitelným způsobem vizualizace našeho síťového provozu. Tyto řídicí panely umožňují rychle získat a sdílet přehledy o síťovém provozu, stejně jako filtrovat dolů a zkoumat případné anomálie. Pomocí kibany můžete tyto řídicí panely přizpůsobit a vytvořit konkrétní vizualizace tak, aby vyhovovaly všem potřebám zabezpečení, auditu a dodržování předpisů.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak vizualizovat protokoly toku nsg pomocí Power BI na stránce [Visualize Protokoly toků nsg pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
