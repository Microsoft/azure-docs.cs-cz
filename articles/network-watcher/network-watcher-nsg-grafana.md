---
title: Správa protokolů toku nsg pomocí Grafany
titleSuffix: Azure Network Watcher
description: Spravujte a analyzujte protokoly toku skupiny zabezpečení sítě v Azure pomocí Sledování sítě a Grafany.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840906"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Správa a analýza protokolů toku skupiny zabezpečení sítě pomocí sledování sítě a grafany

[Protokoly toku skupiny Zabezpečení sítě (NSG)](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které lze použít k pochopení příchozího přenosu dat a odchozího přenosu IP v síťových rozhraních. Tyto protokoly toku zobrazit odchozí a příchozí toky na základě pravidla sítě nsg, nic toku se vztahuje na, 5 řazené kolekce členů informace o toku (zdroj/cíl IP, zdroj/cílový port, protokol), a pokud byl povolen provoz nebo odepřen.

> [!Warning]  
> Následující kroky pracují s protokoly toku verze 1. Podrobnosti naleznete [v tématu Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

Můžete mít mnoho nsgs v síti s protokolování toku povoleno. Toto množství dat protokolování je těžkopádné analyzovat a získat přehledy z protokolů. Tento článek poskytuje řešení pro centrální správu těchto protokolů toku skupiny NSG pomocí Grafana, open source grafů nástroj, ElasticSearch, distribuované vyhledávání a analýzy motoru a Logstash, což je kanál zpracování dat na straně serveru s otevřeným zdrojovým kódem.  

## <a name="scenario"></a>Scénář

Protokoly toku nsg jsou povolené pomocí Network Watcher a jsou uloženy v úložišti objektů blob Azure. Modul plug-in Logstash se používá k připojení a zpracování protokolů toku z úložiště objektů blob a jejich odeslání do elastického vyhledávání.  Jakmile jsou protokoly toku uloženy v elastickém hledání, lze je analyzovat a vizualizovat do přizpůsobených řídicích panelů v Grafaně.

![Pozorovatel sítě NSG Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroky instalace

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupiny zabezpečení sítě

V tomto scénáři musíte mít v účtu povoleno protokolování toku skupiny zabezpečení sítě alespoň v jedné skupině zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě naleznete v následujícím článku [Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Co je třeba zvážit při nastavení

V tomto příkladu grafana, elastické vyhledávání a Logstash jsou nakonfigurovány na Ubuntu 16.04 LTS Server nasazený v Azure. Toto minimální nastavení se používá pro spuštění všech tří součástí – všechny jsou spuštěny na stejném virtuálním počítači. Toto nastavení by mělo být použito pouze pro testování a nekritické úlohy. Logstash, Elasticsearch a Grafana lze navrhovat tak, aby se škálovaly nezávisle na mnoha instancích. Další informace naleznete v dokumentaci ke každé z těchto součástí.

### <a name="install-logstash"></a>Instalace logstash

Logstash slouží ke sloučení protokolů toku formátovaných JSON na úroveň n-tice toku.

1. Chcete-li nainstalovat protokol Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Nakonfigurujte Logstash analyzovat protokoly toku a odeslat je do ElasticSearch. Vytvořte soubor Logstash.conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Přidejte do souboru následující obsah. Změňte název účtu úložiště a přístupový klíč tak, aby odrážely podrobnosti o účtu úložiště:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

Zadaný konfigurační soubor Logstash se skládá ze tří částí: vstupu, filtru a výstupu.
Vstupní část označuje vstupní zdroj protokolů, které bude Protokolstash zpracovávat – v tomto případě použijeme vstupní plugin "azureblob" (nainstalovaný v dalších krocích), který nám umožní přístup k souborům JSON protokolu toku NSG uloženým v úložišti objektů blob. 

Část filtru pak sloučí každý soubor protokolu toku tak, aby se každá jednotlivá řazená kolekce členů toku a její přidružené vlastnosti stala samostatnou událostí Logstash.

Nakonec výstupní část předá každou událost Logstash na server ElasticSearch. Nebojte se upravit soubor konfiguračnísoubor Logstash tak, aby vyhovoval vašim specifickým potřebám.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalace vstupního modulu plug-in Logstash pro úložiště objektů Blob Azure

Tento modul plug-in Logstash umožňuje přímý přístup k protokolům toku z jejich určeného účtu úložiště objektů blob. Chcete-li nainstalovat tento modul plug-in, spusťte z výchozího instalačního adresáře Logstash (v tomto případě /usr/share/logstash/bin) příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto modulu plug-in najdete v [tématu Logstash vstupní plugin pro Objekty BLOB úložiště Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalace elastického vyhledávání

K instalaci elastického vyhledávání můžete použít následující skript. Informace o instalaci elastického hledání naleznete v [tématu Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Instalace Grafany

Chcete-li nainstalovat a spustit grafanu, spusťte následující příkazy:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Další informace o instalaci naleznete [v tématu Instalace na Debian / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Přidání serveru ElasticSearch jako zdroje dat

Dále je třeba přidat elastické hledání indexu obsahující protokoly toku jako zdroj dat. Zdroj dat můžete přidat tak, že vyberete **Přidat zdroj dat** a doplníte formulář s příslušnými informacemi. Ukázku této konfigurace naleznete na následujícím snímku obrazovky:

![Přidání zdroje dat](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Nyní, když jste úspěšně nakonfigurovali Grafana číst z indexu ElasticSearch obsahující protokoly toku skupiny NSG, můžete vytvořit a přizpůsobit řídicí panely. Pokud chcete vytvořit nový řídicí panel, vyberte **Vytvořit první řídicí panel**. Následující ukázková konfigurace grafu zobrazuje toky segmentované podle pravidla nsg:

![Graf řídicího panelu](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Následující snímek obrazovky znázorňuje graf a graf znázorňující horní toky a jejich frekvenci. Toky jsou také zobrazeny pravidlem nsg a toky rozhodnutím. Grafana je vysoce přizpůsobitelná, takže je vhodné vytvořit řídicí panely, které vyhovují vašim specifickým potřebám monitorování. Následující příklad ukazuje typický řídicí panel:

![Graf řídicího panelu](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Závěr

Integrací sledování sítě s elasticsearch a Grafana, nyní máte pohodlný a centralizovaný způsob, jak spravovat a vizualizovat protokoly toku NSG, stejně jako další data. Grafana má řadu dalších výkonných funkcí pro grafy, které lze také použít k další správě protokolů toku a k lepšímu pochopení síťového provozu. Teď, když máte instanci Grafana nastavenou a připojenou k Azure, můžete dál zkoumat další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

- Další informace o používání [služby Network Watcher](network-watcher-monitoring-overview.md).

