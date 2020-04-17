---
title: Metriky Azure Monitoru pro aplikační bránu
description: Přečtěte si, jak pomocí metrik sledovat výkon aplikační brány
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457871"
---
# <a name="metrics-for-application-gateway"></a>Metriky pro aplikační bránu

Aplikační brána publikuje datové body, nazývané metriky, do [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview) pro výkon vaší aplikační brány a back-endových instancí. Tyto metriky jsou číselné hodnoty v uspořádané sadě dat časových řad, které popisují některé aspekty brány aplikace v určitém čase. Pokud existují požadavky protékající aplikační bránou, měří a odesílá své metriky v 60sekundových intervalech. Pokud nejsou žádné požadavky protékající aplikační bránou nebo žádná data pro metriku, metrika se nehlásí. Další informace najdete v tématu [metriky Azure Monitor .](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriky podporované soupoložkou soupoložkou brány aplikace V2

### <a name="timing-metrics"></a>Časové metriky

Aplikační brána poskytuje několik předdefinovaných časování metriky související s požadavek a odpověď, které jsou všechny měřeny v milisekundách. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Pokud existuje více než jeden naslouchací proces v bráně aplikace, pak vždy filtrovat podle dimenze *listener* při porovnávání různých metrik latence s cílem získat smysluplné odvození.

- **Čas připojení back-endu**

  Čas strávený navazováním spojení s back-endovou aplikací. 

  To zahrnuje latenci sítě, stejně jako čas, který back-endový server zásobníku TCP vytvořit nová připojení. V případě TLS zahrnuje také čas strávený na podání ruky. 

- **Doba odezvy back-endu na první bajt**

  Časový interval mezi zahájením navazování připojení k back-endovému serveru a přijetím prvního bajtu hlavičky odpovědi. 

  To se blíží součtu *doby připojení back-endu*, času, který požadavek na dosažení back-endu z aplikační brány, času, který back-endová aplikace odebere k odpovědi (čas, který server využil ke generování obsahu, potenciálně načítat databázové dotazy) a čas, který první bajt odpovědi využil k dosažení aplikační brány z back-endu.

- **Doba odezvy back-endu posledního bajtu**

  Časový interval mezi zahájením navazování připojení k back-endovému serveru a přijetím posledního bajtu těla odezvy. 

  To se blíží součtu *doby odezvy back-endu prvního bajtu* a doby přenosu dat (toto číslo se může značně lišit v závislosti na velikosti požadovaných objektů a latenci sítě serveru).

- **Celkový čas brány aplikace**

  Průměrná doba, kterou trvá pro požadavek, který má být přijat, zpracován a jeho odpověď má být odeslána. 

  Toto je interval od okamžiku, kdy aplikace gateway obdrží první bajt požadavku HTTP do doby, kdy byl odeslán poslední bajt odpovědi klientovi. To zahrnuje dobu zpracování přijatou application gateway, *back-endposlední bajt doba odezvy*, čas přijatý Application Gateway odeslat všechny odpovědi a *RTT klienta*.

- **RTT klienta**

  Průměrná doba odezvy mezi klienty a aplikační bránou.



Tyto metriky lze použít k určení, zda pozorované zpomalení je způsobeno klientské sítě, výkon aplikační brány, back-endové sítě a back-endového serveru TCP sytost zásobníku, výkon back-endové aplikace nebo velké velikosti souboru.

Například Pokud je špička v *back-endu první bajt čas odezvy* trend, ale časový trend *připojení back-endu* je stabilní, pak lze odvodit, že aplikační brána pro latenci back-endu a čas navázání připojení je stabilní a špička je způsobena zvýšením doby odezvy back-endové aplikace. Na druhou stranu pokud špička v *back-endu první bajt doba odezvy* je spojena s odpovídající špičkou v *době připojení back-endu*, pak lze odvodit, že buď síť mezi aplikační bránou a back-endový server nebo back-endový server TCP zásobníku má nasycené. 

Pokud zjistíte špičku v *back-endu poslední bajt doba odezvy,* ale *back-end u první hod doba odezvy* je stabilní, pak lze odvodit, že špička je z důvodu větší soubor je požadováno.

Podobně pokud *celkový čas aplikační brány* má špičku, ale doba *odezvy back-endu poslední bajt* je stabilní, pak může být buď příznakem kritického místa výkonu v aplikační bráně nebo kritickým bodem v síti mezi klientem a aplikační bránou. Navíc pokud *má rtt klienttaké* odpovídající špičku, pak znamená, že degradace je z důvodu sítě mezi klientem a aplikační bránou.

### <a name="application-gateway-metrics"></a>Metriky aplikační brány

Pro aplikační bránu jsou k dispozici následující metriky:

- **Přijaté bajty**

   Počet bajtů přijatých aplikační bránou od klientů

- **Odeslané bajty**

   Počet bajtů odeslaných aplikační bránou klientům

- **Protokol TLS klienta**

   Počet požadavků TLS a požadavků netls iniciovaných klientem, který navázal spojení s aplikační bránou. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS dimenze.

- **Jednotky aktuální kapacity**

   Počet jednotek kapacity spotřebovaných k vyrovnávání zatížení provozu. Existují tři determinanty jednotky kapacity - výpočetní jednotka, trvalá připojení a propustnost. Každá jednotka kapacity se skládá maximálně z: 1 výpočetní jednotky nebo 2500 trvalých připojení nebo propustnosti 2,22 Mb/s.

- **Aktuální výpočetní jednotky**

   Počet spotřebované kapacity procesoru. Faktory ovlivňující výpočetní jednotku jsou připojení TLS/s, výpočty přepisování adres URL a zpracování pravidel WAF. 

- **Aktuální připojení**

   Celkový počet souběžných připojení aktivních z klientů do aplikační brány
   
- **Jednotky odhadované fakturované kapacity**

  S skladovou položkou v2 je cenový model řízen spotřebou. Jednotky kapacity měří náklady založené na spotřebě, které jsou účtovány navíc k pevným nákladům. *Jednotky odhadované fakturované kapacity* udávají počet jednotek kapacity, pomocí kterých se fakturace odhaduje. Vypočítá se jako vyšší hodnota mezi *jednotkami aktuální kapacity* (jednotky kapacity potřebné k vyrovnávání zatížení provozu) a *jednotky s pevnou fakturovatelnou kapacitou* (jednotky minimální kapacity jsou zřízeny).

- **Neúspěšné požadavky**

  Počet požadavků, které aplikační brána obsluhovala s kódy chyb serveru 5xx. To zahrnuje kódy 5xx, které jsou generovány z aplikační brány, stejně jako kódy 5xx, které jsou generovány z back-endu. Počet požadavků lze dále filtrovat tak, aby zobrazoval počet za každou/konkrétní kombinaci nastavení back-endového fondu-http.
   
- **Jednotky s pevnou fakturovatelnou kapacitou**

  Minimální počet jednotek kapacity udržovaných zřízených podle nastavení *Minimální jednotky škálování* (jedna instance se překládá na 10 jednotek kapacity) v konfiguraci Aplikační brána.
   
 - **Nová připojení za sekundu**

   Průměrný počet nových připojení TCP za sekundu vytvořených z klientů do aplikační brány a z aplikační brány do back-endových členů.


- **Stav odpovědi**

   Stav odpovědi HTTP vrácený aplikací brány. Rozdělení kódu stavu odpovědi lze dále kategorizovat tak, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

   Počet bajtů za sekundu, které aplikační brána obsluhovala

- **Celkový počet požadavků**

   Počet úspěšných požadavků, které aplikace gateway obsluhovala. Počet požadavků lze dále filtrovat tak, aby zobrazoval počet za každou/konkrétní kombinaci nastavení back-endového fondu-http.

### <a name="backend-metrics"></a>Back-endové metriky

Pro aplikační bránu jsou k dispozici následující metriky:

- **Stav back-endové odpovědi**

  Počet stavových kódů odpovědí HTTP vrácených back-endy. To nezahrnuje žádné kódy odpovědí generované aplikační bránou. Rozdělení kódu stavu odpovědi lze dále kategorizovat tak, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Počet hostitelů v dobrém stavu**

  Počet backendů, které jsou určeny v pořádku sondou stavu. Můžete filtrovat na základě fondu back-end uzobrazit počet hostitelů v pořádku v určitém back-endfondu.

- **Počet hostitelů ve špatném stavu**

  Počet backendů, které jsou určeny není v pořádku sondou stavu. Můžete filtrovat na základě fondu back-end uzobrazit počet hostitelů není v pořádku v určitém back-endfondu.
  
- **Požadavky za minutu na zdravého hostitele**

  Průměrný počet požadavků přijatých každý člen v pořádku v back-endovém fondu za minutu. Je nutné zadat back-endový fond pomocí dimenze *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriky podporované soupoložkou SKU brány aplikace V1

### <a name="application-gateway-metrics"></a>Metriky aplikační brány

Pro aplikační bránu jsou k dispozici následující metriky:

- **Využití procesoru**

  Zobrazí využití procesorů přidělených aplikační bráně.  Za normálních podmínek by využití procesoru nemělo pravidelně přesáhnout 90 %, protože to může způsobit latenci na webech hostovaných za aplikační bránou a narušit prostředí klienta. Můžete nepřímo řídit nebo zlepšit využití procesoru úpravou konfigurace aplikační brány zvýšením počtu instancí nebo přesunutím na větší velikost skladové položky nebo provedením obojího.

- **Aktuální připojení**

  Počet aktuálních připojení navazujících pomocí aplikační brány

- **Neúspěšné požadavky**

  Počet požadavků, které aplikační brána obsluhovala s kódy chyb serveru 5xx. To zahrnuje kódy 5xx, které jsou generovány z aplikační brány, stejně jako kódy 5xx, které jsou generovány z back-endu. Počet požadavků lze dále filtrovat tak, aby zobrazoval počet za každou/konkrétní kombinaci nastavení back-endového fondu-http.

- **Stav odpovědi**

  Stav odpovědi HTTP vrácený aplikací brány. Rozdělení kódu stavu odpovědi lze dále kategorizovat tak, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

  Počet bajtů za sekundu, které aplikační brána obsluhovala

- **Celkový počet požadavků**

  Počet úspěšných požadavků, které aplikace gateway obsluhovala. Počet požadavků lze dále filtrovat tak, aby zobrazoval počet za každou/konkrétní kombinaci nastavení back-endového fondu-http.

- **Počet blokovaných požadavků brány firewall webové aplikace**
- **Distribuce blokovaných požadavků brány firewall webových aplikací**
- **Distribuce celkového pravidla brány firewall webových aplikací**

### <a name="backend-metrics"></a>Back-endové metriky

Pro aplikační bránu jsou k dispozici následující metriky:

- **Počet hostitelů v dobrém stavu**

  Počet backendů, které jsou určeny v pořádku sondou stavu. Můžete filtrovat na základě fondu back-end uzobrazit počet hostitelů v pořádku v určitém back-endfondu.

- **Počet hostitelů ve špatném stavu**

  Počet backendů, které jsou určeny není v pořádku sondou stavu. Můžete filtrovat na základě fondu back-end uzobrazit počet hostitelů není v pořádku v určitém back-endfondu.

## <a name="metrics-visualization"></a>Vizualizace metrik

Přejděte k aplikační bráně v části **Sledování vyberte** **Metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

Na následujícím obrázku se zobrazí příklad se třemi metrikami zobrazenými za posledních 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pokud chcete zobrazit aktuální seznam metrik, přečtěte si [téma Podporované metriky s Azure Monitorem](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Pravidla upozornění na metriky

Můžete spustit pravidla výstrah na základě metrik pro prostředek. Výstraha může například volat webhooku nebo e-mailem správce, pokud je propustnost aplikační brány nad, pod nebo na prahové hodnotě pro zadané období.

Následující příklad vás provede vytvořením pravidla výstrahy, které odešle e-mail správci poté, co propustnost překročí prahovou hodnotu:

1. vyberte **Přidat upozornění na metriku,** chcete-li otevřít stránku **Přidat pravidlo.** Na tuto stránku se můžete dostat také ze stránky metrik.

   ![Tlačítko Přidat upozornění na metriku][6]

2. Na stránce **Přidat pravidlo** vyplňte oddíly název, podmínka a upozornění a vyberte **OK**.

   * Ve voliči **podmínky** vyberte jednu ze čtyř hodnot: **Větší než**, Větší **nebo rovno**, **Menší než**nebo Menší **nebo rovno**.

   * Ve voliči **období** vyberte období od pěti minut do šesti hodin.

   * Pokud vyberete **vlastníky e-mailů, přispěvatele a čtenáře ,** může být e-mail dynamický na základě uživatelů, kteří mají k tomuto prostředku přístup. V opačném případě můžete do pole **Další e-maily správce** poskytnout seznam uživatelů oddělený chod čárek.

   ![Stránka Přidat pravidlo][7]

Pokud je prahová hodnota překročena, přijde e-mail, který je podobný e-mailu na následujícím obrázku:

![E-mail pro překročení prahové hodnoty][8]

Seznam výstrah se zobrazí po vytvoření upozornění metriky. Poskytuje přehled všech pravidel výstrah.

![Seznam záznamů a pravidel][9]

Další informace o oznámeních s výstrahami najdete v [tématu Příjem oznámení o výstražných upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Další informace o webhooky a jak je můžete používat s výstrahami, navštivte [stránku Konfigurace webhooku v upozornění na metriku Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Další kroky

* Vizualizujte čítač a protokoly událostí pomocí [protokolů Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte svůj protokol aktivit Azure pomocí](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) příspěvku blogu Power BI.
* [Zobrazte a analyzujte protokoly aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogové matné příspěvky.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
