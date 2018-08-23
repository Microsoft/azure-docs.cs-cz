---
title: Správa protokoly skupiny zabezpečení sítě tok pomocí Network Watcheru a Grafany | Dokumentace Microsoftu
description: Správa a analyzovat protokoly skupiny zabezpečení sítě tok v Azure s využitím Network Watcheru a Grafany.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: e375476536e7fe150e3aabcae7cee942deac02d5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057523"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Správa a analýza protokolů toku skupin zabezpečení sítě s využitím Network Watcheru a Grafany

[Síťové protokoly toků skupin zabezpečení (NSG)](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které lze použít k pochopení příchozí a odchozí přenosy na síťových rozhraních. Tyto protokoly toku zobrazení odchozí a příchozí toků na jednotlivé pravidlo skupiny zabezpečení sítě síťového rozhraní tok vztahuje na 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a jestli byl povolený nebo zakázaný provoz.

Může mít mnoho skupin zabezpečení sítě v síti s povoleno protokolování toků. Díky takové množství dat protokolování náročná analyzovat a zkoumat velké vaše protokoly. Tento článek obsahuje některé řešení a centrálně spravovat tyto protokoly toků NSG pomocí služby Grafana, open source grafické zobrazení nástroje, ElasticSearch, distribuované vyhledávání a analytickém modulu a Logstash, což je kanál opensourcových zpracování dat na straně serveru.  

## <a name="scenario"></a>Scénář

Protokoly toků NSG jsou povolené pomocí Network Watcheru a jsou uloženy v úložišti objektů blob v Azure. Modul plug-in se používá k připojení a zpracování protokolů toku z úložiště objektů blob a odeslat je do ElasticSearch.  Jakmile tok protokoly se ukládají do ElasticSearch, mohou být analyzovat a vizualizovat do přizpůsobené řídicí panely v Grafana.

![Skupina zabezpečení sítě Grafana sledovací proces sítě](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Postup instalace

### <a name="enable-network-security-group-flow-logging"></a>Protokolování toků povolit skupiny zabezpečení sítě

V tomto scénáři musí mít síťové zabezpečení skupiny tok protokolování povoleno na alespoň jednu skupinu zabezpečení sítě ve vašem účtu. Pokyny k povolení protokolů toku zabezpečení sítě, najdete v následujícím článku [Úvod k protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Důležité informace o nastavení

V tomto příkladu jsou Grafana a ElasticSearch, Logstash nakonfigurované na serveru se systémem Ubuntu 16.04 LTS nasazené v Azure. Tato minimální instalaci se používají pro spouštění všechny tři komponenty – všechny jsou spuštěné na stejném virtuálním počítači. Toto nastavení slouží pouze pro testování a méně náročné úlohy. Grafana, Logstash a Elasticsearch můžete navržený nezávisle škálovat napříč velký počet instancí. Další informace najdete v dokumentaci pro jednotlivé komponenty.

### <a name="install-logstash"></a>Instalace Logstash

Pomocí Logstash sloučit protokolů toku ve formátu JSON na úroveň tok řazené kolekce členů.

1. Pokud chcete nainstalovat Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurace Logstash pro analýzu protokolů toku a odešlete do ElasticSearch. Vytvoření souboru Logstash.conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Přidejte následující obsah do souboru. Změníte klíč účtu úložiště název a přístup tak, aby odrážely podrobnosti o vašem účtu úložiště:

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

Konfigurační soubor Logstash k dispozici se skládá ze tří částí: vstup, filtrovat a výstup.
Vstupní části určí vstupní zdroj protokoly, které bude zpracovávat Logstash – v takovém případě budeme používat "azureblob" vstupu modulu plug-in (nainstalované v dalších krocích), které vám umožní, abychom soubory JSON protokolů toku NSG uložená v blob storage. 

Část Filtr pak sloučí soubor protokolu každý tok tak, aby každá řazená kolekce členů jednotlivé toku a jeho přidružených vlastností samostatná událost Logstash.

Nakonec výstupní sekce předává každou událost Logstash k ElasticSearch serveru. Teď můžete upravit konfigurační soubor Logstash tak, aby vyhovovala vašim konkrétním potřebám.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Nainstalujte modul plug-in vstupu pro úložiště objektů Blob v Azure

Tento modul plug-in pro Logstash umožňuje přímý přístup k protokolů toku ze svého účtu úložiště objektů blob v určené. Chcete-li nainstalovat tento modul plug-in, z instalace Logstash výchozí adresář (v tomto případu /usr/share/logstash/bin) spusťte příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto plug v najdete v tématu [vstupní modul plug-in pro Logstash pro Azure Storage blob](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalace ElasticSearch

Instalace ElasticSearch, můžete použít následující skript. Informace o instalaci ElasticSearch, naleznete v tématu [řešení Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Instalace služby Grafana

K instalaci a spuštění Grafana, spusťte následující příkazy:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Další informace o instalaci, naleznete v tématu [instalace v Debianu / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Přidání serveru funkce ElasticSearch jako zdroj dat

Dále je třeba přidat index funkce ElasticSearch jako zdroj dat obsahující protokolů toku. Zdroj dat můžete přidat tak, že vyberete **přidat zdroj dat** a vyplnění formuláře příslušné informace. Ukázku této konfigurace najdete na následujícím snímku obrazovky:

![Přidání zdroje dat](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Teď, když jste úspěšně nakonfigurovali Grafana ke čtení z protokolů toku NSG obsahující indexu ElasticSearch, můžete vytvořit a přizpůsobit řídicí panely. Chcete-li vytvořit nový řídicí panel, vyberte **vytvořit svůj první řídicí panel**. V následující ukázkové konfiguraci graf ukazuje toky segmentované podle pravidla skupiny zabezpečení sítě:

![Graf na řídicí panel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Následující snímek obrazovky znázorňuje grafu a graf zobrazující hlavní toků a jejich četnost. Toky jsou také zobrazeny pravidlem NSG a toky podle rozhodnutí. Grafana je vysoce přizpůsobitelné, takže je vhodné vytvořit řídicí panely tak, aby vyhovovala vašim konkrétním potřebám monitorování. Následující příklad ukazuje typické řídicího panelu:

![Graf na řídicí panel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Závěr

Díky integraci služby Network Watcher s ElasticSearch a Grafana, máte teď způsob pohodlný a centralizovanou správu a vizualizaci protokolů toku NSG, jakož i další data. Grafana má několik dalších efektivních funkcí grafovým se taky dají na další správa protokolů toku a až lépe porozumíte provozu vaší sítě. Teď, když máte instanci Grafana nastavení a připojení k Azure a bez obav pokračovat a prozkoumejte další funkce, které nabízí.

## <a name="next-steps"></a>Další postup

- Další informace o používání [Network Watcher](network-watcher-monitoring-overview.md).

