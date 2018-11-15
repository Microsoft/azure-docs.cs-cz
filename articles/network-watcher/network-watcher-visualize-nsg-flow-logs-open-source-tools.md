---
title: Vizualizace protokolů toku NSG sledovací proces sítě Azure s využitím opensourcových nástrojů | Dokumentace Microsoftu
description: Tato stránka popisuje, jak používat open source nástrojů pro vizualizaci protokolů toku NSG.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 6debfaed28be2b5dee5e24a443b791f75f7bea8f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634188"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Vizualizace protokolů toku NSG sledovací proces sítě Azure s využitím opensourcových nástrojů

Protokoly toku skupin zabezpečení sítě poskytují informace, které je možné pochopit příchozí a odchozí přenosy na skupiny zabezpečení sítě. Tyto protokoly toku zobrazení odchozí a příchozí toků na základě na pravidlo, tok se vztahuje na síťové KARTĚ, 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a pokud provoz byl povolen nebo odepřen.

Tyto protokoly toku může být obtížné ruční analyzovat a zkoumat velké. Existuje však několik opensourcových nástrojů, které pomáhají vizualizovat tato data. Tento článek poskytuje protokolů řešení k vizualizaci těchto protokolů pomocí řešení Elastic Stack, který vám umožní rychle indexovat a Vizualizujte váš tok na řídicí panel Kibana.

## <a name="scenario"></a>Scénář

V tomto článku nastavíme řešení, které vám umožní vizualizovat protokoly toků skupin zabezpečení sítě pomocí řešení Elastic Stack.  Modul plug-in vstupní získá protokolů toku přímo z úložiště objektů blob, nakonfigurované pro obsahující protokolů toku. Potom pomocí řešení Elastic Stack protokolů toku bude indexovat a použít k vytvoření nějaký řídicí panel Kibana a vizualizovat informace.

![scénář][scenario]

## <a name="steps"></a>Kroky

