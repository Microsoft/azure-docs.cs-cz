---
title: Vizualizace protokolů toku NSG sledovací proces sítě Azure pomocí nástroje s otevřeným zdrojem | Microsoft Docs
description: Tato stránka popisuje, jak použít open source nástroje k vizualizaci toku protokolů NSG.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f7d51352aa8411e36f4224804c90c2554d4ef9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394166"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Vizualizace protokolů toku NSG sledovací proces sítě Azure pomocí nástroje s otevřeným zdrojem

Skupina zabezpečení sítě toku protokoly poskytují informace, které můžete použít pochopit příchozí a odchozí přenosy IP na skupiny zabezpečení sítě. Tyto protokoly toku zobrazit příchozí a odchozí toky na základě na pravidlo, tok se vztahuje na síťový adaptér, 5 řazené kolekce členů informace o toku (zdrojové nebo cílové IP adresy, zdrojový nebo cílový Port, protokol), a pokud se povoluje nebo odepírá provoz.

Tyto protokoly toku může být obtížné ručně analyzovat a získáte přehled o z. Existuje však několik nástrojů s otevřeným zdrojem, které vám mohou pomoci vizualizovat tato data. Tento článek se poskytují řešení vizualizovat tyto protokoly pomocí elastické zásobníku, která vám umožní rychle indexu a vizualizovat vaše toku přihlášení Kibana řídicí panel.

## <a name="scenario"></a>Scénář

V tomto článku nastavíme řešení, které vám umožňuje vizualizovat protokolů toku skupinu zabezpečení sítě pomocí elastické zásobníku.  O Logstash vstupní modul plug-in obdrží protokoly toku přímo z objektu blob storage, který je nakonfigurován pro obsahující protokoly toku. Potom pomocí elastické zásobníku, protokoly toku indexované se použije vytvoření řídicího panelu Kibana k vizualizaci informací.

![scénář][scenario]

## <a name="steps"></a>Kroky

