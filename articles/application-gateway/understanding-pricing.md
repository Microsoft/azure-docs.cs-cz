---
title: Seznámení s cenami – Azure Application Gateway
description: Tento článek popisuje fakturační proces pro Azure Application Gateway a firewall webových aplikací pro skladové položky V1 až v2.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 1d88379726cfb6c4218c38b9ccc87005609a9aba
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460741"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Principy cen pro Azure Application Gateway a firewall webových aplikací

>[!NOTE]
>Ceny uvedené v tomto článku jsou příklady a jsou k disukázce pouze pro účely ilustrací. Informace o cenách podle vaší oblasti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/application-gateway/).

Azure Application Gateway je řešení vyrovnávání zatížení vrstvy 7, které umožňuje škálovatelné, vysoce dostupné a zabezpečené doručování webových aplikací v Azure.

Neexistují žádné náklady na front-end nebo náklady na ukončení spojené s Application Gateway.
Budou se vám fakturovat jenom za předběžně zřízené a využité prostředky na základě skutečné hodinové spotřeby. Náklady spojené s Application Gateway jsou klasifikovány do dvou součástí: pevné náklady a variabilní náklady. Skutečné náklady na jednotlivé komponenty se budou lišit v závislosti na využívání SKU.

Tento článek popisuje náklady spojené s jednotlivými SKU a doporučujeme, aby uživatelé využili tento dokument k plánování a správě nákladů spojených s Application Gateway Azure.

## <a name="v1-skus"></a>SKU v1

SKU Standard Application Gateway a WAF V1 se účtují jako kombinace:

* Pevné náklady

    Náklady založené na čase konkrétního typu Application Gateway/WAF jsou zřízeny a spuštěny pro zpracování požadavků.
    Komponenta s pevnými náklady vezme v úvahu následující faktory:
    * Úroveň – Standard Application Gateway nebo WAF
    * Velikost – malý, střední & velký
    * Počet instancí – počet instancí, které se mají nasadit

    U instancí N budou náklady spojené s N * náklady na jednu instanci konkrétní vrstvy (standardní & WAF) & velikost (malá, střední & velká).

* Variabilní náklady

    Náklady založené na množství dat zpracovaných Application Gateway/WAF. Žádosti i bajty odpovědí zpracované Application Gateway by se považovaly za fakturace.

Celkové náklady = pevné náklady a variabilní náklady

### <a name="standard-application-gateway"></a>Standardní Application Gateway

Pevné náklady & variabilní náklady budou vypočítány podle typu Application Gateway.
V následující tabulce jsou uvedeny ukázkové ceny na základě snímku Východní USA cen a jsou určeny pouze pro ilustraci.

#### <a name="fixed-cost-east-us-region-pricing"></a>Pevné náklady (ceny Východní USA oblasti)

|              Typ Application Gateway             |  Náklady ($/h)  |
| ------------------------------------------------- | ---------------|
|                     Malý                         |    $0,025      |
|                     Střední                        |    $0,07       |
|                     Velký                         |    $0,32       |

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

#### <a name="variable-cost-east-us-region-pricing"></a>Variabilní náklady (Východní USA cenové oblasti)

|              Zpracovaná data             |  Malé ($/GB)  |  Střední ($/GB) |  Velký ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Prvních 10 TB/měsíc                       |     $0,008     |      Free      |     Free      |
| Dalších 30 TB (10 – 40 TB)/měsíc             |     $0,008     |     $0,007     |     Free      |
| Přes 40 TB/měsíc                        |     $0,008     |     $0,007     |     $0,0035   |

Další informace o cenách podle vaší oblasti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Pevné náklady & variabilní náklady budou vypočítány podle typu zřízené Application Gateway.

V následující tabulce jsou uvedeny ukázkové ceny na základě snímku Východní USA cen a jsou určené pouze pro ilustraci.

#### <a name="fixed-cost-east-us-region-pricing"></a>Pevné náklady (ceny Východní USA oblasti)

|              Typ Application Gateway             |  Náklady ($/h)  |
| ------------------------------------------------- | ---------------|
|                     Malý                         |       Není k dispozici       |
|                     Střední                        |     $0,126     |
|                     Velký                         |     $0,448     |

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

#### <a name="variable-cost-east-us-region-pricing"></a>Variabilní náklady (Východní USA cenové oblasti)

