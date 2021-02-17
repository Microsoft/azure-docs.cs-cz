---
title: DNS Analytics řešení v Azure Monitor | Microsoft Docs
description: Nastavte a použijte řešení DNS Analytics v Azure Monitor k získání přehledu o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: c3934af1af040b8b45175bacde43237802ab82cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582399"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Získejte přehled o vaší infrastruktuře DNS pomocí řešení DNS Analytics Preview.

![Symbol DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

Tento článek popisuje, jak nastavit a používat řešení Azure DNS Analytics v Azure Monitor k získání přehledu o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu.

DNS Analytics vám pomůže:

- Identifikujte klienty, kteří se pokoušejí přeložit názvy škodlivých domén.
- Identifikujte zastaralé záznamy o prostředcích.
- Identifikujte často dotazovaný název domény a klienty DNS prahová.
- Zobrazení žádosti o načtení na serverech DNS.
- Zobrazení selhání dynamické registrace DNS

Řešení shromažďuje, analyzuje a koreluje protokoly a protokoly služby DNS pro Windows a další související data ze serverů DNS.

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../agents/agent-windows.md) | Yes | Řešení shromažďuje informace o DNS z agentů Windows. |
| [Agenti systému Linux](../vm/quick-collect-linux-computer.md) | No | Řešení neshromažďuje informace DNS z přímých agentů systému Linux. |
| [Skupina pro správu nástroje System Center Operations Manager](../agents/om-agents.md) | Yes | Řešení shromažďuje informace o DNS od agentů v připojené skupině pro správu Operations Manager. Přímé připojení od agenta Operations Manager k Azure Monitor není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet úložiště Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Řešení Azure Storage se v řešení nepoužívá. |

### <a name="data-collection-details"></a>Podrobnosti shromažďování dat

Řešení shromažďuje data o inventáři DNS a událostech DNS ze serverů DNS, na kterých je nainstalovaný agent Log Analytics. Tato data se pak nahrají do Azure Monitor a zobrazí se na řídicím panelu řešení. Data související s inventářem, jako je třeba počet serverů DNS, zón a záznamů prostředků, se shromažďují spuštěním rutin služby DNS PowerShell. Data se aktualizují po každém dvou dnech. Data související s událostmi se shromažďují prakticky v reálném čase z [protokolů analýzy a auditu](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) , které nabízí rozšířené protokolování a diagnostika DNS ve Windows Serveru 2012 R2.

## <a name="configuration"></a>Konfigurace

Pro konfiguraci řešení použijte následující informace:

