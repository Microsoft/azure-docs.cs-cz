---
title: Diagnostika s metrikami, výstrahami a stavem prostředků – Azure Standard Load Balancer
description: K diagnostice Standard Load Balancer Azure použijte dostupné metriky, výstrahy a informace o stavu prostředků.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: 43d83d994c9a4ee3cf89b584f6c3835a62fa2cfe
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806002"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostika služby Standard Load Balancer s metrikami, upozorněními a stavem prostředků

Azure Standard Load Balancer zpřístupňuje následující diagnostické možnosti:

* Multidimenzionální **metriky a upozornění**: poskytuje multidimenzionální diagnostické možnosti prostřednictvím [Azure monitor](../azure-monitor/overview.md) pro standardní konfigurace nástroje pro vyrovnávání zatížení. Můžete monitorovat, spravovat a řešit potíže s prostředky standardního nástroje pro vyrovnávání zatížení.

* **Resource Health**: stav Resource Health Load Balancer je k dispozici na stránce Resource Health pod položkou monitor. Tato automatická rezervace vás informuje o aktuální dostupnosti vašeho prostředku Load Balancer.
Tento článek poskytuje rychlou prohlídku těchto funkcí a nabízí způsoby jejich použití pro Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Multidimenzionální metriky

Azure Load Balancer poskytuje multidimenzionální metriky prostřednictvím metrik Azure v Azure Portal a pomáhá získat diagnostické přehledy v reálném čase do vašich prostředků nástroje pro vyrovnávání zatížení. 

Různé konfigurace Standard Load Balancer poskytují následující metriky:

| Metric | Typ prostředku | Popis | Doporučená agregace |
| --- | --- | --- | --- |
| Dostupnost cesty k datům | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer průběžně kontroluje cestu dat z určité oblasti k front-endu nástroje pro vyrovnávání zatížení, a to až ke stacku SDN, který podporuje váš virtuální počítač. Pokud zůstanou instance v pořádku, měření se řídí stejnou cestou jako provoz s vyrovnáváním zatížení vaší aplikace. Ověřuje se také cesta dat, kterou využívají vaši zákazníci. Měření je pro vaši aplikaci neviditelné a nemá vliv na ostatní operace.| Průměr |
| Stav sondy stavu | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer používá distribuovanou službu pro zjišťování stavu, která monitoruje stav koncového bodu vaší aplikace podle nastavení konfigurace. Tato metrika poskytuje agregované zobrazení nebo filtrované zobrazení jednotlivých koncových bodů instancí ve fondu nástroje pro vyrovnávání zatížení. Můžete zjistit, jak Load Balancer vidí stav vaší aplikace na základě vaší konfigurace sondy stavu. |  Průměr |
| Počet SYN (synchronizovaný) | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer neukončuje připojení přes protokol TCP (Transmission Control Protocol) ani nepracuje s toky paketů protokolu TCP nebo UDP. Toky a jejich metody handshake probíhají vždy mezi zdrojem a instancí virtuálního počítače. Při řešení potíží se scénáři souvisejícími s protokolem TCP můžete využít čítače paketů SYN, pomocí kterých můžete zjistit množství pokusů o přihlášení přes protokol TCP. Tato metrika hlásí počet přijatých paketů TCP SYN.| Sum |
| Počet připojení SNAT | Veřejný nástroj pro vyrovnávání zatížení |Standard Load Balancer hlásí počet odchozích toků maskovaných za front-end veřejné IP adresy. Porty překladu adres na základě zdroje (SNAT) jsou vyčerpatelný prostředek. Tato metrika může poskytnout další informace o tom, do jaké míry se vaše aplikace spoléhá na SNAT u odchozích toků. Hlásí se čítače úspěšných a neúspěšných odchozích toků SNAT, které je možné použít k řešení potíží a porozumění stavu odchozích toků.| Sum |
| Přidělené porty SNAT | Veřejný nástroj pro vyrovnávání zatížení | Standard Load Balancer oznamuje počet přidělených portů SNAT na back-end instanci. | Průměr: |
| Používané porty SNAT | Veřejný nástroj pro vyrovnávání zatížení | Standard Load Balancer oznamuje počet portů SNAT, které se využívají na instanci back-endu. | Průměr | 
| Počet bajtů |  Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer hlásí objem zpracovaných dat na front-end. Můžete si všimnout nerovnoměrné distribuce bajtů napříč instancemi back-endu. Očekává se, že Load Balancer algoritmus Azure je založený na tocích | Sum |
| Počet paketů |  Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer hlásí množství zpracovaných paketů na front-end.| Sum |

  >[!NOTE]
  >Pokud používáte distribuci provozu z interního nástroje pro vyrovnávání zatížení prostřednictvím paketu síťové virtuální zařízení nebo brány firewall syn, počet bajtů a metriky počtu paketů nejsou k dispozici a budou zobrazeny jako nula. 
  
  >[!NOTE]
  >Pro počet jednotek SYN, počet paketů, počet připojení SNAT a počet bajtů se nedají použít maximální a minimální agregace. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Zobrazit metriky nástroje pro vyrovnávání zatížení v Azure Portal

