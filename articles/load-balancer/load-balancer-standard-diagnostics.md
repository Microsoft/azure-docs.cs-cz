---
title: Diagnostika Azure Load balancer úrovně Standard | Dokumentace Microsoftu
description: Použijte k dispozici metriky a informace o stavu diagnostiky pro Azure Load balancer úrovně Standard.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: Kumud
ms.openlocfilehash: 6c730455e2d8b590a4e4b9ca0392985918678bf4
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167694"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metriky a stav diagnostiky pro Load balancer úrovně Standard

Azure Load balancer úrovně Standard poskytuje Azure Load balancer úrovně Standard poskytuje následující možnosti diagnostiky prostředků:
* **Vícedimenzionálních metrik**: poskytuje nové multidimenzionální diagnostické možnosti prostřednictvím [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pro veřejné a vnitřní načtení konfigurace nástroje pro vyrovnávání. Můžete monitorovat, spravovat a řešení potíží s prostředky nástroje pro vyrovnávání zatížení.

* **Služba Resource health**: služby Load balancer úrovně stránky na webu Azure Portal a Resource Health stránky (v části monitorování) vystavit Resource Health část pro konfiguraci nástroje pro vyrovnávání zatížení veřejného Load balanceru úrovně Standard.

Tento článek poskytuje stručný přehled těchto možností a nabízí způsoby, jak je používat pro Load balancer úrovně Standard.

## <a name = "MultiDimensionalMetrics"></a>Vícedimenzionálních metrik

Nástroj Azure Load Balancer poskytuje nové vícedimenzionálních metrik přes nové metriky Azure (preview) na webu Azure Portal a pomůže vám získat diagnostické informace v reálném čase do zatížení prostředků nástroje pro vyrovnávání. 

Různé konfigurace Load balanceru úrovně Standard poskytují následující metriky:

| Metrika | Typ prostředku | Popis | Doporučené agregace |
| --- | --- | --- | --- |
| Dostupnost virtuálních IP adres (cesta dostupnost dat) | Veřejný load balancer | Load balanceru úrovně Standard průběžně uplatní na cestu k datům z v rámci oblasti zatížení vyrovnávání front-endu, až zásobník SDN, který podporuje váš virtuální počítač. Za předpokladu, zůstanou v dobrém stavu instance, měření se řídí stejnou cestu jako provoz s vyrovnáváním zatížení vaší aplikace. Na cestu k datům, která vaši zákazníci používají je také ověřován. Měření není viditelný pro vaši aplikaci a nebude v konfliktu s dalšími operacemi.| Průměr |
| Dostupnost vyhrazené IP adresy (Sonda stavu) |  Nástroj pro vyrovnávání zatížení veřejné a vnitřní | Nástroj pro vyrovnávání zatížení používá Distribuovaná služba zjišťování stavu, který sleduje stav vašeho koncového bodu aplikace podle nastavení konfigurace. Tato metrika poskytuje agregace nebo za koncový bod filtrované zobrazení každého koncového bodu instance ve fondu nástroje pro vyrovnávání zatížení. Uvidíte jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace, jak je uvedeno ve vaší konfiguraci sondy stavu. |  Průměr |
| SYN (synchronizace) pakety |  Veřejný load balancer | Load balancer úrovně Standard není ukončit připojení protokolu TCP (Transmission Control) nebo pracovat s TCP nebo UDP paketů toky. Toky a jejich počet metod handshake jsou vždy mezi zdrojem a instanci virtuálního počítače. Lepší řešení potíží s scénáře protokol TCP, které můžete využít SYN čítače paketů o tom, kolik připojení TCP pokusy. Metrika hlásí počet TCP SYN pakety, které byly přijaty.| Průměr |
| Připojení SNAT |  Veřejný Load Balancer |Load balancer úrovně Standard hlásí počet odchozích toků, které jsou masqueraded k veřejné IP adresy front-endu. Zdrojové síťové adresy překladu (SNAT) porty jsou vyčerpatelným prostředků. Tato metrika se mohou vyjádřit údaj o tom, jak často se aplikace spoléhá na SNAT pro odchozí toky s původem. Čítače pro úspěšné i neúspěšné odchozích toků SNAT označené a slouží k odstranění potíží a porozumět stavu vašich odchozích toků.| Průměr |
| Čítače bajtů |  Nástroj pro vyrovnávání zatížení veřejné a vnitřní | Load balancer úrovně Standard sestavy dat zpracovaných za front-endu.| Průměr |
| Čítače paketů |  Nástroj pro vyrovnávání zatížení veřejné a vnitřní | Load balancer úrovně Standard sestavy pakety zpracovaných za front-endu.| Průměr |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Zobrazit metriky nástroje pro vyrovnávání zatížení na webu Azure Portal

Na webu Azure portal poskytuje metriky pro vyrovnávání zatížení prostřednictvím stránky metriky (preview), která je k dispozici na stránce Azure Monitor a oba zatížení nástroje pro vyrovnávání prostředků stránky pro určitý prostředek. 

Pokud chcete zobrazit metriky pro vaše prostředky nástroje pro vyrovnávání zatížení:
1. Přejděte na stránku metriky (preview) a proveďte jednu z následujících akcí:
   * Na stránce prostředků nástroje pro vyrovnávání zatížení vyberte v rozevíracím seznamu Typ metriky.
   * Na stránce Azure Monitor vyberte prostředek nástroje pro vyrovnávání zatížení.
2. Nastavte typ odpovídající agregace.
3. Volitelně můžete nakonfigurujte požadované filtrování a seskupování.

![Ve verzi preview metriky pro Load balancer úrovně Standard](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Obrázek: DIP dostupnosti a stavu sondy stavu metriky Load balanceru úrovně Standard*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Načíst vícedimenzionálních metrik přes rozhraní API prostřednictvím kódu programu

Pokyny k rozhraní API pro získání definice vícerozměrné metriky a hodnoty, najdete v části [rozhraním API REST pro monitorování Azure návod](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Běžné scénáře diagnostiky a doporučené zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Je na cestu k datům a k dispozici pro tento nástroj pro vyrovnávání zatížení virtuální IP adresy?

Metriky dostupnosti virtuálních IP adres popisuje stavu na cestu k datům v rámci oblasti na výpočetní prostředky hostitele, kde jsou umístěné vaše virtuální počítače. Metrika je odraz na stav infrastruktury Azure. Můžete použít metriky pro:
- Monitoruje dostupnost externí služby
- Podívejte se podrobněji a Beru na vědomí, zda je platforma, na kterém je vaše služba nasazená v pořádku nebo určuje, zda je v pořádku hostovaného operačního systému nebo instance aplikace.
- Zjištění, jestli událost souvisí s vaší služby nebo Základní rovina dat. Nepleťte si tato metrika se sonda stavu ("vyhrazené IP adresy dostupnosti").

Získejte dostupnost virtuální IP adresy vašich prostředků Load balanceru úrovně Standard:
1. Ujistěte se, že je vybraný prostředek nástroje pro vyrovnávání zatížení správné. 
2. V **metrika** rozevíracího seznamu vyberte **dostupnost virtuálních IP adres**. 
3. V **agregace** rozevíracího seznamu vyberte **Avg**. 
4. Kromě toho přidat filtr na virtuální IP adresu nebo port virtuální IP adresy jako dimenze požadované front-endovou IP adresu nebo front-end port a seskupíme je podle vybrané dimenze.

![Zjišťování virtuálních IP adres](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek: VIP nástroje pro vyrovnávání zatížení podrobnosti o zjišťování*

Metrika je generován aktivní, integrované měření. Zjišťování služby v rámci oblasti mohou být provoz pro měření. Služba se aktivuje, jakmile vytvoříte nasazení s veřejnou front-endem a pokračuje až do odebrání front-endu. 

>[!NOTE]
>Interní front-endů nejsou v tuto chvíli nepodporuje. 

Paket odpovídající front-endu vašeho nasazení a pravidlo se vygeneruje pravidelně. Přenosech oblast ze zdroje do hostitele ve kterém se nachází virtuální počítač v back endového fondu. Infrastruktura nástroje pro vyrovnávání zatížení provede stejné operace překlad a vyrovnávání zatížení, jak funguje pro všechny ostatní přenosy. Tento test je integrovaná na váš koncový bod s vyrovnáváním zatížení. Po testu dorazí na výpočetní prostředky hostitele, kde je umístěn v dobrém stavu virtuálního počítače v back endového fondu, generuje hostitele výpočetního odpověď pro službu zjišťování. Váš virtuální počítač nezobrazuje tento provoz.

Dostupnost virtuálních IP adres se nezdaří z následujících důvodů:
- Nasazení nemá žádné virtuální počítače v pořádku, zbývající ve fondu back-end. 
- Došlo k výpadku infrastruktury.

Pro diagnostické účely můžete použít [metriku dostupnosti virtuálních IP adres spolu s sondy stavu](#vipavailabilityandhealthprobes).

Použití **průměrné** jako agregace pro většinu scénářů.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Je back endových instancí pro moje virtuální IP adresy reagovat na požadavky sondy?

Metriky stavu sondy stavu popisuje stav nasazení vaší aplikace, které nakonfiguroval můžete při konfiguraci sondy stavu nástroje pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení používá k určení, kam chcete odeslat nové toky stav sondu stavu. Sondy stavu pocházejí z adresy infrastrukturu Azure a jsou viditelné v rámci hostovaného operačního systému virtuálního počítače.

Pokud chcete získat dostupnost vyhrazené IP adresy vašich prostředků Load balanceru úrovně Standard:
1. Vyberte **DIP dostupnosti** metriky s **Avg** typ agregace. 
2. Použijte filtr na požadované virtuální IP adresu nebo port (nebo obojí).

![Dostupnost vyhrazené IP adresy](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Obrázek: Dostupnost virtuálních IP adres nástroje pro vyrovnávání zatížení*

Sondy stavu selhat z následujících důvodů:
- Konfigurace sondy stavu na port, který neprobíhá naslouchání nebo neodpovídá nebo je pomocí nesprávného protokolu. Pokud vaše služba používá vrácení přímá odpověď ze serveru (žádosti PSÚ, nebo plovoucí IP Adresou) pravidla, ujistěte se, že služba naslouchá na IP adresu síťovou kartu Konfigurace protokolu IP a ne jen na zpětné smyčky, který je nakonfigurovaný s front-endových IP adres.
- Váš test není povolena ve skupině zabezpečení sítě, brána firewall operačního systému hosta Virtuálního počítače nebo filtry vrstvy aplikace.

Použití **průměrné** jako agregace pro většinu scénářů.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak můžu ověřit, Moje statistiky odchozí připojení? 

Metrika připojení SNAT popisuje objem úspěšné i neúspěšné připojení pro [odchozích toků](https://aka.ms/lboutbound).

Neúspěšná připojení svazku větší než nula znamená vyčerpání portů SNAT. Můžete třeba dalším šetřením určit co může být příčinou selhání. Vyčerpání portů SNAT manifesty jako selhání stanovit [odchozí tok](https://aka.ms/lboutbound). Přečtěte si článek o odchozích připojení pro pochopení scénáře a mechanismy v práci a na další informace o zmírnění a návrh pro zabránění vyčerpání portů SNAT. 

Pokud chcete získat statistiky připojení SNAT:
1. Vyberte **připojení SNAT** typ metriky a **součet** jako agregace. 
2. Seskupit podle **stav připojení** pro počty SNAT připojení úspěšné i neúspěšné, které jsou reprezentovány samostatné řádky. 

![Připojení SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek: Počet připojení SNAT nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak můžu zkontrolovat pokusy o příchozí/odchozí připojení pro svou službu?

Metrika pakety SYN popisuje objemu TCP SYN paketů, které jsme nebo byly odeslány (pro [odchozích toků](https://aka.ms/lboutbound)), které jsou spojeny s konkrétní front-endu. Tato metrika slouží k pochopení pokusy o připojení TCP ke službě.

Použití **celkový** jako agregace pro většinu scénářů.

![SYN připojení](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek: Počet SYN nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak můžu zkontrolovat spotřebu šířky pásma sítě? 

Bajty a čítače paketů, Metrika popisuje objem bajtů a paketů, které jsou odeslány nebo přijaty pomocí služby na základě za front endu.

Použití **celkový** jako agregace pro většinu scénářů.

Pokud chcete získat statistiky počet bajtů nebo paketu:
1. Vyberte **počet bajtů** a/nebo **počet paketů** typ metriky s **Avg** jako agregace. 
2. Proveďte jednu z následujících akcí:
   * Použijte filtr na konkrétní front-endové IP adresy, front-end port, back endovou IP adresu nebo port back-end.
   * Získání celkové statistiky pro váš prostředek nástroje pro vyrovnávání zatížení bez filtrování.

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek: Počet bajtů nástroje pro vyrovnávání zatížení*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak se Diagnostika Moje nasazení nástroje pro vyrovnávání zatížení?

Pomocí kombinace dostupnosti virtuálních IP adres a metrik sondy stavu na jednom grafu můžete určit, kde hledat potíže a vyřešení problému. Můžete získat jistotu, že Azure pracuje správně a použít tyto znalosti jednoznačně určit, že je konfigurace nebo aplikace je hlavní příčinu.

Metriky sondu stavu můžete použít k pochopení, jak Azure zobrazí stav nasazení podle konfigurace, které jste zadali. Prohlížení sond stavu je vždy skvělým prvním krokem při monitorování a určení příčiny.

Může jít o krok dál a získat přehled o tom, jak zobrazení stavu základní rovině dat, který je zodpovědný za konkrétní nasazení v Azure pomocí metrik dostupnosti virtuálních IP adres. Když zkombinujete obě metriky, můžete izolovat, kde může být v době, jak je znázorněno v tomto příkladu:

![Diagnostika virtuálních IP adres](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Obrázek: Kombinace metrik dostupnosti vyhrazené IP adresy a virtuální IP adresy*

V grafu se zobrazují následující informace:
- Byla infrastruktury sám v pořádku, byla infrastruktury hostování virtuálních počítačů dostupné a více než jeden virtuální počítač byl umístěn v back-endu. Tyto informace je indikován modré trasování dostupnosti virtuálních IP adres, což je 100 % jeho obsahu. 
- Stav testu (DIP dostupnost) je však v 0 procent na začátku grafu, je určeno oranžové trasování. Oblasti v kroužku v zelené zvýraznění kde stav (DIP dostupnosti) se v pořádku, a na kterém bodu tak zákazníkovo nasazení se tak, aby přijímal nové toky.

Graf umožňuje zákazníkům řešení potíží s nasazení sami bez nutnosti uhodnout nebo požádejte podporu, zda dochází k další problémy. Službu nebylo k dispozici, protože sond stavu byly nepovedlo kvůli chybě konfigurace nebo aplikaci, která selhala.

### <a name = "Limitations"></a>Omezení

Dostupnost virtuálních IP adres je aktuálně k dispozici pouze pro veřejné front-endů.

## <a name = "ResourceHealth"></a>Stav služby Resource health

Stav prostředků Load balanceru úrovně Standard je zveřejněný prostřednictvím existující **Resource health** pod **sledování > Stav služby**.

>[!NOTE]
>Stav prostředku nástroje pro vyrovnávání zatížení je momentálně dostupná jenom veřejné konfigurace Load balanceru úrovně Standard. Služba resource health nezveřejňujte interního nástroje pro vyrovnávání prostředků nebo prostředky SKU nástroje Load balancer úrovně Basic.

Chcete-li zobrazit stav svých prostředků veřejného Load balanceru úrovně Standard:
1. Vyberte **monitorování** > **Service Health**.

   ![Stránka monitorování](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek: Odkaz stav služby na Azure monitoru*

2. Vyberte **Resource Health**a ujistěte se, že **ID předplatného** a **typ prostředku = nástroje pro vyrovnávání zatížení** jsou vybrány.

   ![Stav prostředku](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek: Vyberte prostředek pro zobrazení stavu*

3. V seznamu vyberte prostředek nástroje pro vyrovnávání zatížení můžete zobrazit svého historické stavu.

    ![Stav služby Load Balancer stavu](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek: Zobrazení stavu prostředku nástroje pro vyrovnávání zatížení*
 
Různé stavy stavu prostředků a jejich popisy jsou uvedeny v následující tabulce: 

| Stav prostředku | Popis |
| --- | --- |
| K dispozici. | Váš prostředek nástroje pro vyrovnávání veřejné standardního zatížení je v pořádku a k dispozici. |
| Není dostupný | Váš prostředek nástroje pro vyrovnávání veřejné standardního zatížení není v pořádku. Diagnostika stavu tak, že vyberete **Azure Monitor** > **metriky**.<br>(*Není k dispozici* stav může také znamenat, že prostředek není spojena s vaší veřejného load balanceru úrovně standard.) |
| Neznámé | Stav prostředků pro prostředek standardní veřejný nástroj pro vyrovnávání ještě není aktualizovaný.<br>(*Neznámý* stav může také znamenat, že prostředek není spojena s vaší veřejného load balanceru úrovně standard.)  |

## <a name="next-steps"></a>Další postup

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o vaší [odchozí připojení nástroje pro vyrovnávání zatížení](https://aka.ms/lboutbound).
- Další informace o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Další informace o [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) a [jak načíst metriky přes rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metrics/metrics_list).


