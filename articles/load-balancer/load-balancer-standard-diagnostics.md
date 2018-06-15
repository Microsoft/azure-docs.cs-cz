---
title: Azure diagnostics nástroj pro vyrovnávání zatížení | Microsoft Docs
description: Použijte k dispozici informace o stavu a metrik pro diagnostiku pro standardní Vyrovnávání zatížení Azure.
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
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 9d5d596254f673b86650e8d9754dacdb70be0666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179790"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Diagnostika stavu a metrik pro nástroj pro vyrovnávání zatížení

Azure standardní nástroj pro vyrovnávání zatížení poskytuje následující možnosti diagnostiky vaše prostředky:
* **Multidimenzionální metriky**: poskytuje nové vícerozměrných diagnostické funkce pro veřejné a interní zatížení konfigurací služby Vyrovnávání. Můžete monitorovat, spravovat a řešení potíží s prostředky nástroje pro vyrovnávání zatížení.

* **Stav prostředku**: Vyrovnávání zatížení stránky na portálu Azure a stav prostředků stránky (v části monitorování) vystavit části stav prostředku pro konfiguraci služby Vyrovnávání zatížení veřejnou standardní Vyrovnávání zatížení.

Tento článek poskytuje stručný přehled těchto funkcí a nabízí způsoby, jak je používat pro nástroj pro vyrovnávání zatížení.

## <a name = "MultiDimensionalMetrics"></a>Multidimenzionální metriky

Azure Vyrovnávání zatížení poskytuje nové vícerozměrných metriky prostřednictvím nové metriky Azure (preview) na portálu Azure, a pomáhá vám v reálném čase diagnostiky přehledy zatížení vyrovnávání prostředků. 

Různých konfiguracích nástroj pro vyrovnávání zatížení zadejte následující metriky:

| Metrika | Typ prostředku | Popis | Doporučené agregace |
| --- | --- | --- | --- |
| Dostupnost VIP (cesta dostupnost dat) | Nástroj pro vyrovnávání zatížení veřejnou | Standardní Vyrovnávání zatížení nepřetržitě vykonává cesty data z v rámci oblasti na zatížení vyrovnávání front-endu, abyste SDN zásobníku, který podporuje virtuální počítač. Také v pořádku instancí zůstanou, následuje měření stejnou cestu jako Vyrovnávání zatížení sítě provoz vaší aplikace. Cesta data, která vašim zákazníkům použít také byl ověřen. Měření neviditelná do vaší aplikace a nebudou v konfliktu s dalšími operacemi.| Průměr |
| Dostupnost vyhrazené IP adresy (stav testu) |  Nástroj pro vyrovnávání zatížení veřejné a interní | Nástroj pro vyrovnávání zatížení používá Distribuovaná služba zjišťování stavu, která monitoruje stav váš koncový bod aplikace podle nastavení konfigurace. Tato metrika poskytuje agregaci ani na koncový bod filtrované zobrazení každý koncový bod instance ve fondu nástroje pro vyrovnávání zatížení. Jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace, můžete zobrazit podle vaší konfigurace testu stavu. |  Průměr |
| SYN (synchronizovat) paketů |  Nástroj pro vyrovnávání zatížení veřejnou | Nástroj pro vyrovnávání zatížení nepodporuje ukončit připojení protokolu TCP (Transmission Control) nebo interakci s TCP nebo UDP paketů toky. Toky a jejich metodou handshake jsou vždy mezi zdrojovým a instance virtuálního počítače. Chcete-li lépe vyřešit vaše scénáře protokolu TCP, je nutné používat SYN čítače pakety zjistit, kolik připojení TCP se pokusy. Metrika hlásí počet paketů TCP SYN, které byly přijaty.| Průměr |
| Překládat pomocí SNAT připojení |  Nástroj pro vyrovnávání zatížení veřejnou |Nástroj pro vyrovnávání zatížení hlásí počet odchozí toky, které jsou masqueraded na veřejné IP adresy front-endu. Zdroj síťové adresy překlad (překládat pomocí SNAT) porty jsou vyčerpatelným prostředků. Tato metrika mohou poskytnout údaj o tom, jak výraznou je vaše aplikace spoléhat na překládat pomocí SNAT pro odchozí toky původu. Čítače pro úspěšné i neúspěšné odchozí toky překládat pomocí SNAT hlášení a slouží k řešení potíží a pochopení stavu odchozích toků.| Průměr |
| Čítače bajtů |  Nástroj pro vyrovnávání zatížení veřejné a interní | Nástroj pro vyrovnávání zatížení sestavy dat zpracovaných za front-endu.| Průměr |
| Čítače paketů |  Nástroj pro vyrovnávání zatížení veřejné a interní | Nástroj pro vyrovnávání zatížení hlásí paketů zpracovaných za front-endu.| Průměr |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Zobrazit vaše metriky Vyrovnávání zatížení na portálu Azure

