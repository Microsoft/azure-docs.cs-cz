---
title: Automatické škálování a redundantní Application Gateway V2 v zóně
description: Tento článek zavádí Azure Application Standard_v2 a WAF_v2 SKU, která zahrnuje automatické škálování a funkce redundantní v zóně.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: b97dab0f41915ac6193c35cad9a6af812b16fd4a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104887"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatické škálování a redundantní Application Gateway V2 v zóně 

Application Gateway a firewall webových aplikací (WAF) jsou také k dispozici v SKU Standard_v2 a WAF_v2. SKU v2 nabízí vylepšení výkonu a přidává podporu pro důležité nové funkce, jako je automatické škálování, redundance zóny a podpora statických virtuálních IP adres. Stávající funkce v rámci Standard a WAF SKU se budou dál podporovat v nové SKU verze V2 a s několika výjimkami uvedenými v části [porovnání](#differences-with-v1-sku) .

Nová SKU v2 obsahuje následující vylepšení:

- Automatické **škálování**: Nasazení Application Gateway nebo WAF v rámci SKU automatického škálování se může škálovat nahoru nebo dolů na základě změny schémat zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Tato SKU nabízí skutečnou pružnost. V skladové jednotce Standard_v2 a WAF_v2 může Application Gateway provozovat v pevné kapacitě (automatické škálování je zakázané) a v režimu povoleného automatického škálování. Režim pevné kapacity je vhodný pro scénáře s konzistentními a předvídatelnými úlohami. Režim automatického škálování je užitečný v aplikacích, které se zobrazují jako odchylka v provozu aplikací.
- **Redundance zóny**: Nasazení Application Gateway nebo WAF může zahrnovat více Zóny dostupnosti a odstraňuje nutnost zřídit v každé zóně samostatné instance Application Gateway Traffic Manager. Můžete zvolit jednu zónu nebo několik zón, ve kterých se Application Gateway instance nasazují, což zjednodušuje selhání zóny. Back-end fond pro aplikace se dá v rámci zón dostupnosti podobně distribuovat.

  Redundance zóny je dostupná jenom v případě, že jsou dostupné zóny Azure. V ostatních oblastech jsou všechny ostatní funkce podporované. Další informace najdete v tématu [co je zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statická virtuální IP adresa**: SKU Application Gateway v2 podporuje výhradně typ statických virtuálních IP adres. Tím se zajistí, že se virtuální IP adresa přidružená k aplikační bráně nemění pro životní cyklus nasazení, ani po restartování.  V v1 není statická virtuální IP adresa, proto musíte místo IP adresy pro směrování názvu domény použít adresu URL služby Application Gateway, aby bylo možné App Services prostřednictvím aplikační brány.
- **Přepsání hlavičky**: Application Gateway umožňuje přidávat, odebírat nebo aktualizovat žádosti protokolu HTTP a hlavičky odpovědí s SKU v2. Další informace najdete v tématu [přepis hlaviček protokolu HTTP pomocí Application Gateway](rewrite-http-headers.md)
- **Key Vault Integration (Preview)** : Application Gateway v2 podporuje integraci s Key Vault (ve verzi Public Preview) pro certifikáty serveru, které jsou připojené k naslouchacímu procesu s povoleným protokolem HTTPS. Další informace najdete v tématu [ukončení SSL pomocí certifikátů Key Vault](key-vault-certs.md).
- **Kontroler příchozího přenosu služeb Azure Kubernetes (Preview)** : Kontroler příchozího přenosu Application Gateway v2 umožňuje použití Application Gateway Azure jako příchozího přenosu pro službu Azure Kubernetes (AKS), která se označuje jako cluster AKS. Další informace najdete na stránce s [dokumentací](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Vylepšení výkonu**: SKU v2 nabízí až pětinásobné vyšší výkon při snižování zátěže SSL ve srovnání s SKU Standard/WAF.
- **Rychlejší nasazení a čas aktualizace** SKU verze 2 poskytuje rychlejší nasazení a dobu aktualizace ve srovnání s SKU Standard/WAF. To zahrnuje také změny konfigurace WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Podporované oblasti

SKU Standard_v2 a WAF_v2 jsou k dispozici v následujících oblastech: Střed USA – sever, Střed USA – jih, Západní USA Západní USA 2, Východní USA, Východní USA 2, Střed USA, Severní Evropa, Západní Evropa, jihovýchodní Asie, Francie – střed, Velká Británie – západ, Japonsko – východ, Japonsko – západ, Austrálie – východ, Austrálie – jihovýchod, Kanada – střed, Kanada – východ, Východní Asie, Korea Střední, Korea – jih, Jižní Indie, Velká Británie – jih, Střed Indie Západní Indie Jižní Indie.

## <a name="pricing"></a>Ceny

V případě SKU verze v2 je cenový model založený na spotřebě a již není připojen k počtům nebo velikostem instancí. Ceny SKU v2 mají dvě komponenty:

- **Pevná cena** – je to hodinová (nebo částečná hodinová) cena za účelem zřízení brány Standard_v2 nebo WAF_v2.
- **Jednotková cena kapacity** – jedná se o náklady založené na spotřebě, které se účtují i s pevnými náklady. Poplatek za jednotky kapacity se počítá po hodinách nebo částečně po hodinách. Jednotka kapacity má tři dimenze – výpočetní jednotka, trvalá připojení a propustnost. Výpočetní jednotka měří spotřebovanou kapacitu procesoru. Faktory ovlivňující výpočetní jednotku jsou připojení TLS/s, výpočty přepisu adresy URL a zpracování pravidel WAF. Trvalé připojení je míra navázaných připojení TCP k aplikační bráně v daném fakturačním intervalu. Propustnost je průměr MB/s zpracováno systémem v daném fakturačním intervalu.

Každou jednotku kapacity tvoří nejvýše: 1 výpočetní jednotka, nebo 2500 trvalá připojení, nebo propustnost 2,22-MB/s.

Doprovodné materiály k výpočetním jednotkám:

- **Standard_v2** -každá výpočetní jednotka je schopná přibližně 50 připojení za sekundu k certifikátu TLS s klíčem RSA 2048.
- **WAF_v2** – každá výpočetní jednotka může podporovat přibližně 10 souběžných požadavků za sekundu za 70-30% kombinace přenosů s 70% požadavky menší než 2 KB pro získání/odeslání a zbývající vyšší. WAF výkon aktuálně neovlivní velikost odpovědí.

> [!NOTE]
> Každá instance teď může podporovat přibližně 10 kapacitních jednotek.
> Počet požadavků, které může výpočetní jednotka zpracovat, závisí na různých kritériích velikosti klíče certifikátu TLS, algoritmu výměny klíčů, přepisování hlaviček a v případě příchozí velikosti požadavku WAF. Doporučujeme, abyste provedli testy aplikací k určení míry požadavků na výpočetní jednotku. Jednotka kapacity i výpočetní jednotka budou k dispozici jako metrika před zahájením fakturace.

V následující tabulce jsou uvedeny ukázkové ceny a jsou pouze pro ilustrativní účely.

**Ceny v USA – východ**:

|              Název SKU                             | Pevná cena ($/h)  | Jednotková cena kapacity ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Další informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/application-gateway/). Fakturace je naplánována na začátek od 1. července 2019.

**Příklad 1**

Application Gateway Standard_v2 se zřídí bez automatického škálování v režimu ručního škálování s pevnou kapacitou pět instancí.

Pevná cena = 744 (hodiny) × $0,20 = $148,8 <br>
Jednotky kapacity = 744 (hodiny) * 10 jednotek kapacity na instanci * 5 instancí * $0,008 na kapacitu jednotky = $297,6

Total Price = $148,8 + $297,6 = $446,4

**Příklad 2**

Application Gateway standard_v2 se zřídí za měsíc a během této doby obdrží 25 nových připojení SSL za sekundu, průměrně 8,88 MB/s přenosu dat. Za předpokladu, že připojení jsou krátkodobá, vaše cena by byla:

Pevná cena = 744 (hodiny) × $0,20 = $148,8

Jednotková cena kapacity = 744 (hodiny) * Max (25/50 výpočetních jednotek pro připojení/s, jednotka kapacity 8.88/2.22 pro propustnost) * $0,008 = 744 × 4 * 0,008 = $23,81

Total Price = $148.8 + 23.81 = $172,61

> [!NOTE]
> Funkce Max vrátí největší hodnotu ve dvojici hodnot.

**Příklad 3**

Application Gateway WAF_v2 se zřídí po dobu měsíce. Během této doby obdrží 25 nových připojení SSL za sekundu, průměr 8,88 – MB/s a provede požadavek 80 za sekundu. Za předpokladu, že připojení jsou krátkodobá a že výpočet výpočetní jednotky pro aplikaci podporuje 10 RPS na výpočetní jednotku, vaše cena by byla:

Pevná cena = 744 (hodiny) × $0,36 = $267,84

Jednotková cena kapacity = 744 (hodiny) * Max (maximální výpočetní jednotka (25/50 za připojení/s, 80/10 WAF RPS), 8.88/2.22 kapacita jednotky pro propustnost) * $0,0144 = 744 × 8 × 0,0144 = $85,71

Total Price = $267,84 + $85,71 = $353,55

> [!NOTE]
> Funkce Max vrátí největší hodnotu ve dvojici hodnot.

## <a name="scaling-application-gateway-and-waf-v2"></a>Škálování Application Gateway a WAF v2

Application Gateway a WAF se dají nakonfigurovat tak, aby se škálují ve dvou režimech:

- Automatické **škálování** – s povoleným automatickým škálováním jsou SKU Application Gateway a WAF v2 vertikálně navyšovat nebo snížit zatížení na základě požadavků na provoz aplikací. Tento režim nabízí lepší pružnost vaší aplikace a eliminuje nutnost odhadnout velikost nebo počet instancí aplikační brány. Tento režim také umožňuje ušetřit náklady tím, že nevyžaduje, aby brána běžela ve špičce zřízené kapacity pro očekávané maximální zatížení provozu. Je nutné zadat minimální a volitelně maximální počet instancí. Minimální kapacita zajišťuje, že Application Gateway a WAF v2 nedosahují pod stanoveným minimálním počtem instancí, a to i v případě absence provozu. Každá instance se počítá jako 10 dalších rezervovaných jednotek kapacity. 0 znamená žádnou rezervovanou kapacitu a je čistě automatické škálování v podstatě. Počítejte s tím, že 0 dalších minimálních instancí stále zajišťuje vysokou dostupnost služby, která je vždycky zahrnutá do pevné ceny. Volitelně můžete zadat také maximální počet instancí, který zajistí, že Application Gateway nepřekračuje zadaný počet instancí. Bude se vám dál účtovat množství provozu obsluhované bránou. Počty instancí můžou být v rozsahu od 0 do 125. Výchozí hodnota pro maximální počet instancí je 20, pokud není zadána. 
- **Ručně** – můžete také zvolit ruční režim, ve kterém brána neprovede automatické škálování. Pokud je v tomto režimu více provozu, než jaké Application Gateway nebo WAF může zvládnout, mohlo by dojít ke ztrátě provozu. V případě ručního režimu je zadání počtu instancí povinné. Počet instancí se může v rozmezí od 1 do 125 instancí lišit.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porovnání funkcí mezi SKU V1 a SKU v2

Následující tabulka porovnává funkce, které jsou k dispozici u jednotlivých SKU.

|                                                   | SKU v1   | V2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatické škálování                                       |          | &#x2713; |
| Redundance zóny                                   |          | &#x2713; |
| Statická virtuální IP adresa                                        |          | &#x2713; |
| Řadič příchozího přenosu dat (AKS) Azure Kubernetes Service () |          | &#x2713; |
| Integrace se službou Azure Key Vault                       |          | &#x2713; |
| Přepsat hlavičky HTTP (S)                           |          | &#x2713; |
| Směrování na základě adresy URL                                 | &#x2713; | &#x2713; |
| Hostování několika webů                             | &#x2713; | &#x2713; |
| Přesměrování provozu                               | &#x2713; | &#x2713; |
| Firewall webových aplikací (WAF)                    | &#x2713; | &#x2713; |
| Ukončení protokolu SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Komplexní šifrování SSL                         | &#x2713; | &#x2713; |
| Spřažení relací                                  | &#x2713; | &#x2713; |
| Vlastní chybové stránky                                | &#x2713; | &#x2713; |
| Podpora protokolu WebSocket                                 | &#x2713; | &#x2713; |
| Podpora HTTP/2                                    | &#x2713; | &#x2713; |
| Vyprazdňování připojení                               | &#x2713; | &#x2713; |

> [!NOTE]
> SKU autoškále v2 teď podporuje [výchozí sondy stavu](application-gateway-probe-overview.md#default-health-probe) , aby automaticky sledovaly stav všech prostředků v jeho fondu back-endu, a zvýrazňuje tyto členy, které se považují za chybné. Výchozí sonda stavu je automaticky nakonfigurovaná pro back-endy, které nemají žádnou vlastní konfiguraci sondy. Další informace najdete v tématu [sondy stavu ve služby Application Gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Rozdíly s SKU v1

|Rozdíl|Podrobnosti|
|--|--|
|Ověřovací certifikát|Nepodporuje se.<br>Další informace najdete v tématu [Přehled koncového šifrování protokolu SSL s Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Kombinování Standard_v2 a standardních Application Gateway ve stejné podsíti|Nepodporuje se|
|Uživatelem definovaná trasa (UDR) na Application Gateway podsíti|Nepodporuje se|
|NSG pro rozsah portů pro příchozí spojení| -65200 až 65535 pro SKU Standard_v2<br>-65503 až 65534 pro SKU Standard.<br>Další informace najdete v nejčastějších [dotazech](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Protokoly výkonu v diagnostice Azure|Nepodporuje se.<br>Měly by se používat metriky Azure.|
|Fakturace|Fakturace naplánovala zahájení od 1. července 2019.|
|Režim FIPS|V tuto chvíli se nepodporují.|
|Jenom interního nástroje režim|To se v tuto chvíli nepodporuje. Podporují se veřejné a interního nástroje režimy.|
|Integrace Netwatcher|Nepodporuje se.|
|Integrace Azure Security Center|Ještě není k dispozici.

## <a name="migrate-from-v1-to-v2"></a>Migrace z v1 na v2

Skript Azure PowerShell je k dispozici v galerii prostředí PowerShell, který vám může při migraci z verze V1 Application Gateway/WAF do SKU automatického škálování v2. Tento skript vám pomůže zkopírovat konfiguraci z vaší brány v1. Migrace provozu je stále vaše zodpovědnost. Další informace najdete v tématu [migrace Azure Application Gateway z verze V1 na verzi 2](migrate-v1-v2.md).

## <a name="next-steps"></a>Další postup

- [Rychlé zprovoznění: Přímý webový provoz pomocí Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Vytvoření automatického škálování, zóny redundantní aplikační brány s vyhrazenou virtuální IP adresou pomocí Azure PowerShell](tutorial-autoscale-ps.md)
- Přečtěte si další informace o [Application Gateway](overview.md).
- Přečtěte si další informace o [Azure firewall](../firewall/overview.md).
