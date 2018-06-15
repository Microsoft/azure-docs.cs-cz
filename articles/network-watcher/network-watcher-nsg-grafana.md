---
title: Správa sítě skupiny toku protokolů zabezpečení pomocí sledovací proces sítě a Grafana | Microsoft Docs
description: Spravovat a analýzu síťových zabezpečení skupiny toku protokolů v Azure pomocí sledovací proces sítě a Grafana.
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864088"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Spravovat a analýza protokolů toku skupinu zabezpečení sítě pomocí sledovací proces sítě a Grafana

[Síťové protokoly toku skupina zabezpečení (NSG)](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které slouží k pochopení příchozí a odchozí přenosy IP na síťová rozhraní. Tyto protokoly toku zobrazit příchozí a odchozí toky na na základě pravidla NSG, síťový adaptér toku platí pro 5 řazené kolekce členů informace o toku (protokol IP zdroj nebo cíl, zdrojový nebo cílový Port), a pokud se povoluje nebo odepírá provoz.

Velký počet skupin Nsg můžete mít v síti s protokolování toku. Toto množství dat protokolování je náročná analyzovat a získáte přehled o z protokolů. Tento článek poskytuje řešení můžete centrálně spravovat tyto protokoly toku NSG pomocí Grafana, typu open source vytváření grafů nástroj, ElasticSearch, distribuované vyhledávání a stroj analýzy a Logstash, což je kanál s otevřeným zdrojem zpracování dat na straně serveru.  

## <a name="scenario"></a>Scénář

Tok protokolů NSG jsou povolené pomocí sledovací proces sítě a jsou uložené v úložišti objektů blob Azure. Modul plug-in Logstash slouží k připojení a zpracovávat toku protokolů z úložiště objektů blob a posílat je ElasticSearch.  Po toku protokoly jsou uloženy v ElasticSearch, může být analyzován a vizualizována do přizpůsobené řídicí panely v Grafana.

![Grafana sledovací proces sítě NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Postup instalace

### <a name="enable-network-security-group-flow-logging"></a>Protokolování toku povolit skupinu zabezpečení sítě

V tomto scénáři musí mít síťové zabezpečení skupiny toku protokolování zapnuta alespoň jednu skupinu zabezpečení sítě ve vašem účtu. Pokyny k povolení protokolů toku zabezpečení sítě, naleznete v následujícím článku [Úvod do toku protokolování pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Aspekty instalace

V tomto příkladu Grafana, ElasticSearch a Logstash nakonfigurované na serveru LTS 16.04 Ubuntu nasazené v Azure. Tato minimální instalace se používá pro spuštění všechny tři součásti – všechny používají stejný virtuálního počítače. Tato instalace musí být použit pouze pro testování a méně důležité úlohy. Logstash, Elasticsearch a Grafana můžete navržen nezávisle škálovat napříč velký počet instancí. Další informace naleznete v dokumentaci pro každou z těchto součástí.

### <a name="install-logstash"></a>Nainstalujte Logstash

Logstash použijete k vyrovnání protokoly toku formátu JSON na úroveň toku řazené kolekce členů.

1. Chcete-li nainstalovat Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Nakonfigurujte Logstash poslat ElasticSearch a analyzovat protokoly toku. Vytvoření souboru Logstash.conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Do souboru přidejte následující obsah. Změna účtu úložiště názvem a přístupovým klíč tak, aby odrážela údaje o vašem účtu úložiště:

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

Zadaný soubor konfigurace Logstash se skládá ze tří částí: vstupní, výstupní a filtr. Části vstupní označí vstupní zdroj protokoly, které bude zpracovávat Logstash – v tomto případě jsme budete používat "azureblob" vstupní modulu plug-in (nainstalovanou v dalších krocích), vám umožní nám pro přístup k NSG toku protokolu JSON soubory uložené v úložišti objektů blob. 

Část Filtr pak sloučí každý soubor protokolu toku tak, aby každá řazená kolekce členů jednotlivých toku a jeho přidružené vlastnosti stalo samostatná událost Logstash.

Nakonec části výstup předává všechny události Logstash ElasticSearch server. Libosti změnit Logstash konfigurační soubor, který vašim potřebám.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Nainstalujte modul plug-in vstupní Logstash pro úložiště objektů Blob v Azure

Tento modul plug-in Logstash umožňuje přímý přístup k toku protokoly ze svého účtu úložiště určený objekt blob. Tento modul plug-in, instalace z výchozí Logstash instalační adresář (v této případu /usr/share/logstash/bin) spusťte příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o této moduly v najdete v tématu [Logstash vstupní modul plug-in pro Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Nainstalujte ElasticSearch

Chcete-li nainstalovat ElasticSearch můžete použít následující skript. Informace o instalaci ElasticSearch najdete v tématu [elastické zásobníku](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Nainstalujte Grafana

K instalaci a spuštění Grafana, spusťte následující příkazy:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Další informace o instalaci, najdete v části [instalace na Debian / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Přidejte ElasticSearch server jako zdroj dat

Dále musíte přidat index ElasticSearch jako zdroj dat obsahující protokoly toku. Zdroj dat můžete přidat výběrem **zdroj dat přidat** a dokončení formulář s příslušné informace. Ukázka tuto konfiguraci najdete na následujícím snímku obrazovky:

![Přidání zdroje dat](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Vytvořit řídicí panel

Teď, když jste nakonfigurovali úspěšně Grafana číst z indexu ElasticSearch obsahující toku protokolů NSG, můžete vytvořit a přizpůsobení řídicí panely. Chcete-li vytvořit nový řídicí panel, vyberte **vytvořit první řídicí panel**. Následující ukázkové konfiguraci grafu se zobrazuje toky segmentované podle pravidla NSG:

![Řídicí panel grafu](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Následující snímek obrazovky znázorňuje grafu a graf zobrazující hlavní toky a jejich četnost. Toky jsou také uvedené podle pravidla NSG a pro toky podle rozhodnutí. Grafana je vysoce přizpůsobitelné, takže se doporučuje vytvořit řídicí panely tak, aby vyhovovala vašim konkrétním potřebám monitorování. Následující příklad ukazuje typické řídicí panel:

![Řídicí panel grafu](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Závěr

Díky integraci sledovací proces sítě se ElasticSearch a Grafana, nyní máte pohodlný a centralizovaný způsob, jak spravovat a vizualizovat toku protokolů NSG, jakož i další data. Grafana má několik dalších výkonné grafovým funkcí, které lze použít také pro další správu toku protokolů a lépe pochopit síťový provoz. Teď, když máte Grafana instance nastavení a připojení k Azure, klidně si pokračovat v průzkumu další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

- Další informace o používání [sledovací proces sítě](network-watcher-monitoring-overview.md).

