---
title: Síť detekce neoprávněných vniknutí pomocí opensourcových nástrojů a Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat Azure Network Watcher a open source nástrojů k provedení detekce napadení sítě
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
ms.openlocfilehash: 9d77952a96bff6cc2d50ecbd4bde7e499f20de6d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420026"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Síť detekce neoprávněných vniknutí pomocí opensourcových nástrojů a Network Watcher

Zachytávání paketů jsou klíčovou komponentou pro implementaci systémech zjišťování neoprávněného vniknutí sítě (ID) a provádí monitorování zabezpečení sítě (NSM). Existuje několik open source nástrojů ID, které zpracovávají zachytávání paketů a hledejte podpisy vniknutí možný sítě a škodlivé aktivity. Pomocí paketu zaznamená zadaný pomocí Network Watcher můžete analyzovat vaše síť pro všechny škodlivé incidentům narušení nebo ohrožení zabezpečení.

Jeden takový open source nástroj je Suricata ID modul, který používá sady pravidel pro monitorování síťového provozu a aktivují upozornění, vždy, když dojde k podezřelé události. Suricata nabízí modul s víc vlákny, což znamená, že může provádět Analýza provozu sítě s vyšší rychlostí a efektivitu. Další podrobnosti o Suricata a jeho funkce, najdete na svém webu https://suricata-ids.org/.

## <a name="scenario"></a>Scénář

Tento článek vysvětluje, jak nastavit prostředí k provádění detekce napadení sítě pomocí Network Watcher, Suricata a řešení Elastic Stack. Network Watcher dává zachytávání paketů používaný k provádění detekce napadení sítě. Suricata zpracovává zachytávání paketů a vyvolá upozornění podle pakety, které odpovídají jeho dané sady pravidel hrozeb. Tyto výstrahy jsou uloženy v souboru protokolu na místním počítači. Pomocí řešení Elastic Stack protokoly generované Suricata lze pracovat a umožňuje vytvořit řídicí panel Kibana, vám poskytnou vizuální reprezentaci těchto protokolů a prostředek pro rychlé získání přehledu o potenciálních ohroženích zabezpečení sítě.  

![scénář jednoduché webové aplikace][1]

Obě open source nástrojů lze nastavit na Virtuálním počítači Azure, abyste mohli provést tuto analýzu v rámci vlastní síťového prostředí Azure.

## <a name="steps"></a>Kroky

### <a name="install-suricata"></a>Nainstalujte Suricata

Všechny ostatní metody instalace najdete na webu http://suricata.readthedocs.io/en/latest/install.html

1. V terminálu příkazového řádku vašeho virtuálního počítače spusťte následující příkazy:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Pokud chcete ověřit instalaci, spusťte příkaz `suricata -h` zobrazíte úplný seznam příkazů.

### <a name="download-the-emerging-threats-ruleset"></a>Stažení sady pravidel vznikajícími hrozbami

V této fázi nemáme všechna pravidla pro Suricata ke spuštění. Můžete vytvořit vlastní pravidla, pokud existují konkrétní ohrožení vaší sítě, že chcete detekovat, nebo můžete také vyvinuté pomocí sady pravidel z řady poskytovatelů, jako je nově vznikající hrozby nebo VRT pravidla z Snort. Tady používáme volně dostupné sady pravidel nově vznikající hrozby:

Stáhněte si sadu pravidel a zkopírujte do adresáře:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Proces paketů zachytávali Suricata

Pro zpracování paketů zachycuje pomocí Suricata, spusťte následující příkaz:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Pokud chcete zkontrolovat výsledné výstrahy, přečíst soubor fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Nastavit řešení Elastic Stack

Když protokoly, které vytváří Suricata obsahují cenné informace o tom, co se děje v naší síti, tyto soubory protokolu nejsou nejsnadněji si a porozuměli jim. Propojením Suricata řešení Elastic Stack, můžeme vytvořit řídicí panel Kibana a co umožňuje hledat, graf, analyzovat a vyvoďte z našich protokolů.

#### <a name="install-elasticsearch"></a>Instalace Elasticsearch

