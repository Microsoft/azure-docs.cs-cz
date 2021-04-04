---
title: Zjišťování neoprávněných vniknutí k síti pomocí nástrojů Open Source
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí Azure Network Watcher a open source Tools provádět zjišťování neoprávněných vniknutí do sítě.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014548"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Zjišťování neoprávněných vniknutí k síti pomocí Network Watcher a open source nástrojů

Zachytávání paketů představují klíčovou komponentu pro implementaci systémů zjišťování neoprávněných vniknutí k síti (ID) a provádění monitorování zabezpečení sítě (NSM). Existuje několik nástrojů Open Source ID, které zpracovávají pakety a vyhledávají signatury možných vniknutí do sítě a škodlivých aktivit. Pomocí zachytávání paketů poskytovaných Network Watcher můžete analyzovat síť pro jakékoli škodlivé napadení nebo ohrožení zabezpečení.

Jedním z těchto open source nástrojů je Suricata, modul pro identifikaci, který používá RuleSets k monitorování síťového provozu a aktivuje výstrahy vždy, když dojde k podezřelým událostem. Suricata nabízí vícevláknový modul, což znamená, že může provádět analýzu síťového provozu se zvýšenou rychlostí a efektivitou. Další podrobnosti o Suricata a jejích funkcích najdete na webu na adrese https://suricata-ids.org/ .

## <a name="scenario"></a>Scenario

Tento článek vysvětluje, jak nastavit prostředí pro provádění zjišťování neoprávněných vniknutí k síti pomocí Network Watcher, Suricata a elastického zásobníku. Network Watcher poskytuje zachytávání paketů používaných k provádění zjišťování neoprávněných vniknutí do sítě. Suricata zpracovává pakety a aktivuje výstrahy na základě paketů, které odpovídají daným RuleSet hrozbám. Tyto výstrahy se ukládají do souboru protokolu na místním počítači. Pomocí elastického zásobníku se protokoly vygenerované Suricata můžou indexovat a používat k vytvoření řídicího panelu Kibana, který vám poskytne vizuální znázornění protokolů a způsob, jak rychle získat přehled o potenciálních ohroženích zabezpečení sítě.  

![jednoduchý scénář webových aplikací][1]

Na VIRTUÁLNÍm počítači Azure je možné nastavit jak Open Source nástroje, což vám umožní provádět tuto analýzu v rámci vlastního síťového prostředí Azure.

## <a name="steps"></a>Postup

### <a name="install-suricata"></a>Nainstalovat Suricata

Všechny ostatní metody instalace najdete na stránce https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. V terminálu příkazového řádku pro virtuální počítač spusťte následující příkazy:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Chcete-li ověřit instalaci, spusťte příkaz, `suricata -h` který zobrazí úplný seznam příkazů.

### <a name="download-the-emerging-threats-ruleset"></a>Stáhněte si nově vznikající hrozby RuleSet

V této fázi nemáme žádná pravidla, která by Suricata běžet. Můžete vytvořit vlastní pravidla, pokud máte konkrétní hrozby vaší sítě, které chcete zjistit, nebo můžete použít také vytvořené sady pravidel z řady poskytovatelů, jako jsou nově vznikající hrozby nebo pravidla VRT z Snort. Využijeme volně přístupné nově vznikající hrozby RuleSet:

Stáhněte sadu pravidel a zkopírujte je do adresáře:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Zpracování zachycených paketů pomocí Suricata

Pokud chcete zpracovat zachycení paketů pomocí Suricata, spusťte následující příkaz:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Chcete-li zjistit výsledné výstrahy, přečtěte si soubor Fast. log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Nastavení elastického zásobníku

I když protokoly, které Suricata vytváří, obsahují cenné informace o tom, co se děje v naší síti, tyto soubory protokolu nejsou nejjednodušší ke čtení a pochopení. Připojením Suricata pomocí elastického zásobníku můžeme vytvořit řídicí panel Kibana, který nám umožní Hledat, grafy, analyzovat a odvodit přehledy z našich protokolů.

#### <a name="install-elasticsearch"></a>Nainstalovat Elasticsearch

1. Elastická sada z verze 5,0 a vyšší vyžaduje Java 8. Spusťte příkaz `java -version` a ověřte svou verzi. Pokud nemáte nainstalovaný Java, přečtěte si dokumentaci k [Azure-suppored sady JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support).

1. Stáhněte si správný binární balíček pro váš systém:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Další metody instalace najdete v [Elasticsearch instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) .

1. Ověřte, že je Elasticsearch spuštěný s příkazem:

    ```
    curl http://127.0.0.1:9200
    ```

    Měla by se zobrazit odpověď podobná této:

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

