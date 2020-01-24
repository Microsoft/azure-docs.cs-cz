---
title: Azure Monitor metriky pro Application Gateway
description: Naučte se používat metriky k monitorování výkonu služby Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 12759deb3e1775b5170d40cc609fe8c6226bf0d6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704574"
---
# <a name="metrics-for-application-gateway"></a>Metriky pro Application Gateway

Application Gateway zveřejňuje datové body označované jako metriky, aby se [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pro výkon instancí Application Gateway a back-endu. Tyto metriky jsou číselné hodnoty v seřazené sadě dat časových řad, které popisují nějaký aspekt aplikační brány v určitou dobu. Pokud jsou požadavky odesílány prostřednictvím Application Gateway, měří a odesílá své metriky v intervalech 60 – sekund. Pokud neexistují žádné požadavky na Application Gateway ani žádná data pro metriku, metrika není hlášena. Další informace najdete v tématu [Azure monitor metriky](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriky podporované Application Gatewaymi SKU verze 2

### <a name="timing-metrics"></a>Metriky časování

K dispozici jsou následující metriky související s časováním žádosti a odpovědi. Analýzou těchto metrik pro konkrétní naslouchací proces můžete určit, zda zpomalení aplikace v důsledku sítě WAN, Application Gateway, síti mezi Application Gateway a back-end aplikací nebo výkonem back-endu.

> [!NOTE]
>
> Pokud je v Application Gateway více než jeden naslouchací proces, vždy filtrovat podle dimenze *naslouchacího procesu* při porovnávání různých metrik latence, aby bylo možné získat smysluplnější odvození.

- **Čas připojení back-endu**

  Čas strávený navázáním spojení s back-end aplikací To zahrnuje latenci sítě a dobu, kterou zabere zásobník protokolu TCP back-end serveru k navázání nových připojení. V případě protokolu SSL zahrnuje i čas strávený na handshaki. 

- **Doba odezvy prvního bajtu back-endu**

  Časový interval mezi začátky navázání připojení k back-endu serveru a příjem prvního bajtu hlavičky odpovědi To odpovídá součtu *času připojení back-end* a doby odezvy back-end aplikace (čas, kdy server trvalo generování obsahu, potenciálně načtení databázových dotazů a zahájení přenosu odpovědi zpět na Application Gateway).

- **Doba odezvy posledního bajtu back-endu**

  Časový interval mezi začátky navázání připojení k back-endu serveru a příjem posledního bajtu těla odpovědi To odpovídá součtu celkové *doby odezvy back-endu* a času přenosu dat (Toto číslo se může značně lišit v závislosti na velikosti požadovaných objektů a latenci serverové sítě).

- **Celková doba aplikační brány**

  Průměrná doba, kterou trvá zpracování požadavku, a jeho odpověď k odeslání. To se počítá jako průměr z intervalu od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi, přibližně součet doby zpracování Application Gateway a *doby odezvy posledního bajtu back-endu* .

- **Čas odezvy klienta**

  Průměrná doba odezvy mezi klienty a Application Gateway. Tato metrika indikuje, jak dlouho trvá navázání připojení a vrácení potvrzení. 

Tyto metriky se dají použít k určení, jestli je způsobené zpomalení v důsledku Application Gateway, síťového a back-endu TCP zásobníku, výkonu aplikace back-endu nebo velikosti velkých souborů.
Pokud je například špička v první bajtové odezvě back-endu, ale čas připojení back-endu je konstantní, pak je možné odvodit, že brána Application Gateway na latenci v back-endu i čas potřebný k navázání připojení je stabilní a že špička je způsobena tím, že je v důsledku n narůstá doba odezvy back-end aplikace. Podobně platí, že pokud je špička v back-endu první bajtová doba odezvy přidružená k odpovídající špičkě v době připojení back-endu, je možné ji odvodit, protože síť nebo zásobník protokolu TCP na serveru se nastavily na hodnotu sytost. Pokud si všimnete špičky v době, kdy uplynula doba odezvy back-endu, ale doba odezvy prvního bajtu je konstantní, pak je největší špička z důvodu vyžádání většího souboru. Podobně platí, že pokud je celková doba služby Application Gateway mnohem větší než doba odezvy back-endu v bajtech, může to být znaménko kritického bodu výkonu na Application Gateway.



### <a name="application-gateway-metrics"></a>Application Gateway metriky

Pro Application Gateway jsou k dispozici následující metriky:

- **Přijaté bajty**

   Počet bajtů přijatých Application Gateway od klientů

- **Odeslané bajty**

   Počet bajtů odeslaných Application Gateway klientům

- **Protokol TLS klienta**

   Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil připojení k Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).

- **Aktuální jednotky kapacity**

   Počet spotřebovaných jednotek kapacity Jednotky kapacity měří náklady založené na spotřebě, které se účtují i s pevnými náklady. Existují tři determinanty jednotky kapacity a výpočetní jednotky, trvalá připojení a propustnost. Každá jednotka kapacity se skládá z maximálně: 1 výpočetní jednotka nebo 2500 trvalých připojení nebo propustnosti 2,22 MB/s.

- **Aktuální výpočetní jednotky**

   Počet spotřebovaných kapacit procesoru. Faktory ovlivňující výpočetní jednotku jsou připojení TLS/s, výpočty přepisu adresy URL a zpracování pravidel WAF. 

- **Aktuální připojení**

   Počet aktuálních připojení vytvořených pomocí Application Gateway

- **Neúspěšné žádosti**

   Počet neúspěšných žádostí, které Application Gateway obsluhovány Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.


- **Stav odpovědi**

   Application Gateway vrátil stav odpovědi HTTP. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

   Počet bajtů za sekundu, které Application Gateway zasloužily

- **Celkový počet požadavků**

   Počet úspěšných požadavků, které Application Gateway obsluhovány. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

- **Shodná pravidla firewallu webových aplikací**

- **Pravidla aktivované firewallem webových aplikací**

### <a name="backend-metrics"></a>Metriky back-endu

Pro Application Gateway jsou k dispozici následující metriky:

- **Stav odpovědi back-endu**

  Počet stavových kódů odpovědí HTTP vrácených back-endy. Nezahrnuje žádné kódy odpovědí vygenerované Application Gateway. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Počet hostitelů v pořádku**

  Počet back-endy, které jsou v pořádku zjištěny sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit v něm v pořádku/poškozené hostitele v konkrétním back-end fondu.

- **Počet hostitelů není v pořádku**

  Počet back-endy, které jsou zjištěny v nesprávném stavu sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak v konkrétním back-endu hostitele, kteří nejsou v pořádku.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriky podporované Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metriky

Pro Application Gateway jsou k dispozici následující metriky:

- **Využití procesoru**

  Zobrazuje využití procesorů přidělených Application Gateway.  Za normálních podmínek by využití CPU nemělo pravidelně překročit 90%, protože to může způsobit latenci na webech hostovaných za Application Gateway a přerušení prostředí klienta. Můžete nepřímo řídit nebo zdokonalovat využití procesoru změnou konfigurace Application Gateway zvýšením počtu instancí nebo přesunutím na větší velikost SKU nebo obojím.

- **Aktuální připojení**

  Počet aktuálních připojení vytvořených pomocí Application Gateway

- **Neúspěšné žádosti**

  Počet neúspěšných žádostí, které Application Gateway obsluhovány Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

- **Stav odpovědi**

  Application Gateway vrátil stav odpovědi HTTP. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

  Počet bajtů za sekundu, které Application Gateway zasloužily

- **Celkový počet požadavků**

  Počet úspěšných požadavků, které Application Gateway obsluhovány. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

- **Shodná pravidla firewallu webových aplikací**

- **Pravidla aktivované firewallem webových aplikací**

### <a name="backend-metrics"></a>Metriky back-endu

Pro Application Gateway jsou k dispozici následující metriky:

- **Počet hostitelů v pořádku**

  Počet back-endy, které jsou v pořádku zjištěny sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit v něm v pořádku/poškozené hostitele v konkrétním back-end fondu.

- **Počet hostitelů není v pořádku**

  Počet back-endy, které jsou zjištěny v nesprávném stavu sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak v konkrétním back-endu hostitele, kteří nejsou v pořádku.

## <a name="metrics-visualization"></a>Vizualizace metrik

Přejděte na aplikační bránu a v části **monitorování** vyberte **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

Na následujícím obrázku vidíte příklad se třemi metrikami zobrazenými za posledních 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pokud chcete zobrazit aktuální seznam metrik, přečtěte si téma [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Pravidla výstrah pro metriky

Můžete spustit pravidla upozornění založená na metrikách prostředku. Například výstraha může zavolat Webhook nebo poslat e-mailem správce, pokud je propustnost služby Application Gateway nad, níže nebo za stanovenou dobu.

Následující příklad vás provede vytvořením pravidla upozornění, které pošle e-mailem správci po porušení propustnosti prahovou hodnotu:

1. Vyberte **Přidat výstrahu metriky** a otevřete stránku **Přidat pravidlo** . Tuto stránku můžete také kontaktovat ze stránky metriky.

   ![Tlačítko Přidat upozornění na metriku][6]

2. Na stránce **Přidat pravidlo** vyplňte oddíly název, podmínka a Notify a vyberte **OK**.

   * V selektoru **podmínky** vyberte jednu ze čtyř hodnot: **větší než**, **větší než nebo rovno**, **menší**nebo **rovno nebo menší než**.

   * V selektoru **období** vyberte období od pěti minut po 6 hodin.

   * Pokud vyberete možnost **vlastníci, přispěvatelé a čtenáři e-mailu**, může být e-mail dynamický v závislosti na uživatelích, kteří k tomuto prostředku mají přístup. Jinak můžete v poli **Další e-mailové zprávy správce** zadat čárkami oddělený seznam uživatelů.

   ![Přidat stránku pravidla][7]

Pokud dojde k porušení prahové hodnoty, přijde e-mail podobný tomu na následujícím obrázku.

![E-mail pro porušení prahové hodnoty][8]

Po vytvoření výstrahy metriky se zobrazí seznam výstrah. Poskytuje přehled o všech pravidlech výstrah.

![Seznam výstrah a pravidel][9]

Další informace o oznámeních výstrah najdete v tématu [přijímání oznámení](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)o výstrahách.

Další informace o webhookech a o tom, jak je můžete používat s výstrahami, najdete [v tématu Konfigurace Webhooku na upozornění metriky Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Další kroky

* Vizualizujte protokoly čítačů a událostí pomocí [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte si protokol aktivit Azure pomocí Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) Blogový příspěvek.
* Umožňuje [Zobrazit a analyzovat protokoly aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) Blogový příspěvek.

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
