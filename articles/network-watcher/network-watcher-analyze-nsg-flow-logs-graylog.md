---
title: Analýza protokolů toku skupiny zabezpečení sítě Azure – Graylogu | Microsoft Docs
description: Naučte se spravovat a analyzovat protokoly toku skupin zabezpečení sítě v Azure pomocí Network Watcher a Graylogu.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 62f4a06ec729d896dc11a290bc7a5ccc7c321683
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984062"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Správa a analýza protokolů toku skupin zabezpečení sítě v Azure pomocí Network Watcher a Graylogu

[Protokoly toku skupin zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které můžete použít k pochopení příchozího a odchozího provozu IP pro síťová rozhraní Azure. Protokoly toku zobrazují odchozí a příchozí toky pro každé pravidlo skupiny zabezpečení sítě, síťové rozhraní, na které se tok vztahuje, informace o 5-řazené kolekci členů (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) týkající se toku, a pokud byl provoz povolený nebo zakázaný.

V síti můžete mít mnoho skupin zabezpečení sítě s povoleným protokolováním toků. Několik skupin zabezpečení sítě s povoleným protokolováním toků může díky tomu nenáročné analyzovat a získávat poznatky z protokolů. Tento článek poskytuje řešení centrální správy těchto protokolů toku skupin zabezpečení sítě pomocí Graylogu, Open Source správy protokolů a nástroje pro analýzu a Logstash, open source kanálu pro zpracování dat na straně serveru.

> [!Warning]
> Následující kroky fungují s protokoly toku verze 1. Podrobnosti najdete v tématu [Úvod do protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

## <a name="scenario"></a>Scénář

Protokol toků skupin zabezpečení sítě je povolený pomocí Network Watcher. Flow se zaznamenávají do úložiště objektů BLOB v Azure. Modul plug-in Logstash se používá k připojení a zpracování protokolů toku ze služby Blob Storage a jejich posílání do Graylogu. Jakmile se protokoly toků ukládají v Graylogu, dají se analyzovat a vizuálně přizpůsobovat do přizpůsobených řídicích panelů.

![Pracovní postup graylogu](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Instalační kroky

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupin zabezpečení sítě

V tomto scénáři musíte mít povolené protokolování toku skupin zabezpečení sítě ve vašem účtu aspoň v jedné skupině zabezpečení sítě. Pokyny k povolení protokolů toků skupin zabezpečení sítě najdete v následujícím článku [Úvod do protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Nastavení Graylogu

V tomto příkladu jsou Graylogu i Logstash nakonfigurované na serveru Ubuntu 14,04, který je nasazený v Azure.

- Podrobné pokyny k instalaci do Ubuntu najdete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z graylogu.
- Nezapomeňte také nakonfigurovat webové rozhraní Graylogu podle pokynů v [dokumentaci](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

V tomto příkladu se používá minimální nastavení Graylogu (tj. jedna instance Graylogu), ale Graylogu může být navržena tak, aby se v závislosti na potřebách vašeho systému a výroby mohla škálovat napříč prostředky. Další informace o architektuře nebo podrobné příručce architektury najdete v [dokumentaci k](https://docs.graylog.org/en/2.2/pages/architecture.html) graylogu a [architektuře architektury](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylogu se dá nainstalovat mnoha způsoby v závislosti na vaší platformě a preferencích. Úplný seznam možných metod instalace najdete v oficiální [dokumentaci](https://docs.graylog.org/en/2.2/pages/installation.html)k graylogu. Aplikace serveru Graylogu běží na distribucích systému Linux a má následující požadavky:

-  Java SE 8 nebo novější – [Azul dokumentace k Azure JDK](https://aka.ms/azure-jdks)
-  Elastické vyhledávání 2. x (2.1.0 nebo novější) – [dokumentace k instalaci Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 nebo novější – [dokumentace k instalaci MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Nainstalovat Logstash

Logstash se používá k sloučení protokolů toku ve formátu JSON do úrovně řazené kolekce členů toku. Sloučení protokolů toků usnadňuje uspořádání a hledání v Graylogu.

1. Pokud chcete nainstalovat Logstash, spusťte následující příkazy:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Nakonfigurujte Logstash k analýze protokolů toku a jejich odeslání do Graylogu. Vytvořte soubor Logstash. conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Do souboru přidejte následující obsah. Změňte zvýrazněné hodnoty tak, aby odrážely podrobnosti účtu úložiště:

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
   Zadaný konfigurační soubor Logstash se skládá ze tří částí: vstup, filtr a výstup. Vstupní oddíl určuje vstupní zdroj protokolů, které Logstash zpracuje – v takovém případě budete používat vstupní modul plug-in blogu Azure (nainstalovaný v dalších krocích), který nám umožní přístup k souborům JSON protokolu toku skupin zabezpečení sítě, které jsou uložené v úložišti objektů BLOB.

Oddíl Filter pak sloučí jednotlivé soubory protokolu toku, aby se jednotlivé řazené kolekce členů toku a jeho přidružených vlastností staly samostatnou událostí Logstash.

Nakonec oddíl Output předá každou událost Logstash serveru Graylogu. Aby vyhovoval vašim konkrétním potřebám, upravte konfigurační soubor Logstash podle potřeby.

   > [!NOTE]
   > Předchozí konfigurační soubor předpokládá, že server Graylogu byl nakonfigurován na IP adrese zpětné smyčky místního hostitele 127.0.0.1. V takovém případě nezapomeňte změnit parametr hostitele v oddílu Output na správnou IP adresu.

Další pokyny k instalaci Logstash najdete v [dokumentaci k](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Nainstalujte modul plug-in Logstash Input pro úložiště objektů BLOB v Azure.

Modul plug-in Logstash vám umožňuje přímý přístup k protokolům toků z určeného účtu úložiště BLOB. Chcete-li nainstalovat modul plug-in, z výchozího instalačního adresáře Logstash (v tomto případě/usr/share/logstash/bin) spusťte následující příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto modulu plug-in najdete v [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Nastavení připojení z Logstash na Graylogu

Teď, když jste navázali připojení k protokolům Flow pomocí Logstash a nastavili jste server Graylogu, musíte nakonfigurovat Graylogu pro příjem příchozích souborů protokolu.

1. Přejděte na webové rozhraní serveru Graylogu pomocí adresy URL, kterou jste pro něj nakonfigurovali. K rozhraní můžete přistupovat nasměrováním prohlížeče na `http://<graylog-server-ip>:9000/`

2. Chcete-li přejít na stránku konfigurace, v horním navigačním panelu klikněte na rozevírací nabídku **systém** a potom klikněte na možnost **vstupy**.
   Případně přejděte na `http://<graylog-server-ip>:9000/system/inputs`

   ![Začínáme](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Pokud chcete nový vstup spustit, vyberte v rozevíracím seznamu **Vybrat vstup** možnost *GELF UDP* a pak vyplňte formulář. GELF představuje formát protokolu Graylogu Extended. Formát GELF je vyvinutý pomocí Graylogu. Další informace o jeho výhodách najdete v [dokumentaci k](https://docs.graylog.org/en/2.2/pages/gelf.html)graylogu.

   Ujistěte se, že vstup navážete na IP adresu, na které jste nakonfigurovali server Graylogu. IP adresa by se měla shodovat s polem **hostitel** výstupu protokolu UDP konfiguračního souboru Logstash. Výchozí port by měl být *12201*. Zajistěte, aby se port shodoval s polem **port** ve výstupu UDP určeném v konfiguračním souboru Logstash.

   ![Snímek obrazovky ukazuje vstupy Graylogu s možnostmi spouštění a hledání vstupů.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po spuštění vstupu by se měla zobrazit v části **místní vstupy** , jak je znázorněno na následujícím obrázku:

   ![Snímek obrazovky zobrazující oddíl místních vstupů, který obsahuje vstup, který jste spustili.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Další informace o vstupech zpráv Graylogu najdete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Jakmile tyto konfigurace provedete, můžete začít Logstash a začít číst protokoly toku pomocí následujícího příkazu: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Hledání prostřednictvím zpráv Graylogu

Když pro server Graylogu zadáte nějakou dobu, abyste mohli shromažďovat zprávy, můžete zprávy prohledávat. Pokud chcete kontrolovat zprávy odesílané vašemu Graylogu serveru, na stránce konfigurace **vstupů** klikněte na tlačítko**Zobrazit přijaté zprávy**u vstupu GELF UDP, který jste vytvořili. Budete přesměrováni na obrazovku, která bude vypadat podobně jako na následujícím obrázku: 

![Snímek obrazovky ukazuje server Graylogu, který zobrazuje výsledky hledání, histogramy a zprávy.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknutím na modrý odkaz "% {Message}" rozbalíte jednotlivé zprávy a zobrazíte parametry jednotlivých řazených kolekcí členů toku, jak je znázorněno na následujícím obrázku:

![Snímek obrazovky zobrazuje podrobnosti zprávy ze serveru Graylogu.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Ve výchozím nastavení jsou všechna pole zpráv ve vyhledávání obsažena, pokud nevyberete konkrétní pole zprávy, které chcete vyhledat. Pokud chcete vyhledat konkrétní zprávy (tj. – počet řazených kolekcí členů toku z konkrétní zdrojové IP adresy: můžete použít jazyk vyhledávacího dotazu Graylogu, jak je [uvedeno](https://docs.graylog.org/en/2.2/pages/queries.html) níže.

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analýza protokolů toku skupiny zabezpečení sítě pomocí Graylogu

Teď, když je spuštěný Graylogu, můžete využít některé z jeho funkcí k lepšímu pochopení dat protokolu toku. Jedním z těchto způsobů je použití řídicích panelů k vytváření konkrétních zobrazení dat.

### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

1. V horním navigačním panelu vyberte **řídicí panely** nebo přejděte na `http://<graylog-server-ip>:9000/dashboards/`

2. Odtud klikněte na zelené tlačítko **vytvořit řídicí panel** a vyplňte krátký tvar názvem a popisem řídicího panelu. Stisknutím tlačítka **Uložit** vytvořte nový řídicí panel. Zobrazí se řídicí panel podobný následujícímu obrázku:

    ![Snímek obrazovky ukazuje řídicí panely serveru Graylogu s možnostmi pro vytváření a úpravu řídicích panelů.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Přidat widgety

Můžete kliknout na název řídicího panelu, abyste ho viděli, ale teď je prázdný, protože jsme nepřidali žádné widgety. Snadný a užitečný typ widgetu, který se má přidat na řídicí panel, jsou grafy **rychlých hodnot** , které zobrazují seznam hodnot vybraného pole a jejich distribuci.

1. V horním navigačním panelu přejděte zpět na výsledky hledání vstupu UDP, který přijímá protokoly toku **Search** .

2. Na panelu **výsledků hledání** na levou stranu obrazovky najděte kartu **pole** , která obsahuje seznam různých polí každé zprávy řazené kolekce členů příchozího toku.

3. Vyberte libovolný požadovaný parametr pro vizualizaci (v tomto příkladu je vybrán zdroj IP adres). Chcete-li zobrazit seznam možných widgetů, klikněte na šipku rozevíracího seznamu nalevo od pole a pak vyberte možnost **rychlé hodnoty** pro vygenerování widgetu. Mělo by se zobrazit něco podobného jako na následujícím obrázku:

   ![Zdrojová IP adresa](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Odtud můžete vybrat tlačítko **Přidat do řídicího panelu** v pravém horním rohu widgetu a vybrat odpovídající řídicí panel, který chcete přidat.

5. Přejděte zpátky na řídicí panel, abyste viděli widget, který jste právě přidali.

   Do řídicího panelu můžete přidat celou řadu dalších pomůcek, jako jsou histogramy a počty, abyste mohli sledovat důležité metriky, jako je ukázkový řídicí panel, který je znázorněný na následujícím obrázku:

   ![Řídicí panel Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Další vysvětlení na řídicích panelech a dalších typech widgetů najdete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/dashboards.html)k graylogu.

Integrací Network Watcher s Graylogu nyní máte pohodlný a centralizovaný způsob správy a vizualizace protokolů toku skupin zabezpečení sítě. Graylogu má řadu dalších výkonných funkcí, jako jsou datové proudy a výstrahy, které se dají použít také k další správě protokolů toků a k lepšímu pochopení síťového provozu. Teď, když máte Graylogu nastavené a připojené k Azure, můžete dál prozkoumat další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

Naučte se vizualizovat protokoly toku skupin zabezpečení sítě pomocí Power BI tím, že navštívíte seznam [toků toků skupin zabezpečení sítě s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
