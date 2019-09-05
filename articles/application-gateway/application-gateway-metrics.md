---
title: Azure Monitor metriky pro Application Gateway
description: Naučte se používat metriky k monitorování výkonu služby Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309814"
---
# <a name="metrics-for-application-gateway"></a>Metriky pro Application Gateway

Application Gateway zveřejňuje datové body označované jako metriky, aby se [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pro výkon instancí Application Gateway a back-endu. Tyto metriky jsou číselné hodnoty v seřazené sadě dat časových řad, které popisují nějaký aspekt aplikační brány v určitou dobu. Pokud jsou požadavky odesílány prostřednictvím Application Gateway, měří a odesílá své metriky v intervalech 60 – sekund. Pokud neexistují žádné požadavky na Application Gateway ani žádná data pro metriku, metrika není hlášena. Další informace najdete v tématu [Azure monitor metriky](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metriky podporované Application Gatewaymi SKU verze 2

### <a name="timing-metrics"></a>Metriky časování

K dispozici jsou následující metriky související s časováním žádosti a odpovědi. Analýzou těchto metrik můžete určit, jestli zpomalení aplikace v důsledku sítě WAN, Application Gateway, síť mezi Application Gateway a back-endu nebo výkon aplikace.

- **Čas odezvy klienta**

  Průměrná doba odezvy mezi klienty a Application Gateway. Tato metrika indikuje, jak dlouho trvá navázání připojení a vrácení potvrzení.

- **Celková doba aplikační brány**

  Průměrná doba, kterou trvá zpracování požadavku, a jeho odpověď k odeslání. Počítá se jako průměr intervalu od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že to obvykle zahrnuje dobu zpracování Application Gateway, čas, po který jsou pakety požadavků a odpovědí přenášeny přes síť, a čas, kdy server back-end trvala odpověď.

- **Čas připojení back-endu**

  Čas strávený navázáním spojení se serverem back-end. 

- **Doba odezvy prvního bajtu back-endu**

  Časový interval mezi zahájením navázání připojení k back-endu serveru a přijetí prvního bajtu hlavičky odpovědi, odhad doby zpracování back-endu serveru

- **Doba odezvy posledního bajtu back-endu**

  Časový interval mezi zahájením navázání připojení k back-endu serveru a příjem posledního bajtu textu odpovědi

### <a name="application-gateway-metrics"></a>Application Gateway metriky

Pro Application Gateway jsou k dispozici následující metriky:

- **Přijaté bajty**

   Počet bajtů přijatých Application Gateway od klientů

- **Odeslané bajty**

   Počet bajtů odeslaných Application Gateway klientům

- **Protokol TLS klienta**

   Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil připojení k Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).

- **Aktuální jednotky kapacity**

   Počet spotřebovaných jednotek kapacity Jednotky kapacity měří náklady založené na spotřebě, které se účtují i s pevnými náklady. Existují tři determinanty jednotky kapacity a výpočetní jednotky, trvalá připojení a propustnost. Každou jednotku kapacity tvoří nejvýše: 1 výpočetní jednotka, nebo 2500 trvalá připojení, nebo propustnost 2,22-MB/s.

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

[![](media/application-gateway-diagnostics/figure5.png "Zobrazení metriky")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pokud chcete zobrazit aktuální seznam metrik, přečtěte si téma [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Pravidla výstrah pro metriky

Můžete spustit pravidla upozornění založená na metrikách prostředku. Například výstraha může zavolat Webhook nebo poslat e-mailem správce, pokud je propustnost služby Application Gateway nad, níže nebo za stanovenou dobu.

Následující příklad vás provede vytvořením pravidla upozornění, které pošle e-mailem správci po porušení propustnosti prahovou hodnotu:

1. Vyberte **Přidat výstrahu metriky** a otevřete stránku **Přidat pravidlo** . Tuto stránku můžete také kontaktovat ze stránky metriky.

   ![Tlačítko Přidat upozornění na metriku][6]

2. Na stránce **Přidat pravidlo** vyplňte oddíly název, podmínka a Notify a vyberte **OK**.

   * V selektoru **podmínky** vyberte jednu ze čtyř hodnot: **Větší než**, **větší než nebo rovno**, **menší**nebo **rovno nebo menší než nebo rovno**.

   * V selektoru **období** vyberte období od pěti minut po 6 hodin.

   * Pokud vyberete možnost **vlastníci, přispěvatelé a čtenáři e-mailu**, může být e-mail dynamický v závislosti na uživatelích, kteří k tomuto prostředku mají přístup. Jinak můžete v poli **Další e-mailové zprávy správce** zadat čárkami oddělený seznam uživatelů.

   ![Přidat stránku pravidla][7]

Pokud dojde k porušení prahové hodnoty, přijde e-mail podobný tomu na následujícím obrázku.

![E-mail pro porušení prahové hodnoty][8]

Po vytvoření výstrahy metriky se zobrazí seznam výstrah. Poskytuje přehled o všech pravidlech výstrah.

![Seznam výstrah a pravidel][9]

Další informace o oznámeních výstrah najdete v tématu [přijímání oznámení](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)o výstrahách.

Další informace o webhookech a o tom, jak je můžete používat s výstrahami, najdete [v tématu Konfigurace Webhooku na upozornění metriky Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Další postup

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
