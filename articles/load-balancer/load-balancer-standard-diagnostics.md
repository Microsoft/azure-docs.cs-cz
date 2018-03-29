---
title: Pro vyrovnávání zatížení Azure standardní - diagnostiky | Microsoft Docs
description: Pomocí dostupné informace o stavu a metrik pro diagnostiku pro standardní Vyrovnávání zatížení Azure
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
ms.openlocfilehash: 7d60925381abe617f6e2fac51176b8e30517c3ba
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Diagnostika stavu a metrik pro nástroj pro vyrovnávání zatížení

Azure standardní nástroj pro vyrovnávání zatížení poskytuje následující možnosti diagnostiky pro vaše prostředky:
* Multidimenzionální metrika: Nástroj pro vyrovnávání zatížení poskytuje nové vícerozměrných diagnostické funkce pro veřejné a vnitřní konfigurace pro vyrovnávání zatížení. To vám umožňuje monitorovat, spravovat a řešení potíží s prostředky pro vyrovnávání zatížení.

* Stav prostředku: Na stránce nástroje pro vyrovnávání zatížení na portálu Azure a stav prostředků stránky (v části monitorování) vystavit stav prostředku pro konfiguraci veřejné nástroje pro vyrovnávání zatížení standardní služby Vyrovnávání zatížení.

Tento článek poskytuje stručný přehled tyto možnosti a způsoby, jak je použít pro nástroj pro vyrovnávání zatížení.

## <a name = "MultiDimensionalMetrics"></a>Multidimenzionální metriky

Azure nástroj pro vyrovnávání zatížení poskytuje nové vícerozměrných metriky prostřednictvím nové metriky Azure (preview) na portálu a pomáhá vám v reálném čase diagnostiky přehledy o vašich prostředků pro vyrovnávání zatížení. 

Následující metriky jsou dnes k dispozici pro různé konfigurace standardní nástroje pro vyrovnávání zatížení (LB):

| Metrika | Typ prostředku | Popis | Doporučené agregace |
| --- | --- | --- | --- |
| Dostupnost VIP (cesta dostupnost dat) | Veřejné LB | Nástroj pro vyrovnávání zatížení nepřetržitě vykonává cesty data z v rámci oblasti na front-endu nástroj pro vyrovnávání zatížení až SDN zásobníku, který podporuje virtuální počítač. Také v pořádku instancí zůstanou, následuje měření stejnou cestu jako Vyrovnávání zatížení sítě provoz vaší aplikace. Také ověřuje cestu datům, která je používána zákazníky. Měření neviditelná do vaší aplikace a nebudou v konfliktu s dalšími operacemi.| Průměr |
| Dostupnost vyhrazené IP adresy (stav testu) |  Veřejné a interní LB | Nástroj pro vyrovnávání zatížení používá distribuované stavu zjišťování služby, která monitoruje stav váš koncový bod aplikace podle nastavení konfigurace. Tato metrika poskytuje agregaci nebo na koncový bod filtrované zobrazení každé jednotlivé instance koncového bodu nástroji pro vyrovnávání zatížení fondu.  Uvidíte, jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace podle vašeho Konfigurace testu stavu. |  Průměr |
| SYN paketů |  Veřejné LB | Nástroj pro vyrovnávání zatížení nepodporuje ukončit připojení TCP nebo interakci s TCP nebo UDP paketů toky. Toky a jejich metodou handshake jsou vždy mezi zdrojovým a instance virtuálního počítače. Chcete-li lépe vyřešit vaše scénáře protokolu TCP, je nutné používat SYN čítače pakety zjistit, kolik připojení TCP se pokusy. Metrika hlásí počet paketů TCP SYN, které byly přijaty.| Průměr |
| Překládat pomocí SNAT připojení |  Veřejné LB |Nástroj pro vyrovnávání zatížení hlásí počet odchozí toky, které jsou masqueraded do front-endu veřejnou IP adresu. Překládat pomocí SNAT porty jsou vyčerpatelným prostředků. Tato metrika mohou poskytnout údaj o tom, jak výraznou je vaše aplikace spoléhat na překládat pomocí SNAT pro odchozí toky původu.  Čítače pro úspěšné i neúspěšné odchozí toky překládat pomocí SNAT hlášení a slouží k řešení potíží a pochopení stavu odchozích toků.| Průměr |
| Čítače bajtů |  Veřejné a interní LB | Nástroj pro vyrovnávání zatížení sestavy dat zpracovaných za front-endu.| Průměr |
| Čítače paketů |  Veřejné a interní LB | Nástroj pro vyrovnávání zatížení hlásí paketů zpracovaných za front-endu.| Průměr |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Nástroj pro vyrovnávání zatížení metriky zobrazit na portálu

