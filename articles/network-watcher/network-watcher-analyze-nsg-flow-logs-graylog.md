---
title: Analyzovat protokoly toků skupin zabezpečení sítě Azure – Graylogu | Dokumentace Microsoftu
description: Zjistěte, jak spravovat a analyzovat protokoly toků skupin zabezpečení sítě v Azure s využitím Network Watcheru a Graylogu.
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 87d7c39a9340a82813f4df971c03a10be56e8f94
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42054258"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Správa a analyzovat protokoly toků skupin zabezpečení sítě v Azure s využitím Network Watcheru a Graylogu

[Protokoly toků skupin zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které vám pomůže pochopit, příchozí a odchozí provoz IP pro síťových rozhraních Azure. Protokoly toku zobrazení odchozí a příchozí toků na základě sítě zabezpečení skupiny pravidel, síťové rozhraní tok platí pro informace 5 řazené kolekce členů (zdrojová a cílová IP, zdrojový/cílový Port, protokol) o toku, a jestli byl povolený nebo zakázaný provoz .

Může mít víc skupin zabezpečení sítě v síti s povoleno protokolování toků. Několik skupin zabezpečení sítě s povoleno protokolování toku může být náročná analyzovat a zkoumat velké vaše protokoly. Tento článek poskytuje řešení pro tyto síťové protokoly toků skupin zabezpečení použití Graylogu, Správa protokolů opensourcový a nástroj pro analýzu a Logstash, kanál zpracování dat na straně serveru opensourcových centrálně spravovat.

## <a name="scenario"></a>Scénář

Protokoly toků skupin zabezpečení sítě jsou povolené používat Network Watcher. Tok protokolů toku v do Azure blob storage. Modul plug-in se používá k připojení a zpracování protokolů toku z úložiště objektů blob a odeslat je do Graylogu. Jakmile se protokoly toku se ukládají v Graylogu, mohou být analyzovat a vizualizovat do přizpůsobené řídicí panely.

![Graylog pracovní postup]](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Postup instalace

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toků skupin zabezpečení sítě

V tomto scénáři musí mít síťové protokolování toků skupin zabezpečení povolené ve skupině zabezpečení u alespoň jednoho síťového ve vašem účtu. Pro pokyny k povolení síťové protokoly toků skupin zabezpečení, přečtěte si následující článek [Úvod k protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Nastavení Graylogu

V tomto příkladu Graylogu a Logstash jsou nakonfigurované na serveru se systémem Ubuntu 14.04 nasazené v Azure.

- Odkazovat [dokumentaci](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z Graylogu, pro podrobné pokyny, jak nainstalovat na Ubuntu.
- Ujistěte se, že Graylogu webové rozhraní nakonfigurovat pomocí následujících [dokumentaci](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Tento příklad používá nastavení minimální Graylogu (např.) jedna instance Graylogu), ale Graylogu můžete navržený na škálování v prostředcích v závislosti na systém a produkční potřebám. Další informace o architektuře aspekty nebo podrobné architektury průvodce, najdete v článku společnosti Graylogu [dokumentaci](http://docs.graylog.org/en/2.2/pages/architecture.html) a [příručka o architektuře](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylogu je možné nainstalovat mnoha způsoby v závislosti na vaší platformě a předvolby. Úplný seznam metod instalace je to možné, najdete v oficiální společnosti Graylogu [dokumentaci](http://docs.graylog.org/en/2.2/pages/installation.html). Graylogu serverová aplikace běží v Linuxových distribucích a má následující požadavky:

-  Oracle Java SE 8 nebo novějším – [dokumentaci k instalaci Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-  Elastické hledání 2.x (2.1.0 nebo novější)- [dokumentaci k instalaci Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 nebo vyšší – [dokumentaci k instalaci MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalace Logstash

Logstash slouží k vyrovnání protokolů toku ve formátu JSON na úroveň tok řazené kolekce členů. Sloučení protokolů toku usnadňuje protokoly pro organizaci a vyhledávat Graylogu.

1. Pokud chcete nainstalovat Logstash, spusťte následující příkazy:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurace Logstash pro analýzu protokolů toku a odeslat je do Graylogu. Vytvořte soubor Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Přidejte následující obsah do souboru. Změňte zvýrazněné hodnoty tak, aby odrážely podrobnosti o vašem účtu úložiště:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
Konfigurační soubor Logstash k dispozici se skládá ze tří částí: vstup, filtrovat a výstup. Vstupní oddíl určí vstupní zdroj protokoly, které budou zpracovávat Logstash – v takovém případě je budete používat blogu Azure vstupu modulu plug-in (nainstalované v dalších krocích), která umožňuje přístup ke službě flow skupiny zabezpečení sítě protokolu soubory JSON uložené v úložišti objektů blob.

Část Filtr pak sloučí soubor protokolu každý tok tak, aby každá řazená kolekce členů jednotlivé toku a jeho přidružených vlastností samostatná událost Logstash.

Nakonec výstupní sekce předává každou událost Logstash Graylogu server. Tak, aby odpovídaly konkrétní potřeby upravte konfigurační soubor Logstash, podle potřeby.

   > [!NOTE]
   > Předchozí konfigurační soubor se předpokládá, že Graylogu server nakonfigurovaný na adresu zpětné smyčky IP adresu místního hostitele 127.0.0.1. Pokud ne, nezapomeňte změnit parametr hostitelů v části výstupu na správnou IP adresu.

Další pokyny týkající se instalace Logstash, najdete v článku Logstash [dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalace Logstash vstupu modulu plug-in pro úložiště objektů blob v Azure

Modul plug-in umožňuje přímý přístup k protokolů toku ze svého účtu úložiště objektů blob v určené. Chcete-li nainstalovat modul plug-in z Logstash výchozí instalační adresář (v tomto případu /usr/share/logstash/bin), spusťte následující příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto plug v najdete v článku [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Nastavení připojení z Logstash ke Graylogu

Teď, když jste připojení k protokolů toku s využitím Logstash a nastavení serveru Graylogu, budete muset nakonfigurovat Graylogu tak, aby přijímal příchozí soubory protokolu.

1. Přejděte na vaše webové rozhraní Graylogu serveru pomocí adresy URL nakonfigurované pro něj. Lze přistoupit k prostředí přesměrováním prohlížeče `http://<graylog-server-ip>:9000/`

2. Chcete-li přejít na stránku konfigurace, vyberte **systému** rozevírací nabídky v horním navigačním panelu vpravo a pak klikněte na **vstupy**.
   Můžete také přejít na `http://<graylog-server-ip>:9000/system/inputs`

   ![Začínáme](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Chcete-li spustit nový vstup, vyberte *GELF UDP* v **vyberte vstup** rozevíracího seznamu a potom vyplňte formulář. GELF jsou zahrnovaného Graylogu rozšířené formát protokolu. Formát GELF vyvinutý Graylogu. Další informace o jeho výhody, najdete v článku Graylogu [dokumentaci](http://docs.graylog.org/en/2.2/pages/gelf.html).

   Ujistěte se, že pro vstup na IP adresu serveru Graylogu jste nakonfigurovali přes vazbu. IP adresa musí odpovídat **hostitele** pole výstup UDP konfigurační soubor Logstash. Výchozí port by měl být *12201*. Ujistěte se port, který odpovídá **port** pole v protokolu UDP výstup určený v konfigurační soubor Logstash.

   ![Vstupy](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po spuštění vstupu, mělo by se zobrazit se zobrazí v rámci **místní vstupy** části, jak je znázorněno na následujícím obrázku:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Další informace o vstupy Graylogu zpráv, najdete v tématu [dokumentaci](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Jakmile tyto konfigurace se provedly, můžete začít Logstash má začínat čtení protokolů toku pomocí následujícího příkazu: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Hledat zprávy Graylogu

Po povolení nějakou dobu pro váš server Graylogu shromažďovat zprávy, budete moct Hledat zprávy. Ke kontrole zprávy odeslané k vašemu serveru Graylogu z **vstupy** stránce klikněte na konfigurace "**zobrazit přijatých zpráv**" tlačítko GELF UDP vstup, kterou jste vytvořili. Budete přesměrováni na obrazovku, která vypadá podobně jako na následujícím obrázku: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknutím na odkaz blue "% {Message}" rozbalí každou zprávu zobrazíte parametry každý tok řazené kolekce členů, jak je znázorněno na následujícím obrázku:

![Zprávy](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Ve výchozím nastavení všechna pole zprávy jsou zahrnuty do hledání Pokud nevyberete konkrétní zprávu pole pro hledání. Pokud chcete vyhledat konkrétní zprávy (např.) – flow řazenými kolekcemi členů z konkrétní Zdrojová IP adresa) navíc můžete použít jazyk Graylogu vyhledávací dotaz jako [zdokumentované](http://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analýza sítě protokoly toků skupin zabezpečení s využitím Graylogu

Teď, když Graylogu ho nastavit s, můžete některé ze svých funkcí pro lepší pochopení dat protokolů toku. Jeden takový způsob, jak je vytvořit konkrétní zobrazení dat pomocí řídicích panelů.

### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

1. V horním navigačním panelu vyberte **řídicí panely** nebo přejděte na `http://<graylog-server-ip>:9000/dashboards/`

2. Odtud, klikněte na zelené **vytvořit řídicí panel** tlačítko a vyplňte krátký tvar pomocí názvu a popisu řídicí panel. Klikněte **Uložit** pro vytvoření nového řídicího panelu. Zobrazí se řídicí panel podobně jako na následujícím obrázku:

    ![Řídicí panely](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Přidání widgetů

Můžete kliknout na název řídicího panelu a prohlédnout si ho ale v tuto chvíli je prázdná, protože jsme nepřidali žádné pomůcky. Pomůcky snadné a užitečné typu na řídicí panel se **rychlé hodnoty** grafy, které zobrazují seznam hodnot pole vybrané, a jejich distribuci.

1. Přejděte zpět na výsledky hledání UDP vstupu, který přijímá protokolů toku s výběrem **hledání** z horního navigačního panelu.

2. V části **výsledek vyhledávání** panelu na levé straně obrazovky, vyhledejte **pole** kartu, která obsahuje seznam různých polí příchozí zprávy tok řazené kolekce členů.

3. Vyberte žádné požadované parametry, ve kterém chcete vizualizovat (v tomto příkladu je vybrán zdroj IP). Chcete-li zobrazit seznam možných widgetů, klikněte na modrou šipkou rozevíracího seznamu na levé straně pole a pak vyberte **rychlé hodnoty** ke generování widgetu. By měl vypadat podobně jako na následujícím obrázku:

   ![Zdrojová IP adresa](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Odtud můžete vybrat **přidat na řídicí panel** tlačítko v pravém horním rohu widgetu a vyberte odpovídající řídicí panel, který chcete přidat.

5. Přejděte zpět na řídicí panel zobrazit ve widgetu, který jste právě přidali.

   Můžete přidat širokou škálu jiných widgetů, například histogramy a počty na řídicí panel můžete sledovat důležité metriky, jako je například ukázkový řídicí panel je znázorněno na následujícím obrázku:

   ![Řídicí panel Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Další vysvětlení na řídicích panelech a jiné druhy widgetů, najdete na Graylogu [dokumentaci](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Díky integraci služby Network Watcher s Graylogu, máte teď pohodlný a centralizovaný způsob správy a vizualizovat protokoly toků skupin zabezpečení sítě. Graylogu má několik dalších efektivních funkcí, jako jsou datové proudy a výstrahy, které můžete také použít na další správa protokolů toku a až lépe porozumíte provozu vaší sítě. Teď, když máte Graylogu nastavení a připojení k Azure a bez obav pokračovat a prozkoumejte další funkce, které nabízí.

## <a name="next-steps"></a>Další postup

Zjistěte, jak vizualizovat vaše síťové protokoly toků skupin zabezpečení v Power BI návštěvou [protokoly s Power BI toků skupin zabezpečení sítě vizualizovat](network-watcher-visualize-nsg-flow-logs-power-bi.md).
