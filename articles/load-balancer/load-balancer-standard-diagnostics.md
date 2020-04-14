---
title: Diagnostika s metrikami, výstrahami a stavem prostředků – Azure Standard Load Balancer
description: Pomocí dostupných metrik, výstrah a informací o stavu prostředků můžete diagnostikovat standardní vyvažovač evidenční ho zatížení Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 9003d35ce2eea18aa912a866802b026bb923aa08
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272691"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostika služby Standard Load Balancer s metrikami, upozorněními a stavem prostředků

Azure Standard Load Balancer poskytuje následující diagnostické funkce:

* **Vícerozměrné metriky a výstrahy**: Poskytuje vícerozměrné diagnostické funkce prostřednictvím [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview) pro standardní konfigurace nástroje pro vyrovnávání zatížení. Můžete sledovat, spravovat a řešit standardní prostředky pro vyrovnávání zatížení.

* **Stav prostředků:** Stránka Vyrovnávání zatížení na portálu Azure a stránka Stav prostředků (v části Monitor) zveřejňují část Stav prostředků pro standardní vyvyčažadlo zatížení. 

Tento článek poskytuje rychlou prohlídku těchto funkcí a nabízí způsoby, jak je použít pro standardní vyrovnávání zatížení. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Vícerozměrné metriky

Azure Balancer poskytuje vícerozměrné metriky prostřednictvím metrik Azure na webu Azure Portal a pomáhá vám získat diagnostické přehledy v reálném čase o vašich prostředcích nástroje pro vyrovnávání zatížení. 

Různé konfigurace standardního nástroje pro vyrovnávání zatížení poskytují následující metriky:

| Metrika | Typ prostředku | Popis | Doporučená agregace |
| --- | --- | --- | --- |
| Dostupnost cesty k datům | Veřejný a interní systém vyrovnávání zatížení | Standardní vyrovnávání zatížení nepřetržitě cvičí cestu dat z oblasti do front-endu vyrovnávání zatížení, a to až do zásobníku SDN, který podporuje váš virtuální počítač. Tak dlouho, dokud v pořádku instance zůstávají, měření následuje stejnou cestu jako provoz s vyrovnáváním zatížení vaší aplikace. Cesta k datům, kterou používají vaši zákazníci, je také ověřena. Měření je pro vaši aplikaci neviditelné a nenarušuje jiné operace.| Průměr |
| Stav zdravotní sondy | Veřejný a interní systém vyrovnávání zatížení | Standardní nástroj pro vyrovnávání zatížení používá distribuovanou službu zjišťování stavu, která monitoruje stav koncového bodu aplikace podle nastavení konfigurace. Tato metrika poskytuje agregované nebo koncové hodované zobrazení každého koncového bodu instance ve fondu vyrovnávání zatížení. Můžete vidět, jak nástroj pro vyrovnávání zatížení zobrazení stavu vaší aplikace, jak je uvedeno v konfiguraci sondy stavu. |  Průměr |
| SYN (synchronizovat) pakety | Veřejný a interní systém vyrovnávání zatížení | Standardní likvidátor zatížení neukončuje připojení tcp (Transmission Control Protocol) ani nekomunikuje s toky paketů TCP nebo UDP. Toky a jejich handshakes jsou vždy mezi zdrojem a instancí virtuálního zařízení. Chcete-li lépe řešit scénáře protokolu TCP, můžete použít čítače paketů SYN, abyste pochopili, kolik pokusů o připojení TCP je provedeno. Metrika hlásí počet přijatých paketů TCP SYN.| Průměr |
| Připojení SNAT | Veřejný odvykač zatížení |Standardní vyrovnávání zatížení hlásí počet odchozích toků, které jsou maskovány do front-endu veřejné IP adresy. Porty pro překlad zdrojových síťových adres (SNAT) jsou vyčerpatelným zdrojem. Tato metrika může poskytnout informace o tom, jak silně vaše aplikace spoléhá na SNAT pro odchozí původní toky. Čítače pro úspěšné a neúspěšné odchozí toky SNAT jsou hlášeny a lze je použít k řešení potíží a pochopení stavu odchozích toků.| Průměr |
| Přidělené porty SNAT | Veřejný odvykač zatížení | Standardní vyrovnávání zatížení hlásí počet portů SNAT přidělených na instanci back-endu | Průměrná. |
| Použité porty SNAT | Veřejný odvykač zatížení | Standardní nástroje pro vyrovnávání zatížení hlásí počet portů SNAT, které jsou využívány pro instanci back-endu. | Průměr | 
| Čítače bajtů |  Veřejný a interní systém vyrovnávání zatížení | Standardní vyrovnávání zatížení hlásí data zpracovaná na front-end. Můžete si všimnout, že bajty nejsou rovnoměrně rozděleny mezi back-endové instance. To se očekává, protože algoritmus Azure Balancer je založen na tocích | Průměr |
| Čítače paketů |  Veřejný a interní systém vyrovnávání zatížení | Standardní vyrovnávání zatížení hlásí pakety zpracované na front-end.| Průměr |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Zobrazení metrik vyrovnávání zatížení na webu Azure Portal