- Na každém serveru DNS, který chcete monitorovat, musíte mít agenta [Windows](../agents/agent-windows.md) nebo [Operations Manager](../agents/om-agents.md) .
- Řešení DNS Analytics můžete přidat do pracovního prostoru Log Analytics z [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Můžete také použít proces popsaný v tématu [přidání Azure monitor řešení z galerie řešení](solutions.md).

Řešení začne shromažďovat data bez nutnosti další konfigurace. K přizpůsobení shromažďování dat ale můžete použít následující konfiguraci.

### <a name="configure-the-solution"></a>Konfigurace řešení

Kliknutím na položku **Konfigurace** na řídicím panelu řešení otevřete stránku konfigurace DNS Analytics. Existují dva typy změn konfigurace, které lze provést:

- **Allowlisted názvy domén**. Řešení nezpracovává všechny vyhledávací dotazy. Udržuje povolených přípon názvů domén. Řešení vyhledávacích dotazů, které jsou přeloženy na názvy domén, které odpovídají příponám názvů domén v tomto povolených, nejsou zpracovávány řešením. Nezpracovávání názvů domén allowlisted přispívá k optimalizaci dat odesílaných do Azure Monitor. Výchozí povolených zahrnuje oblíbené názvy veřejných domén, jako je například www.google.com a www.facebook.com. Úplný výchozí seznam můžete zobrazit posouváním.

  Seznam můžete upravit tak, aby se přidala libovolná přípona názvu domény, pro kterou chcete zobrazit přehledy vyhledávání. Můžete také odebrat libovolnou příponu názvu domény, pro kterou nechcete zobrazit přehledy vyhledávání.

- **Prahová hodnota klienta prahová** Klienti DNS, kteří překročí prahovou hodnotu pro počet požadavků na vyhledávání, se v okně **klienti DNS** zvýrazní. Výchozí prahová hodnota je 1 000. Prahovou hodnotu můžete upravit.

    ![Názvy domén Allowlisted](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Sady Management Pack

Pokud používáte Microsoft Monitoring Agent pro připojení k pracovnímu prostoru Log Analytics, je nainstalovaná tato Management Pack:

- Sada Microsoft DNS data collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Pokud je vaše skupina pro správu Operations Manager připojená k pracovnímu prostoru Log Analytics, po přidání tohoto řešení se do Operations Manager nainstalují následující sady Management Pack. Není nutná žádná konfigurace ani údržba těchto sad Management Pack:

- Sada Microsoft DNS data collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Konfigurace nástroje Microsoft System Center Advisor DNS Analytics (Microsoft.IntelligencePack.Dns.Configuration)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../agents/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Použití řešení DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Dlaždice DNS obsahuje počet serverů DNS, ve kterých se shromažďují data. Zahrnuje taky počet požadavků, které klienti udělali k překladu škodlivých domén za posledních 24 hodin. Když kliknete na dlaždici, otevře se řídicí panel řešení.

![Dlaždice DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Řídicí panel řešení

Řídicí panel řešení zobrazuje souhrnné informace o různých funkcích řešení. Obsahuje také odkazy na podrobné zobrazení pro analýzu a diagnostiku forenzní. Ve výchozím nastavení se data zobrazují za posledních sedm dní. Rozsah data a času lze změnit pomocí **ovládacího prvku výběru data a času**, jak je znázorněno na následujícím obrázku:

![Ovládací prvek výběru času](./media/dns-analytics/dns-time.png)

Řídicí panel řešení zobrazuje následující okna:

**Zabezpečení DNS**. Oznamuje klienty DNS, kteří se pokoušejí komunikovat se škodlivými doménami. Pomocí kanálů Microsoft Threat Intelligence DNS Analytics můžou detekovat klientské IP adresy, které se pokoušejí získat přístup k škodlivým doménám. V mnoha případech jsou zařízení nakažená malwarem "vytáčet" na "Command and Control" Center pro škodlivou doménu tím, že překládá název domény malwaru.

![Okno zabezpečení DNS](./media/dns-analytics/dns-security-blade.png)

Po kliknutí na IP adresu klienta v seznamu se otevře okno hledání protokolu s podrobnostmi o vyhledávání příslušného dotazu. V následujícím příkladu DNS Analytics zjistila, že komunikace byla provedena s [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Výsledky hledání v protokolu zobrazující IRCBot](./media/dns-analytics/ircbot.png)

Tyto informace vám pomůžou identifikovat:

- IP adresa klienta, která iniciovala komunikaci.
- Název domény, který se překládá na škodlivou IP adresu.
- IP adresy, na které se název domény překládá.
- Škodlivá IP adresa.
- Závažnost problému.
- Důvod, proč se Blocklisting škodlivá IP adresa
- Čas detekce

**Dotazování domén**. Poskytuje nejčastěji používané názvy domén, které klienti DNS ve vašem prostředí dotazují. Můžete zobrazit seznam všech dotazovaných názvů domén. V hledání v protokolu můžete také přejít k podrobnostem o požadavku vyhledávání pro konkrétní název domény.

![Okno dotazované domény](./media/dns-analytics/domains-queried-blade.png)

**Klienti DNS**. Oznamuje klientům *porušení prahové hodnoty* pro počet dotazů ve zvoleném časovém období. Můžete zobrazit seznam všech klientů DNS a podrobnosti o dotazech, které jsou v hledání v protokolu provedené.

![Okno klienti DNS](./media/dns-analytics/dns-clients-blade.png)

**Dynamické registrace DNS**. Oznamuje selhání registrace názvu. Všechny chyby registrace [záznamů prostředků](https://en.wikipedia.org/wiki/List_of_DNS_record_types) adres (typ a a AAAA) jsou zvýrazněny spolu s klientskými IP adresami, které vytvořily žádosti o registraci. Tyto informace pak můžete použít k vyhledání hlavní příčiny selhání registrace pomocí následujících kroků:

1. Vyhledejte zónu, která je autoritativní pro název, který se klient pokouší aktualizovat.

1. Použijte řešení pro kontrolu informací o inventáři této zóny.

1. Ověřte, zda je dynamická aktualizace pro zónu povolena.

1. Ověřte, jestli je zóna nakonfigurovaná na zabezpečenou dynamickou aktualizaci, nebo ne.

    ![Okno dynamické registrace DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Žádosti o registraci názvu** Horní dlaždice zobrazuje trend úspěšných a neúspěšných požadavků na dynamickou aktualizaci DNS. Dolní dlaždice obsahuje prvních 10 klientů, kteří odesílají neúspěšné požadavky na aktualizaci DNS na servery DNS seřazené podle počtu selhání.

![Okno žádosti o registraci názvu](./media/dns-analytics/name-reg-req-blade.png)

**Ukázkové dotazy pro analýzy DDI** Obsahuje seznam nejběžnějších vyhledávacích dotazů, které přímo načítají nezpracované analytické údaje.


![Ukázkové dotazy](./media/dns-analytics/queries.png)

Tyto dotazy můžete použít jako výchozí bod pro vytváření vlastních dotazů pro přizpůsobené vytváření sestav. Dotazy odkazují na stránku hledání protokolu DNS Analytics, kde se zobrazují výsledky:

- **Seznam serverů DNS**. Zobrazuje seznam všech serverů DNS, které mají přidružený plně kvalifikovaný název domény, název domény, název doménové struktury a IP adresy serveru.
- **Seznam zóny DNS**. Zobrazuje seznam všech zón DNS s názvem přidružené zóny, stavem dynamické aktualizace, názvové servery a stavem podepisování DNSSEC.
- **Nepoužívané záznamy prostředků** Zobrazuje seznam všech nepoužívaných/zastaralých záznamů prostředků. Tento seznam obsahuje název záznamu prostředku, typ záznamu prostředku, přidružený server DNS, čas generování záznamu a název zóny. Pomocí tohoto seznamu můžete identifikovat záznamy prostředků DNS, které se už nepoužívají. Na základě těchto informací můžete tyto položky odebrat ze serverů DNS.
- **Načítání dotazů serverů DNS**. Zobrazuje informace, abyste mohli získat perspektivu zatížení DNS na serverech DNS. Tyto informace vám pomůžou naplánovat kapacitu serverů. Můžete přejít na kartu **metriky** a změnit zobrazení na grafickou vizualizaci. Toto zobrazení vám pomůže pochopit, jak je zatížení DNS distribuované napříč vašimi servery DNS. Zobrazuje trendy míry dotazů DNS pro každý server.

    ![Výsledky hledání protokolu dotazů serverů DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Zóny DNS načtení dotazu**. Zobrazuje statistiku DNS Zone-to-Second všech zón na serverech DNS spravovaných řešením. Klikněte na kartu **metriky** a změňte zobrazení z podrobných záznamů na grafickou vizualizaci výsledků.
- **Události konfigurace**. Zobrazuje všechny události změny konfigurace služby DNS a související zprávy. Tyto události pak můžete filtrovat na základě času události, ID události, serveru DNS nebo kategorie úlohy. Tato data vám pomůžou při konkrétní době auditovat změny provedené na konkrétních serverech DNS.
- **Protokol analýzy DNS**. Zobrazí všechny analytické události na všech serverech DNS spravovaných řešením. Tyto události pak můžete filtrovat na základě času události, ID události, serveru DNS, IP adresy klienta, která vytvořila vyhledávací dotaz, a kategorie úlohy typ dotazu. Analytické události serveru DNS umožňují sledování aktivit na serveru DNS. Analytická událost se protokoluje pokaždé, když server odesílá nebo přijímá informace DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Hledání pomocí DNS Analytics prohledávání protokolu

Na stránce prohledávání protokolu můžete vytvořit dotaz. Výsledky hledání můžete filtrovat pomocí ovládacích prvků omezující vlastnosti. Můžete také vytvořit rozšířené dotazy pro transformaci, filtrování a vytváření sestav výsledků. Začněte tím, že použijete následující dotazy:

1. Do **pole vyhledávací dotaz** zadejte, pokud `DnsEvents` chcete zobrazit všechny události DNS vygenerované servery DNS spravovanými řešením. Výsledky uvádějí data protokolu pro všechny události související s vyhledávacími dotazy, dynamickými registracemi a změnami konfigurace.

    ![Hledání protokolu DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Chcete-li zobrazit data protokolu pro vyhledávací dotazy, vyberte **LookUpQuery** jako filtr **podtypu** z ovládacího prvku omezující vlastnosti na levé straně. Zobrazí se tabulka se seznamem všech událostí vyhledávacího dotazu pro vybrané časové období.

    b. Chcete-li zobrazit data protokolu pro dynamické registrace, vyberte **DynamicRegistration** jako filtr **podtypu** z ovládacího prvku omezující vlastnost na levé straně. Zobrazí se tabulka se seznamem všech událostí dynamické registrace pro vybrané časové období.

    c. Chcete-li zobrazit data protokolu pro změny konfigurace, vyberte **ConfigurationChange** jako filtr **podtypu** z ovládacího prvku omezující vlastnost na levé straně. Zobrazí se tabulka se seznamem všech událostí změny konfigurace pro vybrané časové období.

1. Do **pole vyhledávací dotaz** zadejte, `DnsInventory` Chcete-li zobrazit všechna data týkající se inventarizace DNS pro servery DNS spravované řešením. Výsledky uvádějí data protokolu pro servery DNS, zóny DNS a záznamy prostředků.

    ![Hledání protokolu DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Řešení potíží

Běžné kroky při řešení potíží:

1. Chybějící data vyhledávání DNS – Chcete-li tento problém vyřešit, zkuste resetovat konfiguraci nebo načíst stránku konfigurace jenom jednou na portálu. Pro obnovení stačí změnit nastavení na jinou hodnotu a pak ho změnit zpátky na původní hodnotu a uložit konfiguraci.

## <a name="suggestions"></a>Návrhy

Pokud chcete poskytnout zpětnou vazbu, přejděte na [stránku Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) , kde najdete nápady, jak pracovat s funkcemi DNS Analytics. 

## <a name="next-steps"></a>Další kroky

V [protokolech dotazů](../logs/log-query-overview.md) zobrazíte podrobné záznamy protokolu DNS.
