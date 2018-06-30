---
title: Řešení DNS analýzy v Azure Log Analytics | Microsoft Docs
description: Nastavit a použít řešení DNS analýzy v analýzy protokolů získat přehled o infrastruktuře DNS na zabezpečení, výkonu a operací.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: a6f63fac85008425f473f431ae85d04f62eed667
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127587"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Shromažďovat statistiky o infrastruktury služby DNS s řešením DNS Analytics Preview

![Symbol DNS Analytics](./media/log-analytics-dns/dns-analytics-symbol.png)

Tento článek popisuje, jak nastavit a používat Azure DNS Analytics řešení v Azure Log Analytics získat přehled o infrastruktuře DNS na zabezpečení, výkonu a operací.

Analýza DNS vám pomůže:

- Identifikace klientů, kteří se pokusí přeložit názvy škodlivý domény.
- Identifikujte zastaralé záznamy o prostředcích.
- Identifikujte často dotazovaných doménách a talkative klienty DNS.
- Zobrazení žádosti o zatížení serverů DNS.
- Zobrazení dynamické chyby registrace DNS.

Řešení shromažďuje, analyzuje a korelaci DNS Windows analýzy a protokoly auditu a další související data z vašich serverů DNS.

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které podporuje toto řešení:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ano | Řešení shromažďuje informace DNS z agentů v systému Windows. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Řešení neshromažďuje informace DNS z přímé agenty Linux. |
| [Skupina pro správu System Center Operations Manager](log-analytics-om-agents.md) | Ano | Řešení shromažďuje informace DNS z agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení z agenta nástroje Operations Manager k Operations Management Suite se nevyžaduje. Do úložiště služby Operations Management Suite se předají data ze skupiny pro správu. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Úložiště Azure není používán řešení. |

### <a name="data-collection-details"></a>Podrobnosti kolekce dat