|              Zpracovaná data             |  Malé ($/GB)  |  Střední ($/GB) |  Velký ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Prvních 10 TB/měsíc                       |     $0,008     |      Free      |     Free      |
| Dalších 30 TB (10 – 40 TB)/měsíc             |     $0,008     |     $0,007     |     Free      |
| Přes 40 TB/měsíc                        |     $0,008     |     $0,007     |     $0,0035   |

Další informace o cenách podle vaší oblasti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Přenosy odchozích dat – data odcházející z datových center Azure z aplikačních bran se budou účtovat za standardní [sazby za přenos dat](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Příklad 1 (a) – standardní Application Gateway s 1 počtem instancí

Předpokládejme, že jste zřídili standardní Application Gateway typu média s 1 instancí a za měsíc zpracovává 500 GB. Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Pevná cena = $0,07 × 730 (hodiny) = $51,1 Odhad měsíčních cen je založený na 730 hodinách využití za měsíc.

Proměnné cost = Free (střední úroveň nemá žádné náklady za prvních 10 TB zpracovaných za měsíc), celkové náklady = $51,1 + 0 = $51,1 

> [!NOTE]
> Pro podporu scénářů s vysokou dostupností je potřeba nastavit minimálně 2 instance pro SKU v1. Přečtěte si [smlouvu SLA pro Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Příklad 1 (b) – standardní Application Gateway s počtem instancí > 1

Předpokládejme, že jste zřídili standardní Application Gateway typu média s pěti instancemi a za měsíc zpracovává 500 GB. Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Pevná cena = 5 (počet instancí) × $0,07 × 730 (hodiny) = $255,5 Odhad měsíčních cen vychází 730 z hodin využití za měsíc.

Proměnné cost = Free (střední úroveň nemá žádné náklady za prvních 10 TB zpracovaných za měsíc), celkové náklady = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Příklad 2 – WAF Application Gateway

Řekněme, že jste zřídili malý typ Standard Application Gateway a WAF velký typ Application Gateway v prvních 15 dnech v měsíci. Malá Aplikační brána zpracovává 15 TB po dobu, po kterou je aktivní, a rozsáhlá Aplikační brána WAF zpracovává v době, kdy je aktivní, procesy 100 TB. Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto: 

###### <a name="small-instance-standard-application-gateway"></a>Malá instance Standard Application Gateway

24 hodin × 15 dní = 360 hodin

Pevná cena = $0,025 × 360 (hodiny) = $9

Variabilní náklady = 10 * 1000 × $0.008/GB + 5 × 1000 * $0.008/GB = $120

Celkové náklady = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>WAF Application Gateway velké instance
24 hodin × 15 dní = 360 hodin

Pevná cena = $0,448 × 360 (hodiny) = $161,28

Variabilní náklady = 60 × 1000 * $0.0035/GB = $210 (vysoká úroveň nemá žádné náklady na prvních 40 TB zpracovaných za měsíc)

Celkové náklady = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>2 – SKU  

SKU Application Gateway v2 a WAF v2 podporují automatické škálování a zajištění vysoké dostupnosti ve výchozím nastavení.

### <a name="key-terms"></a>Klíčové výrazy

##### <a name="capacity-unit"></a>Jednotka kapacity 
Jednotka kapacity je míra využití kapacity pro Application Gateway v různých parametrech.

Jediná jednotka kapacity se skládá z následujících parametrů:
* trvalá připojení 2500
* 2,22. propustnost MB/s
* 1 výpočetní jednotka

Pokud dojde k překročení některého z těchto parametrů, je nutné mít další jednotky kapacity (celkem n), a to i v případě, že ostatní dva parametry nepřekročí omezení této jedné jednotky kapacity.
Parametr s nejvyšším využitím mezi třemi výše se interně použije pro výpočet kapacitních jednotek, které se pak účtují.

##### <a name="compute-unit"></a>Výpočetní jednotka
Výpočetní jednotka je míra spotřebované výpočetní kapacity. Faktory ovlivňující výpočet spotřební jednotky jsou připojení TLS/s, výpočty přepisu adresy URL a zpracování pravidel WAF. Počet požadavků, které může výpočetní jednotka zpracovat, závisí na různých kritériích velikosti klíče certifikátu TLS, algoritmu výměny klíčů, přepisování hlaviček a v případě velikosti požadavku WAF-příchozí.

Doprovodné materiály k výpočetním jednotkám:
* Standard_v2 – pro každý výpočetní jednotku je možné, že certifikát TLS s klíčem RSA 2048 je schopný přibližně 50 připojení za sekundu.

* WAF_v2 – každá výpočetní jednotka může podporovat přibližně 10 souběžných požadavků za sekundu po 70-30% kombinace provozu s 70% požadavky menší než 2 KB pro získání/odeslání a zbývající vyšší. WAF výkon aktuálně neovlivní velikost odpovědí.

##### <a name="instance-count"></a>Počet instancí 
Předběžné zřizování prostředků pro SKU Application Gateway v2 je definováno z podmínek počtu instancí. Každá instance garantuje minimálně 10 kapacitních jednotek v souvislosti se schopností zpracování. Stejná instance může potenciálně podporovat více než 10 kapacitních jednotek pro různé vzory přenosů v závislosti na parametrech kapacity kapacity.

Ručně definované škálování a omezení nastavené pro automatické škálování (minimální nebo maximální) se nastaví v počtu instancí. Ruční nastavení škálování počtu instancí a minimální počet instancí v konfiguraci automatického škálování bude mít za následek vyhradení 10 jednotek kapacity/instance. Tato vyhrazená kapacitní jednotky se budou účtovat tak dlouho, dokud bude Application Gateway aktivní bez ohledu na skutečnou spotřebu prostředků. Pokud skutečná spotřeba přechází z 10 prahových jednotek nebo prahové hodnoty instance, budou se za součást proměnné účtovat další jednotky kapacity.

Skladové jednotky v2 se účtují na základě spotřeby a tvoří dvě části:

* Pevné náklady

    Náklady založené na době, kdy je zajištěno zřízení Application Gateway v2/WAF v2 a k dispozici pro zpracování požadavků. Tím zajistíte vysokou dostupnost, i když je 0 instancí rezervováno zadáním 0 v minimálním počtu instancí jako součást automatického škálování. 
    
    Pevné náklady zahrnují také náklady spojené s veřejnou IP adresou připojenou k Application Gateway.

    Počet instancí spuštěných v jakémkoli časovém okamžiku není považován za fixní náklady pro skladové položky v2. Pevné náklady na spuštění Standard_V2 (nebo WAF_V2) by byly stejné za hodinu, a to bez ohledu na počet instancí spuštěných ve stejné oblasti Azure.

* Náklady na jednotku kapacity 

    Náklady založené na počtu kapacitních jednotek, které jsou buď rezervované, nebo využité, podle potřeby pro zpracování příchozích požadavků.  Náklady na bázi spotřeby se vypočítávají každou hodinu.

Celkové náklady = pevná cena + náklady na kapacitu

> [!NOTE]
> Částečně hodina se účtuje jako plná hodina.

V následující tabulce jsou uvedeny ukázkové ceny na základě snímku Východní USA cen a jsou určené pouze pro ilustraci.

#### <a name="fixed-costs-east-us-region-pricing"></a>Pevné náklady (ceny Východní USA oblasti)

|              Skladová položka V2             |  Náklady ($/h)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

#### <a name="variable-costs-east-us-region-pricing"></a>Variabilní náklady (Východní USA cenové oblasti)

|        Jednotka kapacity         |  Standard_V2 ($/h)  |  WAF_V2 ($/h) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

Další informace o cenách podle vaší oblasti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Přenosy odchozích dat – data odcházející z datových center Azure z aplikačních bran se budou účtovat za standardní [sazby za přenos dat](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--manual-scaling"></a>Příklad 1 (a) – ruční škálování 
Řekněme, že jste zřídili Standard_V2 Application Gateway s ručním škálováním nastaveným na 8 instancí po celý měsíc. Během této doby obdrží průměrnou přenos dat 88,8 MB/s.

Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

1 CU může zpracovávat propustnost 2,22 MB/s.

Kapacitní jednotky se vyžaduje pro zpracování 88,8 MB/s = 88,8/2,22 = 40 kapacitní jednotky 

Předem zřízené kapacitní jednotky = 8 (počet instancí) × 10 = 80 

Od 80 (Rezervovaná kapacita) > 40 (požadovaná kapacita), nejsou potřeba žádné další kapacitní jednotky. 

Pevná cena = $0,246 × 730 (hodiny) = $179,58

Variabilní náklady = $0,008 × 8 (jednotky instancí) × 10 (jednotky kapacity) × 730 (hodiny) = $467,2

Celkové náklady = $179,58 + $467,2 = $646,78

![Diagram ručního škálování 1](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Příklad 1 (b) – ruční škálování pomocí provozu přesahujícího zřízenou kapacitu

Řekněme, že jste zřídili Standard_V2 Application Gateway s ručním škálováním nastaveným na 3 instance po celý měsíc. Během této doby obdrží průměrnou přenos dat 88,8 MB/s.

Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

1 CU může zvládnout propustnost 2,22 MB/s.

Kapacitní jednotky se vyžaduje pro zpracování 88,8 MB/s = 88,8/2,22 = 40 

Předem zřízené kapacitní jednotky = 3 (počet instancí) × 10 = 30 

Od 40 (požadovaná kapacita) > 30 (Rezervovaná kapacita), jsou potřeba další kapacitní jednotky.
Počet dodatečných kapacitní jednotky by měl záviset na volné kapacitě dostupné u jednotlivých instancí.

Pokud byla pro použití v rámci 3 rezervovaných instancí dostupná kapacita zpracování ekvivalentní 10 dalším kapacitní jednotky.

Pevná cena = $0,246 × 730 (hodiny) = $179,58

Proměnné cost = $0,008 * (3 (jednotky instancí) * 10 (jednotky kapacity) + 5 (další jednotky kapacity)) * 730 (hodiny) = $204,4

Celkové náklady = $179,58 + $204,4 = $383,98

![Diagram ručního škálování 2](./media/pricing/manual-scale-2.png)

> [!NOTE]
> V případě ručního škálování může další žádosti překračující maximální kapacitu zpracování rezervovaných instancí způsobit dopad na dostupnost vaší aplikace. V situacích vysokého zatížení můžou rezervované instance poskytnout více než 10 kapacitních jednotek pro zpracování v závislosti na konfiguraci a typu příchozích požadavků. Doporučuje se ale zřídit počet instancí podle požadavků na provoz.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Příklad 2 – WAF_V2 instance s automatickým škálováním

Předpokládejme, že jste zřídili WAF_V2 s povoleným automatickým škálováním a pro celý měsíc jste nastavili minimální počet instancí na 6. Zatížení požadavku způsobilo horizontální navýšení kapacity instance WAF a využití kapacitních jednotek 65 (škálování z 5 kapacitních jednotek, zatímco 60 jednotek bylo rezervováno) pro celý měsíc.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

Pevná cena = $0,443 × 730 (hodiny) = $323,39

Variabilní náklady = $0,0144 × 65 (jednotky kapacity) × 730 (hodiny) = $683,28

Celkové náklady = $323,39 + $683,28 = $1006,67

![Diagram automatického škálování 2](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Skutečný provoz zjištěný pro váš Application Gateway pravděpodobně nemá takový konstantní vzor provozu a zjištěné zatížení vašeho Application Gateway by se pohybovalo v závislosti na skutečném využití.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Příklad 3 (a) – WAF_V2 instance s automatickým škálováním a 0 min. konfigurace škálování

Předpokládejme, že jste zřídili WAF_V2 s povoleným automatickým škálováním a pro celý měsíc jste nastavili minimální počet instancí na 0. Zatížení požadavku na WAF je minimální, ale konzistentně je k dispozici za hodinu celý měsíc. Zatížení je pod kapacitou jedné jednotky kapacity.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

Pevná cena = $0,443 × 730 (hodiny) = $323,39

Variabilní náklady = $0,0144 * 1 (jednotky kapacity) × 730 (hodiny) = $10,512

Celkové náklady = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Příklad 3 (b) – WAF_V2 instance s automatickým škálováním s 0 min. počtem instancí

Předpokládejme, že jste zřídili WAF_V2 s povoleným automatickým škálováním a pro celý měsíc jste nastavili minimální počet instancí na 0. V celém měsíci však existuje 0 provozu směrovaného na instanci WAF.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Pevná cena = $0,443 × 730 (hodiny) = $323,39

Variabilní náklady = $0,0144 × 0 (jednotky kapacity) × 730 (hodiny) = $0

Celkové náklady = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Příklad 3 (C) – WAF_V2 instance s ručním škálováním nastaveným na 1 instanci

Řekněme, že jste zřídili WAF_V2 a nastavíte ji na ruční škálování s minimální přijatelnou hodnotou 1 instance v celém měsíci. V celém měsíci však existuje 0 provozu směrovaného na WAF.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

Pevná cena = $0,443 × 730 (hodiny) = $323,39

Variabilní náklady = $0,0144 * 1 (počet instancí) × 10 (jednotky kapacity) × 730 (hodiny) = $105,12

Celkové náklady = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Příklad 4 – WAF_V2 s automatickým škálováním, výpočty jednotek kapacity

Předpokládejme, že jste zřídili WAF_V2 s povoleným automatickým škálováním a pro celý měsíc jste nastavili minimální počet instancí na 0. Během této doby obdrží 25 nových připojení TLS za sekundu s průměrem přenosu dat 8,88 MB/s.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Odhad měsíčních cen vychází z 730 hodin využití za měsíc.

Pevná cena = $0,443 × 730 (hodiny) = $323,39

Variabilní náklady = $0,0144 × 730 (hodiny) * {Max (25/50, 8.88/2.22)} = $23,36 (4 jednotky kapacity potřebné ke zpracování 8,88 MB/s)

Celkové náklady = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Příklad 5 (a) – Standard_V2 s automatickým škálováním, výpočty na základě času

Předpokládejme, že jste zřídili standard_V2 s povoleným automatickým škálováním a nastavíte minimální počet instancí na 0 a tato Aplikační brána je aktivní 2 hodiny.
Během první hodiny přijímá provoz, který může být zpracován 10 jednotkami kapacity a během druhé hodiny přijímá provoz, který pro zpracování zatížení vyžadoval 20 kapacitních jednotek.
Náklady na Application Gateway s využitím výše uvedených cen se vypočtou takto:

Pevná cena = $0,246 × 2 (hodiny) = $0,492

Variabilní náklady = $0,008 * 10 (jednotky kapacity) × 1 (hodiny) + $0,008 * 20 (jednotky kapacity) * 1 (hodiny) = $0,24

Celkové náklady = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Monitorování fakturovaného využití

Množství spotřeby můžete zobrazit pro různé parametry (výpočetní jednotka, propustnost & trvalá připojení), stejně jako součást metrik Application Gateway v části **monitorování** .

![Diagram oddílu metriky](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Užitečné metriky pro odhad nákladů

* Aktuální jednotky kapacity

    Počet jednotek kapacity spotřebovaných k vyrovnávání zatížení provozu mezi třemi parametry – aktuálními připojeními, propustností a výpočetní jednotkou

* Pevné fakturovatelné jednotky kapacity

    Minimální počet přidělených kapacitních jednotek se zřídil podle nastavení minimální počet instancí (jedna instance překládá na 10 kapacitních jednotek) v konfiguraci Application Gateway.

* Odhadované účtované jednotky kapacity

    Odhadované účtované jednotky kapacity udávají počet jednotek kapacity, na základě kterého se odhaduje vyúčtování. Tato hodnota se vypočte jako větší z hodnot aktuální jednotky kapacity (jednotky kapacity nutné k vyrovnávání zatížení provozu) a pevné fakturovatelné jednotky kapacity (minimální počet zřízených jednotek kapacity).

K dispozici jsou také další metriky, jako je propustnost, aktuální připojení a výpočetní jednotky, které vám pomohou pochopit kritické body a odhadnout počet požadovaných kapacitních jednotek. Podrobné informace jsou k dispozici v [Application Gateway metriky](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Příklad – odhad využití kapacitních jednotek

**Pozorované metriky:**

* Výpočetní jednotky = 17,38
* Propustnost = 1.37 M bajtů/s – 10,96 MB/s
* Aktuální připojení = 123.08 k
* Počet vypočítaných jednotek kapacity = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Zjištěné jednotky kapacity v metrikách = 49,23

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny fungují v Azure Application Gateway, najdete v následujících článcích:

* [Stránka s cenami za Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Cenová Kalkulačka Azure Application Gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