Azure Portal zpřístupňuje metriky nástroje pro vyrovnávání zatížení prostřednictvím stránky metriky, která je k dispozici na stránce prostředků nástroje pro vyrovnávání zatížení pro konkrétní prostředek a Azure Monitor stránku. 

Chcete-li zobrazit metriky pro prostředky Standard Load Balancer:
1. Přejít na stránku metriky a proveďte jednu z následujících akcí:
   * Na stránce prostředek nástroje pro vyrovnávání zatížení vyberte v rozevíracím seznamu Typ metriky.
   * Na stránce Azure Monitor vyberte prostředek nástroje pro vyrovnávání zatížení.
2. Nastavte vhodný typ agregace metriky.
3. Volitelně můžete nakonfigurovat požadované filtrování a seskupování.
4. Volitelně můžete nakonfigurovat časový rozsah a agregaci. Ve výchozím nastavení se čas zobrazuje v UTC.

  >[!NOTE] 
  >Časová agregace je důležitá při interpretaci určitých metrik, protože data jsou vzorkovat jednou za minutu. Pokud je časová agregace nastavená na pět minut a pro metriky, jako je například přidělování SNAT, se používá typ agregace metriky, zobrazí se v grafu pět navrácených celkových portů SNAT. 

![Metriky pro Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Obrázek: metrika dostupnosti datové cesty pro Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Načtěte multidimenzionální metriky prostřednictvím rozhraní API.

Pokyny k rozhraní API pro načítání multidimenzionálních definic a hodnot naleznete v tématu [návod k Azure Monitoring REST API](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Tyto metriky se dají zapsat do účtu úložiště přidáním [nastavení diagnostiky](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) pro kategorii všechny metriky. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurace upozornění na multidimenzionální metriky ###

Azure Standard Load Balancer podporuje snadno konfigurovatelné výstrahy pro multidimenzionální metriky. Nakonfigurujte vlastní prahové hodnoty pro konkrétní metriky, abyste mohli aktivovat výstrahy s různou úrovní závažnosti, abyste mohli využívat možnosti monitorování prostředků bez dotykového ovládání.

Konfigurace upozornění:
1. Přejít na dílčí okno výstrahy pro nástroj pro vyrovnávání zatížení
1. Vytvoření nového pravidla upozornění
    1.  Konfigurace podmínky výstrahy
    1.  Volitelné Přidat skupinu akcí pro automatizovanou opravu
    1.  Přiřazení závažnosti, názvu a popisu výstrahy, která umožňuje intuitivní reakci

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Běžné diagnostické scénáře a doporučená zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Je cesta k datům a dostupná pro moje Load Balancer front-end?
<details><summary>Rozbalit</summary>

Metrika dostupnosti datových cest popisuje stav cesty dat v rámci oblasti pro výpočetní hostitele, ve kterém se nachází vaše virtuální počítače. Metrika je odrazem stavu infrastruktury Azure. Metriku můžete použít k těmto akcím:
- Monitorování externí dostupnosti vaší služby
- Dig hlubší a zjistěte, jestli je platforma, na které je vaše služba nasazená, v dobrém stavu, nebo jestli je instance hostovaného operačního systému nebo aplikace v pořádku.
- Izolujte, jestli událost souvisí se službou nebo základní rovinou dat. Nezaměňujte tuto metriku se stavem sondy stavu ("dostupnost instance back-endu").

Jak získat dostupnost cesty k datům pro prostředky Standard Load Balancer:
1. Ujistěte se, že je vybrán správný prostředek nástroje pro vyrovnávání zatížení. 
2. V rozevíracím seznamu **metrika** vyberte **dostupnost cesty k datům**. 
3. V rozevíracím seznamu **agregace** vyberte **střední**. 
4. Kromě toho přidejte filtr na front-end IP adresu nebo port front-endu jako dimenzi s požadovanou front-end IP adresou nebo front-end port a pak je seskupte podle vybrané dimenze.

![Zjišťování VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek: Load Balancer podrobnosti o probingu front-endu*

Metrika je generována aktivním měřením v pásmu. Služba zjišťování v rámci této oblasti představuje provoz pro měření. Služba se aktivuje hned po vytvoření nasazení s veřejným front-end a bude pokračovat, dokud neodeberete front-end. 

Paket, který odpovídá front-end a pravidlu nasazení, se generuje pravidelně. Projde oblast ze zdroje na hostitele, kde se nachází virtuální počítač ve fondu back-end. Infrastruktura nástroje pro vyrovnávání zatížení provádí stejné operace vyrovnávání zatížení a překladu, protože funguje pro všechny ostatní přenosy. Tento test je v rámci vašeho koncového bodu s vyrovnáváním zatížení v pásmu. Po doručení testu do výpočetního hostitele, kde je umístěn zdravý virtuální počítač ve fondu back-end, vygeneruje výpočetní hostitel odpověď na službu zjišťování. Váš virtuální počítač tento provoz nevidí.

Dostupnost DataPath se nezdařila z následujících důvodů:
- Vaše nasazení nemá ve fondu back-end žádné dobré virtuální počítače. 
- Došlo k výpadku infrastruktury.

Pro účely diagnostiky můžete použít [metriku dostupnosti datových cest společně se stavem sondy stavu](#vipavailabilityandhealthprobes).

Pro většinu scénářů použijte **průměr** jako agregaci.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Reagují back-end instance pro můj Load Balancer k testům?
<details>
  <summary>Rozbalit</summary>
Metrika stavu sondy stavu popisuje stav nasazení aplikace, jak je nakonfigurován při konfiguraci sondy stavu nástroje pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení využívá stav sondy stavu k určení, kam se mají posílat nové toky. Sondy stavu pocházejí z adresy infrastruktury Azure a jsou viditelné v hostovaném operačním systému virtuálního počítače.

Postup získání stavu testu stavu pro prostředky Standard Load Balancer:
1. Vyberte metriku **stavu sondy** stavu s **průměrem** typu agregace. 
2. Použijte filtr na požadované IP adrese nebo portu front-endu (nebo obojí).

Sondy stavu selžou z následujících důvodů:
- Sondu stavu můžete nakonfigurovat na port, který nenaslouchá nebo nereaguje nebo používá nesprávný protokol. Pokud vaše služba používá pravidla přímého vrácení serveru (DSR nebo plovoucí IP adresy), ujistěte se, že služba naslouchá na IP adrese konfigurace protokolu IP síťové karty, a ne jenom na zpětné smyčce, která je nakonfigurovaná s front-end IP adresou.
- Vaše sonda není povolená skupinou zabezpečení sítě, bránou firewall hostovaného operačního systému virtuálního počítače nebo filtry aplikační vrstvy.

Pro většinu scénářů použijte **průměr** jako agregaci.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Návody se podívat na Statistika odchozího připojení? 
<details>
  <summary>Rozbalit</summary>
Metrika připojení SNAT popisuje objem úspěšných a neúspěšných připojení pro [odchozí toky](./load-balancer-outbound-connections.md).

Svazek neúspěšných připojení, který je větší než nula, indikuje vyčerpání portů SNAT. Abyste zjistili, co můžou tyto chyby způsobovat, musíte prozkoumat další. Manifesty vyčerpání portů SNAT jako neúspěšné navázání [odchozího toku](./load-balancer-outbound-connections.md). Přečtěte si článek o odchozích připojeních, abyste porozuměli scénářům a mechanismům v práci, a zjistili si, jak zmírnit a navrhovat, abyste se vyhnuli vyčerpání portů SNAT. 

Získání statistiky připojení SNAT:
1. Vyberte **připojení SNAT** typ metriky a **součet** jako agregaci. 
2. Seskupit podle **stavu připojení** pro úspěšné a neúspěšné počty připojení SNAT, které mají být reprezentovány různými řádky. 

![Připojení SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek: počet připojení Load Balancer SNAT*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Návody kontrolovat využití a přidělování portů SNAT?
<details>
  <summary>Rozbalit</summary>
Metrika používaných portů SNAT sleduje, kolik portů SNAT se spotřebovává pro udržování odchozích toků. To indikuje, kolik jedinečných toků mezi internetovým zdrojem a back-end virtuálním počítačem nebo sadou škálování virtuálního počítače, která je za nástrojem pro vyrovnávání zatížení, a nemá veřejnou IP adresu. Porovnáním počtu portů SNAT, které používáte s přidělenou metrikou portů SNAT, můžete zjistit, jestli vaše služba má nebo nehrozí v nebezpečí vyčerpání SNAT, a výsledný výstupní tok se nezdařil. 

Pokud vaše metrika signalizuje riziko selhání [odchozího toku](./load-balancer-outbound-connections.md) , proveďte odkaz na článek a proveďte kroky, které tuto skutečnost zmírnit, abyste zajistili stav služby.

Zobrazení využití a přidělení portu SNAT:
1. Nastavte časovou agregaci grafu na 1 minutu, aby se zobrazila požadovaná data.
1. Vyberte **použité porty SNAT** nebo **přidělené porty SNAT** jako typ metriky a **průměr** jako agregaci.
    * Ve výchozím nastavení jsou tyto metriky průměrný počet portů SNAT, které jsou přiděleny nebo využity jednotlivými virtuálními počítači back-endu nebo VMSS, a odpovídající všechny veřejné IP adresy front-endu namapované na Load Balancer, agregované přes protokoly TCP a UDP.
    * Zobrazení celkových portů SNAT používaných nástrojem nebo přiděleným pro nástroj pro vyrovnávání zatížení použití **součtu** agregace metriky
1. Filtr na konkrétní **typ protokolu**, sadu **back-end** serverů nebo **IP adresy front-endu**.
1. Pokud chcete monitorovat stav na back-end nebo front-endové instance, použijte rozdělení. 
    * Rozdělení poznámky umožňuje zobrazit pouze jednu metriku. 
1. Například pro monitorování využití SNAT pro toky TCP na počítač, agregované podle **průměru**, rozdělení podle **IP adresy back-endu** a filtrování podle **typu protokolu**. 

![Přidělení a využití SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Obrázek: Průměrná doba přidělení a využití portu TCP SNAT pro sadu back-end virtuálních počítačů*

![Využití SNAT podle instance back-endu](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Obrázek: využití portu TCP SNAT na back-end instanci*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Návody kontrolovat pokusy o příchozí a odchozí připojení pro moji službu?
<details>
  <summary>Rozbalit</summary>
Metrika paketů SYN popisuje objem paketů TCP SYN, které byly doručeny nebo odeslány (pro [odchozí toky](./load-balancer-outbound-connections.md)), které jsou přidruženy k určitému front-endu. Tuto metriku můžete použít k porozumění pokusům o připojení TCP ke službě.

Pro většinu scénářů použijte **součet** jako agregaci.

![Připojení SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek: Load Balancer počet SYN*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Návody kontrolovat využití šířky pásma sítě? 
<details>
  <summary>Rozbalit</summary>
Metriky čítače bajtů a paketů popisují objem bajtů a paketů, které vaše služba odesílá nebo přijímá, na front-endové bázi.

Pro většinu scénářů použijte **součet** jako agregaci.

Postup získání statistiky počtu bajtů nebo paketů:
1. Vyberte typ metriky **počet bajtů** nebo **počet paketů** a **součet** jako agregaci. 
2. Proveďte jednu z následujících akcí:
   * Použijte filtr na konkrétní IP adresu front-endu, front-end port, back-end IP adresu nebo back-end port.
   * Získejte celkové statistiky prostředku nástroje pro vyrovnávání zatížení bez jakéhokoli filtrování.

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek: Load Balancer počet bajtů*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Návody diagnostikovat nasazení nástroje pro vyrovnávání zatížení?
<details>
  <summary>Rozbalit</summary>
Když použijete kombinaci metrik stavu dostupnosti datové cesty a sondy stavu v jednom grafu, můžete zjistit, kde najít problém a vyřešit problém. Můžete získat jistotu, že Azure pracuje správně, a pomocí tohoto vědomí jednoznačně určit, jestli je konfigurace nebo aplikace hlavní příčinou.

Metriky stavu sondy můžete použít k pochopení, jak Azure zobrazuje stav vašeho nasazení podle konfigurace, kterou jste zadali. Prohlížení sond stavu je vždy skvělým prvním krokem při monitorování nebo určení příčiny.

Můžete si tak učinit krok podrobněji a použít metriku dostupnosti datových cest k získání přehledu o tom, jak Azure zobrazuje stav základní roviny dat, která je zodpovědná za vaše konkrétní nasazení. Když kombinujete obě metriky, můžete izolovat, kde může být chyba, jak je znázorněno v následujícím příkladu:

![Kombinování metrik dostupnosti datových cest a stavu sondy stavu](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Obrázek: kombinování datových cest a metrik stavu sondy stavu*

Graf zobrazuje následující informace:
- Infrastruktura hostující vaše virtuální počítače nebyla k dispozici a na začátku grafu je 0 procent. Později je infrastruktura v pořádku a virtuální počítače byly dosažitelné a více než jeden virtuální počítač byl umístěný do back-endu. Tyto informace jsou označeny modrým trasováním pro dostupnost cesty k datům, které byly později v 100 procentech. 
- Stav sondy stavu, který je označen fialovým trasováním, je na začátku grafu na 0 procent. KRUHOVÁ oblast ve zelených světlech, kde se stav sondy stavu stal v pořádku a v jakém okamžiku bylo nasazení zákazníka schopné přijmout nové toky.

Graf umožňuje zákazníkům řešit vlastní řešení bez nutnosti odhadování nebo požádáte o podporu, jestli dochází k ostatním problémům. Služba není k dispozici, protože sondy stavu selhaly kvůli chybné konfiguraci nebo selhání aplikace.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stav prostředku

Stav prostředků Standard Load Balancer se zveřejňuje prostřednictvím stávajícího **stavu prostředků** v části **monitorování > Service Health**. Vyhodnocuje se každé **dvě minuty** měřením dostupnosti dat, která určuje, jestli jsou k dispozici koncové body vyrovnávání zatížení front-endu.

| Stav prostředku | Popis |
| --- | --- |
| K dispozici | Váš prostředek standardního nástroje pro vyrovnávání zatížení je v pořádku a dostupný. |
| Snížený výkon | Váš standardní nástroj pro vyrovnávání zatížení má platformy nebo uživatelem iniciované události, které mají vliv na výkon. Metrika dostupnosti cesty k datům hlásila stav mezi 25 % a 90 % po dobu alespoň dvou minut. Dosáhnete středně silného dopadu na výkon. [Postupujte podle pokynů pro řešení potíží s RHC](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) a zjistěte, jestli neexistují uživatelem iniciované události, které by měly vliv na dostupnost.
| Neaktivní | Váš prostředek standardního nástroje pro vyrovnávání zatížení není v pořádku. Metrika dostupnosti DataPath ohlásila méně než 25% stavu minimálně pro dvě minuty. Pro příchozí připojení budete mít výrazný dopad na výkon nebo nedostatečná dostupnost. Mohou existovat události uživatele nebo platformy, které způsobují nedostupnost. [Postupujte podle pokynů pro řešení potíží s RHC](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) , abyste zjistili, jestli neexistují uživatelem iniciované události, které mají vliv na dostupnost. |
| Neznámý | Stav prostředku pro prostředek standardního nástroje pro vyrovnávání zatížení se ještě neaktualizoval nebo nepřijal informace o dostupnosti cesty k datům za posledních 10 minut. Tento stav by měl být přechodný a jakmile se přijmou data, měl by odrážet správný stav. |

Zobrazení stavu prostředků veřejné Standard Load Balancer:
1. Vyberte **monitor**  >  **Service Health**.

   ![Monitorovat stránku](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek: odkaz Service Health na Azure Monitor*

2. Vyberte **Resource Health** a pak se ujistěte, že je vybraná možnost **ID předplatného** a **typ prostředku = Load Balancer** .

   ![Stav prostředku](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek: výběr prostředku pro zobrazení stavu*

3. V seznamu vyberte prostředek Load Balancer pro zobrazení jeho historických stavů.

    ![Stav Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek: Load Balancer zobrazení stavu prostředků*
 
Popis obecného stavu prostředku je k dispozici v [dokumentaci k RHC](../service-health/resource-health-overview.md). Konkrétní stavy pro Azure Load Balancer jsou uvedené v následující tabulce: 


## <a name="next-steps"></a>Další kroky

- Přečtěte si o používání [přehledů](https://docs.microsoft.com/azure/load-balancer/load-balancer-insights) k zobrazení těchto metrik, které jsou pro vaše Load Balancer předem nakonfigurované.
- Přečtěte si další informace o [Standard Load Balancer](./load-balancer-overview.md).
- Přečtěte si další informace o [odchozím připojení k nástroji pro vyrovnávání zatížení](./load-balancer-outbound-connections.md).
- Přečtěte si o [Azure monitor](../azure-monitor/overview.md).
- Přečtěte si o [Azure Monitor REST API](/rest/api/monitor/) a [o tom, jak pomocí REST API načíst metriky](/rest/api/monitor/metrics/list).
