---
title: Automatické škálování a zónově redundantní služby Application Gateway v2
description: Tento článek představuje azure aplikační Standard_v2 a WAF_v2 skladové položky, která zahrnuje funkce automatickéškálování a zóna redundantní.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 4cd2969f9a56c96af2b2c6db216f6829a080260c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371282"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatické škálování a zónově redundantní služby Application Gateway v2 

Brána aplikace a brána firewall webových aplikací (WAF) jsou také k dispozici pod Standard_v2 a WAF_v2 skladovou položkou. Skladová položka v2 nabízí vylepšení výkonu a přidává podporu pro důležité nové funkce, jako je automatické škálování, redundance zón a podpora statických VIRTUÁLNÍCH IP. Existující funkce v rámci standardní a WAF Skladové položky nadále podporovány v nové v2 Skladové položky, s několika výjimkami uvedenými v části [porovnání.](#differences-with-v1-sku)

Nová skladová položka v2 obsahuje následující vylepšení:

- **Automatické škálování**: Nasazení aplikační brány nebo WAF v rámci skladové položky automatického škálování lze vertikálně navýšit nebo snížit kapacitu na základě změny vzorů zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Tato skladová položka nabízí skutečnou pružnost. Ve Standard_v2 a WAF_v2 skladové položky může aplikační brána pracovat jak v pevné kapacitě (automatické škálování zakázáno), tak v režimu s povoleným automatickým škálováním. Režim s pevnou kapacitou je užitečný pro scénáře s konzistentními a předvídatelnými úlohami. Režim automatického škálování je výhodný v aplikacích, které vidí odchylku v provozu aplikací.
- **Redundance zóny**: Nasazení aplikační brány nebo WAF může přesahovat více zón dostupnosti, což odebírá potřebu zřídit samostatné instance aplikační brány v každé zóně pomocí Traffic Manageru. Můžete zvolit jednu zónu nebo více zón, kde jsou nasazeny instance aplikační brány, což je odolnější vůči selhání zóny. Back-endový fond pro aplikace může být podobně distribuován mezi zónami dostupnosti.

  Redundance zóny je k dispozici jenom tam, kde jsou k dispozici zóny Azure. V jiných oblastech jsou podporovány všechny ostatní funkce. Další informace najdete v tématu [Co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Static VIP**: Aplikační brána v2 SKU podporuje výhradně statický typ VIP. Tím je zajištěno, že virtuální ip adresy přidružené k bráně aplikace nezmění pro životní cyklus nasazení, a to i po restartování.  Ve v1 není statická virtuální ip adresa, takže místo IP adresy pro směrování názvu domény do app services přes aplikační bránu je nutné použít adresu URL brány aplikace namísto IP adresy pro směrování názvu domény do služby App Services.
- **Přepsání záhlaví**: Aplikační brána umožňuje přidávat, odebírat nebo aktualizovat hlavičky požadavků a odpovědí HTTP pomocí sku sku v2. Další informace naleznete [v tématu Přepis záhlaví PROTOKOLU HTTP pomocí aplikace Gateway](rewrite-http-headers.md)
- **Integrace trezoru klíčů**: Aplikační brána v2 podporuje integraci s trezorem klíčů pro serverové certifikáty, které jsou připojeny k naslouchacím líným a listenerům s povoleným protokolem HTTPS. Další informace naleznete v [tématu SSL ukončení s certifikáty trezoru klíčů](key-vault-certs.md).
- **Řadič příchozího přenosu dat služby Azure Kubernetes:** Řadič příchozího přenosu dat služby Application Gateway v2 umožňuje, aby se aplikační brána Azure používala jako příchozí přenos dat pro službu Azure Kubernetes Service (AKS) známou jako Cluster AKS. Další informace naleznete v tématu [Co je řadič příchozího přenosu dat brány aplikace?](ingress-controller-overview.md).
- **Vylepšení výkonu**: Skladová položka v2 nabízí až 5x lepší výkon ssl zátěže ve srovnání se standardní/WAF SKU.
- **Rychlejší nasazení a aktualizace** Skladová položka v2 poskytuje rychlejší nasazení a čas aktualizace ve srovnání se standardní/WAF SKU. To zahrnuje také změny konfigurace WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Podporované oblasti

SKU Standard_v2 a WAF_v2 je k dispozici v následujících oblastech: Severní střed USA, Jižní Střed USA, Západní USA, Západní USA 2, Východní USA, Východní USA 2, Střední USA, Severní Evropa, Západní Evropa, Jihovýchodní Asie, Francie – střed, Velká Británie – západ, Japonsko – východ, Japonsko – západ, Austrálie – východ , Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Východní Asie, Korea – střed, Korea – jih, Spojené království – jih, Střední Indie, Západní Indie, Jižní Indie.

## <a name="pricing"></a>Ceny

S skladovou položkou v2 je cenový model řízen spotřebou a již není připojen k počtu nebo velikostem instancí. Ceny skladové položky v2 mají dvě součásti:

- **Pevná cena** - Jedná se o hodinovou (nebo částečnou hodinovou) cenu pro zřízení Standard_v2 nebo WAF_v2 gateway. Vezměte prosím na vědomí, že 0 dalších minimálních instancí stále zajišťuje vysokou dostupnost služby, která je vždy zahrnuta s pevnou cenou.
- **Kapacita Jednotková cena** – Jedná se o náklady založené na spotřebě, které se účtují navíc k pevným nákladům. Poplatek za jednotku kapacity se také počítá každou hodinu nebo částečnou každou hodinu. Jednotky kapacity mají tři dimenze – výpočetní jednotku, trvalá připojení a propustnost. Výpočetní jednotka je míra spotřebované kapacity procesoru. Faktory ovlivňující výpočetní jednotku jsou připojení TLS/s, výpočty přepisování adres URL a zpracování pravidel WAF. Trvalé připojení je míra navázání připojení TCP k bráně aplikace v daném intervalu fakturace. Propustnost je průměrná megabity/s zpracované systémem v daném fakturačním intervalu.  Fakturace se provádí na úrovni jednotky kapacity pro cokoli nad rezervovaný počet instancí.

Každá jednotka kapacity se skládá maximálně z: 1 výpočetní jednotky nebo 2500 trvalých připojení nebo propustnosti 2,22 Mb/s.

Vedení výpočetní jednotky:

- **Standard_v2** – Každá výpočetní jednotka je schopna přibližně 50 připojení za sekundu s certifikátem TLS s rsa 2048bitovým klíčem.
- **WAF_v2** – každá výpočetní jednotka může podporovat přibližně 10 souběžných požadavků za sekundu pro 70-30% kombinaci provozu se 70% požadavky menšími než 2 KB GET/POST a zůstávají vyšší. Výkon WAF není aktuálně ovlivněn velikostí odpovědi.

> [!NOTE]
> Každá instance může aktuálně podporovat přibližně 10 kapacitních jednotek.
> Počet požadavků, které může výpočetní jednotka zpracovat, závisí na různých kritériích, jako je velikost klíče certifikátu TLS, algoritmus výměny klíčů, přepsání záhlaví a v případě velikosti příchozího požadavku WAF. Doporučujeme provést testy aplikací k určení rychlosti požadavků na výpočetní jednotku. Jednotka kapacity i výpočetní jednotka budou k dispozici jako metrika před zahájením fakturace.

V následující tabulce jsou uvedeny příklady cen a jsou pouze pro ilustrační účely.

**Ceny v USA na východ :**

|              Název skladové položky                             | Pevná cena ($/hod.)  | Kapacita Jednotková cena ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Další informace o cenách naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Příklad 1**

Aplikační brána Standard_v2 je zřízena bez automatického škálování v režimu ručníškálování s pevnou kapacitou pěti instancí.

Pevná cena = 744(hodin) * $0.20 = $148.8 <br>
Jednotky kapacity = 744 (hodiny) * 10 kapacitní jednotka na instanci * 5 instancí * $0.008 za jednotku kapacity hodin = $297.6

Celková cena = $148.8 + $297.6 = $446.4

**Příklad 2**

Aplikační brána standard_v2 je zřízena po dobu jednoho měsíce s nulovými minimálními instancemi a během této doby obdrží 25 nových připojení SSL za sekundu, průměr přenosu dat 8,88 Mb/s. Za předpokladu, že spojení je krátkodobé, vaše cena by byla:

Pevná cena = 744(hodin) * $0.20 = $148.8

Jednotková cena kapacity = 744(hodin) * Max (výpočetní jednotka 25/50 pro připojení/s, 8,88/2,22 kapacitní jednotky pro propustnost) * $0.008 = 744 * 4 * 0.008 = $23.81

Celková cena = $148.8+23.81 = $172.61

Jak můžete vidět, fakturuje se vám pouze čtyři jednotky kapacity, nikoli pro celou instanci. 

> [!NOTE]
> Funkce Max vrátí největší hodnotu v páru hodnot.


**Příklad 3**

Brána aplikace standard_v2 je zřízena po dobu jednoho měsíce, s minimálně pěti instancemi. Za předpokladu, že neexistuje žádný provoz a spojení jsou krátkodobé, vaše cena by byla:

Pevná cena = 744(hodin) * $0.20 = $148.8

Jednotková cena kapacity = 744(hodin) * Max (0/50 výpočetní jednotka pro připojení/s, 0/2,22 kapacitní jednotka pro propustnost) * $0.008 = 744 * 50 * 0.008 = $297.60

Celková cena = $148.80+297.60 = $446.4

V tomto případě se vám účtuje celých pět instancí, i když není žádný provoz.

**Příklad 4**

Aplikační brána standard_v2 je zřízena po dobu jednoho měsíce, s minimálně pět instancí, ale tentokrát je v průměru 125 mb/s přenos dat a 25 SSL připojení za sekundu. Za předpokladu, že neexistuje žádný provoz a spojení jsou krátkodobé, vaše cena by byla:

Pevná cena = 744(hodin) * $0.20 = $148.8

Jednotková cena kapacity = 744(hodin) * Max (výpočetní jednotka 25/50 pro připojení/s, 125/2,22 kapacitní jednotka pro propustnost) * $0.008 = 744 * 57 * 0.008 = $339.26

Celková cena = $148.80+339.26 = $488.06

V takovém případě se vám bude účtovat celých pět instancí plus sedm jednotek kapacity (což je 7/10 instance).  

**Příklad 5**

WAF_v2 brány aplikace se zřizováno po dobu jednoho měsíce. Během této doby obdrží 25 nových připojení SSL za s, průměr 8.88-Mbps přenos dat a dělá 80 žádost za sekundu. Za předpokladu, že připojení jsou krátkodobé a výpočet výpočetní jednotky pro aplikaci podporuje 10 RPS na výpočetní jednotku, vaše cena by byla:

Pevná cena = 744(hodin) * $0.36 = $267.84

Jednotková cena kapacity = 744(hodin) * Max (výpočetní jednotka Max (25/50 pro připojení/s, 80/10 WAF RPS), 8,88/2,22 kapacitní jednotky pro propustnost) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Celková cena = $267.84 + $85.71 = $353.55

> [!NOTE]
> Funkce Max vrátí největší hodnotu v páru hodnot.

## <a name="scaling-application-gateway-and-waf-v2"></a>Změna velikosti aplikační brány a WAF v2

Aplikační bránu a WAF lze nakonfigurovat tak, aby se škálovaly ve dvou režimech:

- **Automatické škálování** – s povoleným automatickým škálováním se navertí navertí nebo dolů na škálování aplikační brány a virtuálních zařízení WAF v2 na základě požadavků na provoz aplikací. Tento režim nabízí lepší pružnost vaší aplikace a eliminuje potřebu uhodnout velikost aplikační brány nebo počet instancí. Tento režim také umožňuje ušetřit náklady tím, že nevyžadují bránu ke spuštění na špičkové zřízené kapacity pro předpokládané maximální zatížení provozu. Je nutné zadat minimální a volitelně maximální počet instancí. Minimální kapacita zajišťuje, že aplikační brána a WAF v2 nespadají pod minimální počet instancí zadaný, a to i v případě, že neexistuje provoz. Každá instance se počítá jako 10 dalších jednotek vyhrazené kapacity. Nula neznamená žádnou vyhrazenou kapacitu a je čistě autoškálování v přírodě. Vezměte prosím na vědomí, že nulové dodatečné minimální instance stále zajišťují vysokou dostupnost služby, která je vždy zahrnuta s pevnou cenou. Můžete také volitelně zadat maximální počet instancí, který zajišťuje, že aplikační brána není škálovat nad zadaný počet instancí. Bude se vám nadále účtovat množství provozu obsluhované bránou. Počty instancí může být v rozsahu od 0 do 125. Výchozí hodnota pro maximální počet instancí je 20, pokud není zadán.
- **Ruční** – Můžete také zvolit ruční režim, ve kterém se brána nebude automaticky škálovat. V tomto režimu, pokud je větší provoz, než co aplikace gateway nebo WAF může zpracovat, může to mít za následek ztrátu provozu. V ručním režimu je zadání počtu instancí povinné. Počet instancí se může lišit od 1 do 125 instancí.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porovnání funkcí mezi skladovou položkou v1 a skladovou položkou v2

Následující tabulka porovnává funkce, které jsou k dispozici s každou skladovou položkou.

|                                                   | v1 Skladová položka   | v2 Skladová položka   |
| ------------------------------------------------- | -------- | -------- |
| Automatické škálování                                       |          | &#x2713; |
| Redundance zóny                                   |          | &#x2713; |
| Statické VIP                                        |          | &#x2713; |
| Řadič příchozího přenosu dat služby Azure Kubernetes (AKS) |          | &#x2713; |
| Integrace se službou Azure Key Vault                       |          | &#x2713; |
| Přepsání záhlaví HTTP(S)                           |          | &#x2713; |
| Směrování na základě adresy URL                                 | &#x2713; | &#x2713; |
| Hostování několika webů                             | &#x2713; | &#x2713; |
| Přesměrování provozu                               | &#x2713; | &#x2713; |
| Firewall webových aplikací (WAF)                    | &#x2713; | &#x2713; |
| Vlastní pravidla WAF                                  |          | &#x2713; |
| Ukončení protokolu SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Komplexní šifrování SSL                         | &#x2713; | &#x2713; |
| Spřažení relací                                  | &#x2713; | &#x2713; |
| Stránky vlastních chyb                                | &#x2713; | &#x2713; |
| Podpora protokolu WebSocket                                 | &#x2713; | &#x2713; |
| Podpora HTTP/2                                    | &#x2713; | &#x2713; |
| Vyprázdnění připojení                               | &#x2713; | &#x2713; |

> [!NOTE]
> Automatické škálování v2 Skladová položka nyní podporuje [výchozí sondy stavu](application-gateway-probe-overview.md#default-health-probe) automaticky sledovat stav všech prostředků v jeho back-endfondu a zvýraznit ty back-endové členy, které jsou považovány za nefunkční. Výchozí sonda stavu je automaticky nakonfigurována pro back-endy, které nemají žádnou vlastní konfiguraci sondy. Další informace najdete [v tématu sondy stavu v bráně aplikace](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Rozdíly s v1 SKU

|Rozdíl|Podrobnosti|
|--|--|
|Ověřovací certifikát|Není podporováno.<br>Další informace naleznete v [tématu Přehled ssl od konce do konce pomocí aplikační brány](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Míchání Standard_v2 a standardní aplikační brány ve stejné podsíti|Nepodporuje se|
|Uživatelem definovaná trasa (UDR) v podsíti Aplikační brána|Podporováno (konkrétní scénáře). V náhledu.<br> Další informace o podporovaných scénářích naleznete v [tématu Přehled konfigurace aplikační brány](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|NSG pro rozsah příchozích portů| - 65200 až 65535 pro Standard_v2 Skladové položky<br>- 65503 až 65534 pro standardní skladovou položku.<br>Další informace naleznete v [nejčastějších dotazech](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Protokoly výkonu v diagnostice Azure|Není podporováno.<br>Metriky Azure by se měly používat.|
|Fakturace|Fakturace má začít 1.|
|Režim FIPS|Ty nejsou v současné době podporovány.|
|Pouze režim ILB|Tato tato podpora není v současné době podporována. Veřejný režim a režim ILB společně je podporován.|
|Integrace Netwatcher|Není podporováno.|
|Integrace služby Azure Security Center|Zatím není k dispozici.

## <a name="migrate-from-v1-to-v2"></a>Migrace z v1 na v2

Skript Azure PowerShell je k dispozici v galerii PowerShellu, který vám pomůže migrovat z vaší aplikační brány v1/WAF do sku automatického škálování v2. Tento skript vám pomůže zkopírovat konfiguraci z brány v1. Migrace dopravy je stále vaší odpovědností. Další informace najdete [v tématu Migrace brány aplikací Azure z v1 na 2](migrate-v1-v2.md).

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Vytvoření automatickéškálování, zónově redundantní aplikační brány s vyhrazenou virtuální IP adresou pomocí Azure PowerShellu](tutorial-autoscale-ps.md)
- Další informace o [application gateway](overview.md).
- Další informace o [Azure Firewall](../firewall/overview.md).
