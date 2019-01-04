---
title: Postup konfigurace služby App Service Environment v1 – Azure
description: Konfigurace, Správa a monitorování služby App Service Environment v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723208"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurace App Service Environment v1

> [!NOTE]
> Tento článek je o App Service Environment v1.  Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Na vysoké úrovni služby Azure App Service Environment se skládá z několika hlavní součásti:

* Služba hostovaná výpočetní prostředky, které běží ve službě App Service Environment
* Storage
* Databáze
* Classic(V1) nebo Azure Manager(V2) prostředku virtuální sítě (VNet) 
* Podsítě ve službě App Service Environment hostované spuštěná v ní

### <a name="compute-resources"></a>Výpočet prostředků
Výpočetní prostředky, které používáte pro vašich fondů zdrojů čtyři.  Každý App Service Environment (ASE) obsahuje sadu front-endů a tři fondy pracovních procesů je to možné. Není nutné používat všechny fondy pracovních procesů tři--Pokud chcete, můžete stačí použít jeden nebo dva.

Hostitelé ve fondech zdrojů (front-endy ani pracovní procesy) nejsou přímo dostupné klientům. Nelze připojit se k nim, změnit jejich zřizování pomocí protokolu RDP (Remote Desktop) nebo fungují jako správce na ně.

Můžete nastavit prostředků fondu množství a velikosti. Ve službě ASE máte čtyři možnosti velikosti, které jsou označeny jako P1 až P4. Podrobnosti o těchto velikostech a cenách najdete v tématu [ceny služeb App Service](https://azure.microsoft.com/pricing/details/app-service/).
Změna počtu nebo velikosti je volána operace škálování.  Operace pouze jeden škálování může být v průběhu najednou.

**Front-endů**: Front-endů jsou koncové body HTTP/HTTPS pro vaše aplikace, které jsou uloženy ve vaší službě ASE. Při spuštění úlohy ve front-endů.

* Služba ASE začíná dvěma P2s, což je dostatečná pro vývojové a testovací úlohy a úlohy v produkčním prostředí nízké úrovně. Důrazně doporučujeme P3s pro střední náročné produkční úlohy.
* Pro střední pro náročné produkční úlohy doporučujeme vám, že máte alespoň čtyři P3s zajistit nejsou dostatečná front-endů spuštěn, když dojde k plánované údržby. Aktivity plánované údržby přinese dolů jeden front-endu v čase. To snižuje celkovou dostupnou kapacitu front-endu během údržby nevyžadovala.
* Front-endů může trvat až hodinu zřizování. 
* Pro další ladění škálování, měli byste sledovat procento využití procesoru, paměti a aktivní požadavky metriky pro front-endový fond. Pokud procenta využití procesoru nebo paměti jsou vyšší než 70 procent při spuštění P3s, přidejte další front-endů. Pokud hodnota aktivních požadavků předběhli aktuální fázi navýšení kapacity na 15 000 na 20 000 požadavků za front-endu, měli byste také přidat další front-endů. Cílem je zajistit procenta využití procesoru a paměti níže 70 % a ukončit aktivní požadavky odstávkou navýšení kapacity na nižší než 15 000 požadavků za front, pokud používáte P3s.  

**Pracovní procesy**: Zaměstnanci jsou, kde skutečně běží. Při škálování vašich plánech služby App Service, která používá si pracovní procesy ve fondu přidružených pracovních procesů.

* Nelze přidat okamžitě pracovních procesů. Může trvat až hodinu zřizování.
* Velikost výpočetních prostředků pro jakýkoli fond škálování bude trvat < 1 hodina za aktualizační doména. Existují 20 aktualizačních domén ve službě ASE. Pokud můžete škálovat výpočetní velikost fondu pracovních procesů s 10 instancí, může trvat až 10 hodin.
* Pokud změníte velikost výpočetních prostředků, které se používají ve fondu pracovních procesů, způsobí souvisejícím s úplným spuštěním aplikace, které jsou spuštěny v tomto fondu pracovních procesů.

Nejrychlejší způsob, jak změnit velikost výpočetních prostředků fondu pracovních procesů, na kterém neběží žádné aplikace je:

* Vertikálně snížit kapacitu počtu pracovních procesů na 2.  Minimální vertikálně velikost na portálu je 2. Bude trvat několik minut, než uvolnit vaší instance. 
* Vyberte nové výpočty velikosti a počtu instancí. Tady bude trvat až 2 hodiny.

Pokud vaše aplikace potřebuje větší velikost výpočetních prostředků, nemůžete využít předchozí pokynů. Místo změny velikosti fondu pracovních procesů, který je hostitelem aplikace, kterou můžete naplnit jiný fond pracovních procesů s pracovními procesy požadované velikosti a přesuňte aplikace do tohoto fondu.

* Vytvoření další instance potřebné výpočetního prostředí v jiném fondu pracovních procesů. To bude trvat až hodinu, dokončete.
* Změnit přiřazení vaše plány služby App Service, které jsou hostiteli aplikace, které vyžadují větší velikost fondu nově nakonfigurovaný pracovního procesu. To je rychlé operace, která by měla trvat méně než minutu.  
* Pokud nepotřebujete tyto nevyužité instance, vertikálně snížit kapacitu první fond pracovních procesů. Tato operace trvá několik minut na dokončení.

**Automatické škálování**: Jedním z nástrojů, které vám mohou pomoci při správě využití výpočetních prostředků provádí automatické škálování. Můžete použít automatické škálování pro front-endu nebo fondy pracovních procesů. Můžete provádění akcí, například zvýšení vaší instance libovolného typu fondu ráno a snížit večer. Nebo třeba přidat instance když počet pracovních procesů, které jsou k dispozici ve fondu pracovních procesů klesne pod určitou prahovou hodnotu.

Pokud chcete nastavit pravidla automatického škálování kolem metriky fondu výpočetních prostředků a vzít v úvahu čas, který vyžaduje zřizování. Další podrobnosti o automatické škálování služby App Service Environment najdete v tématu [postup konfigurace automatického škálování ve službě App Service Environment][ASEAutoscale].

### <a name="storage"></a>Storage
Každá služba ASE má nakonfigurovanou 500 GB úložiště. Zde se používá ve všech aplikacích v této službě ASE. Tento prostor úložiště je součástí služby ASE a aktuálně není možné přepnout na použití vašeho prostoru úložiště. Pokud provádíte úpravy směrování virtuální sítě nebo zabezpečení, budete muset přesto umožňuje přístup ke službě Azure Storage – nebo služby ASE nemůže fungovat.

### <a name="database"></a>Databáze
Databáze obsahuje informace, které definují prostředí, jakož i podrobnosti o aplikacích, které jsou spuštěny v rámci něj. Toto je příliš součástí předplatného Azure uchovávat. Není něco, co máte přímé schopnost pracovat. Pokud provádíte úpravy směrování virtuální sítě nebo zabezpečení, budete muset přesto umožňuje přístup k SQL Azure – nebo služby ASE nemůže fungovat.

### <a name="network"></a>Síť
Virtuální síť, která se použije vaše služba ASE může být ten, který jste při vytváření služby ASE nebo disk, který jste provedli předem. Když vytvoříte podsíť během vytváření služby ASE, vynutí službu ASE ve stejné skupině prostředků jako virtuální síť. Pokud potřebujete skupinu prostředků, které používá vaše služba ASE je jiná než u vaší virtuální sítě, budete muset vytvořit službu ASE pomocí šablony resource Manageru.

Existují některá omezení, virtuální síť, která se používá pro službu ASE:

* Virtuální síť musí být regionální virtuální síť.
* Musí být podsíť s 8 nebo více adres, ve kterém je nasazená služba ASE.
* Po podsíť se používá k hostování služby ASE, nebude možné změnit rozsah adres podsítě. Z tohoto důvodu doporučujeme, aby podsíť obsahuje minimálně 64 adres pro případné budoucí růst služby ASE.
* Může existovat nic jiného v podsíti, ale služby ASE.

Na rozdíl od hostovanou službu, která obsahuje službu ASE [virtuální sítě] [ virtualnetwork] a podsítě jsou v rámci uživatelského ovládacího prvku.  Můžete spravovat virtuální síti prostřednictvím virtuální sítě uživatelského rozhraní nebo Powershellu.  Služba ASE je možné nasadit v klasický nebo virtuální síti správce prostředků.  Portál a rozhraní API prostředí se mírně liší mezi klasickou virtuální sítí a virtuálních sítí správce prostředků, ale prostředí v ASE je stejný.

Virtuální síť, která slouží jako hostitel služby ASE můžete použít buď soukromé definice RFC1918 IP adresy nebo ho můžete použít veřejné IP adresy.  Pokud chcete použít rozsah IP, která není předmětem definice RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) pak budete muset vytvořit virtuální síť a podsíť pro službu ASE před časem vytvoření služby ASE.