Další pokyny k instalaci elastického vyhledávání najdete v tématu [instalace](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) stránky.

### <a name="install-logstash"></a>Nainstalovat Logstash

1. Pro instalaci Logstash spusťte následující příkazy:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Dál musíme nakonfigurovat Logstash pro čtení z výstupu eve.jsv souboru. Vytvořte soubor logstash. conf pomocí:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Do souboru přidejte následující obsah (Ujistěte se, že je cesta k eve.jsv souboru správná):

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

1. Ujistěte se, že máte správná oprávnění k eve.jsv souboru, aby Logstash mohl soubor ingestovat.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Spuštění Logstash spustíte příkazem:

    ```
    sudo /etc/init.d/logstash start
    ```

Další pokyny k instalaci Logstash najdete v [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) .

### <a name="install-kibana"></a>Instalace Kibany

1. Spusťte následující příkazy a nainstalujte Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Pro spuštění Kibana použijte příkazy:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Pokud chcete zobrazit webové rozhraní Kibana, přejděte na `http://localhost:5601`
1. Pro tento scénář je vzor indexu použitý pro protokoly Suricata "logstash-*".

1. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvořte příchozí pravidlo NSG, které umožní přístup k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvoření řídicího panelu Kibana

Pro tento článek poskytujeme ukázkový řídicí panel, který vám umožní zobrazit trendy a podrobnosti v upozorněních.

1. Sem stáhněte [soubor s](https://aka.ms/networkwatchersuricatavisualization)řídicím panelem [, soubor](https://aka.ms/networkwatchersuricatadashboard)vizualizace a uložený soubor [hledání.](https://aka.ms/networkwatchersuricatasavedsearch)

1. Na kartě **Správa** Kibana přejděte na **uložené objekty** a importujte všechny tři soubory. Pak na kartě **řídicí panel** můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a řídicí panely, které jsou přizpůsobené všem metrikám svého vlastního zájmu. Přečtěte si další informace o vytváření vizualizací Kibana z [oficiální dokumentace](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)Kibana.

![řídicí panel kibana][2]

### <a name="visualize-ids-alert-logs"></a>Vizualizace protokolů výstrah ID

Vzorový řídicí panel nabízí několik vizualizací protokolů výstrah Suricata:

1. Výstrahy podle GeoIP – mapa znázorňující distribuci výstrah podle jejich země nebo oblasti původu na základě geografického umístění (určeného IP adresou)

    ![Geografická IP adresa][3]

1. 10 nejčastějších výstrah – Shrnutí 10 nejčastějších výstrah a jejich popis. Kliknutím na jednotlivé výstrahy filtrujete řídicí panel na informace týkající se konkrétní výstrahy.

    ![Obrázek 4][4]

1. Počet výstrah – celkový počet výstrah aktivovaných RuleSet

    ![Obrázek 5][5]

1. Prvních 20 zdrojů/cílových IP adres/portů – výsečové grafy zobrazující prvních 20 IP adres a portů, na kterých se aktivovaly výstrahy. Můžete vyfiltrovat konkrétní IP adresy nebo porty a zjistit, kolik výstrah se spouští.

    ![Obrázek 6][6]

1. Souhrn výstrah – tabulka shrnující konkrétní podrobnosti o jednotlivých výstrahách. Tuto tabulku můžete přizpůsobit tak, aby zobrazovala další parametry zájmu každé výstrahy.

    ![Obrázek 7][7]

Další dokumentaci k vytváření vlastních vizualizací a řídicích panelů najdete v článku o [oficiální dokumentaci k Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Závěr

Díky kombinování zachycení paketů poskytovaných nástroji Network Watcher a open source ID, jako je Suricata, můžete zjišťovat neoprávněné vniknutí k síti pro nejrůznější hrozby. Tyto řídicí panely umožňují rychle odhalit trendy a anomálie v rámci vaší sítě a také dig data, abyste zjistili hlavní příčiny výstrah, jako jsou například zlomyslní uživatelé nebo zranitelné porty. Tato extrahovaná data vám umožní informovat o tom, jak reagovat a chránit vaši síť před škodlivými pokusy o vniknutí, a vytvořit pravidla, která zabraňují budoucímu vniknutí do vaší sítě.

## <a name="next-steps"></a>Další kroky

Naučte se, jak aktivovat zachycení paketů na základě výstrah, pomocí [zachytávání paketů, pomocí kterého provedete proaktivní monitorování sítě pomocí Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Naučte se vizualizovat protokoly toku NSG pomocí Power BI tím, že navštívíte [vizualizace NSG toků protokolů s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png