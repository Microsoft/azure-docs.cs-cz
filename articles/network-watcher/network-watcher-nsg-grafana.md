---
title: Správa protokolů toku NSG pomocí Grafana
titleSuffix: Azure Network Watcher
description: Spravujte a analyzujte protokoly toku skupin zabezpečení sítě v Azure pomocí Network Watcher a Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656304"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Správa a analýza protokolů toku skupiny zabezpečení sítě pomocí Network Watcher a Grafana

[Protokoly toku pro skupinu zabezpečení sítě (NSG)](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které se dají použít k pochopení příchozího a odchozího provozu IP na síťových rozhraních. Tyto protokoly toku ukazují odchozí a příchozí toky na základě pravidla pro NSG, síťové rozhraní, ke kterému se tok vztahuje, 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolen nebo odepřen.

V síti můžete mít mnoho skupin zabezpečení sítě s povoleným protokolováním toků. Toto množství dat protokolování umožňuje nenáročným analyzovat a získávat poznatky z protokolů. Tento článek poskytuje řešení centrální správy těchto protokolů toku NSG pomocí Grafana, open source nástroje pro vytváření grafů, ElasticSearch, distribuovaného vyhledávacího a analytického stroje a Logstash, což je open source kanál pro zpracování dat na straně serveru.  

## <a name="scenario"></a>Scenario

Protokoly toku NSG jsou povolené pomocí Network Watcher a jsou uložené v úložišti objektů BLOB v Azure. Modul plug-in Logstash se používá k připojení a zpracování protokolů toku ze služby Blob Storage a jejich posílání do ElasticSearch.  Jakmile se protokoly toků ukládají v ElasticSearch, dají se analyzovat a vizuálně přizpůsobovat do přizpůsobených řídicích panelů v Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroky instalace

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupin zabezpečení sítě

V tomto scénáři musíte mít povolené protokolování toku skupin zabezpečení sítě ve vašem účtu aspoň v jedné skupině zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě najdete v následujícím článku [Úvod do protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Co je třeba zvážit při nastavení

V tomto příkladu jsou Grafana, ElasticSearch a Logstash nakonfigurované na serveru Ubuntu 16,04 LTS nasazeném v Azure. Tato minimální instalace se používá pro spouštění všech tří komponent – všechny jsou spuštěné na stejném virtuálním počítači. Toto nastavení by se mělo používat jenom pro testování a méně důležité úlohy. Logstash, Elasticsearch a Grafana se dají navrhovat tak, aby se nezávisle škálovat napříč mnoha instancemi. Další informace najdete v dokumentaci pro každou z těchto součástí.

### <a name="install-logstash"></a>Nainstalovat Logstash

Pomocí Logstash můžete shrnout protokoly toku ve formátu JSON do úrovně řazené kolekce členů toku.

1. Pokud chcete nainstalovat Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Nakonfigurujte Logstash k analýze protokolů toku a jejich odeslání do ElasticSearch. Vytvořte soubor Logstash. conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Do souboru přidejte následující obsah. Změňte název účtu úložiště a přístupový klíč tak, aby odrážely podrobnosti účtu úložiště:

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
    "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
    "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
    "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
    "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
    "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
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

Zadaný konfigurační soubor Logstash se skládá ze tří částí: vstup, filtr a výstup.
Vstupní oddíl určuje vstupní zdroj protokolů, které Logstash zpracuje – v tomto případě budeme používat vstupní modul plug-in azureblobu (nainstalovaný v dalších krocích), který nám umožní přístup k souborům JSON protokolu toku NSG uloženým v úložišti objektů BLOB. 

Oddíl Filter pak sloučí jednotlivé soubory protokolu toku, aby se jednotlivé řazené kolekce členů toku a jeho přidružených vlastností staly samostatnou událostí Logstash.

Nakonec oddíl Output předá každou událost Logstash serveru ElasticSearch. Můžete si klidně upravit konfigurační soubor Logstash tak, aby vyhovoval vašim konkrétním potřebám.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalace vstupního modulu plug-in Logstash pro úložiště objektů BLOB v Azure

Tento modul plug-in Logstash vám umožňuje přímý přístup k protokolům toků z určeného účtu úložiště BLOB. Pokud chcete tento modul plug-in nainstalovat z výchozího instalačního adresáře Logstash (v tomto případě/usr/share/logstash/bin), spusťte příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto modulu plug-in najdete v tématu [Vstupní modul plug-in Logstash pro objekty blob Azure Storage](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Nainstalovat ElasticSearch

K instalaci ElasticSearch můžete použít následující skript. Informace o instalaci ElasticSearch najdete v tématu [elastický zásobník](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Nainstalovat Grafana

Pokud chcete nainstalovat a spustit Grafana, spusťte následující příkazy:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Další informace o instalaci najdete v tématu [instalace na Debian/Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Přidání serveru ElasticSearch jako zdroje dat

Dále je nutné přidat index ElasticSearch obsahující protokoly toku jako zdroj dat. Zdroj dat můžete přidat výběrem možnosti **Přidat zdroj dat** a vyplněním formuláře odpovídajícími informacemi. Ukázku této konfigurace najdete na následujícím snímku obrazovky:

![Přidání zdroje dat](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Teď, když jste úspěšně nakonfigurovali Grafana ke čtení z indexu ElasticSearch obsahujícího protokoly NSG flow, můžete vytvářet a přizpůsobovat řídicí panely. Pokud chcete vytvořit nový řídicí panel, vyberte **vytvořit první řídicí panel**. Následující ukázka konfigurace grafu zobrazuje toky segmentované podle NSG pravidla:

![Graf řídicích panelů](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Následující snímek obrazovky znázorňuje graf a graf znázorňující horní toky a jejich četnost. Toky také ukazují pravidlo NSG a toky podle rozhodnutí. Grafana je vysoce přizpůsobitelná, takže je vhodné vytvořit řídicí panely, které budou vyhovovat vašim konkrétním požadavkům na monitorování. Následující příklad ukazuje typický řídicí panel:

![Snímek obrazovky znázorňující konfiguraci ukázkového grafu s toky segmentované podle NSG pravidla.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Závěr

Integrací Network Watcher s ElasticSearch a Grafana teď máte pohodlný a centralizovaný způsob správy a vizualizace protokolů toku NSG i dalších dat. Grafana má řadu dalších výkonných funkcí pro grafy, které se dají použít i k dalšímu správě protokolů Flow a k lepšímu pochopení síťového provozu. Teď, když máte nanastavenou instanci Grafana a připojili se k Azure, můžete si i nadále prozkoumat další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o použití [Network Watcher](network-watcher-monitoring-overview.md).