### <a name="enable-network-security-group-flow-logging"></a>Protokolování toku povolit skupinu zabezpečení sítě
V tomto scénáři musí mít síťové zabezpečení skupiny toku protokolování zapnuta alespoň jednu skupinu zabezpečení sítě ve vašem účtu. Pokyny k povolení protokolů toku zabezpečení sítě, naleznete v následujícím článku [Úvod do toku protokolování pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>Nastavit elastické zásobníku
Propojením protokolů NSG toku s elastické zásobníku můžeme vytvořit řídicí panel Kibana a co umožňuje vyhledávat, graf, analyzovat a statistiky odvozena z našich protokolů.

#### <a name="install-elasticsearch"></a>Nainstalujte Elasticsearch

1. Elastické zásobníku z verze 5.0 a vyšší vyžaduje Java 8. Spusťte příkaz `java -version` zkontrolujte vaši verzi. Pokud nemáte java nainstalovat, najdete v dokumentaci k na [Oracle na webu](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Stáhněte si správné binární balíček pro váš systém:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Ostatní metody instalace najdete na [Elasticsearch instalace](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ověřte, zda je spuštěna Elasticsearch pomocí příkazu:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Byste měli vidět odpověď podobná této:

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

Další pokyny k instalaci elastické vyhledávání, naleznete na stránce [instalace](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Nainstalujte Logstash

1. Chcete-li nainstalovat Logstash spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Další budeme muset nakonfigurovat Logstash přístup a analyzovat protokoly toku. Vytvoření souboru logstash.conf pomocí:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Do souboru přidejte následující obsah:

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

Další pokyny k instalaci Logstash naleznete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Nainstalujte modul plug-in vstupní Logstash pro úložiště objektů blob v Azure

Tento modul plug-in Logstash vám umožní přímý přístup k toku protokoly ze svého účtu úložiště určený. Chcete-li nainstalovat tento modul plug-in, spusťte příkaz z výchozí Logstash instalační adresář (v této případu /usr/share/logstash/bin):

```bash
logstash-plugin install logstash-input-azureblob
```

Chcete-li spustit Logstash spusťte příkaz:

```bash
sudo /etc/init.d/logstash start
```

Další informace o tento modul plug-in, najdete v dokumentaci k [sem](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-kibana"></a>Nainstalujte Kibana

1. Spusťte následující příkazy pro instalaci Kibana:

  ```bash
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Chcete-li spustit Kibana použijte příkazy:

  ```bash
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Chcete-li zobrazit vaše Kibana webové rozhraní, přejděte na `http://localhost:5601`
1. V tomto scénáři je vzor indexu používá pro protokoly toku "protokolů nsg toku". Můžete změnit vzor indexu v části "výstupní" logstash.conf souboru.

1. Pokud chcete zobrazit řídicí panel Kibana vzdáleně, vytvoření příchozího pravidla NSG povolení přístupu k **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Vytvořit řídicí panel Kibana

V tomto článku uvádíme na ukázkový řídicí panel můžete prohlédnout podrobnosti a trendy v upozornění.

![Obrázek 1][1]

1. Stáhněte si soubor řídicí panel [sem](https://aka.ms/networkwatchernsgflowlogdashboard), soubor vizualizace [sem](https://aka.ms/networkwatchernsgflowlogvisualizations)a soubor uloženého hledání [zde](https://aka.ms/networkwatchernsgflowlogsearch).

1. V části **správy** kartě z Kibana, přejděte na **uložit objekty** a importovat všechny tři soubory. Potom z **řídicí panel** karta můžete otevřít a načíst ukázkový řídicí panel.

Můžete také vytvořit vlastní vizualizace a přizpůsobit směrem metriky týkající se vlastní řídicí panely. Další informace o vytváření vizualizací Kibana z na Kibana [oficiální dokumentaci](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Vizualizace protokolů NSG toku

Ukázkový řídicí panel poskytuje několik vizualizace toku protokolů:

1. Toky podle rozhodnutí/směr v čase - čas řady grafy znázorňující počet toků za časové období. Můžete upravit jednotku času a rozpětí obě tyto vizualizace. Toky rozhodnutím zobrazuje podíl povolit nebo odepřít rozhodnutí, zatímco toky ve směru zobrazuje podíl příchozí a odchozí přenosy. S tyto vizuální prvky můžete prozkoumat provoz trendů v čase a vyhledejte všechny špičky nebo neobvyklou vzory.

  ![Obrázek 2][2]

1. Toky podle cílové/zdrojový Port – výsečové grafy znázorňující rozpis toků k příslušným portům. K tomuto zobrazení se zobrazí vaše nejčastěji používané porty. Pokud kliknete na určitém portu v rámci výsečového grafu, zbývající část řídicího panelu vyfiltruje dolů toky tento port.

  ![figure3][3]

1. Počet toky a Nejdřívější čas protokolu – metriky ukazuje toků zaznamenaných a datum nejdřívější protokolu zaznamenat.

  ![figure4][4]

1. Toky NSG a pravidlo – pruhový graf ukazuje rozdělení toků v jednotlivých skupinách NSG a také distribuce pravidel v jednotlivých skupinách NSG. Zde se zobrazí, které skupina NSG a pravidla generované nejvíce provoz.

  ![figure5][5]

1. TOP 10 zdrojové nebo cílové IP adresy – pruhové grafy znázorňující prvních 10 zdrojové a cílové IP adresy. Můžete upravit tyto grafy zobrazíte více nebo méně nejvyšší IP adresy. Zde vidíte nejčastěji výskytu IP adresy, jakož i provoz rozhodnutí (povolit nebo zakázat) prováděné směrem každý IP.

  ![figure6][6]

1. Tok řazené kolekce členů – Tato tabulka ukazuje, informace obsažené v rámci každé toku řazené kolekce členů a také odpovídající hodnot a pravidla.

  ![figure7][7]

Na panelu dotazů v horní části řídicího panelu, můžete filtrovat pomocí dolů řídicím panelu založené na libovolný parametr toky, jako je například ID předplatného, skupiny prostředků, pravidla nebo jakoukoli jinou proměnnou, které vás zajímají. Další informace o Kibana na dotazy a filtry, naleznete [oficiální dokumentaci](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Závěr

Kombinací protokolů toku skupinu zabezpečení sítě se elastické zásobníkem budeme mít spolu výkonný a přizpůsobit způsob vizualizace naše síťový provoz. Tyto řídicí panely vám umožňují rychle získat a sdílet uplatnitelné informace o vaší síti a také filtru dolů a prozkoumat na všechny potenciální anomálií. Kibana můžete přizpůsobit tyto řídicí panely a vytváření konkrétní vizualizací, které splňují všechny potřeby zabezpečení, auditování a dodržování předpisů.

## <a name="next-steps"></a>Další postup

Zjistěte, jak toku protokolů NSG s Power BI vizualizovat navštivte stránky [vizualizovat NSG toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