Protože tato možnost umístí do virtuální sítě Azure App Service, znamená to, že vaše aplikace, které jsou hostované ve vaší službě ASE teď přístup k prostředkům, které jsou k dispozici prostřednictvím ExpressRoute nebo virtuální privátní sítě site-to-site (VPN) přímo. Aplikace, které jsou ve službě App Service Environment nevyžadují dalších síťových funkcí pro přístup k prostředkům, které jsou k dispozici pro virtuální síť, která je hostitelem služby App Service Environment. To znamená, že není nutné získat k prostředkům v nebo připojené k virtuální síti pomocí integrace virtuální sítě nebo hybridní připojení. Stále můžete oba tyto funkce ale pro přístup k prostředkům v sítích, které nejsou připojené k virtuální síti.

Například můžete integrovat s virtuální síť, která je ve vašem předplatném, ale není připojené k virtuální síti, které vaše služba ASE je v můžete integrace virtuální sítě. Stále také můžete Hybrid Connections pro přístup k prostředkům, které jsou v jiných sítích, stejně jako obvykle.  

Pokud máte virtuální síť nakonfigurovanou síť VPN ExpressRoute, byste měli znát některé požadavky na směrování, které má služba ASE. Existují některé konfigurace trasy definované uživatelem (UDR), které nejsou kompatibilní s ASE. Další podrobnosti o spuštění služby ASE ve virtuální síti s využitím ExpressRoute najdete v tématu [spuštění služby App Service Environment ve virtuální síti s využitím ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpečení příchozího provozu
Existují dva primární metody řízení příchozího provozu do služby ASE.  Groups(NSGs) zabezpečení sítě můžete použít k řízení jaké IP adresy můžete přístup k vaší službě ASE podle postupu popsaného tady [řízení příchozího provozu ve službě App Service Environment](app-service-app-service-environment-control-inbound-traffic.md) a můžete taky nakonfigurovat službu ASE s interním nástrojem Load Balancer (ILB).  Tyto funkce lze také společně Pokud budete chtít omezit přístup pomocí skupin zabezpečení sítě pro vaše služba ASE s ILB.

Při vytváření služby ASE se vytvoří virtuální IP adresu ve virtuální síti.  Existují dva typy virtuálních IP adres, externí a interní.  Při vytváření služby ASE s externí virtuální IP Adresou vaší aplikace ve vaší službě ASE budou přístupné přes internet směrovatelné IP adresu. Při výběru interní služby ASE s ILB nakonfigurují a nebude přímo přístup k Internetu.  Službu ASE nadále vyžaduje externí virtuální IP Adresou, ale používá se pouze pro přístup k Azure správu a údržbu.  

Při vytváření služba ASE s ILB zadejte subdoménu používá služba ASE s ILB a bude muset spravovat vlastní službu DNS pro subdomény, kterou zadáte.  Vzhledem k tomu, že nastavíte název subdomény, budete potřebovat ke správě certifikát používaný pro přístup k protokolu HTTPS.  Po vytvoření služby ASE se výzva k zadání certifikátu.  Další informace o vytváření a používání prostředí ILB ASE [interního nástroje Load Balancer pomocí služby App Service Environment][ILBASE]. 

## <a name="portal"></a>Portál
Můžete spravovat a monitorovat službu App Service Environment pomocí uživatelského rozhraní na webu Azure Portal. Pokud máte službu ASE, pak jste pravděpodobně uvidíte symbol služby App Service na vaše postranním panelu. Tento symbol se používá k reprezentování prostředí App Service na webu Azure Portal:

![App Service Environment symbol][1]

Otevřete uživatelské rozhraní, které jsou uvedeny všechny vaše služby App Service Environment, můžete použít ikonu nebo vyberte dvojitou šipkou (">" symbol) v dolní části na bočním panelu vyberte App Service Environment. Pokud vyberete jednu ze služby ase uvedené, otevřete uživatelského rozhraní, který slouží k jeho monitorování a správu.

![Uživatelské rozhraní pro monitorování a Správa služby App Service Environment][2]

Toto první okno obsahuje některé vlastnosti služby ASE, spolu s grafu metrik za fond zdrojů. Některé vlastnosti, které jsou uvedeny v **Essentials** bloku jsou také hypertextové odkazy, které se otevře okno, který je spojen s ním. Například můžete vybrat **virtuální sítě** název otevřete uživatelské rozhraní přidružené k virtuální síti, na kterém vaše služba ASE. **Plány služby App Service** a **aplikace** každý otevřete oken, které uvádějí tyto položky, které jsou ve vaší službě ASE.  

### <a name="monitoring"></a>Monitorování
Grafy umožňují zobrazit různé metriky výkonu v jednotlivých oblastí zdrojů. Pro front-endový fond můžete monitorovat průměrné využití procesoru a paměti. Pro fondy pracovních procesů můžete sledovat množství, které se používá a množství, které je k dispozici.

Použití více App Service, které můžete provést plány pracovní procesy ve fondu pracovních procesů. Zatížení není distribuován stejným způsobem jako s front-endové servery, takže využití procesoru a paměti neposkytují většinu cestě užitečné informace. Je důležitější sledovat, kolik pracovních procesů, které jste už použili a jsou k dispozici – zejména pokud spravujete tohoto systému k použití jiným uživatelům.  

Také vám pomůže všechny metriky, které lze sledovat v grafech nastavení výstrah. Nastavení výstrah tady funguje stejně jako jinde ve službě App Service. Upozornění můžete nastavit buď z **výstrahy** uživatelského rozhraní část nebo při bližším pohledu všechny metriky uživatelského rozhraní a vyberete **přidat upozornění**.

![Metriky uživatelského rozhraní][3]

Metriky, které se právě probírali jsou metriky služby App Service Environment. Existují také metriky, které jsou k dispozici na úrovni plánu služby App Service. Je to, kde sledování procesoru a paměti je velmi výhodné.

Všechny plány služby App Service ve službě ASE jsou vyhrazené plány služby App Service. To znamená, že jenom aplikace, které jsou spuštěné v hostitelích přidělené, plán služby App Service jsou aplikace v tomto plánu služby App Service. Chcete-li zobrazit podrobnosti o plánu služby App Service, otevřete plán služby App Service z některého ze seznamů v Uživatelském rozhraní služby ASE nebo z **plánů App Service Procházet** (které jsou uvedeny všechny z nich).   

### <a name="settings"></a>Nastavení
V okně služby ASE se **nastavení** oddíl, který obsahuje několik důležitých funkcí:

**Nastavení** > **vlastnosti**: **Nastavení** okno se automaticky otevře při opětovném připojení do okna vaší služby ASE. V horní části **vlastnosti**. Existuje několik položek tady, které jsou redundantní uvidíte v **Essentials**, ale je velmi užitečný, co je **virtuální IP adresu**, stejně jako **odchozí IP adresy**.

![Okna nastavení a vlastnosti][4]

**Nastavení** > **IP adresy**: Když vytvoříte aplikaci IP vrstvy SSL (Secure Sockets) ve vaší službě ASE, potřebujete adresu IP SSL. Pokud chcete získat, musí vaše služba ASE adresy IP SSL, které vlastní, které mohou být přiděleny. Po vytvoření prostředí ASE má jednu adresu IP SSL pro tento účel, ale můžete přidat další. Není zpoplatněny pro další SSL IP adresy, jak je znázorněno v [služby App Service – ceny][AppServicePricing] (v oddílu na připojení SSL). Další cena je cena IP SSL.

**Nastavení** > **front-Endového fondu** / **fondy pracovních procesů**: Každá z těchto oken fondu prostředků vám umožňuje zobrazit informace pouze o tento fond zdrojů, kromě toho, že ovládací prvky plně škálování tento fond zdrojů.  

Základní okna pro každý fond zdrojů obsahuje graf pomocí metrik pro tento fond zdrojů. Stejně jako u grafů v okně služby ASE můžete přejít do grafu a nastavit výstrahy podle potřeby. Nastavení upozornění v okně služby ASE pro konkrétní prostředek fondu udělá to stejné jako to z fondu zdrojů. Z fondu pracovních procesů **nastavení** okno, budete mít přístup ke všem aplikacím nebo plány služby App Service, které běží v tomto fondu pracovních procesů.

![Nastavení fondu pracovních procesů uživatelského rozhraní][5]

### <a name="portal-scale-capabilities"></a>Možnosti škálování na portálu
Existují tři operace škálování:

* Změna počtu IP adres ve službě ASE, které jsou k dispozici pro použití protokolu IP SSL.
* Změna velikosti výpočetního prostředku, který se používá ve fondu zdrojů.
* Změna počtu výpočetních prostředků, které se používají ve fondu zdrojů ručně nebo pomocí automatického škálování.

Na portálu existují tři způsoby, jak řídit počet serverů, které máte ve vašich fondů zdrojů:

* Operace škálování z hlavního okno služby ASE v horní části. Můžete provádět více škálování změny konfigurace fondů front-endu a pracovního procesu. Všechny jsou použity jako jediná operace.
* Ruční škálování operace z fondu prostředků jednotlivých **škálování** okno, které je pod **nastavení**.
* Automatické škálování, který jste nastavili z fondu prostředků jednotlivých **škálování** okno.

Pokud chcete použít operace škálování v okně služby ASE, jezdcem množství a uložte. Toto uživatelské rozhraní také podporuje změnu velikosti.  

![Škálování uživatelského rozhraní][6]

Pokud chcete využít funkce ruční nebo automatické škálování fondu konkrétní prostředek, přejděte na **nastavení** > **front-Endového fondu adres** / **fondy pracovních procesů** jako je to vhodné. Pak otevřete na fond, který chcete změnit. Přejděte na **nastavení** > **horizontální navýšení kapacity** nebo **nastavení** > **vertikálně navýšit kapacitu**. **Horizontální navýšení kapacity** okno umožňuje řídit množství instance. **Vertikálně navýšit kapacitu** můžete nastavit velikost prostředku.  

![Nastavení škálování uživatelského rozhraní][7]

## <a name="fault-tolerance-considerations"></a>Aspekty odolnosti proti chybám
Můžete nakonfigurovat službu App Service Environment používat až 55 celkový počet výpočetních prostředků. Tyto 55 výpočetní prostředky jenom 50 umožňuje hostovat úlohy. Důvod pro to má dva účely. Existuje minimálně 2 front-endu výpočetní prostředky.  Který zůstane až do 53 k podpoře přidělení fondu pracovních procesů. Aby bylo možné zajistit odolnost proti chybám, musíte mít další výpočetní prostředek, který je přidělen dle následujících pravidel:

* Každý fond pracovních procesů musí aspoň 1 dalšího výpočetního prostředku, který není k dispozici pro přiřazení úloh.
* Když počtu využívaných výpočetních prostředků ve fondu pracovních procesů překročí určitou hodnotu, jiné výpočetních prostředků je potřeba pro odolnost proti chybám. Toto není případ ve front-endový fond.

V každém fondu jednoho pracovního jsou požadavky odolnosti proti chybám, které pro danou hodnotu X prostředky přiřazené k fondu pracovních procesů:

* Pokud je v rozmezí 2 až 20 X, množství použitelné výpočetní prostředky, které můžete použít pro úlohy je X-1.
* Pokud je v rozmezí 21 až 40 X, množství použitelné výpočetní prostředky, které můžete použít pro úlohy je X-2.
* Pokud X je 41 až 53, je velikost použitelné výpočetní prostředky, které můžete použít pro úlohy X-3.

Nároků má 2 front-endové servery a 2 pracovní procesy.  Pomocí výše uvedených příkazů pak, tady je pár příkladů vyjasnit:  

* Pokud máte v jeden fond pracovních procesů 30, můžete je 28 použít k hostování zatížení.
* Pokud jeden fond obsahuje 2 pracovní procesy, pak 1 umožňuje hostovat úlohy.
* Pokud máte 20 pracovních procesů v jeden fond, pak 19 slouží k hostování zatížení.  
* Pokud máte 21 pracovních procesů v jeden fond, lze potom stále pouze 19 hostovat úlohy.  

Je důležité aspekty odolnosti proti chybám, ale je potřeba mít na paměti při škálování nad určité hranice. Pokud chcete přidat další kapacity přejít z 20 instancí, přejděte do 22 nebo vyšší protože 21 nepřidává žádné další kapacitu. Totéž platí, bude vyšší než 40, kde je nejbližší číslo, který přidá kapacity 42.  

## <a name="deleting-an-app-service-environment"></a>Odstranění služby App Service Environment
Pokud chcete odstranit službu App Service Environment, jednoduše použijte **odstranit** akce v horní části okna služby App Service Environment. Když to uděláte, zobrazí se výzva k zadání názvu služby App Service Environment potvrďte, že Opravdu chcete provést. Všimněte si, že při odstranění služby App Service Environment můžete odstranit veškerý obsah v ní také.  

![Odstranění služby App Service Environment uživatelského rozhraní][9]  

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
