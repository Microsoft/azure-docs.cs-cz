---
title: Analýza protokolů toku skupiny zabezpečení sítě Azure - Graylog | Microsoft Docs
description: Zjistěte, jak spravovat a analyzovat protokoly toku skupiny sítě zabezpečení v Azure pomocí sledovací proces sítě a Graylog.
services: network-watcher
documentationcenter: na
author: mareat
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
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639281"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Spravovat a analyzovat protokoly toku skupiny sítě zabezpečení v Azure pomocí sledovací proces sítě a Graylog

[Protokoly toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) poskytují informace, které můžete použít k pochopení příchozí a odchozí provoz IP pro rozhraní sítě Azure. Tok protokoly zobrazit příchozí a odchozí toky na za základ pravidla zabezpečení skupiny sítě, síťové rozhraní toku vztahuje na informace 5-n-tice (zdroj nebo cíl, zdrojový nebo cílový Port, protokol IP) o toku, a pokud se povoluje nebo odepírá provoz .

Můžete mít víc skupin zabezpečení sítě ve vaší síti s protokolování toku. Několik skupin zabezpečení sítě s toku protokolování může být náročná analyzovat a získáte přehled o z protokolů. Tento článek poskytuje řešení můžete centrálně spravovat tyto sítě skupiny toku protokolů zabezpečení pomocí Graylog, Správa protokolu s otevřeným zdrojem a nástroj pro analýzu a Logstash, kanál s otevřeným zdrojem zpracování dat na straně serveru.

## <a name="scenario"></a>Scénář

Protokoly toku skupiny zabezpečení sítě jsou povolené pomocí sledovací proces sítě. Tok protokoly tok v do Azure blob storage. Modul plug-in Logstash slouží k připojení a zpracovávat toku protokolů z úložiště objektů blob a posílat je Graylog. Po toku protokoly jsou uloženy v Graylog, může být analyzován a vizualizována do přizpůsobené řídicí panely.

