---
title: Vizualizace protokolů toku NSG – elastický zásobník
titleSuffix: Azure Network Watcher
description: Spravujte a analyzujte protokoly toku skupin zabezpečení sítě v Azure pomocí Network Watcher a elastického zásobníku.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0896df301718c74e63a9e18c74615130fa80c952
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986253"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Vizualizace protokolů toku skupin zabezpečení sítě, které jsou ve službě Azure Network Watcher, s použitím opensourcových nástrojů

Protokoly toku skupin zabezpečení sítě poskytují informace, které se dají použít k pochopení příchozího a odchozího provozu IP v skupinách zabezpečení sítě. Tyto protokoly toku ukazují odchozí a příchozí toky pro každé pravidlo. síťové rozhraní, ke kterému se tok vztahuje, je 5 informací řazené kolekce členů toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolený nebo zakázaný.

Tyto protokoly toků můžou být obtížné ručně analyzovat a získávat z nich přehledy. Existuje však několik open source nástrojů, které vám mohou vizualizovat tato data. Tento článek vám poskytne řešení pro vizualizaci těchto protokolů pomocí elastického zásobníku, který vám umožní rychle indexovat a vizualizovat protokoly toku na řídicím panelu Kibana.

## <a name="scenario"></a>Scénář

V tomto článku nastavíme řešení, které vám umožní vizualizovat protokoly toku skupin zabezpečení sítě pomocí elastického zásobníku.  Vstupní modul plug-in Logstash získá protokoly toku přímo z objektu BLOB úložiště nakonfigurovaného pro obsahující protokoly toku. Poté pomocí elastického zásobníku budou protokoly toku indexovány a použity k vytvoření řídicího panelu Kibana k vizualizaci informací.

![Diagram znázorňuje scénář, který umožňuje vizualizovat protokoly toku skupin zabezpečení sítě pomocí elastického zásobníku.][scenario]

