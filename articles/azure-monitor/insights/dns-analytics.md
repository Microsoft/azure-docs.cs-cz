---
title: Řešení DNS Analytics ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Nastavte a použijte řešení DNS Analytics v Azure Monitoru, abyste získali přehled o infrastruktuře DNS o zabezpečení, výkonu a operacích.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657329"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>S přehledy o infrastruktuře DNS můžete získat pomocí řešení DNS Analytics Preview

![Symbol DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

Tento článek popisuje, jak nastavit a používat řešení Azure DNS Analytics v Azure Monitoru ke shromažďování přehledů o infrastruktuře DNS v oblasti zabezpečení, výkonu a operací.

Služba DNS Analytics vám pomůže:

- Identifikujte klienty, kteří se pokoušejí vyřešit škodlivé názvy domén.
- Identifikujte zastaralé záznamy o prostředcích.
- Identifikujte často dotazovaná doménová jména a hovorové klienty DNS.
- Zobrazení zatížení požadavků na serverech DNS.
- Zobrazení dynamických chyb registrace DNS.

Řešení shromažďuje, analyzuje a koreluje protokoly analýzy a auditu služby Windows DNS a další související data ze serverů DNS.

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje podporované tímto řešením:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../platform/agent-windows.md) | Ano | Řešení shromažďuje informace DNS od agentů systému Windows. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace DNS od přímých agentů Linuxu. |
| [Skupina pro správu nástroje System Center Operations Manager](../platform/om-agents.md) | Ano | Řešení shromažďuje informace DNS od agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení agenta Operations Manager u Azure Monitor není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet služby Azure Storage](../platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure se nepoužívá řešení. |

### <a name="data-collection-details"></a>Podrobnosti o shromažďování dat

Řešení shromažďuje data inventáře DNS a událostí DNS ze serverů DNS, kde je nainstalován agent Analýzy protokolů. Tato data se pak nahrají do Azure Monitoru a zobrazí se na řídicím panelu řešení. Data související se inventářem, například počet serverů DNS, zón a záznamů o prostředcích, se shromažďují spuštěním rutin dns prostředí PowerShell. Data jsou aktualizována jednou za dva dny. Data související s událostmi jsou shromažďována téměř v reálném čase z [analytických protokolů a protokolů auditu poskytovaných](https://technet.microsoft.com/library/dn800669.aspx#enhanc) rozšířeným protokolováním a diagnostikou DNS v systému Windows Server 2012 R2.

## <a name="configuration"></a>Konfigurace

Ke konfiguraci řešení použijte následující informace:

- Na každém serveru DNS, který chcete sledovat, musíte mít agenta [systému Windows](../platform/agent-windows.md) nebo [nástroje Operations Manager.](../platform/om-agents.md)
- Řešení DNS Analytics můžete přidat do pracovního prostoru Log Analytics z [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Proces popsaný v [části Přidání řešení Azure Monitor](solutions.md)uvidíte také v Galerii řešení .

Řešení začne shromažďovat data bez nutnosti další konfigurace. K přizpůsobení shromažďování dat však můžete použít následující konfiguraci.

### <a name="configure-the-solution"></a>Konfigurace řešení

Na řídicím panelu řešení kliknutím na **Konfigurace** otevřete stránku Konfigurace služby DNS Analytics. Existují dva typy změn konfigurace, které můžete provést:

- **Názvy domén uvedené na seznamu povolených**. Řešení nezpracovává všechny vyhledávací dotazy. Udržuje whitelist přípon názvů domén. Vyhledávací dotazy, které se přelašují na názvy domén, které odpovídají příponám názvů domén v tomto seznamu povolených adres, nejsou řešením zpracovány. Nezpracování názvů domén uvedených na seznamu povolených pomáhá optimalizovat data odeslaná do Služby Azure Monitor. Výchozí seznam povolených obsahuje populární názvy veřejných domén, například www.google.com a www.facebook.com. Celý výchozí seznam můžete zobrazit posouváním.

  Seznam můžete upravit tak, aby přidával libovolnou příponu názvu domény, pro kterou chcete zobrazit přehledy vyhledávání. Můžete také odebrat libovolnou příponu názvu domény, pro kterou nechcete zobrazit přehledy vyhledávání.

- **Upovídaný práh klienta**. Klienti DNS, kteří překračují prahovou hodnotu pro počet vyhledávacích požadavků, jsou zvýrazněni v okně **Klienti DNS.** Výchozí prahová hodnota je 1 000. Prahovou hodnotu můžete upravit.

    ![Názvy domén na seznamu povolených](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Sady Management Pack

Pokud používáte Microsoft Monitoring Agent pro připojení k pracovnímu prostoru Log Analytics, je nainstalována následující sada Management Pack:

- Sada Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Pokud je vaše skupina pro správu nástroje Operations Manager připojena k pracovnímu prostoru Analýzy protokolů, jsou v nástroji Operations Manager při přidání tohoto řešení nainstalovány následující sady Management Pack. Neexistuje žádná požadovaná konfigurace nebo údržba těchto sad Management Pack:

- Sada Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Konfigurace analýzy SLUŽBY DNS poradce systémového centra (Microsoft.IntelligencePack.Dns.Configuration)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Použití řešení DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Dlaždice DNS obsahuje počet serverů DNS, na kterých jsou data shromažďována. Zahrnuje také počet požadavků klientů na vyřešení škodlivých domén za posledních 24 hodin. Po kliknutí na dlaždici se otevře řídicí panel řešení.

![Dlaždice DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Řídicí panel řešení

Řídicí panel řešení zobrazuje souhrnné informace o různých funkcích řešení. Obsahuje také odkazy na podrobný pohled na forenzní analýzu a diagnostiku. Ve výchozím nastavení jsou data zobrazena za posledních sedm dní. Rozsah data a času můžete změnit pomocí **ovládacího prvku výběru data a času**, jak je znázorněno na následujícím obrázku:

![Řízení výběru času](./media/dns-analytics/dns-time.png)

Řídicí panel řešení zobrazuje následující čepele:

**Zabezpečení DNS**. Hlásí klienty DNS, kteří se pokoušejí komunikovat se škodlivými doménami. Pomocí zdrojů Microsoft threat intelligence dokáže služba DNS Analytics rozpoznat klientské IP adresy, které se pokoušejí získat přístup ke škodlivým doménám. V mnoha případech, malware-infikované zařízení "vytočit" na "příkaz a řízení" centrum škodlivé domény vyřešením malware název domény.

![Okno DNS Security](./media/dns-analytics/dns-security-blade.png)

Po klepnutí na ip adresu klienta v seznamu se otevře hledání protokolu a zobrazí se podrobnosti o vyhledávání příslušného dotazu. V následujícím příkladu služba DNS Analytics zjistila, že komunikace byla provedena pomocí [zařízení IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Protokolovat výsledky hledání zobrazující ircbot](./media/dns-analytics/ircbot.png)

Tyto informace vám pomohou identifikovat:

- Ip klienta, který inicioval komunikaci.
- Název domény, který se překládá na škodlivou ADRESU IP.
- Adresy IP, na které se název domény překládá.
- Škodlivá IP adresa.
- Závažnost problému.
- Důvod pro zařazení na černou listinu škodlivé IP.
- Doba detekce.

**Dotazované domény**. Poskytuje nejčastější názvy domén dotazované klienty DNS ve vašem prostředí. Můžete zobrazit seznam všech dotazovaných názvů domén. Můžete také přejít k podrobnostem vyhledávací žádosti o konkrétní název domény ve vyhledávání protokolu.

![Okno dotazovaných domén](./media/dns-analytics/domains-queried-blade.png)

**Klienti DNS**. Hlásí *klientům, kteří překračují prahovou hodnotu* pro počet dotazů ve zvoleném časovém období. Můžete zobrazit seznam všech klientů DNS a podrobnosti o dotazech, které provedli ve vyhledávání protokolů.

![Okno klienti DNS](./media/dns-analytics/dns-clients-blade.png)

**Dynamické registrace DNS**. Hlásí selhání registrace názvů. Všechny chyby registrace [pro záznamy o prostředcích](https://en.wikipedia.org/wiki/List_of_DNS_record_types) adresy (typ A a AAAA) jsou zvýrazněny spolu s IP adresy klienta, které podaly žádosti o registraci. Tyto informace pak můžete použít k nalezení hlavní příčiny selhání registrace pomocí následujících kroků:

1. Vyhledejte zónu, která je autoritativní pro název, který se klient pokouší aktualizovat.

1. Řešení slouží ke kontrole informací o zásobách této zóny.

1. Ověřte, zda je povolena dynamická aktualizace zóny.

1. Zkontrolujte, zda je zóna nakonfigurována pro zabezpečenou dynamickou aktualizaci či nikoli.

    ![Dynamické registrace DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Žádosti o registraci názvu**. Horní dlaždice zobrazuje spojnice trendu úspěšných a neúspěšných požadavků na dynamickou aktualizaci DNS. Dolní dlaždice obsahuje seznam 10 nejlepších klientů, kteří odesílají neúspěšné požadavky na aktualizaci DNS na servery DNS seřazené podle počtu chyb.

![Okno požadavky na registraci názvu](./media/dns-analytics/name-reg-req-blade.png)

**Ukázkové dotazy Analýzy DDI**. Obsahuje seznam nejběžnějších vyhledávacích dotazů, které přímo načítají nezpracovaná analytická data.


![Ukázkové dotazy](./media/dns-analytics/queries.png)

Tyto dotazy můžete použít jako výchozí bod pro vytváření vlastních dotazů pro vlastní vytváření sestav. Odkaz na dotazy na stránku hledání protokolu služby DNS Analytics, kde jsou zobrazeny výsledky:

- **Seznam serverů DNS**. Zobrazí seznam všech serverů DNS s přidruženým názvem FQDN, názvem domény, názvem doménové struktury a ip adresami serveru.
- **Seznam zón DNS**. Zobrazí seznam všech zón DNS s názvem přidružené zóny, stavem dynamické aktualizace, názvovými servery a stavem podepisování DNSSEC.
- **Nepoužívané záznamy o prostředcích**. Zobrazí seznam všech nepoužívaných nebo zastaralých záznamů o prostředcích. Tento seznam obsahuje název záznamu o prostředku, typ záznamu prostředku, přidružený server DNS, čas generování záznamů a název zóny. Tento seznam můžete použít k identifikaci záznamů o prostředcích DNS, které se již nepoužívají. Na základě těchto informací pak můžete tyto položky odebrat ze serverů DNS.
- **Servery DNS načítají dotaz**. Zobrazuje informace, takže můžete získat perspektivu zatížení DNS na serverech DNS. Tyto informace vám mohou pomoci naplánovat kapacitu serverů. Chcete-li změnit zobrazení na grafickou vizualizaci, můžete přejít na kartu **Metriky.** Toto zobrazení vám pomůže pochopit, jak je zatížení DNS distribuováno mezi servery DNS. Zobrazuje trendy rychlosti dotazování DNS pro každý server.

    ![Výsledky hledání protokolu dotazů dns serverů](./media/dns-analytics/dns-servers-query-load.png)

- **Načtení dotazu zón DNS**. Zobrazuje statistiky dns zóny dotazování za sekundu všech zón na serverech DNS spravovaných řešením. Kliknutím na kartu **Metriky** změníte zobrazení z podrobných záznamů na grafickou vizualizaci výsledků.
- **Události konfigurace**. Zobrazuje všechny události změny konfigurace DNS a přidružené zprávy. Tyto události pak můžete filtrovat podle času události, ID události, serveru DNS nebo kategorie úkolů. Data vám mohou pomoci auditovat změny provedené na konkrétních serverech DNS v určitých časech.
- **Analytický protokol DNS**. Zobrazuje všechny analytické události na všech serverech DNS spravovaných řešením. Tyto události pak můžete filtrovat podle času události, ID události, serveru DNS, IP adresy klienta, která provedla vyhledávací dotaz, a kategorie úloh typu dotazu. Analytické události serveru DNS umožňují sledování aktivity na serveru DNS. Analytická událost je zaznamenána při každém odeslání nebo přijetí informací DNS serverem.

### <a name="search-by-using-dns-analytics-log-search"></a>Vyhledávání pomocí vyhledávání protokolů služby DNS Analytics

Na stránce Hledání protokolu můžete vytvořit dotaz. Výsledky hledání můžete filtrovat pomocí ovládacích prvků omezující vlastnosti. Můžete také vytvořit rozšířené dotazy pro transformaci, filtrování a vytváření zpráv o výsledcích. Začněte pomocí následujících dotazů:

1. Do **pole vyhledávacího dotazu**zadejte, chcete-li `DnsEvents` zobrazit všechny události DNS generované servery DNS spravované řešením. Ve výsledcích jsou uvedena data protokolu pro všechny události související s vyhledávacími dotazy, dynamickými registracemi a změnami konfigurace.

    ![Vyhledávání protokolu DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Chcete-li zobrazit data protokolu pro vyhledávací dotazy, vyberte **LookUpQuery** jako filtr **podtypu** z ovládacího prvku omezující vlastnost vlevo. Zobrazí se tabulka se seznamem všech událostí vyhledávacího dotazu pro vybrané časové období.

    b. Chcete-li zobrazit data protokolu pro dynamické registrace, vyberte **DynamicRegistration** jako filtr **podtypu** z ovládacího prvku omezující vlastnost vlevo. Zobrazí se tabulka se seznamem všech událostí dynamické registrace pro vybrané časové období.

    c. Chcete-li zobrazit data protokolu pro změny konfigurace, vyberte **ConfigurationChange** jako filtr **podtypu** z ovládacího prvku omezující vlastnost vlevo. Zobrazí se tabulka se seznamem všech událostí změny konfigurace pro vybrané časové období.

1. Do **pole vyhledávacího dotazu**zadejte, `DnsInventory` chcete-li zobrazit všechna data související se inventářem DNS pro servery DNS spravované řešením. Ve výsledcích jsou uvedena data protokolu pro servery DNS, zóny DNS a záznamy o prostředcích.

    ![Vyhledávání protokolu DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Řešení potíží

Běžné kroky řešení potíží:

1. Chybějící data vyhledávání DNS – Chcete-li tento problém vyřešit, zkuste resetovat konfiguraci nebo pouze načíst konfigurační stránku jednou na portálu. Chcete-li resetovat, stačí změnit nastavení na jinou hodnotu, potom ji změnit zpět na původní hodnotu a uložit konfiguraci.

## <a name="feedback"></a>Váš názor

Chcete-li poskytnout zpětnou vazbu, navštivte [stránku UserVoice služby Log Analytics,](https://aka.ms/dnsanalyticsuservoice) kde můžete zveřejnit nápady pro funkce DNS Analytics, na kterých můžete pracovat. 

## <a name="next-steps"></a>Další kroky

[Protokoly dotazů](../log-query/log-query-overview.md) pro zobrazení podrobných záznamů protokolu DNS.