Portál Azure zpřístupňuje metriky vyrovnávání zatížení prostřednictvím stránky Metriky, která je k dispozici na stránce prostředků vyrovnávání zatížení pro konkrétní prostředek a na stránce Azure Monitor. 

Chcete-li zobrazit metriky pro zdroje standardního vyrovnávání zatížení:
1. Přejděte na stránku Metriky a proveďte jednu z následujících akcí:
   * Na stránce prostředku vyrovnávání zatížení vyberte typ metriky v rozevíracím seznamu.
   * Na stránce Azure Monitor vyberte prostředek vyrovnávání zatížení.
2. Nastavte příslušný typ agregace metriky.
3. Volitelně můžete nakonfigurovat požadované filtrování a seskupování.
4. Volitelně můžete nakonfigurovat časový rozsah a agregaci. Ve výchozím nastavení je čas zobrazen v UTC.

  >[!NOTE] 
  >Agregace času je důležité při interpretaci určité metriky jako data vzorkování jednou za minutu. Pokud je časová agregace nastavena na pět minut a pro metriky, jako je například přidělení SNAT, se použije typ agregace metriky, graf se zobrazí pětkrát více než celkový počet přidělených portů SNAT. 

![Metriky pro standardní vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Obrázek: Metrika dostupnosti cesty k datům pro standardní vyrovnávání zatížení*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Programové načítání vícerozměrných metrik pomocí api

Pokyny k rozhraní API pro načítání vícerozměrných definic a hodnot metrik najdete [v tématu Azure Monitoring REST API návod .](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api) Tyto metriky lze zapsat do účtu úložiště pouze prostřednictvím možnosti Všechny metriky. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurace výstrah pro vícerozměrné metriky ###

Azure Standard Load Balancer podporuje snadno konfigurovatelné výstrahy pro vícerozměrné metriky. Nakonfigurujte vlastní prahové hodnoty pro konkrétní metriky tak, aby se aktivovaly výstrahy s různou úrovní závažnosti, aby se posílilo bezdotykové monitorování prostředků.

Konfigurace upozornění:
1. Přejít na dílčí okno výstrahy pro vykladač zatížení
1. Vytvoření nového pravidla upozornění
    1.  Konfigurace stavu výstrahy
    1.  (Nepovinné) Přidání skupiny akcí pro automatickou opravu
    1.  Přiřazení závažnosti výstrahy, názvu a popisu, který umožňuje intuitivní reakci

  >[!NOTE]
  >Okno konfigurace stavu výstrahy zobrazí časové řady pro historii signálu. K dispozici je možnost filtrovat tuto časovou řadu podle dimenzí, jako je back-endová IP adresa. Tím se vyfiltruje graf časových řad, ale **nikoli** samotné upozornění. Výstrahy pro konkrétní adresy IP back-endu nelze konfigurovat.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Běžné diagnostické scénáře a doporučená zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Je cesta k datům nahoru a je k dispozici pro front-end vykladače vyrovnávání zatížení?
<details><summary>Rozbalit</summary>

Metrika dostupnosti cesty k datům popisuje stav datové cesty v rámci oblasti k výpočetnímu hostiteli, kde jsou umístěny vaše virtuální počítače. Metrika je odrazem stavu infrastruktury Azure. Pomocí metriky můžete:
- Sledování externí dostupnosti služby
- Hlouběji a zjistěte, zda je platforma, na které je vaše služba nasazená, v pořádku nebo zda je váš hostovaný operační počítač nebo instance aplikace v pořádku.
- Izolujte, zda událost souvisí s vaší službou nebo základní rovinou dat. Nepleťte si tuto metriku se stavem sondy stavu ("dostupnost back-endové instance").

Dostupnost cesty k datům pro standardní prostředky vyrovnávání zatížení:
1. Zkontrolujte, zda je vybrán správný prostředek pro vyrovnávání zatížení. 
2. V rozevíracím seznamu **Metrika** vyberte **dostupnost cesty k datům**. 
3. V rozevíracím seznamu **Agregace** vyberte **možnost Vg**. 
4. Dále přidejte filtr na front-endovou IP adresu nebo port front-endu jako dimenzi s požadovanou front-endovou IP adresou nebo front-endovým portem a seskupte je podle vybrané dimenze.

![VIP sondování](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek: Podrobnosti sondování frontendového sondování vykladače zatížení*

Metrika je generována aktivním měřením v pásmu. Sondovací služba v rámci oblasti pochází provoz pro měření. Služba se aktivuje, jakmile vytvoříte nasazení s veřejným front-endem, a bude pokračovat, dokud neodeberete front-end. 

Pravidelně se generuje paket odpovídající front-endu a pravidlu nasazení. Prochází oblast ze zdroje do hostitele, kde se nachází virtuální ho virtuálního ms v back-endfondu. Infrastruktura nástroje pro vyrovnávání zatížení provádí stejné operace vyrovnávání zatížení a překladu jako u všech ostatních přenosů. Tato sonda je v pásmu na koncový bod s vyrovnáváním zatížení. Po sonda dorazí na výpočetní hostitele, kde je umístěn virtuální počítač v pořádku ve fondu back-endu, výpočetní hostitel generuje odpověď na službu zjišťování. Váš virtuální počítač tento provoz neuvidí.

Dostupnost datové cesty se nezdaří z následujících důvodů:
- Vaše nasazení nemá žádné virtuální počítače v pořádku zbývající ve fondu back-endu. 
- Došlo k výpadku infrastruktury.

Pro diagnostické účely můžete použít [metriku dostupnost cesty k datům spolu se stavem sondy stavu](#vipavailabilityandhealthprobes).

Pro většinu scénářů použijte jako agregaci **průměr.**
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Odpovídají back-endové instance pro můj balancer na sondy?
<details>
  <summary>Rozbalit</summary>
Metrika stavu sondy stavu popisuje stav nasazení aplikace, jak jste nakonfigurovali při konfiguraci sondy stavu nástrojpro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení používá stav sondy stavu k určení, kam chcete odeslat nové toky. Sondy stavu pocházejí z adresy infrastruktury Azure a jsou viditelné v rámci hostovaného operačního systému virtuálního počítače.

Získání stavu sondy pro vaše prostředky standardního nástrojového bilance zatížení:
1. Vyberte metriku **Stav sondy stavu** s typem agregace **Avg.** 
2. Použití filtru na požadovanou ip adresu nebo port front-endu (nebo obojí).

Sondy stavu se nezdaří z následujících důvodů:
- Nakonfigurujete sondu stavu na port, který nenaslouchá nebo neodpovídá nebo používá nesprávný protokol. Pokud vaše služba používá pravidla přímého vrácení serveru (DSR nebo plovoucí IP), ujistěte se, že služba naslouchá na IP adrese konfigurace IP rozhraní NIC a nikoli pouze na zpětné smyčce, která je nakonfigurována s adresou IP front-endu.
- Vaše sonda není povolena skupinou zabezpečení sítě, bránou firewall hostovaného operačního systému virtuálního počítače nebo filtry aplikační vrstvy.

Pro většinu scénářů použijte jako agregaci **průměr.**
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak zjistím statistiku odchozího připojení? 
<details>
  <summary>Rozbalit</summary>
Metrika připojení SNAT popisuje objem úspěšných a neúspěšných připojení pro [odchozí toky](https://aka.ms/lboutbound).

Neúspěšný objem připojení větší než nula označuje vyčerpání portu SNAT. Je nutné prozkoumat další zjistit, co může být příčinou těchto selhání. Vyčerpání portu SNAT se projevuje jako selhání při vytváření [odchozího toku](https://aka.ms/lboutbound). Projděte si článek o odchozích připojeních, abyste porozuměli scénářům a mechanismům v práci a zjistili, jak zmírnit a navrhnout, abyste se vyhnuli vyčerpání portu SNAT. 

Chcete-li získat statistiky připojení SNAT:
1. Vyberte typ **metriky Připojení SNAT** a **Součet** jako agregace. 
2. **Seskupit** podle stavu připojení pro úspěšné a neúspěšné počty připojení SNAT, které jsou reprezentovány různými řádky. 

![Připojení SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek: Počet připojení SNAT pro vyrovnávání zatížení*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Jak zkontroluji využití a přidělení portu SNAT?
<details>
  <summary>Rozbalit</summary>
Metrika využití SNAT označuje, kolik jedinečných toků je vytvořeno mezi internetovým zdrojem a back-endovým virtuálním počítačem nebo škálovací sadou virtuálního počítače, která je za nástrojem pro vyrovnávání zatížení a nemá veřejnou IP adresu. Porovnáním s metrikou přidělení SNAT můžete určit, zda vaše služba zažívá nebo je ohrožena vyčerpáním SNAT a výsledkem selhání odchozího toku. 

Pokud metriky označují riziko selhání [odchozího toku,](https://aka.ms/lboutbound) odkazujte na článek a podnikněte kroky ke zmírnění tohoto stavu, abyste zajistili stav služby.

Zobrazení využití a přidělení portu SNAT:
1. Nastavte časovou agregaci grafu na 1 minutu, aby bylo zajištěno, že se zobrazí požadovaná data.
1. Vyberte **snat využití** a/nebo **přidělení SNAT** jako typ metriky a **průměr** jako agregace
    * Ve výchozím nastavení se jedná o průměrný počet portů SNAT přidělených nebo používaných jednotlivými virtuálními servery nebo virtuálními mandy, což odpovídá všem veřejným IP adresy front-endu namapovaným na vyrovnávání zatížení, agregované přes TCP a UDP.
    * Zobrazení celkového počtu portů SNAT používaných nebo přidělených pro vyrovnávání zatížení použijte **součet** agregace metrik
1. Filtrujte na konkrétní **typ protokolu**, sadu ip **adresy back-endu**a/nebo **ip adresy front-endu**.
1. Chcete-li sledovat stav na back-end nebo front-end instance, použijte rozdělení. 
    * Rozdělení poznámek umožňuje zobrazit pouze jednu metriku najednou. 
1. Chcete-li například sledovat využití protokolu SNAT pro toky Protokolu TCP na počítač, agregovat podle **průměru**, rozdělit podle **ip adresy back-endu** a filtrovat podle typu **protokolu**. 

![Přidělení a využití SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Obrázek: Průměrné přidělení a využití portu TCP SNAT pro sadu back-endových virtuálních počítače*

![Využití SNAT podle instance back-endu](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Obrázek: Využití portu TCP SNAT na instanci back-endu*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak lze zkontrolovat pokusy o příchozí nebo odchozí připojení pro svou službu?
<details>
  <summary>Rozbalit</summary>
Metrika paketů SYN popisuje objem paketů TCP SYN, které byly doručeny nebo odeslány (pro [odchozí toky),](https://aka.ms/lboutbound)které jsou přidruženy k určitému front-endu. Tuto metriku můžete použít k pochopení pokusů o připojení TCP k vaší službě.

Pro většinu scénářů použijte jako agregaci **součet.**

![Připojení SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek: Počet SYN vykladače zatížení*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak lze zkontrolovat spotřebu šířky pásma sítě? 
<details>
  <summary>Rozbalit</summary>
Metrika čítače bajtů a paketů popisuje objem bajtů a paketů, které jsou odesílány nebo přijímány vaší službou na základě front-endu.

Pro většinu scénářů použijte jako agregaci **součet.**

Chcete-li získat statistiku počtu bajtů nebo paketů:
1. Vyberte typ **metriky Počet bajtů** nebo **Počet paketů** s agregací **Avg.** 
2. Proveďte jednu z následujících akcí:
   * Použijte filtr na konkrétní front-end IP, front-end port, back-end IP nebo back-end port.
   * Získejte celkové statistiky pro prostředek vyrovnávání zatížení bez filtrování.

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek: Počet bajtů vyvažovači zatížení*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Jak diagnostikuji nasazení vyvyčovávače zatížení?
<details>
  <summary>Rozbalit</summary>
Pomocí kombinace metrikdostupnost cesty k datům a stavu sondy stavu v jednom grafu můžete zjistit, kde chcete problém vyhledat a problém vyřešit. Můžete získat jistotu, že Azure funguje správně a použít tyto znalosti přesvědčivě určit, že konfigurace nebo aplikace je hlavní příčinou.

Pomocí metrik sondy stavu můžete pochopit, jak Azure zobrazení stavu vašeho nasazení podle konfigurace, kterou jste poskytli. Při pohledu na zdravotní sondy je vždy skvělý první krok při sledování nebo určení příčiny.

Můžete to udělat o krok dál a použít metriku dostupnosti cesty k datům, abyste získali přehled o tom, jak Azure zhlédne stav základní datové roviny, která je zodpovědná za vaše konkrétní nasazení. Když zkombinujete obě metriky, můžete izolovat, kde může být chyba, jak je znázorněno v tomto příkladu:

![Kombinace metrik dostupnosti datové cesty a stavu sondy stavu](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Obrázek: Kombinování metrik dostupnosti datové cesty a stavu sondy stavu*

Graf zobrazuje následující informace:
- Infrastruktura hostující vaše virtuální počítače nebyla k dispozici a na začátku grafu byla 0 procent. Později byla infrastruktura v pořádku a virtuální servery byly dosažitelné a víc než jeden virtuální virtuální byl umístěn v back-endu. Tyto informace jsou označeny modrým trasování pro dostupnost datové cesty, která byla později na 100 procent. 
- Stav sondy stavu, označený fialovým trasováním, je na začátku grafu na 0 procent. Zakroužkovaná oblast zeleně upozorňuje, kde se stav sondy stavu stal zdravým a v tomto okamžiku bylo nasazení zákazníka schopno přijmout nové toky.

Graf umožňuje zákazníkům řešit řešení potíží s nasazením samostatně, aniž by museli hádat nebo se ptát na podporu, zda dochází k dalším problémům. Služba nebyla k dispozici, protože sondy stavu se nezdařily z důvodu chybné konfigurace nebo neúspěšné aplikace.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stav zdroje

Stav pro standardní prostředky nástroj pro vyrovnávání zatížení je vystaven prostřednictvím **existujícího stavu prostředků** v části **Sledování stavu služby >**.

Chcete-li zobrazit stav veřejných prostředků standardního vyrovnávání zatížení:
1. Vyberte **možnost Sledovat** > **stav služby**.

   ![Stránka monitoru](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek: Odkaz Stav služby na Azure Monitoru*

2. Vyberte **Stav prostředků**a ujistěte se, že jsou **vybrány ID předplatného** a typ prostředku = **Vyrovnávání zatížení.**

   ![Stav zdroje](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek: Výběr zdroje pro zobrazení stavu*

3. V seznamu vyberte prostředek nástroj pro vyrovnávání zatížení, chcete-li zobrazit jeho historický stav.

    ![Stav nástrojpro vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek: Zobrazení stavu prostředků vykladače zatížení*
 
Různé stavy stavu prostředků a jejich popisy jsou uvedeny v následující tabulce: 

| Stav zdroje | Popis |
| --- | --- |
| K dispozici. | Standardní prostředek pro vyrovnávání zatížení je v pořádku a k dispozici. |
| Neaktivní | Standardní prostředek pro vyrovnávání zatížení není v pořádku. Diagnostikujte stav výběrem metrik **azure monitoru** > **Metrics**.<br>(*Nedostupný* stav může také znamenat, že prostředek není připojen ke standardnímu nástroji pro vyrovnávání zatížení.) |
| Není známo | Stav prostředku pro standardní prostředek nástroj pro vyrovnávání zatížení ještě nebyl aktualizován.<br>(*Neznámý* stav může také znamenat, že prostředek není připojen ke standardnímu nástrojpro vyrovnávání zatížení.)  |

## <a name="next-steps"></a>Další kroky

- Další informace o [standardním vyvykladaču zatížení](load-balancer-standard-overview.md).
- Další informace o [odchozím připojení zařízení pro vyrovnávání zatížení](https://aka.ms/lboutbound).
- Další informace o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Přečtěte si o [rozhraní REST rozhraní Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) api a jak [načíst metriky prostřednictvím rozhraní REST API](/rest/api/monitor/metrics/list).
