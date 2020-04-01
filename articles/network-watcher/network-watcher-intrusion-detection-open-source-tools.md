---
title: Provádění zjišťování narušení sítě pomocí nástrojů s otevřeným zdrojovým kódem
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak používat Azure Network Watcher a open source nástroje k provedení zjišťování narušení sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1bd823d94552d1e920b367b6576b0e3bb74aefb2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474924"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Provádění zjišťování narušení sítě pomocí nástroje Sledování sítě a open source

Zachycení paketů je klíčovou součástí pro implementaci systémů detekce narušení sítě (IDS) a provádění monitorování zabezpečení sítě (NSM). Existuje několik nástrojů IDS s otevřeným zdrojovým kódem, které zpracovávají zachytávání paketů a hledají podpisy možných síťových vniknutí a škodlivé aktivity. Pomocí zachycení paketů poskytovaných sledovacím programem sítě můžete analyzovat síť na jakékoli škodlivé vniknutí nebo chyby zabezpečení.

Jedním z takových open source nástroj je Suricata, IDS motor, který používá pravidla pro sledování síťového provozu a aktivuje výstrahy vždy, když dojde k podezřelým událostem. Suricata nabízí vícevláknový motor, což znamená, že může provádět analýzu síťového provozu se zvýšenou rychlostí a účinností. Pro více informací o Suricata a jeho https://suricata-ids.org/schopnosti, navštivte jejich webové stránky na .

## <a name="scenario"></a>Scénář

Tento článek vysvětluje, jak nastavit prostředí k provedení zjišťování narušení sítě pomocí sledování sítě, Suricata a elastický zásobník. Sledování sítě poskytuje zachycení paketů, které se používají k detekci narušení sítě. Suricata zpracovává zachytávání paketů a aktivuje výstrahy na základě paketů, které odpovídají dané sadě pravidel hrozeb. Tyto výstrahy jsou uloženy v souboru protokolu v místním počítači. Pomocí elastického zásobníku mohou být protokoly generované suricatou indexovány a použity k vytvoření řídicího panelu Kibana, který poskytuje vizuální reprezentaci protokolů a prostředky k rychlému získání přehledu o potenciálních chybách zabezpečení sítě.  

![jednoduchý scénář webové aplikace][1]

Oba nástroje s otevřeným zdrojovým kódem lze nastavit na virtuálním počítači Azure, což vám umožní provádět tuto analýzu v rámci vlastního síťového prostředí Azure.

## <a name="steps"></a>Kroky

### <a name="install-suricata"></a>Instalace Suricata

Všechny ostatní způsoby instalace naleznete nahttps://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. V terminálu příkazového řádku virtuálního počítače spusťte následující příkazy:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Chcete-li ověřit instalaci, `suricata -h` spusťte příkaz a zobcesezobrazíte úplný seznam příkazů.

### <a name="download-the-emerging-threats-ruleset"></a>Stažení sady pravidel Vznikající hrozby

V této fázi nemáme žádná pravidla pro Suricata spustit. Můžete vytvořit vlastní pravidla, pokud existují konkrétní hrozby pro vaši síť, kterou chcete zjistit, nebo můžete také použít vyvinuté sady pravidel od řady poskytovatelů, jako jsou vznikající hrozby nebo pravidla VRT od společnosti Snort. Používáme volně přístupné emerging threats pravidlo zde:

Stáhněte sadu pravidel a zkopírujte je do adresáře:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Zpracovat zachytávání paketů pomocí suricata

Chcete-li zpracovat zachycení paketů pomocí suricata, spusťte následující příkaz:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Chcete-li zkontrolovat výsledné výstrahy, přečtěte si soubor fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Nastavení elastického zásobníku

Zatímco protokoly, které Suricata vytváří obsahují cenné informace o tom, co se děje v naší síti, tyto soubory protokolu nejsou nejjednodušší číst a pochopit. Propojením Suricata s elastickým zásobníkem můžeme vytvořit řídicí panel Kibana, který nám umožňuje vyhledávat, vytvářet grafy, analyzovat a odvozovat poznatky z našich protokolů.

#### <a name="install-elasticsearch"></a>Instalace elastického vyhledávání

