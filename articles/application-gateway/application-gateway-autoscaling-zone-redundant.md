---
title: Automatické škálování a zónově redundantní Application Gateway v Azure
description: Tento článek představuje Standard_v2 aplikace Azure a SKU WAF_v2, který obsahuje funkce automatického škálování a zónově redundantní.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/22/2019
ms.author: victorh
ms.openlocfilehash: 8e17c5e34ec3e2397c3054b1d0e0d97dbf410db2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986872"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Automatické škálování a zónově redundantní služba Application Gateway 

Služba Application Gateway a Firewall webových aplikací (WAF) jsou dostupné v rámci Standard_v2 a WAF_v2 SKU. V2 SKU nabízí vylepšení výkonu a přidává podporu pro důležité nové funkce jako podporu pro virtuální IP adresy statické, automatické škálování a redundanci zón. Existující funkce v části Standard a WAF SKU nadále podporovány v rámci nové SKU v2 s několika výjimkami, které jsou uvedeny v [porovnání](#differences-with-v1-sku) oddílu.

Nové SKU v2 zahrnuje následující vylepšení:

- **Automatické škálování**: Služba Application Gateway nebo WAF nasazení na základě automatického škálování SKU můžete vertikálně navýšit kapacitu nebo dolů podle aktuálních vzory zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Tato skladová jednotka nabízí true elasticitu. Standard_v2 a WAF_v2 SKU Application Gateway může pracovat v pevnou kapacitu (automatické škálování, zakázané) a v režimu s povoleným automatickým Škálováním. Režim pevné kapacity je užitečné pro scénáře s konzistentní a předvídatelné zatížení. Režim automatického škálování je výhodné v aplikacích, které najdete v článku odchylky v provozu aplikací.
- **Zóna redundance**: Application Gateway nebo nasazení WAF může zahrnovat více zón dostupnosti, není potřeba zřizovat samostatných instancí Application Gateway v každé zóně s Traffic Managerem. Můžete zónu jednoho nebo několika zónami, ve které jsou nasazené instance aplikační brány, díky tomu odolnější vůči selhání zóny. Fond back-endu pro aplikace můžete podobně distribuované napříč zónami dostupnosti.

  Redundanci zón je k dispozici, pouze pokud Azure nejsou k dispozici zóny. V jiných oblastech jsou podporovány všechny ostatní funkce. Další informace najdete v tématu [co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Virtuální IP adresy statické**: Application gateway v2 skladová položka podporuje statické virtuální IP adresy zadejte výhradně. Tím se zajistí, že nedojde ke změně virtuální IP adresy přidružené k službě application gateway pro životní cyklus nasazení, i po restartování počítače.
- **Přepište záhlaví**: Služba Application Gateway umožňuje přidat, odebrat nebo aktualizovat hlavičky požadavku a odpovědi protokolu HTTP s v2 SKU. Další informace najdete v tématu [hlavičky HTTP přepsat pomocí služby Application Gateway](rewrite-http-headers.md)
- **Integrace se službou Key Vault (preview)**: Application Gateway v2 podporuje integraci se službou Key Vault (ve verzi public preview) pro certifikáty serveru, které jsou připojeny k naslouchací procesy HTTPS povolené. Další informace najdete v tématu [ukončení protokolu SSL s využitím služby Key Vault certifikátů](key-vault-certs.md).
- **Azure Kubernetes Service příchozího přenosu dat kontroler (preview)**: Kontroler příchozího přenosu dat v2 Application Gateway umožňuje Azure Application Gateway má být použit jako příchozího přenosu dat pro Azure Kubernetes Service (AKS) označuje jako clusteru AKS. Další informace najdete v tématu [stránky dokumentace](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Vylepšení výkonu**: V2, které skladová jednotka nabízí až 5 X lepší SSL snižování zátěže výkon ve srovnání se Standard/WAF SKU.
- **Rychlejší nasazení a aktualizace** v2 SKU zajišťuje rychlejší nasazení a aktualizace čas Standard/WAF SKU. To zahrnuje také změny konfigurace WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Podporované oblasti

Standard_v2 a WAF_v2 SKU je k dispozici v následujících oblastech: USA (střed) – sever, střední část jihu USA, USA – Západ, USA – západ 2, USA – východ, USA – východ 2, střed USA, Severní Evropa, západní Evropa, jihovýchodní Asie, Francie – střed, Velká Británie – Západ, Japonsko – východ, Japonsko – západ. V budoucnu přibudou další oblasti.

## <a name="pricing"></a>Ceny

Cenový model s v2 SKU doprovází využití a je již připojen k instanci počtu nebo velikosti. Ceny SKU v2 má dvě součásti:

- **Pevná cena** – Toto je po hodinách (nebo hodiny) cenu za zřízení Standard_v2 nebo WAF_v2 brány.
- **Cena ze jednotku kapacity** – to je založené na spotřebě náklady, které se účtuje pevný náklady. Poplatek za jednotku kapacity se také počítá hodinové nebo jeho část každou hodinu. Existují tři dimenze na kapacitní jednotky – výpočetní jednotku, trvalé připojení a propustnosti. Výpočetní jednotka je míra využité kapacity procesoru. Faktory ovlivňující výpočetní jednotka jsou TLS připojení za sekundu, přepisování adres URL výpočty a zpracování pravidla WAF. Trvalé připojení je míra navázané připojení TCP ke službě application gateway v daném fakturačním intervalu. Propustnost je průměrná megabity/sec zpracovaných v daném fakturačním intervalu.

Každá Kapacitní jednotka se skládá z nejvíce: 1 výpočetní jednotku, nebo 2 500 trvalá připojení nebo propustnost 2.22 MB/s.

Výpočetní jednotka pokyny:

- **Standard_v2** – každá výpočetní jednotka je schopen přibližně 50 připojení za sekundu s certifikátem TLS klíče RSA 2048 bitů.
- **WAF_v2** – každá výpočetní jednotka podporuje přibližně 10 souběžných požadavků za sekundu pro 70-30 % kombinaci provozu s využitím 70 % požadavků menší než 2 KB GET/POST a zbývající vyšší. WAF výkon není ovlivněn velikost odpovědi aktuálně.

> [!NOTE]
> Každá instance může podporovat aktuálně přibližně 10 kapacitních jednotek.
> Počet požadavků, které dokáže zpracovat výpočetní jednotka závisí na různých kritérií, jako je velikost klíče certifikátu TLS, pokud chcete algoritmus výměny klíčů, záhlaví přepisů a v případě WAF příchozí žádosti o velikosti. Doporučujeme že provádět testy aplikace k určení frekvence požadavků na výpočetní jednotku. Jednotka kapacity a výpočetní jednotka bude k dispozici jako metriku před fakturace začíná.

**Ceny v USA – východ**:

|              Název skladové položky                             | Pevná cena ($/ hod)  | Cena ze jednotku kapacity ($/ CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Informace o cenách najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/application-gateway/). Fakturace je naplánováno spuštění na 1. července 2019.

**Příklad 1**

Application Gateway Standard_v2 je zřízený bez automatického škálování v režimu ručního škálování s pevnou kapacitu pět instancí.

Pevná cena = 744(hours) * $0.20 = $148.8 <br>
Kapacitní jednotky = 744 jednotku kapacity (hodiny) 10 za instanci * pět instancí * 0.008 $ za hodinu jednotky kapacity = $297.6

Celková cena = 148.8 $ + $297.6 = $446.4

**Příklad 2**

Zřízení Application Gateway standard_v2 po dobu jednoho měsíce a během této doby přijme 25 nová SSL připojení/s, průměrná přenosu dat 8.88 MB/s. Za předpokladu, že připojení jsou krátké životnost, by měl ceny:

Pevná cena = 744(hours) * $0.20 = $148.8

Cena za jednotku kapacity = 744(hours) * Max (25/50 výpočetních jednotek pro připojení za sekundu, 8.88/2.22 Kapacitní jednotka propustnosti) * $0.008 = 744 * 4 * 0.008 = $23.81

Celková cena = $148. 23.81 8 + = $172.61

**Příklad 3**

Application Gateway WAF_v2 se zřizuje po dobu jednoho měsíce. Během této doby přijme 25 nová SSL připojení/s, průměrná přenosu dat 8.88 MB/s a nemá 80 požadavku za sekundu. Za předpokladu, že připojení jsou krátké žít a, podporuje výpočetní jednotka výpočtu pro aplikaci za výpočetní jednotka 10 RPS, vaše cena by byla:

Pevná cena = 744(hours) * $0.36 = $267.84

Cena za jednotku kapacity = 744(hours) * Max (výpočetní jednotky Max(25/50 for connections/sec, 80/10 WAF RPS), 8.88/2.22 Kapacitní jednotka propustnosti) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Celková cena = 267.84 $ + $85.71 = $353.55

## <a name="scaling-application-gateway-and-waf-v2"></a>Škálování Application Gateway a WAF v2

Waf služby Application Gateway a dá se škálovat ve dvou režimech:

- **Automatické škálování** – povolení automatického škálování Application Gateway a WAF v2 SKU vertikálně navyšovat nebo snižovat podle požadavků na provoz aplikace. Tento režim nabízí lepší pružnost do vaší aplikace a eliminuje nutnost odhadnout počet velikost nebo instance brány aplikací. Tento režim vám také umožní snížit náklady tím, které nevyžadují spuštění brány v ve špičce zřízené kapacity pro očekávané maximální zatížení. Zákazníkům musíte zadat minimální a volitelně maximálním počtem instancí. Minimální kapacitu zajistí, že služba Application Gateway a WAF v2 neklesne pod minimální počet instancí zadali, dokonce i v případě neexistence provoz. Bude se účtovat tuto minimální kapacitu i v případě neexistence veškerý provoz. Také v případě potřeby můžete zadat maximální počet instancí, které zajišťuje, že službu Application Gateway nemá škálování za zadaný počet instancí. Bude nadále účtovat množství přenos poskytovaný službou Gateway. Počet instancí musí být v rozsahu 0 až 125. Výchozí hodnota pro maximální počet instancí je 20, pokud není zadaný.
- **Ruční** -také další možnost – Ruční režim, ve kterém je brána nebude automatického škálování. V tomto režimu dojde větší provoz, než co Application Gateway nebo WAF může zpracovat, může to vést ke ztrátě provoz. S ruční režim určující počet instancí je povinný. Počet instancí se může lišit od 1 do 125 instancí.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porovnání funkcí mezi v1 SKU a v2 SKU

Následující tabulka porovnává funkce, které jsou dostupné v jednotlivých SKU.

|                                                   | V1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatické škálování                                       |          | &#x2713; |
| Redundanci zón                                   |          | &#x2713; |
| Statické virtuálních IP adres                                        |          | &#x2713; |
| Azure kontroler příchozího přenosu dat Kubernetes Service (AKS) |          | &#x2713; |
| Integrace se službou Azure Key Vault                       |          | &#x2713; |
| Přepsání hlavičky protokolu HTTP (S)                           |          | &#x2713; |
| Směrování na základě adresy URL                                 | &#x2713; | &#x2713; |
| Hostování několika webů                             | &#x2713; | &#x2713; |
| Přesměrování přenosů                               | &#x2713; | &#x2713; |
| Firewall webových aplikací (WAF)                    | &#x2713; | &#x2713; |
| Ukončení protokolu SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Šifrování SSL začátku do konce                         | &#x2713; | &#x2713; |
| Spřažení relací                                  | &#x2713; | &#x2713; |
| Vlastní chybové stránky                                | &#x2713; | &#x2713; |
| Podpora protokolu WebSocket                                 | &#x2713; | &#x2713; |
| Podpora HTTP/2                                    | &#x2713; | &#x2713; |
| Vyprazdňování připojení                               | &#x2713; | &#x2713; |

> [!NOTE]
> Automatické škálování v2 teď podporuje SKU [sondy stavu výchozí](application-gateway-probe-overview.md#default-health-probe) automaticky monitorování stavu všech prostředků v jeho back endovém fondu a zvýraznit těchto členů back-end, které jsou považovány za není v pořádku. Výchozí kontroly stavu je automaticky konfigurována pro back-endů, které nemají žádnou konfiguraci vlastní test paměti. Další informace najdete v tématu [sondy stavu ve službě application gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Rozdíly mezi v1 SKU

|Rozdíl|Podrobnosti|
|--|--|
|Certifikát pro ověřování|Nepodporuje se.<br>Další informace najdete v tématu [přehled koncového šifrování protokolu SSL pomocí Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Kombinování Standard_v2 a standardní Application Gateway ve stejné podsíti|Nepodporuje se|
|Uživatelem definovanou trasou (UDR) na podsítě Application Gateway.|Nepodporuje se|
|Skupina NSG pro rozsah portu pro příchozí spojení| -65200 až 65535. pro Standard_v2 SKU<br>-65503 až 65534 pro standardní SKU.<br>Další informace najdete v tématu [nejčastější dotazy k](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Protokoly výkonu v diagnostice Azure|Nepodporuje se.<br>Metriky Azure je třeba použít.|
|Fakturace|Fakturace naplánováno ke spuštění na 1. července 2019.|
|Režim FIPS|Ty nejsou aktuálně podporovány.|
|Režim pouze ILB|To není aktuálně podporováno. Veřejné a režim ILB společně se nepodporuje.|
|Integrace sledování sítě|Nepodporuje se.|
|Integrace Azure Support Center|Zatím není k dispozici.

## <a name="next-steps"></a>Další postup

- [Rychlé zprovoznění: Přímé webového provozu s využitím Azure Application Gateway – Azure portal](quick-create-portal.md)
- [Vytvoření automatickým Škálováním, zóna redundantní služba application gateway s vyhrazenou virtuální IP adresu pomocí Azure Powershellu](tutorial-autoscale-ps.md)
- Další informace o [Application Gateway](overview.md).
- Další informace o [Brána Firewall služby Azure](../firewall/overview.md).