Portál Azure poskytuje metriky Vyrovnávání zatížení prostřednictvím stránky metriky (Preview), která je k dispozici na stránce prostředek pro vyrovnávání zatížení pro určitý prostředek pro vyrovnávání zatížení a také na stránce monitorování Azure. 

Chcete-li zobrazit metriky pro vaše prostředky standardní Vyrovnávání zatížení, procházet metrik (preview) na jednom z těchto umístění, vyberte (prostředek pro vyrovnávání zatížení Pokud v části monitorování stránky) zadejte metrika z rozevíracího seznamu nastavte příslušný typ agregace a volitelně Nakonfigurujte požadované filtrování a seskupení a budete moci zobrazit Historický přehled pro metriku v zadané podmínkách.

![Metriky náhled pro nástroj pro vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Obrázek - DIP dostupnosti / stav testu stavu Metrika pro vyrovnávání zatížení*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Načtení vícerozměrných metriky programově pomocí rozhraní API

Rozhraní API pokyny pro načítání Multi-Dimensional metrika definice a hodnoty jsou k dispozici v [monitorování Walkthrouh rozhraní API REST > Multi-Dimensional rozhraní API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)

### <a name = "DiagnosticScenarios"></a>Běžné scénáře diagnostiky a doporučené zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Je cesta k datům nahoru a k dispozici pro moje VIP pro vyrovnávání zatížení?

Metrika dostupnosti VIP popisuje stav cestu k datům v rámci oblasti pro hostitele výpočetního, kde se nachází virtuální počítače. Je health reflexe infrastruktury Azure. Tato metrika se používá k můžete použít:
- Monitoruje dostupnost externí služby
- Prozkoumat podrobněji a zjistit, zda je v pořádku platformy, na kterém je nasazený vaší služby, nebo zda hostovaný operační systém nebo instanci aplikace jsou v pořádku
- Zjistěte, jestli událost souvisí s služby nebo základní roviny data. Nezaměňujte to s stav testu ("DIP dostupnosti").

Pokud chcete získat dostupnost virtuálních IP adres pro vaše prostředky nástroj pro vyrovnávání zatížení:
- Zkontrolujte, zda že je vybrán správný prostředek pro vyrovnávání zatížení. 
- Vyberte **VIP dostupnosti** z **metrika** rozevíracího seznamu. 
- Vyberte **průměr** pro **agregace**. 
- Kromě toho přidáte filtr na virtuální adresy IP adresu nebo port VIP jako dimenze se vyžaduje front-endovou IP adresou nebo front-endový port a skupinu vybraných dimenzí.

![Zjišťování virtuálních IP adres](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek - VIP pro vyrovnávání zatížení zjišťování podrobnosti*

Metrika je generován aktivní, integrované měření. Testování služby v rámci oblasti pochází přenosy dat pro toto měření se aktivuje, jakmile vytvoříte nasazení s veřejnou front-endovou a bude pokračovat, dokud neodeberete front-endu. 

>[!NOTE]
>Interní frontends nejsou podporovány v tuto chvíli. 

Generuje se pravidelně paket odpovídající front-endové a pravidlo vaše nasazení. Přenosech oblasti ze zdroje hostitele, kde je umístěn virtuální počítač ve fondu back-end. Vyrovnávání zatížení infrastruktury provede stejné operace překlad a vyrovnávání zatížení, stejně jako pro všechny ostatní přenosy. Tento test je integrované v zatížení vyrovnáváním koncový bod. Jakmile tato kontrola na hostitele výpočetního dorazí, kde se nachází v pořádku virtuálního počítače ve fondu back-end, vygeneruje hostitele výpočetního odpověď k testování služby. Virtuální počítač nejsou vidět tento provoz.
Dostupnost VIP selže z následujících důvodů:
- Nasazení nemá žádné virtuální počítače v pořádku, zbývající ve fondu back-end. 
- Výpadek infrastruktury došlo k chybě, způsobí, že dostupnost virtuálních IP adres k selhání.

Můžete použít [metriku dostupnosti VIP společně s stav testu k diagnostickým účelům](#vipavailabilityandhealthprobes).

Použití **průměrná** jako agregace pro většinu scénářů.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>Instance back-end pro moje VIP neodpovídá na sondy?

Metrika stav testu stavu stav nasazení vaší aplikace popisuje, jak nakonfigurovat při konfiguraci test stavu nástroje pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení používá k určení kam má posílat nové toky stav kontroly stavu. Sondy stavu pocházejí z adresy infrastrukturu Azure a jsou viditelné v rámci hostovaného operačního systému virtuálního počítače.

Chcete-li získat dostupnost vyhrazené IP adresy pro vaše prostředky nástroj pro vyrovnávání zatížení
- Vyberte **DIP dostupnosti** metriky s **průměr** typ agregace. 
- Filtr použijte u požadované virtuální IP adresy IP adresu nebo port (nebo obě).

![Dostupnost vyhrazené IP adresy](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Obrázek - dostupnosti VIP pro vyrovnávání zatížení*

Sondy stavu selhat z následujících důvodů:
- Pokud nakonfigurujete test stavu na port, který je nenaslouchá nebo neodpovídá nebo se nesprávný protokol. Pokud vaše služba používá pravidla DSR (plovoucí IP adresy), musíte zajistit, že vaše služba naslouchá na IP adresu konfigurace protokolu IP na síťový adaptér a není právě na zpětné smyčky nakonfigurované front-endovou IP adresu.
- Pokud váš test není povolen podle skupinu zabezpečení sítě, brána firewall operačního systému hosta Virtuálního počítače nebo aplikační vrstvu filtry.

Použití **průměrná** jako agregace pro většinu scénářů.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak zkontrolovat statistiku Moje odchozí připojení? 

Metrika překládat pomocí SNAT připojení popisuje objem úspěšné i neúspěšné (pro [odchozí toky](https://aka.ms/lboutbound)).

Neúspěšné připojení svazku, který je větší než nula označuje vyčerpání port překládat pomocí SNAT. Musí prošetřily a zjistěte, co může být příčinou selhání. Vyčerpání port překládat pomocí SNAT manifesty jako selhání navázat [odchozí toky](https://aka.ms/lboutbound). Přečtěte si článek na odchozí připojení k pochopení scénáře, mechanismy v práci a jak zmírnit / design předejdete vyčerpání port překládat pomocí SNAT. 

Chcete-li získat překládat pomocí SNAT připojení statistiky,
- Vyberte **připojení překládat pomocí SNAT** typ metriky a **součet** jako agregace. 
- skupiny ve **stav připojení** pro úspěšné i neúspěšné připojení překládat pomocí SNAT počty reprezentována pomocí různých odvětví. 

![Překládat pomocí SNAT připojení](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek - počet připojení překládat pomocí SNAT nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Jak zkontrolovat pokusy o příchozí / odchozí připojení pro mé služby?

Metrika SYN pakety popisuje objem TCP SYN paketů, které se zobrazila, nebo byly odeslány (pro [odchozí toky](https://aka.ms/lboutbound)) přidružené k dané front-endu. Tato metrika slouží k pochopení TCP pokusy o připojení k službě.
Celkový počet použijte jako agregace pro většinu scénářů.

![SYN připojení](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek - počet SYN nástroje pro vyrovnávání zatížení*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak zkontrolovat Moje spotřeba šířky pásma sítě? 

Bajtů / čítače paketů metrika popisuje svazku bajtů a pakety odesílat nebo přijímat na základě na front-endové služby.
Použití **celkový** jako agregace pro většinu scénářů.

Chcete-li získat statistiky počet bajtů nebo paketu
- Vyberte **počet bajtů** nebo **počet** metriky typ s **průměr** jako agregace. 
- Použijte filtr na konkrétní front-endovou IP, front-endový port nebo back-end IP nebo portu nejistá. 
- nebo získání celkové statistiky pro prostředek pro vyrovnávání zatížení bez jakéhokoli filtrování.


Některá zobrazení příklad pro metriky s různými konfiguracemi-

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek – počet bajtů pro vyrovnávání zatížení*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak mohu diagnostikovat Moje nasazení nástroje pro vyrovnávání zatížení?

Kombinace VIP dostupnosti & testy stavu metriky pro jeden graf může umožní určete, kam vyhledat potíže a problém vyřešit. Můžete získat záruku toho, že Azure pracuje správně a použijte ji jednoznačně určit konfigurace nebo je hlavní příčinou aplikace.

Stav testu metriky vám pomůže pochopit, jak Azure zobrazení stavu nasazení podle konfiguraci, kterou jste zadali. Prohlížení sondy stavu služby je vždy skvělé prvním krokem při monitorování nebo zjišťování příčinu.

Můžete provádět další krok a použít VIP dostupnosti metriky a získáte přehled o tom, jak Azure zobrazení stavu základní data roviny zodpovědná za konkrétní nasazení. Když zkombinujete oba metriky, můžete izolovat, kde může být chyby, jak je ukázáno v tomto příkladu:

![Diagnostika VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Obrázek – kombinace vyhrazené IP adresy a VIP metrikách dostupnosti*

Graf zobrazuje následující informace:
- Samotný infrastruktury byl v pořádku, infrastrukturu hostování virtuálních počítačů byla dostupná a více než jeden virtuální počítač byl uveden do back-end. To vyplývá z modré trasování pro VIP dostupnosti, která ukazuje na 100 %. 
- Stav testu (DIP dostupnosti) je však na 0 % na začátku grafu podle oranžové trasování. Oblasti v kroužku v red označuje, kde testy stavu (DIP dostupnosti) jsou v pořádku a okamžiku zákazníka nasazení bylo nemůže přijímat nové toky.

Graf povoleno zákazníka k nasazení na své vlastní řešení bez nutnosti uhodnout nebo požádejte podporu, zda byly další problémy, ke kterým dochází. Zákazníka služby nebyl k dispozici, protože nebylo možné sondy stavu z důvodu nesprávnou konfiguraci nebo aplikaci, která selhala.

### <a name = "Limitations"></a>Omezení

- Dostupnost VIP je aktuálně dostupné jen pro veřejné frontends.

## <a name = "ResourceHealth"></a>Stav prostředku

Stav pro prostředky. nástroj pro vyrovnávání zatížení je vystaven přes existující **stav prostředku** pod **sledování > Stav služby**.

>[!NOTE]
>Stav prostředku pro vyrovnávání zatížení je aktuálně dostupné pro veřejné konfigurace pouze standardní Vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení prostředkům nebo prostředkům základní SKU Vyrovnávání zatížení nezveřejňují stav prostředku.

Pro zobrazení stavu prostředků veřejný Standard pro vyrovnávání zatížení
 - Přejděte do **monitorování > služby stavu**.

  ![Stránka Sledování](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek – stav služby u monitorování, Azure*

 - Vyberte **stav prostředku** a ujistěte se, zda správný, **ID předplatného** a **typ prostředku = nástroj pro vyrovnávání zatížení** je vybrána.

  ![Stav prostředku](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek – vyberte prostředek pro zobrazení stavu*

 - Klikněte na nástroj pro vyrovnávání zatížení prostředků ze seznamu zobrazíte stav jejich historie.

    ![Stav služby Vyrovnávání zatížení](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek – zobrazení stavu prostředků pro vyrovnávání zatížení*
 
Následující tabulka uvádí stav různých prostředků a jejich popisy. 

| Stav prostředku | Popis |
| --- | --- |
| Dostupný | Prostředek veřejný Standard pro vyrovnávání zatížení je v pořádku a k dispozici |
| Není k dispozici. | Veřejný Standard pro vyrovnávání zatížení prostředku není v pořádku. Diagnostika stavu prostřednictvím Azure monitorování > metriky. (*Také to může znamenat, že prostředek není veřejný Standard pro vyrovnávání zatížení*) |
| Unknown | Stav prostředku pro veřejné standardní nástroj pro vyrovnávání zatížení dosud nebyla aktualizována. (*Také to může znamenat, že prostředek není veřejný Standard pro vyrovnávání zatížení*)  |

## <a name="next-steps"></a>Další kroky

- Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md)
- Další informace o [odchozí nástroje pro vyrovnávání zatížení připojení](https://aka.ms/lboutbound)