Řešení shromažďuje DNS inventář a data související s DNS ze serverů DNS kde je nainstalován agent analýzy protokolů. Tato data pak nahrán do analýzy protokolů a zobrazit v řídicím panelu řešení. Shromažďuje data související s inventářem, například počet serverů DNS, zóny a záznamy o prostředcích, spuštěním rutin Powershellu DNS. Data se aktualizují každých dvou dnů. Událost související data jsou shromažďována téměř v reálném čase z [analýzy a protokoly auditu](https://technet.microsoft.com/library/dn800669.aspx#enhanc) poskytované rozšířené protokolování DNS a Diagnostika ve Windows serveru 2012 R2.

## <a name="configuration"></a>Konfigurace

Pomocí následujících informací nakonfigurujte řešení:

- Musíte mít [Windows](log-analytics-windows-agent.md) nebo [nástroje Operations Manager](log-analytics-om-agents.md) agent na každém serveru DNS, který chcete monitorovat.
- Řešení analýzy DNS můžete přidat do pracovního prostoru služby Operations Management Suite z [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Můžete také použít proces popsaný v [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).

Řešení spustí shromažďování dat bez nutnosti další konfigurace. Můžete však použít následující konfigurace přizpůsobit shromažďování dat.

### <a name="configure-the-solution"></a>Konfigurovat řešení

Na řídicím panelu řešení, klikněte na tlačítko **konfigurace** otevřete stránku konfigurace analýzy DNS. Existují dva typy změny konfigurace, které můžete použít:

- **Názvů domén seznam povolených adres**. Řešení nezpracovává všechny dotazy vyhledávání. Udržuje povolených přípon názvů domén. Vyhledávací dotazy, které přeložit názvy domén, které odpovídají přípony názvů domén v této povolených nejsou zpracovávány řešení. Nezpracuje názvy domén seznam povolených adres pomáhá optimalizovat data přenášená k analýze protokolů. Seznam povolených adres výchozí obsahuje názvy oblíbených veřejné domény, například www.google.com a www.facebook.com. Dokončení výchozím seznamu můžete zobrazit posouvání.

 Můžete upravit seznam přípon názvů domén, které chcete zobrazit insights vyhledávání pro přidání. Můžete také odebrat přípon názvů domén, které nechcete zobrazit insights vyhledávání pro.

- **Prahová hodnota talkative klienta**. Klienty DNS, které překročí prahovou hodnotu pro počet požadavky na vyhledávání jsou vyznačené na **klienty DNS** okno. Výchozí prahová hodnota je 1 000. Můžete upravit prahovou hodnotu.

    ![Seznam povolených adres názvů domén](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Sady Management Pack

Pokud používáte Microsoft Monitoring Agent pro připojení do pracovního prostoru služby Operations Management Suite, je nainstalována následující sada management pack:

- Microsoft DNS dat kolekce Intelligence Pack (Microsft.IntelligencePacks.Dns)

Pokud do pracovního prostoru služby Operations Management Suite je připojené skupině pro správu nástroje Operations Manager, nenainstalují se následující sady management Pack v nástroji Operations Manager při přidání tohoto řešení. Neexistuje žádné požadovanou konfiguraci nebo údržby těchto sad management Pack:

- Microsoft DNS dat kolekce Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Konfigurace DNS analýzy Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Použít řešení DNS Analytics

Tato část vysvětluje všechny řídicí panel funkce a jejich použití.

Po přidání řešení do pracovního prostoru, na dlaždici řešení na stránce Přehled služby Operations Management Suite poskytuje rychlý přehled infrastruktury služby DNS. Obsahuje počet serverů DNS, kde jsou shromažďována data. Zahrnuje také počet žádostí odeslaných klienty k vyřešení škodlivý domén za posledních 24 hodin. Když kliknete na dlaždici, otevře se řídicí panel řešení.

![Dlaždice DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Řídicí panel řešení

Řídicí panel řešení obsahuje souhrnné informace o různých funkcí řešení. Obsahuje taky odkazy na podrobná zobrazení pro forenzní analýzy a diagnostiku. Ve výchozím nastavení se zobrazí data za posledních 7 dní. Datum a čas rozsahu můžete změnit pomocí **ovládacího prvku pro výběr data a času**, jak je znázorněno na následujícím obrázku:

![Ovládacího prvku pro výběr čas](./media/log-analytics-dns/dns-time.png)

Řídicí panel řešení zobrazuje následující okna:

**Zabezpečení DNS**. Sestavy klienty DNS, které se pokouší komunikovat se zlými úmysly doménami. Pomocí Microsoft threat intelligence kanály můžete DNS Analytics detekuje klienta IP adres, který se pokoušíte získat přístup kyberzločinci domén. V mnoha případech nakažená malwarem zařízení "vytočit" center "příkazy a ovládání" škodlivý domény pomocí překladu názvu domény malwaru.

![Okno zabezpečení DNS](./media/log-analytics-dns/dns-security-blade.png)

Když kliknete na IP adresu klienta v seznamu, vyhledávání protokolu otevře a zobrazí podrobnosti o vyhledávání příslušných dotazu. V následujícím příkladu DNS Analytics zjistil, že komunikace bylo provedeno s [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Výsledky hledání protokolu zobrazující ircbot](./media/log-analytics-dns/ircbot.png)

Informace umožňuje identifikovat:

- Klient IP, která iniciovala komunikace.
- Název domény, který se přeloží na škodlivé IP.
- IP adresy, které překládá se název domény.
- Škodlivé IP adresa.
- Závažnost problému.
- Důvod seznamů zakázaných škodlivé IP.
- Čas detekce.

**Domény dotaz**. Poskytuje nejčastěji se vyskytující názvy domén, která je dotazována klienti DNS ve vašem prostředí. Můžete zobrazit seznam všech názvů domény dotaz. Můžete také přejít dolů na žádost o vyhledávání podrobné informace o konkrétní doménu v hledání protokolů.

![Okno dotazovaný domén](./media/log-analytics-dns/domains-queried-blade.png)

**Klienti DNS**. Sestavy klientů *před nedodržením prahovou hodnotu* pro počet dotazů ve zvoleném časovém období. Můžete zobrazit seznam všech klientů DNS a podrobnosti o dotazy provedené je v hledání protokolů.

![Okno klienty DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registrace serveru DNS dynamické**. Sestavy název chyby registrace. Všechny chyby při registraci pro adresu [záznamy o prostředcích](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A a AAAA) jsou vyznačené společně s klienta IP adres, který vytvořil žádosti o registraci. Tyto informace pak můžete najít příčinu selhání registrace pomocí následujících kroků:

1. Najít zónu, který je autoritativní pro název, který se klient pokouší aktualizovat.

2. Použijte řešení ke kontrole informací o inventáři této zóny.

3. Ověřte, že je povolena dynamická aktualizace pro zónu.

4. Zkontrolujte, zda zóny je nakonfigurován pro zabezpečené dynamické aktualizace, nebo ne.

    ![Dynamické okno registrace serveru DNS](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Název žádosti o registraci**. Horní dlaždice ukazuje spojnici trendu úspěšné i neúspěšné požadavky na dynamické aktualizace DNS. Dlaždici nižší uvádí top 10 klienty, kteří odesílají žádosti o aktualizace se nezdařila DNS na servery DNS, seřazené podle počet selhání.

![Okno požadavky registrace názvu ](./media/log-analytics-dns/name-reg-req-blade.png)

**Ukázkové dotazy Analytics DDI**. Obsahuje seznam nejčastějších dotazů vyhledávání, které načíst nezpracovaná analytická data přímo.


![Ukázkové dotazy](./media/log-analytics-dns/queries.png)

Tyto dotazy můžete použít jako východisko pro vytvoření vlastní dotazy pro vytváření přizpůsobených sestav. Dotazy na odkaz na stránku hledání DNS analýzy protokolů, kde se nezobrazí výsledky:

- **Seznam serverů DNS**. Zobrazuje seznam všech serverů DNS s jejich přidružené plně kvalifikovaný název domény, název domény, název doménové struktury a server IP adresy.
- **Seznam zón DNS**. Zobrazuje seznam všech zón DNS s název přidružené zóny, dynamická aktualizace stavu, názvové servery a stav podepsání DNSSEC.
- **Záznamy o prostředcích nepoužívané**. Zobrazuje seznam všech záznamů prostředků nepoužívané nebo zastaralé. Tento seznam obsahuje název záznamu prostředků, typ záznamu o prostředku, přidružené k serveru DNS, doba generování záznamů a název zóny. Tento seznam slouží k identifikaci záznamy prostředků DNS, které jsou již používán. Na základě těchto informací, můžete pak odebrat tyto záznamy ze serverů DNS.
- **Servery DNS dotaz zatížení**. Obsahuje informace, abyste měli hlediska zatížení DNS na serverech DNS. Tyto informace mohou pomoci při plánování kapacity pro servery. Můžete přejít na **metriky** a změňte zobrazení grafické vizualizaci. Toto zobrazení pomáhá pochopit, jak je zatížení DNS distribuován do vaše servery DNS. Dotaz DNS zobrazuje trendy rychlost pro každý server.

    ![Výsledky hledání protokolu dotazu servery DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Zóny DNS dotaz zatížení**. Zobrazuje statistiku zóny dotaz za sekundu DNS všech zón na serverech DNS, který je spravován systémem řešení. Klikněte **metriky** a změňte zobrazení z podrobné záznamy na grafické vizualizaci výsledky.
- **Události konfigurace**. Zobrazí všechny události změny konfigurace služby DNS a související zprávy. Potom můžete filtrovat tyto události podle času serveru DNS událostí, ID události, nebo kategorii úloh. Data můžete auditovat změny provedené na servery DNS v určitých časech.
- **Analytické protokol DNS**. Zobrazí všechny analytické události ve všech serverech DNS spravuje řešení. Potom můžete filtrovat tyto události podle času serveru DNS událostí, ID události, IP adresa klienta, který vytvořil dotaz vyhledávání a kategorie úloh typ dotazu. Analytické události DNS serveru povolit aktivity sledování na serveru DNS. Analytické událost je protokolována pokaždé, když server odeslání nebo přijetí informací DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Hledání s použitím hledání DNS analýzy protokolů

Na stránce hledání protokolů můžete vytvořit dotaz. Můžete filtrovat výsledky hledání pomocí ovládacích prvků omezující vlastnosti. Můžete také vytvořit pokročilými dotazy transformace, filtr a sestavy na výsledky. Spusťte v následujících dotazech:

1. V **vyhledávacího dotazu pole**, typ `DnsEvents` zobrazíte všechny události DNS generovaných servery DNS, které spravuje řešení. Výsledky seznam data protokolu pro všechny události související s dotazy vyhledávání, dynamických registrací a změny konfigurace.

    ![Hledání DnsEvents protokolů](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Chcete-li zobrazit data protokolu pro vyhledávací dotazy, vyberte **LookUpQuery** jako **dílčí** filtru z ovládacího prvku omezující vlastnosti na levé straně. Tabulka, která obsahuje seznam všech událostí vyhledávací dotaz pro vybrané časové období se zobrazí.

    b. Chcete-li zobrazit data protokolu pro dynamické registrace, vyberte **DynamicRegistration** jako **dílčí** filtru z ovládacího prvku omezující vlastnosti na levé straně. Tabulka, která zobrazuje všechny události dynamickou registraci pro vybrané časové období se zobrazí.

    c. Chcete-li zobrazit data protokolu pro změny konfigurace, vyberte **ConfigurationChange** jako **dílčí** filtru z ovládacího prvku omezující vlastnosti na levé straně. Tabulka, která obsahuje seznam všech událostí změny konfigurace pro vybrané časové období se zobrazí.

2. V **vyhledávacího dotazu pole**, typ `DnsInventory` zobrazíte všechny související s inventářem data DNS pro servery DNS, které spravuje řešení. Výsledky seznam data protokolu pro servery DNS, zóny DNS a záznamy o prostředcích.

    ![Hledání DnsInventory protokolů](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Váš názor

Poskytnete zpětnou vazbu dvěma způsoby:

- **UserVoice**. POST nápady pro analýzy DNS funkce pro práci na. Přejděte [Operations Management Suite UserVoice stránky](https://aka.ms/dnsanalyticsuservoice).
- **Připojení k naší kohorty**. Vždy zajímá s nové zákazníky připojení k naší kohorty využívat nové funkce a Pomozte nám vylepšit DNS Analytics. Pokud vás zajímá připojení naše kohorty, vyplňte [tento rychlý průzkum](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Další postup

[V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné záznamy protokolu DNS.