Portál Azure zpřístupní metriky Vyrovnávání zatížení prostřednictvím stránky metriky (preview), která je k dispozici na obou zatížení vyrovnávání prostředků stránky pro určitý prostředek a monitorování Azure stránky. 

Chcete-li zobrazit metriky pro vaše prostředky nástroj pro vyrovnávání zatížení:
1. Přejít na stránku metriky (preview) a proveďte jednu z následujících akcí:
   * Na stránce prostředků nástroje pro vyrovnávání zatížení v rozevíracím seznamu vyberte typ metriky.
   * Na stránce monitorování Azure vyberte prostředek pro vyrovnávání zatížení.
2. Nastavte typ odpovídající agregace.
3. Volitelně nakonfigurujte požadované filtrování a seskupení.

![Metriky náhled pro nástroj pro vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Obrázek: Dostupnost vyhrazené IP adresy a metriky stav kontroly stavu pro nástroj pro vyrovnávání zatížení*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Načtení vícerozměrných metriky programově pomocí rozhraní API

Pokyny k rozhraní API pro načítání vícerozměrných definice metrik a hodnoty, najdete v části [monitorování REST API služby Azure návod](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Běžné scénáře diagnostiky a doporučené zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Je cestu k datům a k dispozici pro moje vyrovnávání zátěže virtuální adresy IP?

Metrika dostupnosti VIP popisuje stav cestu k datům v rámci oblasti pro hostitele výpočetního, kde se nachází virtuální počítače. Metrika je odraz stav infrastruktury Azure. Můžete použít metrika na:
- Monitoruje dostupnost externí služby
- Prozkoumat podrobněji a zjistit, jestli je v pořádku platformy, na kterém je nasazený služby nebo zda je v pořádku hostovaný operační systém nebo instanci aplikace.
- Zjistěte, jestli událost souvisí s služby nebo základní roviny data. Nezaměňujte tato metrika se stav testu ("DIP dostupnosti").

Pokud chcete získat dostupnost virtuálních IP adres pro vaše prostředky nástroj pro vyrovnávání zatížení:
1. Zkontrolujte, zda že je vybrán zdroj nástroje pro vyrovnávání zatížení správné. 
2. V **metrika** rozevíracího seznamu vyberte **VIP dostupnosti**. 
3. V **agregace** rozevíracího seznamu vyberte **průměr**. 
4. Kromě toho přidat filtr na virtuální adresy IP adresu nebo port VIP jako dimenze s požadované front-end IP adresu nebo front-end port a seskupovat je vybraných dimenzí.

![Zjišťování virtuálních IP adres](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek: VIP pro vyrovnávání zatížení zkušební fáze podrobnosti*

Metrika je generován aktivní, integrované měření. Testování služby v rámci oblasti pochází provoz pro měření. Služba je aktivována, jakmile vytvoříte nasazení s veřejných front end a pokračuje, dokud neodeberete front-endu. 

>[!NOTE]
>Interní front-end nejsou podporovány v tuto chvíli. 

Generuje se pravidelně paket odpovídající front-endu vašeho nasazení a pravidla. Přenosech oblasti ze zdroje hostitele, kde je umístěn virtuální počítač ve fondu back-end. Infrastruktury nástroje pro vyrovnávání zatížení provádí stejné operace překlad a vyrovnávání zatížení, stejně jako pro všechny ostatní přenosy. Tento test je integrovaná na koncový bod služby Vyrovnávání zatížení sítě. Po sondy na hostitele výpočetního dorazí, kde se nachází v pořádku virtuálního počítače ve fondu back-end, hostitele výpočetního generuje odpovědi ke službě testování. Virtuální počítač nejsou vidět tento provoz.

Dostupnost VIP selže z následujících důvodů:
- Nasazení nemá žádné virtuální počítače v pořádku, zbývající ve fondu back-end. 
- Došlo k výpadku infrastruktury.

Pro účely diagnostiky, můžete použít [metriku dostupnosti VIP společně s stav testu](#vipavailabilityandhealthprobes).

Použití **průměrná** jako agregace pro většinu scénářů.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Instance back-end pro moje VIP neodpovídá na sondy?

Metrika stav testu stavu stav nasazení vaší aplikace popisuje, jak nakonfigurovat při konfiguraci test stavu systému nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení používá k určení kam má posílat nové toky stav kontroly stavu. Sondy stavu pocházejí z adresy infrastrukturu Azure a jsou viditelné v rámci hostovaného operačního systému virtuálního počítače.

Pokud chcete získat dostupnost vyhrazené IP adresy pro vaše prostředky nástroj pro vyrovnávání zatížení:
1. Vyberte **DIP dostupnosti** metriky s **průměr** typ agregace. 
2. Filtr použijte u požadované virtuální IP adresy IP adresu nebo port (nebo obě).

![Dostupnost vyhrazené IP adresy](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Obrázek: Dostupnost VIP pro vyrovnávání zatížení*

Sondy stavu selhat z následujících důvodů:
- Konfigurace test stavu na port, který je nenaslouchá nebo neodpovídá nebo je nesprávný protokol. Pokud vaše služba používá přímá odpověď ze serveru (DSR nebo plovoucí IP adresa) pravidla, ujistěte se, že služba naslouchá na IP adresu konfigurace protokolu IP v Síťovém a nikoli jen na zpětné smyčky, který je nakonfigurovaný s front-end IP adresy.
- Skupina zabezpečení sítě, brána firewall operačního systému hosta Virtuálního počítače nebo vrstvy filtry aplikace není povolená vaší testu.

Použití **průměrná** jako agregace pro většinu scénářů.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak zkontrolovat statistiku Moje odchozí připojení? 

Metrika připojení překládat pomocí SNAT popisuje objem úspěšné i neúspěšné připojení pro [odchozí toky](https://aka.ms/lboutbound).

Neúspěšné připojení hlasitosti větší než nula označuje vyčerpání port překládat pomocí SNAT. Chcete-li zjistit, co může být příčinou selhání musíte prozkoumat další. Vyčerpání port překládat pomocí SNAT manifesty jako selhání navázat [odchozího toku](https://aka.ms/lboutbound). Přečtěte si článek o odchozí připojení pochopit scénáře a mechanismy v práci a k zjistěte, jak zmírnit a navrhnout předejdete vyčerpání port překládat pomocí SNAT. 

Pokud chcete získat statistiku překládat pomocí SNAT připojení:
1. Vyberte **připojení překládat pomocí SNAT** typ metriky a **součet** jako agregace. 
2. Seskupit podle **stav připojení** pro úspěšné i neúspěšné počtů překládat pomocí SNAT připojení, které jsou reprezentované pomocí různých odvětví. 

![Překládat pomocí SNAT připojení](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek: Počet připojení překládat pomocí SNAT nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak zkontrolovat pokusy o příchozí nebo odchozí připojení pro mé služby?

Metriky pakety SYN popisuje objem TCP SYN paketů, které se zobrazila, nebo byly odeslány (pro [odchozí toky](https://aka.ms/lboutbound)) přidružené určité front-endu. Tato metrika vám pomůže pochopit TCP pokusy o připojení k službě.

Použití **celkový** jako agregace pro většinu scénářů.

![SYN připojení](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek: Počet SYN nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak zkontrolovat Moje spotřeba šířky pásma sítě? 

Bajty a čítače paketů, Metrika popisuje svazku bajtů a paketů, které jsou odesílat nebo přijímat služby na základě end na popředí.

Použití **celkový** jako agregace pro většinu scénářů.

Pokud chcete získat statistiky počet bajtů nebo paketu:
1. Vyberte **počet bajtů** nebo **počet** metriky typu, s **průměr** jako agregace. 
2. Proveďte jednu z následujících akcí:
   * Použijte filtr na konkrétní front-end IP adresu, front-end port, back-end IP nebo back-end port.
   * Získání celkové statistiky pro vaše prostředek pro vyrovnávání zatížení bez jakéhokoli filtrování.

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek: Počet bajtů pro vyrovnávání zatížení*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak mohu diagnostikovat Moje nasazení nástroje pro vyrovnávání zatížení?

Pomocí kombinace dostupnosti virtuálních IP adres a stav testu metriky na jednom grafu můžete určit, kde vyhledat potíže a problém vyřešit. Můžete získat záruku toho, že Azure pracuje správně a použít tyto znalosti jednoznačně určit, že konfigurace nebo aplikací je hlavní příčinu.

Stav testu metriky vám pomůže pochopit, jak Azure zobrazení stavu nasazení podle konfiguraci, kterou jste zadali. Prohlížení sondy stavu služby je vždy skvělé prvním krokem při monitorování nebo zjišťování příčinu.

Můžete provádět ho další krok a použít VIP dostupnosti metriky a získáte přehled o tom, jak Azure zobrazení stavu základní roviny data, která je odpovědná za konkrétní nasazení. Když zkombinujete oba metriky, můžete izolovat, kde může být chyby, jak je ukázáno v tomto příkladu:

![Diagnostika VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Obrázek: Kombinování vyhrazené IP adresy a VIP metrikách dostupnosti*

Grafu zobrazí následující informace:
- Samotný infrastruktury byl v pořádku, infrastrukturu hostování virtuálních počítačů byla dostupná a více než jeden virtuální počítač byl uveden do back-end. Tyto informace je indikován blue trasování pro VIP dostupnosti, která na 100 %. 
- Stav testu (DIP dostupnosti) je však na 0 procent na začátku grafu, jak oranžové trasování. Oblasti v kroužku v zelená označuje, kde stav (DIP dostupnosti) jsou v pořádku, a v okamžiku nasazení zákazníka se moci přijímat nové toky.

Graf umožňuje zákazníkům řešení nasazení ve svých vlastních bez nutnosti uhodnout nebo požádejte podporu, zda dochází k další problémy. Službu nebylo k dispozici, protože nebylo možné sondy stavu z důvodu nesprávnou konfiguraci nebo aplikaci, která selhala.

### <a name = "Limitations"></a>Omezení

Dostupnost VIP je aktuálně k dispozici pouze pro elementy end veřejných front.

## <a name = "ResourceHealth"></a>Stav prostředku

Stav pro prostředky. nástroj pro vyrovnávání zatížení je vystaven přes existující **stav prostředku** pod **sledování > Stav služby**.

>[!NOTE]
>Stav prostředku pro vyrovnávání zatížení je aktuálně dostupné pro pouze veřejné konfigurace nástroj pro vyrovnávání zatížení. Interní služby load vyrovnávání prostředků nebo prostředky základní SKU služby Vyrovnávání zatížení nezveřejňují stav prostředku.

Pro zobrazení stavu prostředků veřejného nástroj pro vyrovnávání zatížení:
1. Vyberte **monitorování** > **služby stavu**.

   ![Stránka Sledování](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek: Stav služby odkaz na Azure monitorování*

2. Vyberte **stav prostředku**a potom zkontrolujte, zda **ID předplatného** a **typ prostředku = nástroj pro vyrovnávání zatížení** jsou vybrány.

   ![Stav prostředku](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek: Vyberte prostředek pro zobrazení stavu*

3. V seznamu vyberte prostředek pro vyrovnávání zatížení můžete zobrazit jeho historických stav.

    ![Stav služby Vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek: Zobrazení stavu prostředků pro vyrovnávání zatížení*
 
V následující tabulce jsou uvedené různé stavy stavu prostředků a jejich popisy: 

| Stav prostředku | Popis |
| --- | --- |
| Dostupné | Prostředek veřejné standardního zatížení vyrovnávání je v pořádku a k dispozici. |
| Není k dispozici | Prostředek veřejné standardního zatížení vyrovnávání není v pořádku. Diagnostika stavu výběrem **Azure monitorování** > **metriky**.<br>(*Není k dispozici* stav také může znamenat, že prostředek není připojen k nástroj pro vyrovnávání zatížení veřejnou standardní.) |
| Neznámý | Stav prostředku prostředku nástroje pro vyrovnávání zatížení veřejné standardní dosud nebyla aktualizována.<br>(*Neznámé* stav také může znamenat, že prostředek není připojen k nástroj pro vyrovnávání zatížení veřejnou standardní.)  |

## <a name="next-steps"></a>Další postup

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o vaší [odchozí připojením ke službě Vyrovnávání zatížení](https://aka.ms/lboutbound).