## <a name="steps"></a>Postup

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupin zabezpečení sítě
V tomto scénáři musíte mít povolené protokolování toku skupin zabezpečení sítě ve vašem účtu aspoň v jedné skupině zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě najdete v následujícím článku [Úvod do protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Nastavení elastického zásobníku
Díky propojení protokolů toku NSG s elastickým zásobníkem můžeme vytvořit řídicí panel Kibana, který nám umožní Hledat, graf, analyzovat a odvodit přehledy z našich protokolů.

#### <a name="install-elasticsearch"></a>Nainstalovat Elasticsearch

1. Elastická sada z verze 5,0 a vyšší vyžaduje Java 8. Spusťte příkaz `java -version` a ověřte svou verzi. Pokud nemáte nainstalovaný Java, přečtěte si dokumentaci k [Azure-suppored sady JDK](https://aka.ms/azure-jdks).
2. Stáhněte si správný binární balíček pro váš systém:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Další metody instalace najdete v [Elasticsearch instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) .

3. Ověřte, že je Elasticsearch spuštěný s příkazem:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Měla by se zobrazit odpověď podobná této:

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

Další pokyny k instalaci elastického hledání najdete v [pokynech k instalaci](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Nainstalovat Logstash

1. Pro instalaci Logstash spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Dál musíme nakonfigurovat Logstash pro přístup k protokolům Flow a jejich analýzu. Vytvořte soubor logstash. conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Do souboru přidejte následující obsah:

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
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
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

Další pokyny k instalaci Logstash najdete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalace vstupního modulu plug-in Logstash pro úložiště objektů BLOB v Azure

Tento modul plug-in Logstash vám umožní přímý přístup k protokolům toků z určeného účtu úložiště. Pokud chcete tento modul plug-in nainstalovat z výchozího instalačního adresáře Logstash (v tomto případě/usr/share/logstash/bin), spusťte příkaz:

```bash
logstash-plugin install logstash-input-azureblob
```

Spuštění Logstash spustíte příkazem:

```bash
sudo /etc/init.d/logstash start
```

Další informace o tomto modulu plug-in najdete v [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Nainstalovat Kibana

1. Spusťte následující příkazy a nainstalujte Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Pro spuštění Kibana použijte příkazy:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Pokud chcete zobrazit webové rozhraní Kibana, přejděte na `http://localhost:5601`
4. Pro tento scénář je vzor indexu použitý pro protokoly toku "NSG-Flow-logs". Vzor indexu můžete změnit v části "výstup" v souboru logstash. conf.
5. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo NSG, které umožní přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvoření řídicího panelu Kibana

Vzorový řídicí panel pro zobrazení trendů a podrobností v upozorněních se zobrazuje na následujícím obrázku:

![Obrázek 1][1]

Stáhněte si [soubor řídicího panelu](https://aka.ms/networkwatchernsgflowlogdashboard), [soubor vizualizace](https://aka.ms/networkwatchernsgflowlogvisualizations)a [uložený hledaný soubor](https://aka.ms/networkwatchernsgflowlogsearch).

Na kartě **Správa** Kibana přejděte na **uložené objekty** a importujte všechny tři soubory. Pak na kartě **řídicí panel** můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely, které jsou přizpůsobené všem metrikám svého vlastního zájmu. Přečtěte si další informace o vytváření vizualizací Kibana z [oficiální dokumentace](https://www.elastic.co/guide/en/kibana/current/visualize.html)Kibana.

### <a name="visualize-nsg-flow-logs"></a>Vizualizace protokolů toku NSG

Vzorový řídicí panel nabízí několik vizualizací protokolů Flow:

1. Toky podle rozhodnutí nebo směr v grafech časových řad času znázorňující počet toků za časové období. Můžete upravit jednotku času a rozpětí obou těchto vizualizací. V části toky podle rozhodnutí se zobrazuje poměr povolených nebo odepřených rozhodnutí, zatímco toky podle směru ukazují poměr příchozích a odchozích přenosů. Pomocí těchto vizuálů můžete sledovat trendy provozu v průběhu času a hledat jakékoli špičky nebo neobvyklé vzory.

   ![Snímek obrazovky ukazuje vzorový řídicí panel s toky podle rozhodnutí a směru v průběhu času.][2]

2. Toky podle cílového/zdrojového portu – výsečové grafy znázorňující rozpis toků na příslušné porty. V tomto zobrazení uvidíte nejčastěji používané porty. Pokud kliknete na konkrétní port v rámci výsečového grafu, zbývající část řídicího panelu se vyfiltruje na toky tohoto portu.

   ![Snímek obrazovky s ukázkovým řídicím panelem, který obsahuje toky podle cíle a zdrojového portu.][3]

3. Počet toků a nejstarší čas protokolu – metriky ukazují počet zaznamenaných toků a datum nejstaršího zaznamenaného protokolu.

   ![Snímek obrazovky ukazuje vzorový řídicí panel s počtem toků a nejdřívějším časem protokolu.][4]

4. Toky podle NSG a rule – pruhový graf zobrazující distribuci toků v rámci jednotlivých NSG a také distribuci pravidel v rámci jednotlivých NSG. Tady si můžete prohlédnout, která NSG a pravidla vygenerovala nejvíce provozu.

   ![Snímek obrazovky se vzorovým řídicím panelem, který obsahuje N S G a pravidlo.][5]

5. Nejčastějších 10 zdrojů a cílových IP adres – pruhové grafy zobrazující prvních 10 zdrojových a cílových IP adres. Úpravou těchto grafů můžete zobrazit více nebo méně hlavních IP adres. Tady se můžete podívat na nejčastější IP adresy a také rozhodnutí o provozu (povolit nebo odepřít) pro každou IP adresu.

   ![Snímek obrazovky se vzorovým řídicím panelem, který obsahuje prvních deset zdrojů a cílů I P.][6]

6. Řazené kolekce členů toku – v této tabulce jsou uvedeny informace obsažené v rámci každé řazené kolekce členů toku a odpovídající avit a pravidlo.

   ![Snímek obrazovky znázorňuje tok záznamů dat toků v tabulce.][7]

Pomocí panelu dotazů v horní části řídicího panelu můžete vyfiltrovat řídicí panel na základě libovolného parametru toků, jako je ID předplatného, skupiny prostředků, pravidlo nebo jakákoli jiná proměnná zájmu. Další informace o dotazech a filtrech Kibana najdete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html) .

## <a name="conclusion"></a>Závěr

Kombinací protokolu toku skupin zabezpečení sítě s elastickým zásobníkem jsme získali výkonný a přizpůsobitelný způsob, jak vizualizovat náš síťový provoz. Tyto řídicí panely umožňují rychle získat a sdílet přehledy o síťových přenosech a také vyfiltrovat a prozkoumat případné anomálie. Pomocí Kibana můžete tyto řídicí panely přizpůsobit a vytvořit konkrétní vizualizace, které budou vyhovovat požadavkům na zabezpečení, audit a dodržování předpisů.

## <a name="next-steps"></a>Další kroky

Naučte se vizualizovat protokoly toku NSG pomocí Power BI tím, že navštívíte [vizualizace NSG toků protokolů s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
