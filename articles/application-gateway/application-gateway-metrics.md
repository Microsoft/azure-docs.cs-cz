---
title: Azure Monitor metriky pro Application Gateway
description: Naučte se používat metriky k monitorování výkonu služby Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: absha
ms.openlocfilehash: ce349a0539986d88f689c53fc2099877df8030bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87424388"
---
# <a name="metrics-for-application-gateway"></a>Metriky pro Application Gateway

Application Gateway zveřejňuje datové body označované jako metriky, aby se [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pro výkon instancí Application Gateway a back-endu. Tyto metriky jsou číselné hodnoty v seřazené sadě dat časových řad, které popisují nějaký aspekt aplikační brány v určitou dobu. Pokud jsou požadavky odesílány prostřednictvím Application Gateway, měří a odesílá své metriky v intervalech 60 – sekund. Pokud neexistují žádné požadavky na Application Gateway ani žádná data pro metriku, metrika není hlášena. Další informace najdete v tématu [Azure monitor metriky](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriky podporované Application Gatewaymi SKU verze 2

### <a name="timing-metrics"></a>Metriky časování

Application Gateway poskytuje několik vestavěných metrik časování vztahujících se k žádosti a odpovědi, které se měří v milisekundách. 

![Diagram metrik časování pro Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Pokud je v Application Gateway více než jeden naslouchací proces, vždy filtrovat podle dimenze *naslouchacího procesu* při porovnávání různých metrik latence, aby bylo možné získat smysluplnější odvození.

- **Čas připojení back-endu**

  Čas strávený navázáním spojení s back-end aplikací. 

  To zahrnuje latenci sítě a dobu, kterou zabere zásobník protokolu TCP back-end serveru k navázání nových připojení. V případě protokolu TLS zahrnuje i čas strávený metodou handshake. 

- **Doba odezvy prvního bajtu back-endu**

  Časový interval mezi začátky navázání připojení k back-endu serveru a příjem prvního bajtu hlavičky odpovědi 

  To bude odpovídat součtu *času připojení back-endu*, času, který požadavek přijal pro přístup k back-endu z Application Gateway, což je čas, který aplikace back-end zavedla k tomu, aby reagovala na Application Gateway z back-endu.

- **Doba odezvy posledního bajtu back-endu**

  Časový interval mezi začátky navázání připojení k back-endu serveru a příjem posledního bajtu těla odpovědi 

  Je to přibližný součet *doby odezvy prvního bajtu z back-end* a doby přenosu dat (toto číslo se může výrazně lišit v závislosti na velikosti požadovaných objektů a latenci sítě serveru).

- **Celková doba aplikační brány**

  Průměrná doba, kterou trvá, než se požadavek přijme, zpracuje a pošle odpověď. 

  Toto je interval od času, kdy Application Gateway obdrží první bajt požadavku HTTP na čas, kdy byl klientovi odeslán poslední bajt odpovědi. To zahrnuje dobu zpracování trvání Application Gateway, *čas odezvy back-endu posledního bajtu*, čas potřebný Application Gateway k odeslání všech odpovědí a času *odezvy klienta*.

- **Čas odezvy klienta**

  Průměrná doba odezvy mezi klienty a Application Gateway.



Tyto metriky se dají použít k určení, jestli je pozorovaná zpomalení z důvodu klientské sítě, Application Gateway výkonu, back-end sítě a back-endu TCP zásobníku, výkonu aplikace back-endu nebo velikosti velkých souborů.

Například pokud dojde k špičkám ve trendu *prvního bajtu doby odezvy back-endu* , ale trend *času připojení back-* endu je stabilní, pak je možné odvodit, že brána Application Gateway na latenci back-endu a čas potřebný k navázání připojení je stabilní, a špička je způsobena nárůstem doby odezvy back-end aplikace. Na druhé straně platí, že pokud je špička v *back-endu doba odezvy prvního bajtu* přidružená k odpovídajícímu špičku v *době připojení back-endu*, je možné odvodit, že síť mezi Application Gateway a back-end serverem nebo zásobníkem protokolu TCP back-end serveru byla sytost. 

Pokud si všimnete špičky v *době, kdy uplynula doba odezvy back-endu* , ale *Doba odezvy prvního bajtu* je stabilní, je možné ji odvodit, protože se požaduje větší požadovaný soubor.

Podobně platí, že pokud má služba *Application Gateway celkový čas* špičky, ale *čas posledního bajtu back-endu* je stabilní, může to být buď znaménko kritického bodu výkonu na Application Gateway nebo kritické místo v síti mezi klientem a Application Gateway. Pokud navíc *klient RTT* má také odpovídající špičku, znamená to, že je degradace z důvodu sítě mezi klientem a Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway metriky

Pro Application Gateway jsou k dispozici následující metriky:

- **Přijaté bajty**

   Počet bajtů přijatých Application Gateway od klientů

- **Odeslané bajty**

   Počet bajtů odeslaných Application Gateway klientům

- **Protokol TLS klienta**

   Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil připojení k Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).

- **Aktuální jednotky kapacity**

   Počet jednotek kapacity spotřebovaný pro vyrovnávání zatížení provozu. Existují tři determinanty jednotky kapacity a výpočetní jednotky, trvalá připojení a propustnost. Každá jednotka kapacity se skládá z maximálně: 1 výpočetní jednotka nebo 2500 trvalých připojení nebo propustnosti 2,22 MB/s.

- **Aktuální výpočetní jednotky**

   Počet spotřebovaných kapacit procesoru. Faktory ovlivňujícími výpočetní jednotku jsou počet připojení TLS za sekundu, výpočty přepisu adres URL a zpracování pravidel WAF. 

- **Aktuální připojení**

   Celkový počet souběžných připojení aktivních od klientů k Application Gateway
   
- **Odhadované účtované jednotky kapacity**

  V případě SKU verze v2 je cenový model založený na spotřebě. Jednotky kapacity měří náklady na základě spotřeby, které se účtují nad rámec pevných nákladů. *Odhadované jednotky s rozpisem kapacity* označují počet jednotek kapacity, pomocí kterých je fakturace odhadovaná. Tato hodnota se vypočte jako větší z hodnot *aktuální jednotky kapacity* (jednotky kapacity nutné k vyrovnávání zatížení provozu) a *pevné fakturovatelné jednotky kapacity* (minimální počet zřízených jednotek kapacity).

- **Neúspěšné žádosti**

  Počet požadavků, které Application Gateway obsluhovány s kódy chyb 5xx serveru. To zahrnuje 5xx kódy, které jsou generovány z Application Gateway, a také kódy 5xx, které jsou generovány z back-endu. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.
   
- **Pevné fakturovatelné jednotky kapacity**

  Minimální počet zřízených jednotek kapacity podle nastavení *Minimální počet jednotek škálování* v konfiguraci služby Application Gateway (jedna instance znamená 10 jednotek kapacity).
   
 - **Nová připojení za sekundu**

   Průměrný počet nových připojení TCP za sekundu zavedených z klientů na Application Gateway a od Application Gateway ke členům back-endu.


- **Stav odpovědi**

   Application Gateway vrátil stav odpovědi HTTP. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

   Počet bajtů za sekundu, které Application Gateway zasloužily

- **Celkový počet požadavků**

   Počet úspěšných požadavků, které Application Gateway obsluhovány. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

### <a name="backend-metrics"></a>Metriky back-endu

Pro Application Gateway jsou k dispozici následující metriky:

- **Stav odpovědi back-endu**

  Počet stavových kódů odpovědí HTTP vrácených back-endy. Nezahrnuje žádné kódy odpovědí vygenerované Application Gateway. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Počet hostitelů v dobrém stavu**

  Počet back-endy, které jsou v pořádku zjištěny sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak počet nefunkčních hostitelů v konkrétním back-end fondu.

- **Počet hostitelů ve špatném stavu**

  Počet back-endy, které jsou zjištěny v nesprávném stavu sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak počet nezdravých hostitelů v konkrétním back-end fondu.
  
- **Počet požadavků za minutu na hostitele v pořádku**

  Průměrný počet žádostí přijatých každým zdravým členem v back-endu fondu za minutu. Je nutné zadat fond back-endu pomocí dimenze *problémových bezproblémových* .  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metriky podporované Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metriky

Pro Application Gateway jsou k dispozici následující metriky:

- **Využití procesoru**

  Zobrazuje využití procesorů přidělených službě Application Gateway.  Za normálních podmínek by využití procesorů nemělo pravidelně překračovat 90 %, protože to může způsobit latenci na webech hostovaných za službou Application Gateway a narušení klientského prostředí. Využití procesoru můžete řídit nebo vylepšit nepřímo úpravou konfigurace služby Application Gateway, a to zvýšením počtu instancí, přechodem na větší velikost SKU nebo obojím najednou.

- **Aktuální připojení**

  Počet aktuálních připojení vytvořených pomocí Application Gateway

- **Neúspěšné žádosti**

  Počet požadavků, které Application Gateway obsluhovány s kódy chyb 5xx serveru. To zahrnuje 5xx kódy, které jsou generovány z Application Gateway, a také kódy 5xx, které jsou generovány z back-endu. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

- **Stav odpovědi**

  Application Gateway vrátil stav odpovědi HTTP. Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**

  Počet bajtů za sekundu, které Application Gateway zasloužily

- **Celkový počet požadavků**

  Počet úspěšných požadavků, které Application Gateway obsluhovány. Počet požadavků může být dále filtrován tak, aby zobrazoval počet pro každý nebo konkrétní back-end fond – kombinace nastavení http.

- **Počet blokovaných požadavků firewallu webových aplikací**
- **Distribuce blokovaných požadavků firewallu webových aplikací**
- **Distribuce všech pravidel v firewallu webových aplikací**

### <a name="backend-metrics"></a>Metriky back-endu

Pro Application Gateway jsou k dispozici následující metriky:

- **Počet hostitelů v dobrém stavu**

  Počet back-endy, které jsou v pořádku zjištěny sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak počet nefunkčních hostitelů v konkrétním back-end fondu.

- **Počet hostitelů ve špatném stavu**

  Počet back-endy, které jsou zjištěny v nesprávném stavu sondou stavu. Můžete filtrovat podle fondu back-endu a zobrazit tak počet nezdravých hostitelů v konkrétním back-end fondu.

## <a name="metrics-visualization"></a>Vizualizace metrik

Přejděte na aplikační bránu a v části **monitorování** vyberte **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

Na následujícím obrázku vidíte příklad se třemi metrikami zobrazenými za posledních 30 minut:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Zobrazení metriky." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

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
* [Vizualizujte si protokol aktivit Azure pomocí Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) Blogový příspěvek.
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