1. Elastický zásobník z verze 5.0 a vyšší vyžaduje Java 8. Spusťte `java -version` příkaz a zkontrolujte svou verzi. Pokud nemáte nainstalovanou java, podívejte se do dokumentace na [Azure-suppored JDKs](https://aka.ms/azure-jdks).

1. Stáhněte si správný binární balíček pro váš systém:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Další způsoby instalace lze nalézt na [Elasticsearch Instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ověřte, zda je pomocí příkazu spuštěno elastické hledání:

    ```
    curl http://127.0.0.1:9200
    ```

    Měli byste vidět odpověď podobnou této:

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

Další pokyny k instalaci elastického vyhledávání naleznete na stránce [Instalace](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalace logstash

1. Chcete-li nainstalovat Logstash spustit následující příkazy:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Dále musíme nakonfigurovat Logstash číst z výstupu souboru eve.json. Vytvořte soubor logstash.conf pomocí:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Přidejte do souboru následující obsah (ujistěte se, že cesta k souboru eve.json je správná):

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

1. Ujistěte se, že udělit správná oprávnění k souboru eve.json tak, aby Logstash můžete ingestovat soubor.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Spuštění příkazu Logstash:

    ```
    sudo /etc/init.d/logstash start
    ```

Další pokyny k instalaci logstash naleznete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalace Kibany

1. Chcete-li nainstalovat kibanu, spusťte následující příkazy:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Chcete-li spustit Kibana, použijte příkazy:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Chcete-li zobrazit webové rozhraní kibana, přejděte na`http://localhost:5601`
1. V tomto scénáři je index vzor použitý pro protokoly Suricata je "logstash-*"

1. Chcete-li zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo nsg umožňující přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvoření řídicího panelu Kibana

V tomto článku jsme vám poskytli ukázkový řídicí panel, ve kterých můžete zobrazit trendy a podrobnosti ve vašich výstrahách.

1. Stáhněte si soubor řídicího panelu [zde](https://aka.ms/networkwatchersuricatadashboard), vizualizační soubor [zde](https://aka.ms/networkwatchersuricatavisualization)a uložený vyhledávací soubor [zde](https://aka.ms/networkwatchersuricatasavedsearch).

1. Na kartě **Správa** v Kibaně přejděte na **Uložené objekty** a importujte všechny tři soubory. Potom můžete na kartě **Řídicí panel** otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely přizpůsobené metrikám vašeho vlastního zájmu. Přečtěte si více o vytváření vizualizací Kibana z [oficiální dokumentace](https://www.elastic.co/guide/en/kibana/current/visualize.html)Kibany .

![řídicí panel kibana][2]

### <a name="visualize-ids-alert-logs"></a>Vizualizace protokolů výstrah IDS

Ukázkový řídicí panel poskytuje několik vizualizací protokolů výstrah Suricata:

1. Upozornění geoip - mapa zobrazující rozdělení záznamů podle jejich země / oblasti původu na základě zeměpisné polohy (určeno IP)

    ![geo ip][3]

1. Top 10 Upozornění - souhrn 10 nejčastějších aktivovaných výstrah a jejich popis. Kliknutím na jednotlivé výstrahy filtrujete řídicí panel na informace týkající se dané konkrétní výstrahy.

    ![obrázek 4][4]

1. Počet výstrah – celkový počet výstrah aktivovaných sadou pravidel

    ![obrázek 5][5]

1. Prvních 20 zdrojových/cílových IP adresaí/portů – výsečové grafy zobrazující 20 nejlepších IP adresy a porty, na kterých byly spuštěny výstrahy. Můžete filtrovat dolů na konkrétní IP adresy /porty zobrazíte, kolik a jaký druh výstrah y jsou spouštěny.

    ![obrázek 6][6]

1. Souhrn výstrah – tabulka shrnující konkrétní podrobnosti jednotlivých výstrah. Tuto tabulku můžete přizpůsobit tak, aby zobrazovala další parametry zájmu pro každou výstrahu.

    ![obrázek 7][7]

Další dokumentaci k vytváření vlastních vizualizací a řídicích panelů naleznete [v oficiální dokumentaci společnosti Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Závěr

Kombinací sběru paketů poskytovaného nástrojem Sledování sítě a nástroji IDS s otevřeným zdrojovým kódem, jako je například Suricata, můžete provádět zjišťování narušení sítě pro širokou škálu hrozeb. Tyto řídicí panely umožňují rychle rozpoznat trendy a anomálie v rámci sítě a také prohledejte data a zjišťujte hlavní příčiny výstrah, jako jsou agenti uživatelů se zlými úmysly nebo zranitelné porty. Pomocí těchto extrahovaných dat můžete činit informovaná rozhodnutí o tom, jak reagovat na síť a chránit ji před škodlivými pokusy o vniknutí, a vytvořit pravidla, která zabrání budoucím vniknutí do sítě.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak aktivovat sběr paketů na základě výstrah na stránce [Použití sběru paketů k proaktivnímu monitorování sítě pomocí funkcí Azure](network-watcher-alert-triggered-packet-capture.md)

Přečtěte si, jak vizualizovat protokoly toku nsg pomocí Power BI na stránce [Visualize Protokoly toků nsg pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
