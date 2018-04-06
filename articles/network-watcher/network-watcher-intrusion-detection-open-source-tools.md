---
title: Provedení zjišťování neoprávněných vniknutí sítě s otevřeným zdrojem nástroje a sledovací proces sítě Azure | Microsoft Docs
description: Tento článek popisuje, jak použít sledovací proces sítě Azure a otevřete source nástroje k provedení zjišťování neoprávněných vniknutí sítě
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Provedení zjišťování neoprávněných vniknutí sítě s sledovací proces sítě a open source nástroje

Zachycení paketu jsou klíčovou komponentou pro implementace systémy zjišťování neoprávněných vniknutí sítě (ID) a provádění monitorování zabezpečení sítě (NSM). Existuje několik nástrojů ID s otevřeným zdrojem, které zpracování paketů zachycení a vyhledejte podpis vniknutí možné sítě a škodlivé aktivity. Pomocí paketu zaznamená zadaný ve sledovací proces sítě, můžete analyzovat vaše síť pro všechny škodlivé vniknutí nebo ohrožení zabezpečení.

Jeden takový nástroj s otevřeným zdrojem je Suricata, modul ID, která používá sady pravidel pro monitorování síťového provozu a aktivuje upozornění pokaždé, když dojde k podezřelé události. Suricata nabízí modul Vícevláknová, což znamená, že ho můžete provádět analýzy zatížení sítě s vyšší rychlostí a efektivitu. Další podrobnosti o Suricata a jeho funkce, navštivte webovou jejich na https://suricata-ids.org/.

## <a name="scenario"></a>Scénář

Tento článek vysvětluje, jak nastavit svoje prostředí k provedení zjišťování neoprávněných vniknutí sítě pomocí sledovací proces sítě, Suricata a elastické zásobníku. Sledovací proces sítě vám poskytne zachytávání paketů použít k provedení zjišťování neoprávněných vniknutí sítě. Suricata zpracovává paketu zachycení a aktivační události výstrahy podle paketů, které odpovídají jeho dané ruleset hrozeb. Tyto výstrahy jsou uloženy v souboru protokolu na místním počítači. Používání elastické zásobníku, protokoly Suricata může být indexované a použít k vytvoření řídicího panelu Kibana, vám poskytnou vizuální reprezentace protokoly a znamená a rychle získáte přehled o na potenciální ohrožení zabezpečení sítě.  

![scénář jednoduché webové aplikace][1]

Oba nástroje s otevřeným zdrojem můžete být nastavit ve virtuálním počítači Azure, umožňuje provádět analýzy v rámci Azure prostředí sítě.

## <a name="steps"></a>Kroky

### <a name="install-suricata"></a>Nainstalujte Suricata

Všechny ostatní metody instalace najdete v článku http://suricata.readthedocs.io/en/latest/install.html

1. V terminál příkazového řádku virtuálního počítače spusťte následující příkazy:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Pokud chcete ověřit instalaci, spusťte příkaz `suricata -h` chcete zobrazit úplný seznam příkazů.

### <a name="download-the-emerging-threats-ruleset"></a>Stáhnout ruleset vznikající hrozby

V této fázi nemáme žádná pravidla pro Suricata ke spuštění. Můžete vytvořit vlastní pravidla, pokud existují specifické hrozby k síti, že chcete zjistit, nebo můžete také vyvinuté pomocí sady pravidel z mnoha různých poskytovatelé, například vznikající hrozby nebo VRT pravidla z Snort. Tady používáme volně přístupné ruleset vznikající hrozby:

Stáhněte sadu pravidel a zkopírujte je do adresáře:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Proces paketu zachytávali Suricata

Při zpracování paketů zaznamená pomocí Suricata, spusťte následující příkaz:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Kontrola výsledné výstrah, přečtěte si soubor fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Nastavit elastické zásobníku

Když protokoly, které vytváří Suricata obsahují cenné informace o dění na naše síť, tyto soubory protokolu nejsou nejjednodušší ke čtení a pochopení. Připojením Suricata elastické zásobníkem, můžeme vytvořit řídicí panel Kibana a co umožňuje vyhledávat, graf, analyzovat a statistiky odvozena z našich protokolů.

#### <a name="install-elasticsearch"></a>Nainstalujte Elasticsearch

