---
title: Analýza protokolů toku skupiny zabezpečení sítě Azure – Graylog | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat a analyzovat protokoly toku skupiny zabezpečení sítě v Azure pomocí Network Watcher a Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842899"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Správa a analýza protokolů toku skupiny zabezpečení sítě v Azure pomocí Sledování sítě a Graylogu

[Protokoly toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které můžete použít k pochopení příchozího přenosu dat a odchozího přenosu IP pro síťová rozhraní Azure. Protokoly toku zobrazují odchozí a příchozí toky na základě pravidel pro skupinu zabezpečení sítě, síťové rozhraní, na které se tok vztahuje, informace o 5 řazených sítích (zdrojová/cílová IP adresa, zdrojový/cílový port, protokol) a zda byl provoz povolen nebo odepřen.

V síti můžete mít mnoho skupin zabezpečení sítě s povoleným protokolováním toku. Několik skupin zabezpečení sítě s povoleným protokolováním toku může způsobit těžkopádné analyzovat a získat přehledy z protokolů. Tento článek poskytuje řešení pro centrální správu těchto protokolů toku skupiny zabezpečení sítě pomocí Graylog, open source log management a analytický nástroj a Logstash, open source server-side kanál pro zpracování dat.

> [!Warning]
> Následující kroky pracují s protokoly toku verze 1. Podrobnosti naleznete [v tématu Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

## <a name="scenario"></a>Scénář

Protokoly toku skupiny zabezpečení sítě jsou povoleny pomocí sledovacího programu sítě. Tok protokoly toku toku do úložiště objektů blob Azure. Modul plug-in Logstash se používá k připojení a zpracování protokolů toku z úložiště objektů blob a jejich odeslání do Graylogu. Jakmile jsou protokoly toku uloženy v Graylogu, mohou být analyzovány a vizualizovány do přizpůsobených řídicích panelů.

![Pracovní postup Graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Kroky instalace

### <a name="enable-network-security-group-flow-logging"></a>Povolení protokolování toku skupiny zabezpečení sítě

V tomto scénáři musíte mít v účtu povoleno protokolování toku skupiny zabezpečení sítě alespoň pro jednu skupinu zabezpečení sítě. Pokyny k povolení protokolů toku skupiny zabezpečení sítě naleznete v následujícím článku [Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Nastavení Graylogu

V tomto příkladu jsou Graylog i Logstash nakonfigurované na serveru Ubuntu 14.04, nasazeném v Azure.

- Podívejte se do [dokumentace](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) od Graylog, krok za krokem návod, jak nainstalovat na Ubuntu.
- Nezapomeňte také nakonfigurovat webové rozhraní Graylog podle [dokumentace](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Tento příklad používá minimální nastavení Graylog (tj. jedna instance Graylog), ale Graylog lze navrhovat škálovat napříč prostředky v závislosti na vašem systému a produkčních potřeb. Další informace o architektonických aspektech nebo podrobném architektonickém průvodci naleznete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/architecture.html) graylogu a [v architektonickém průvodci](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog lze nainstalovat mnoha způsoby, v závislosti na vaší platformě a předvolbách. Úplný seznam možných způsobů instalace naleznete v oficiální [dokumentaci graylogu](https://docs.graylog.org/en/2.2/pages/installation.html). Serverová aplikace Graylog běží na linuxových distribucích a má následující předpoklady:

-  Java SE 8 nebo novější – [dokumentace Azul Azure JDK](https://aka.ms/azure-jdks)
-  Elastické vyhledávání 2.x (2.1.0 nebo novější) - [Dokumentace k instalaci elastického vyhledávání](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 nebo novější – [instalační dokumentace MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalace logstash

Logstash se používá ke sloučení protokolů toku formátovaných JSON na úroveň n-tice toku. Sloučení protokolů toku usnadňuje uspořádání a hledání v Graylogu.

1. Chcete-li nainstalovat protokol Logstash, spusťte následující příkazy:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Nakonfigurujte protokol, který analyzuje protokoly toku a odesílá je do Graylogu. Vytvořte soubor Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Přidejte do souboru následující obsah. Změňte zvýrazněné hodnoty tak, aby odrážely podrobnosti o účtu úložiště:

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
   Zadaný konfigurační soubor Logstash se skládá ze tří částí: vstupu, filtru a výstupu. Vstupní část označuje vstupní zdroj protokolů, které bude Protokolstash zpracovávat – v tomto případě budete používat plugin pro vstup blogu Azure (nainstalovaný v dalších krocích), který nám umožňuje přístup k souborům JSON protokolu skupiny zabezpečení sítě uloženým v úložišti objektů blob.

Část filtru pak sloučí každý soubor protokolu toku tak, aby se každá jednotlivá řazená kolekce členů toku a její přidružené vlastnosti stala samostatnou událostí Logstash.

Nakonec výstupní část předá každou událost Logstash serveru Graylog. Chcete-li, aby vyhovoval vašim specifickým potřebám, upravte konfigurační soubor Logstash podle potřeby.

   > [!NOTE]
   > Předchozí konfigurační soubor předpokládá, že server Graylog byl nakonfigurován na adrese IP 127.0.0.1 zpětné smyčky místního hostitele. Pokud ne, nezapomeňte změnit parametr hostitele ve výstupní části na správnou ADRESU IP.

Další pokyny k instalaci aplikace Logstash naleznete v [dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)k protokolu Logstash .

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalace vstupního modulu plug-in Logstash pro úložiště objektů blob Azure

Modul plug-in Logstash umožňuje přímý přístup k protokolům toku z jejich určeného účtu úložiště objektů blob. Chcete-li modul plug-in nainstalovat, spusťte z výchozího instalačního adresáře Logstash (v tomto případě /usr/share/logstash/bin) následující příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o tomto modulu plug-in naleznete v [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Nastavení připojení z Logstash do Graylogu

Nyní, když jste navázali připojení k protokolům toku pomocí Logstash a nastavili server Graylog, je třeba nakonfigurovat Graylog tak, aby přijímal příchozí soubory protokolu.

1. Přejděte do webového rozhraní serveru Graylog server pomocí adresy URL, kterou jste pro něj nakonfigurovali. K rozhraní se dostanete tak, že prohlížeč nasměruje te`http://<graylog-server-ip>:9000/`

2. Chcete-li přejít na konfigurační stránku, vyberte rozevírací nabídku **Systém** na horním navigačním panelu vpravo a klepněte na **položku Vstupy**.
   Případně přejděte na`http://<graylog-server-ip>:9000/system/inputs`

   ![Začínáme](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Chcete-li spustit nový vstup, vyberte v rozevíracím souboru **Select input** možnost *GELF UDP* a vyplňte formulář. GELF je zkratka pro Graylog Extended Log Format. Formát GELF je vyvinut graylogem. Další informace o jeho výhodách naleznete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/gelf.html)k Graylogu .

   Ujistěte se, že jste svázání min. Adresa IP by měla odpovídat **hostitelskému** poli výstupu UDP konfiguračního souboru Logstash. Výchozí port by měl být *12201*. Ujistěte se, že port odpovídá **poli portu** ve výstupu UDP určeném v konfiguračním souboru Logstash.

   ![Vstupy](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po spuštění vstupu, měli byste vidět, že se objeví v části **Místní vstupy,** jak je znázorněno na následujícím obrázku:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Další informace o vstupech zpráv Graylog naleznete v [dokumentaci](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Po provedení těchto konfigurací můžete spustit protokol Logstash a začít číst v `sudo systemctl start logstash.service`protokolech toku pomocí následujícího příkazu: .

### <a name="search-through-graylog-messages"></a>Hledání ve zprávách Graylog

Poté, co nějaký čas pro váš server Graylog shromažďovat zprávy, jste schopni prohledávat zprávy. Chcete-li zkontrolovat zprávy odesílané na server Graylog, klikněte na stránce **Konfigurace vstupů** na tlačítko **"Zobrazit přijaté zprávy**" vytvořeného vstupu GELF UDP. Budete přesměrováni na obrazovku, která vypadá podobně jako na následujícím obrázku: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknutím na modrý odkaz %{Message}se každá zpráva rozbalí a zobrazí se parametry jednotlivých n-tice toku, jak je znázorněno na následujícím obrázku:

![Zprávy](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Ve výchozím nastavení jsou všechna pole zpráv zahrnuta do hledání, pokud nevyberete konkrétní pole zprávy, které chcete vyhledat. Chcete-li vyhledat konkrétní zprávy (tj. – tok n-tic z konkrétní zdrojové IP) můžete použít vyhledávací dotazovací jazyk Graylog jako [dokumentovaný](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analýza protokolů toku skupiny zabezpečení sítě pomocí programu Graylog

Teď, když Graylog nastavit běží, můžete použít některé z jeho funkcí lépe pochopit data protokolu toku. Jedním z takových způsobů je pomocí řídicích panelů k vytvoření konkrétních zobrazení dat.

### <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

1. Na horním navigačním panelu vyberte **Řídicí panely** nebo přejděte na`http://<graylog-server-ip>:9000/dashboards/`

2. Odtud klikněte na zelené tlačítko **Vytvořit řídicí panel** a vyplňte krátký formulář s názvem a popisem řídicího panelu. Stisknutím tlačítka **Uložit** vytvořte nový řídicí panel. Zobrazí se řídicí panel podobný následujícímu obrázku:

    ![Řídicí panely](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Přidání widgetů

Můžete kliknout na název řídicího panelu, abyste ho viděli, ale právě teď je prázdný, protože jsme nepřidali žádné widgety. Jednoduchý a užitečný widget typu, který lze přidat na řídicí panel, jsou **grafy rychlých hodnot,** které zobrazují seznam hodnot vybraného pole a jejich distribuci.

1. Přejděte zpět na výsledky hledání vstupu UDP, který přijímá protokoly toku, výběrem **možnosti Hledat** z horního navigačního panelu.

2. V panelu **Výsledky hledání** na levé straně obrazovky najděte kartu **Pole,** která obsahuje seznam různých polí každé zprávy o řazené kolekce členů o příchozích tocích.

3. Vyberte libovolný požadovaný parametr, ve kterém chcete vizualizovat (v tomto příkladu je vybrán zdroj IP). Chcete-li zobrazit seznam možných widgetů, klikněte na modrou rozevírací šipku vlevo od pole a pak vyberte **Rychlé hodnoty** pro generování widgetu. Měli byste vidět něco podobného na následujícím obrázku:

   ![Zdrojová IP adresa](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Odtud můžete vybrat tlačítko **Přidat do řídicího panelu** v pravém horním rohu widgetu a vybrat odpovídající řídicí panel, který chcete přidat.

5. Přejděte zpět na řídicí panel a podívejte se na widget, který jste právě přidali.

   Na řídicí panel můžete přidat celou řadu dalších widgetů, jako jsou histogramy a počty, abyste mohli sledovat důležité metriky, například ukázkový řídicí panel zobrazený na následujícím obrázku:

   ![Řídicí panel Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Další vysvětlení na řídicích panelech a dalších typech widgetů naleznete v [dokumentaci graylogu](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Integrací sledování sítě se společností Graylog nyní máte pohodlný a centralizovaný způsob správy a vizualizace protokolů toku skupiny zabezpečení sítě. Graylog má řadu dalších výkonných funkcí, jako jsou datové proudy a výstrahy, které lze také použít k další správě protokolů toku a lepšímu pochopení síťového provozu. Teď, když máte Graylog nastavený a připojený k Azure, můžete dál zkoumat další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak vizualizovat protokoly toku skupiny zabezpečení sítě pomocí Power BI na stránce [Visualize protokoly toků skupinzabezpečení sítě pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