1. Řešení Elastic Stack z verze 5.0 a vyšším vyžaduje Java 8. Spusťte příkaz `java -version` k ověření verze. Pokud nemáte nainstalované java, podívejte se na dokumentaci na [Azure trvalé JDK](https://aka.ms/azure-jdks).

1. Stáhněte balíček správné binární pro váš systém:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Jiné metody instalace najdete v [instalace Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ověřte spuštění Elasticsearch pomocí příkazu:

    ```
    curl http://127.0.0.1:9200
    ```

    Měli byste vidět odpovědi podobně jako tato:

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

Další pokyny k instalaci elastické hledání najdete na stránce [instalace](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalace Logstash

1. Instalace Logstash, spusťte následující příkazy:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Dále musíme konfigurace Logstash pro čtení z výstupu eve.json souboru. Vytvoření souboru logstash.conf pomocí:

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

1. Ujistěte se, že abyste správná oprávnění k souboru eve.json tak, aby Logstash ingestované soubor.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Spusťte Logstash pomocí příkazu:

    ```
    sudo /etc/init.d/logstash start
    ```

Další pokyny týkající se instalace Logstash, najdete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Nainstalujte Kibana

1. Spuštěním následujících příkazů nainstalujte Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Spuštění Kibana pomocí příkazů:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Chcete-li zobrazit vaše webové rozhraní Kibana, přejděte na `http://localhost:5601`
1. V tomto scénáři je index používaným pro protokoly Suricata "logstash-*"

1. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo NSG umožňuje přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvořte řídicí panel Kibana

Tento článek uvádíme ukázkový řídicí panel si můžete zobrazit trendy a podrobnosti v upozornění.

1. Stáhněte si soubor řídicí panel [tady](https://aka.ms/networkwatchersuricatadashboard), soubor vizualizace [tady](https://aka.ms/networkwatchersuricatavisualization)a soubor uloženého hledání [tady](https://aka.ms/networkwatchersuricatasavedsearch).

1. V části **správu** kartu z Kibana, přejděte na **uložit objekty** a importovat všechny tři soubory. Potom z **řídicí panel** kartu můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely, které jsou přizpůsobené pro vlastní metriky. Další informace o vytváření Kibana vizualizace z vaší Kibana [oficiální dokumentaci](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![řídicí panel kibana][2]

### <a name="visualize-ids-alert-logs"></a>Vizualizace protokolů ID výstrahy

Ukázkový řídicí panel poskytuje několik vizualizací Suricata upozornění protokolů:

1. Výstrahy podle GeoIP – mapě a ukáže distribuce výstrahy podle země původu na základě geografického umístění (určené IP)

    ![geograficky ip][3]

1. Prvních 10 výstrah – přehled 10 nejčastějších aktivované výstrahy a jejich popis. Kliknutím na jednotlivé výstrahy filtrů dolů na řídicím panelu Informace týkající se toto konkrétní upozornění.

    ![Obrázek 4][4]

1. Počet výstrah – celkový počet upozornění aktivovaných pomocí sady pravidel

    ![Obrázek 5][5]

1. 20 zdrojové nebo cílové IP adresy a porty - výsečové grafy znázorňující nahoře 20 IP adres a portů této výstrahy se spustila v. Můžete filtrovat seznam na konkrétní IP adresy a porty zobrazíte kolik a jaký druh výstrahy se aktivují.

    ![Obrázek 6][6]

1. Souhrn upozornění – tabulku se souhrnem konkrétní podrobnosti o každé jednotlivé výstrahy. Tato tabulka zobrazíte další parametry potřebné pro každé upozornění můžete přizpůsobit.

    ![Obrázek 7][7]

Další dokumentaci k vytváření vlastních vizualizací a řídicích panelů najdete v tématu [oficiální dokumentaci společnosti Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Závěr

Díky kombinaci paketů zaznamená zadaný tak, že sledovací proces sítě a open source ID nástrojů, jako je například Suricata, můžete provést detekce napadení sítě pro širokou škálu hrozby. Tyto řídicí panely umožňují rychle odhalovat trendy a anomálie v rámci vaší sítě, jako dobře dig o datech a zjistit, že hlavní příčiny výstrahy, jako je například uživatel se zlými úmysly agentů nebo zranitelné porty. S Tato extrahovaná data můžete provádět informovaná rozhodnutí o tom, jak reagovat na ně a chránit vaši síť z jakékoli pokusy o neoprávněná vniknutí škodlivých a vytvoření pravidla pro ochranu budoucí vniknutí do vaší sítě.

## <a name="next-steps"></a>Další postup

Zjistěte, jak aktivovat zachytávání paketů, které jsou založeny na výstrahách návštěvou [použití zachytávání paketů k proaktivnímu monitorování sítě s využitím Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Zjistěte, jak vizualizovat vaše protokoly toků NSG s Power BI návštěvou [toků NSG vizualizaci protokolů pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