1. Elastické zásobníku z verze 5.0 a vyšší vyžaduje Java 8. Spusťte příkaz `java -version` zkontrolujte vaši verzi. Pokud nemáte java nainstalovat, najdete v dokumentaci k na [Oracle na webu](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Stáhněte si správné binární balíček pro váš systém:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Ostatní metody instalace najdete na [Elasticsearch instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ověřte, zda je spuštěna Elasticsearch pomocí příkazu:

    ```
    curl http://127.0.0.1:9200
    ```

    Byste měli vidět odpověď podobná této:

    ```
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

Další pokyny k instalaci elastické vyhledávání, naleznete na stránce [instalace](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Nainstalujte Logstash

1. Chcete-li nainstalovat Logstash spusťte následující příkazy:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Další, je potřeba nakonfigurovat Logstash číst z výstupu eve.json souboru. Vytvoření souboru logstash.conf pomocí:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Přidejte následující obsah do souboru (ujistěte se, zda je cesta k souboru eve.json správný):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Zajistěte, aby k správná oprávnění k souboru eve.json aby Logstash můžete ingestování soubor.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Chcete-li spustit Logstash spusťte příkaz:

    ```
    sudo /etc/init.d/logstash start
    ```

Další pokyny k instalaci Logstash naleznete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Nainstalujte Kibana

1. Spusťte následující příkazy pro instalaci Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Chcete-li spustit Kibana použijte příkazy:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Chcete-li zobrazit vaše Kibana webové rozhraní, přejděte na `http://localhost:5601`
1. V tomto scénáři je vzor indexu používá pro protokoly Suricata "logstash-*"

1. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvoření příchozího pravidla NSG povolení přístupu k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvořit řídicí panel Kibana

V tomto článku uvádíme na ukázkový řídicí panel můžete prohlédnout podrobnosti a trendy v upozornění.

1. Stáhněte si soubor řídicí panel [sem](https://aka.ms/networkwatchersuricatadashboard), soubor vizualizace [sem](https://aka.ms/networkwatchersuricatavisualization)a soubor uloženého hledání [zde](https://aka.ms/networkwatchersuricatasavedsearch).

1. V části **správy** kartě z Kibana, přejděte na **uložit objekty** a importovat všechny tři soubory. Potom z **řídicí panel** karta můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a přizpůsobit směrem metriky týkající se vlastní řídicí panely. Další informace o vytváření vizualizací Kibana z na Kibana [oficiální dokumentaci](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![řídicí panel kibana][2]

### <a name="visualize-ids-alert-logs"></a>Vizualizace ID výstrahy protokoly

Ukázkový řídicí panel poskytuje několik vizualizace Suricata výstrahy protokolů:

1. Výstrahy podle GeoIP – mapu zobrazující distribuci výstrahy podle země původu podle zeměpisného umístění (určené IP)

    ![geograficky ip][3]

1. TOP 10 výstrahy – souhrn nejčastěji se vyskytující 10 aktivovány výstrahy a jejich popis. Kliknutím na jednotlivé výstrahy filtruje dolů řídicím panelu na informace týkající se této konkrétní výstrahu.

    ![Obrázek 4][4]

1. Počet výstrah – celkový počet výstrahy aktivovány sada pravidel pro

    ![Obrázek 5][5]

1. Byla aktivována nejvyšší 20 zdrojové nebo cílové IP adresy nebo porty - výsečové grafy znázorňující nejvyšší 20 IP adresy a porty této výstrahy. Můžete filtrovat na konkrétní IP adresy nebo porty a kolik a jaký druh výstrahy dolů, se aktivují.

    ![Obrázek 6][6]

1. Souhrn výstrah – tabulky Shrnutí konkrétní podrobnosti o každém individuální výstrahu. Tato tabulka zobrazíte další parametry, které vás zajímají pro každou výstrahu můžete přizpůsobit.

    ![Obrázek 7][7]

Další dokumentaci týkající se vytvoření vlastního vizualizace a řídicích panelů najdete v tématu [oficiální dokumentaci na Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Závěr

Kombinací paketu zaznamená zadaný sledovací proces sítě a ID nástroje s otevřeným zdrojem, jako je Suricata, je možné provést zjišťování neoprávněných vniknutí sítě pro širokou škálu hrozeb. Tyto řídicí panely vám umožňují rychle rozpoznat trendy a anomálie v rámci vaší sítě, jako dobře dig do data, která mají-li zjistit, že hlavní příčiny výstrahy, jako je například uživatel se zlými úmysly agentů nebo citlivé porty. S Tento extrahovaná data můžete provést informované rozhodnutí o tom, jak reagovat na ochránit síť před všechny pokusy o škodlivé vniknutí a vytvářet pravidla pro budoucí před proniknutím k vaší síti.

## <a name="next-steps"></a>Další postup

Zjistěte, jak aktivovat zachycení paketů, které jsou založeny na výstrahách navštivte stránky [pomocí zachytáváním paketů provádět monitorování proaktivní sítě s Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Zjistěte, jak toku protokolů NSG s Power BI vizualizovat navštivte stránky [vizualizovat NSG toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