![Graylog pracovní postup]](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Postup instalace

### <a name="enable-network-security-group-flow-logging"></a>Povolit protokolování toku skupiny zabezpečení sítě

V tomto scénáři musí mít síťové zabezpečení skupiny toku protokolování na skupiny zabezpečení alespoň jedna síť ve vašem účtu. Pokyny k povolení síťových protokolů toku skupiny zabezpečení, najdete v následujícím článku [Úvod do toku protokolování pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Nastavení Graylog

V tomto příkladu jsou Graylog a Logstash nakonfigurované na serveru Ubuntu 14.04, nasazené v Azure.

- Odkazovat [dokumentace](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z Graylog, pro podrobné pokyny, jak nainstalovat na Ubuntu.
- Nezapomeňte také nakonfigurovat webové rozhraní Graylog podle [dokumentaci](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Tento příklad používá minimální instalace Graylog (tj. jedna instance Graylog), ale může být navržen Graylog škálování mezi prostředky v závislosti na systém a provozním potřebám. Další informace o architektuře aspekty nebo průvodce po hloubky architektury, najdete v článku na Graylog [dokumentace](http://docs.graylog.org/en/2.2/pages/architecture.html) a [příručka o architektuře](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog lze nainstalovat v mnoha způsoby, v závislosti na platformě a předvolby. Úplný seznam metod možné instalace, najdete v části Graylog na oficiální [dokumentaci](http://docs.graylog.org/en/2.2/pages/installation.html). Graylog serverová aplikace běží na Linuxových distribucích a má následující požadavky:

-   Oracle SE Java 8 nebo novějším – [dokumentaci k instalaci pro Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elastické vyhledávání 2.x (2.1.0 nebo novější)- [Elasticsearch instalace dokumentace](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   MongoDB 2.4 nebo novější – [instalace dokumentace pro MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Nainstalujte Logstash

Logstash se používá k vyrovnání protokoly toku formátu JSON na úroveň toku řazené kolekce členů. Vyrovnání protokoly toku usnadňuje protokoly uspořádání a hledání v Graylog.

1.  Chcete-li nainstalovat Logstash, spusťte následující příkazy:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  Nakonfigurujte Logstash poslat Graylog a analyzovat protokoly toku. Vytvořte soubor Logstash.conf:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Do souboru přidejte následující obsah. Změna zvýrazněné hodnoty tak, aby odrážela údaje o vašem účtu úložiště:

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
Zadaný soubor konfigurace Logstash se skládá ze tří částí: vstupní, výstupní a filtr. Části vstupní označí vstupního zdroje protokolů, které bude zpracovávat Logstash – v tomto případě jsme budete používat Azure blog vstupní modulu plug-in (nainstalovanou v dalších krocích), který umožňuje přístup k tok skupiny zabezpečení sítě protokolu soubory JSON uložené v úložišti objektů blob.

Část Filtr pak sloučí každý soubor protokolu toku tak, aby každá řazená kolekce členů jednotlivých toku a jeho přidružené vlastnosti stalo samostatná událost Logstash.

Nakonec části výstup předává všechny události Logstash Graylog server. Tak, aby vyhovovala konkrétní potřebuje, upravte konfigurační soubor Logstash, podle potřeby.

   > [!NOTE]
   > Předchozí konfiguračním souboru se předpokládá, že Graylog server nakonfigurovaný na IP adresu zpětné smyčky místního hostitele 127.0.0.1. Pokud ne, nezapomeňte změnit parametr hostitelů v sekci Výstup na správnou IP adresu.

Další pokyny k instalaci Logstash, najdete v článku Logstash [dokumentaci](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalace modulu plug-in pro Azure blob storage Logstash vstup

Modul plug-in Logstash umožňuje přímý přístup k toku protokoly ze svého účtu úložiště určený objekt blob. Instalace modulu plug-in z výchozí Logstash instalační adresář (v této případu /usr/share/logstash/bin), spusťte následující příkaz:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Další informace o této moduly v najdete v tématu [dokumentaci](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Nastavit připojení z Logstash k Graylog

Teď, když jsme vytvořili připojení k protokolům toku pomocí Logstash a nastavení serveru Graylog, je potřeba nakonfigurovat Graylog tak, aby přijímal příchozí soubory protokolu.

1.  Přejděte do vaší webové rozhraní Graylog serveru pomocí adresy URL nakonfigurované pro ni. Přesměrováním prohlížeč tak, aby se dostanete rozhraní`http://<graylog-server-ip>:9000/`

2.  Přejděte na stránku konfigurace, vyberte **systému** rozevírací nabídky v horním navigačním panelu vpravo a pak klikněte na **vstupy**.
    Případně přejděte na`http://<graylog-server-ip>:9000/system/inputs`

    ![Začínáme](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Chcete-li spustit novou vstup, vyberte *GELF UDP* v **vyberte vstup** rozevíracího seznamu a potom vyplňte formulář. GELF znamená Graylog rozšířený formát protokolu. Formát GELF vyvinutý Graylog. Další informace o jeho výhody, najdete v článku Graylog [dokumentaci](http://docs.graylog.org/en/2.2/pages/gelf.html).

    Ujistěte se, že pro vazbu vstup IP nakonfigurovaný Graylog server na. IP adresa by měla odpovídat **hostitele** pole výstupu UDP Logstash konfiguračního souboru. Výchozí port by měl být *12201*. Zkontrolujte, zda port odpovídá **port** pole v protokolu UDP výstup uvedených v konfiguračním souboru Logstash.

    ![Vstupy](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    Po spuštění vstupu, měli byste vidět zobrazí v části **místní vstupy** části, jak je znázorněno na následujícím obrázku:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Další informace o Graylog zpráva vstupy, naleznete [dokumentaci](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  Jakmile se provedly tyto konfigurace, můžete začít Logstash má začínat čtení v protokolech toku pomocí následujícího příkazu:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Hledání v Graylog zprávy

Po povolení chvíli pro váš server Graylog shromažďovat zprávy, budete moci vyhledávat prostřednictvím zprávy. Zkontrolujte zprávy odeslané na váš server Graylog z **vstupy** klikněte na stránce konfigurace "**zobrazit přijaté zprávy**" tlačítko GELF UDP vstup, kterou jste vytvořili. Budete přesměrováni na obrazovku, který vypadá podobně jako na následujícím obrázku: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknutím na odkaz blue "% {Message}" rozšíří každou zprávu zobrazíte parametry každý tok řazené kolekce členů, jak je znázorněno na následujícím obrázku:

![Zprávy](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Ve výchozím nastavení jsou všechna pole zpráv součástí hledání Pokud nevyberete konkrétní zpráva pole pro vyhledávání. Pokud chcete vyhledat konkrétní zprávy (tj. – toku řazenými kolekcemi členů z konkrétního zdroje IP) můžete použít dotazovacího jazyka pro vyhledávání Graylog jako [zdokumentovaný](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analýza sítě zabezpečení skupiny toku protokolů pomocí Graylog

Teď, když Graylog ho nastavili spuštěna, jsme používat některé z jeho funkce pro lepší pochopení naše toku dat protokolu. Jeden takový způsob je pomocí řídicích panelů můžete vytvořit konkrétní zobrazení dat.

### <a name="create-a-dashboard"></a>Vytvořit řídicí panel

1.  V horním navigačním panelu, vyberte **řídicí panely** nebo přejděte na`http://<graylog-server-ip>:9000/dashboards/`

2.  Zde, klikněte na tlačítko zeleným **vytvořit řídicí panel** tlačítko a vyplňte formulář krátký název a popis z řídicího panelu. Stiskněte tlačítko **Uložit** tlačítko pro vytvoření nového řídicího panelu. Zobrazí řídicí panel podobně jako na následujícím obrázku:

    ![Řídicí panely](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Přidat pomůcky

Můžete kliknout na název řídicího panelu ji zobrazit, ale teď jeho prázdné, protože jsme nepřidali žádné pomůcky. Pomůcka snadno a užitečné typ přidat na řídicí panel se **rychlé hodnoty** grafy, které budou zobrazovat seznam hodnot vybrané pole a jejich distribuci.

1.  Vraťte se na výsledky hledání UDP vstupu, který přijímá protokoly toku výběrem **vyhledávání** horním navigačním panelu.

2.  V části **výsledek hledání** panelu na levé straně obrazovky, vyhledejte **pole** kartu, která obsahuje seznam různých polí příchozí zprávy toku řazené kolekce členů.

3.  Vyberte libovolný požadovaný parametr, ve kterém k vizualizaci (v tomto příkladu jsme vybrali zdrojové IP adresy). Chcete-li zobrazit seznam možných pomůcky, klikněte na šipku rozevíracího seznamu blue vlevo od pole a poté vyberte **rychlé hodnoty** ke generování widgetu. Měli byste vidět něco podobného jako na následujícím obrázku:

    ![Zdrojová IP adresa](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  Odtud můžete vybrat **přidat na řídicí panel** tlačítko v pravém horním rohu widgetu a vyberte odpovídající řídicí panel, který chcete přidat.

5.  Přejděte zpět na řídicí panel zobrazíte pomůcky, který jste právě přidali.

    Celou řadu dalších pomůcky například histogramy a počty můžete přidat na řídicí panel můžete sledovat důležité metriky, například ukázkový řídicí panel znázorněno na následujícím obrázku:

    ![Řídicí panel Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Další vysvětlení na řídicí panely a dalších typů pomůcky, najdete na Graylog [dokumentaci](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Díky integraci sledovací proces sítě se Graylog, máme teď pohodlný a centralizovaný způsob, jak spravovat a vizualizovat protokolů toku skupiny zabezpečení sítě. Graylog má počet další funkce, jako jsou datové proudy a výstrahy, které lze použít také pro další správu toku protokolů a lépe pochopit síťový provoz. Teď, když máte Graylog nastavení a připojení k Azure, klidně si pokračovat v průzkumu další funkce, které nabízí.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak protokolů toku skupiny zabezpečení sítě s Power BI vizualizovat navštivte stránky [skupinu zabezpečení sítě vizualizovat toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