### <a name="enable-network-security-group-flow-logging"></a>Protokolování toků povolit skupiny zabezpečení sítě
V tomto scénáři musí mít síťové zabezpečení skupiny tok protokolování povoleno na alespoň jednu skupinu zabezpečení sítě ve vašem účtu. Pokyny k povolení protokolů toku zabezpečení sítě, najdete v následujícím článku [Úvod k protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Nastavit řešení Elastic Stack
Propojením protokolů toku NSG řešení Elastic Stack, můžeme vytvořit řídicí panel Kibana a co umožňuje hledat, graf, analyzovat a vyvoďte z našich protokolů.

#### <a name="install-elasticsearch"></a>Instalace Elasticsearch

1. Řešení Elastic Stack z verze 5.0 a vyšším vyžaduje Java 8. Spusťte příkaz `java -version` k ověření verze. Pokud nemáte nainstalované java, podívejte se na dokumentaci na [Azure trvalé JDK](https://aka.ms/azure-jdks).
2. Stáhněte balíček správné binární pro váš systém:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Jiné metody instalace najdete v [instalace Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Ověřte spuštění Elasticsearch pomocí příkazu:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Měli byste vidět odpovědi podobně jako tato:

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

Další pokyny k instalaci elastické vyhledávání, najdete v tématu [pokyny k instalaci](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalace Logstash

1. Instalace Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Dále musíme konfigurace Logstash pro přístup k a analýza protokolů toku. Vytvoření souboru logstash.conf pomocí:

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

Další pokyny týkající se instalace Logstash, najdete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Nainstalujte modul plug-in vstupu pro úložiště objektů blob v Azure

Tento modul plug-in vám umožní přímo ze svého účtu úložiště určený přístup k protokolům toku. K instalaci tohoto modulu plug-in, spusťte příkaz z adresáře instalace Logstash výchozí (v tomto případu /usr/share/logstash/bin):

```bash
logstash-plugin install logstash-input-azureblob
```

Spusťte Logstash pomocí příkazu:

```bash
sudo /etc/init.d/logstash start
```

Další informace o tomto modulu plug-in, najdete [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Nainstalujte Kibana

1. Spuštěním následujících příkazů nainstalujte Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Spuštění Kibana pomocí příkazů:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Chcete-li zobrazit vaše webové rozhraní Kibana, přejděte na `http://localhost:5601`
4. V tomto scénáři index používaným pro protokoly toků je "-protokolů toku nsg-". Můžete změnit model indexů v části "výstupní" logstash.conf souboru.
5. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo NSG umožňuje přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvořte řídicí panel Kibana

Ukázkový řídicí panel zobrazovat trendy a podrobnosti upozornění je zobrazeno na následujícím obrázku:

![Obrázek 1][1]

Stáhněte si [řídicí panely](https://aka.ms/networkwatchernsgflowlogdashboard), [soubor vizualizace](https://aka.ms/networkwatchernsgflowlogvisualizations)a [uložit soubor výsledků hledání](https://aka.ms/networkwatchernsgflowlogsearch).

V části **správu** kartu z Kibana, přejděte na **uložit objekty** a importovat všechny tři soubory. Potom z **řídicí panel** kartu můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely, které jsou přizpůsobené pro vlastní metriky. Další informace o vytváření Kibana vizualizace z vaší Kibana [oficiální dokumentaci](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Vizualizace protokolů toku NSG

Ukázkový řídicí panel poskytuje několik vizualizace protokolů toku:

1. Toky podle rozhodnutí/směr v čase - čas řady grafy znázorňující počet toků za časové období. Jednotka času a značku span objektu obě tyto vizualizace můžete upravit. Toky podle rozhodnutí zobrazuje podíl povolují nebo odpírají rozhodnutí, poměr příchozí a odchozí přenosy dat se zobrazí toky podle směru. S těmito vizuály můžete prozkoumat provoz trendy v čase a vyhledejte všechny špičky nebo neobvyklé vzory.

   ![Obrázek 2][2]

2. Toky podle cílového a zdrojového portu – koláčové grafy zobrazující rozdělení toky k příslušným portům. V tomto zobrazení uvidíte nejčastěji používané porty. Pokud kliknete na konkrétní port ve výsečovém grafu, zbývající část řídicího panelu se vyfiltrovat toky tohoto portu.

   ![figure3][3]

3. Počet toků a Nejdřívější čas protokolu – metriky zobrazuje počet zaznamenaných toků a datum protokolu nejdřívější zachytit.

   ![figure4][4]

4. Toky podle NSG a pravidel – pruhový graf, zobrazí se v něm distribuce toků v jednotlivých skupinách NSG a také distribuce pravidel v jednotlivých skupinách NSG. Tady vidíte, které skupiny zabezpečení sítě a pravidel vygenerované nejvíce přenosů.

   ![figure5][5]

5. Prvních 10 zdrojové nebo cílové IP adresy – pruhové grafy znázorňující prvních 10 zdrojových a cílových IP adres. Můžete upravit tyto grafy pro zobrazení více či méně nejčastější IP adresy. Tady vidíte nejběžněji vyskytují IP adresy, jakož i provoz rozhodnutí (povolit nebo zakázat) prováděné na jednotlivých IP.

   ![figure6][6]

6. Tok řazené kolekce členů – Tato tabulka uvádí informace obsažené v rámci každý tok řazené kolekce členů také odpovídající nastavení a pravidla.

   ![figure7][7]

Použití dotazů pruhu v horní části řídicího panelu, můžete vyfiltrovat řídicí panel žádné parametry toků, jako je například ID předplatného, skupiny prostředků, pravidla nebo jakoukoli jinou proměnnou, která vás zajímá. Další informace o dotazech v Kibana a filtry, najdete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Závěr

Kombinací protokolů toků skupin zabezpečení sítě pomocí řešení Elastic Stack budeme mít Navrhněte výkonné a přizpůsobitelné způsob, jak vizualizovat naše síťový provoz. Tyto řídicí panely umožňují rychle získat a sdílet přehledy o vašich síťových přenosů, stejně jako filtr dolů a prozkoumat na potenciální anomálie. Pomocí Kibana, můžete přizpůsobit tyto řídicí panely a vytvářet konkrétní vizualizace podle potřeby zabezpečení, auditování a dodržování předpisů.

## <a name="next-steps"></a>Další postup

Zjistěte, jak vizualizovat vaše protokoly toků NSG s Power BI návštěvou [toků NSG vizualizaci protokolů pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
