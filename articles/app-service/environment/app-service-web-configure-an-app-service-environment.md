---
title: Postup konfigurace aplikační službu prostředí v1
description: Konfiguraci, správu a monitorování v1 App Service Environment
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
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837215"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurování aplikace, služby prostředí v1

> [!NOTE]
> Tento článek je o v1 App Service Environment.  Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Na vysoké úrovni služby Azure App Service Environment se skládá z několika hlavní součásti:

* Výpočetní prostředky, které běží ve službě hostované služby App Service Environment
* Úložiště
* Databáze
* Classic(V1) nebo prostředků Azure Manager(V2) virtuální síť (VNet) 
* Podsíť s službu App Service Environment hostované v ní

### <a name="compute-resources"></a>Výpočetní prostředky
Použijete výpočetní prostředky pro vaše fondy zdrojů čtyři.  Každé aplikaci služby prostředí (App Service Environment) obsahuje sadu front-end a tři fondy možné pracovních procesů. Nemusíte používat všechny tři pracovní fondy – Pokud chcete, můžete stačí použít jeden nebo dva kusy.

Hostitelé ve fondech zdrojů (front-end a pracovníci) nejsou přímo přístupné pro klienty. Nelze použít protokol RDP (Remote Desktop) připojit se k nim, změnit jejich zřizování nebo fungují jako správce na ně.

Můžete nastavit prostředků fondu množství a velikosti. V App Service Environment máte čtyři možnosti velikosti, které jsou označeny P1 prostřednictvím P4. Podrobnosti o těchto velikosti a jejich cenách najdete v tématu [služby App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).
Změna počtu a velikosti se nazývá operace škálování.  Operace pouze jeden škálování může být v průběhu najednou.

**Front-končí**: skončení front jsou koncové body protokolu HTTP nebo HTTPS pro vaše aplikace, které jsou uložené v vaší App Service Environment. Úlohy nelze spustit v skončení front.

* App Service Environment začíná dva P2s, což je dostačující pro procesy vývoje/testování a nízké úrovně produkčním prostředí. Důrazně doporučujeme P3s pro průměrné pro velkou produkčním prostředí.
* Průměrné až velkou produkčním prostředí doporučujeme, abyste měli aspoň čtyři P3s zajistit nejsou dostatečná front-end spuštěn, když dojde k plánované údržby. Aktivity plánované údržby se otevře dolů jeden front-endu najednou. To snižuje celkové dostupné kapacity front-end během údržby aktivity.
* Front-end může trvat až jednu hodinu zřídit. 
* Pro další doladění škálování, měli byste sledovat procento využití procesoru, paměti a metriky aktivních požadavků pro front-endu fond. Pokud procenta využití procesoru nebo paměti jsou vyšší než 70 procent při spuštění P3s, přidejte další front-end. Pokud hodnota aktivních požadavků zobrazí průměr na 15 000 na 20 000 požadavky na front-endu, měli byste také přidat další front-end. Obecným cílem je zachování 70 % využití procesoru a paměti procenta níže a aktivních požadavků průměrování na nižší než 15 000 požadavků za přední koncovou když používáte P3s.  

**Pracovníci**: zaměstnanci jsou, kde vaše aplikace ve skutečnosti spuštěna. Škálovat vaše plány služby App Service, používající si pracovní procesy ve fondu přidružené pracovního procesu.

* Nelze přidat okamžitě pracovních procesů. Může trvat až jednu hodinu zřídit.
* Velikost výpočetních prostředků pro žádného fondu škálování bude trvat < 1 hodina na doménu aktualizace. V App Service Environment nejsou 20 aktualizaci domény. Pokud můžete škálovat výpočetní velikost fondu pracovních procesů 10 instancemi, může trvat až 10 hodin.
* Pokud změníte velikost výpočetní prostředky, které se používají ve fondu pracovních procesů, způsobí cold spustí aplikace, které jsou spuštěné v tomto fondu pracovních procesů.

Nejrychlejší způsob, jak změnit velikost výpočetních prostředků fondu pracovních procesů, které neběží žádné aplikace je:

* Snižovat množství pracovníkům 2.  Minimální vertikálně velikost na portálu je 2. To bude trvat několik minut se zrušit přidělení vaše instance. 
* Vyberte novou velikost výpočetních a počet instancí. Zde bude trvat až 2 hodin.

Pokud vaše aplikace vyžadují větší velikost výpočetních prostředků, nelze využít výhod předchozí pokyny. Místo změníte velikost fondu pracovních procesů, který je hostitelem tyto aplikace, můžete naplnit jiný pracovní fond s pracovníky požadované velikosti a přesunout do tohoto fondu aplikací.

* Vytvořte další instance velikost potřebných výpočtů v jiném fondu pracovního procesu. Bude to trvat jednu hodinu, k dokončení.
* Přiřadit vaše plány služby App Service, které jsou hostiteli aplikace, které potřebují větší velikost do fondu nově nakonfigurovaná pracovního procesu. Jedná se o rychlé operaci, která by měla trvat méně než minutu.  
* Pokud již nejsou potřebné tyto nepoužívané instance snižovat první fondu pracovních procesů. Tato operace trvá několik minut.

**Automatické škálování**: jeden z nástroje, které mohou pomoci při správě vaší spotřeby výpočetních prostředků je automatické škálování. Můžete použít automatické škálování pro front-end nebo fondy pracovních procesů. Můžete provádět akce, například zvýšení vaší instance libovolného typu fondu ráno a snížit večer. Nebo možná přidáte instancí, když počet pracovních procesů, které jsou dostupné ve fondu pracovních procesů klesne pod určitou mez.

Pokud chcete nastavit pravidla škálování kolem metrika fondu výpočetních prostředků a potom mějte na paměti ve chvíli, zřizování vyžaduje. Další podrobnosti o automatické škálování prostředí App Service najdete v tématu [postup konfigurace automatického škálování ve službě App Service Environment][ASEAutoscale].

### <a name="storage"></a>Úložiště
Každý App Service Environment je nakonfigurovaný s 500 GB úložiště. Tento prostor se používá napříč všechny aplikace app Service Environment. Tento prostor úložiště je součástí App Service Environment a nelze aktuálně přepnout používat váš prostor úložiště. Pokud provádíte úpravy ke směrování virtuální sítě nebo zabezpečení, je potřeba stále povolit přístup k úložišti Azure – nebo nemůže fungovat App Service Environment.

### <a name="database"></a>Databáze
Databáze obsahuje informace, které definují prostředí a také informace o aplikacích, které jsou spuštěny v něm. Toto je příliš součástí předplatného Azure uchovávat. Není něco, co máte přímé možnost manipulace s. Pokud provádíte úpravy ke směrování virtuální sítě nebo zabezpečení, je potřeba stále povolit přístup k Azure SQL – nebo nemůže fungovat App Service Environment.

### <a name="network"></a>Síť
Virtuální síť, která se používá s vaší App Service Environment může být ten, který jste provedli, když jste vytvořili App Service Environment nebo ten, který jste předem. Když vytvoříte podsítě během vytváření App Service Environment, vynutí si App Service Environment ve stejné skupině prostředků jako virtuální síť. Pokud potřebujete skupině prostředků používané vaší App Service Environment jiné než u vaší virtuální sítě, budete muset vytvořit váš App Service Environment pomocí šablony správce prostředků.

Existují některá omezení ve virtuální síti, který se používá pro App Service Environment:

* Virtuální síť musí být regionální virtuální síť.
* Existuje, je potřeba podsíť s 8 nebo více adres, které se nasadí App Service Environment.
* Po podsítě se používá k hostování App Service Environment, nebude možné změnit rozsah adres podsítě. Z tohoto důvodu doporučujeme, aby podsíť obsahuje minimálně 64 adresy pro přizpůsobení případnému budoucímu růstu App Service Environment.
* Může být nic jiného v podsíti, ale App Service Environment.

Na rozdíl od hostovanou službu, která obsahuje App Service Environment [virtuální sítě] [ virtualnetwork] a podsíť je za uživatelský ovládací prvek.  Můžete spravovat virtuální sítě prostřednictvím uživatelského rozhraní virtuální sítě nebo prostředí PowerShell.  App Service Environment se dá nasadit v klasický nebo virtuální sítě Resource Manager.  Portál a rozhraní API prostředí se mírně liší Classic a Resource Manager virtuálních sítí, ale prostředí App Service Environment je stejný.

Virtuální síť, která se používá k hostování App Service Environment můžete použít buď soukromé RFC1918 IP adresy nebo ji můžete použít veřejné IP adresy.  Pokud chcete použít rozsah adres IP, která není předmětem RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) je třeba vytvořit virtuální síť a podsíť pro použití ve vašem App Service Environment před vytvoření App Service Environment.

Protože tato možnost umístí do virtuální sítě Azure App Service, znamená to, že vaše aplikace, které jsou hostované v vaší App Service Environment nyní přístup k prostředkům, které jsou k dispozici prostřednictvím ExpressRoute nebo virtuální privátní sítě site-to-site (VPN) přímo. Aplikace, které jsou v rámci služby App Service Environment nevyžadují další funkce sítě pro přístup k prostředkům, které jsou k dispozici pro virtuální síť, která je hostitelem služby App Service Environment. To znamená, že nemusíte mít přístup k prostředkům v nebo připojení k virtuální síti pomocí integrace virtuální sítě nebo hybridní připojení. Přesto můžete obou těchto funkcí když pro přístup k prostředkům v sítích, které nejsou připojené k virtuální síti.

Například můžete integrace virtuální sítě k integraci s virtuální síť, která je v rámci vašeho předplatného, ale není připojený k virtuální síti, který vaše App Service Environment je v. Hybridní připojení můžete stále taky pro přístup k prostředkům, které jsou v jiných sítích, podobně jako obvykle.  

Pokud máte virtuální síť nakonfigurované připojení VPN pomocí ExpressRoute byste měli vědět některé směrování potřeb, které má App Service Environment. Existují některé konfigurace trasy definované uživatelem (UDR), které jsou kompatibilní s App Service Environment. Další podrobnosti o spuštění App Service Environment ve virtuální síti s ExpressRoute najdete v tématu [spuštění služby App Service Environment ve virtuální síti s ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpečení příchozí provoz
Existují dvě metody primární řídit příchozí provoz vaší App Service Environment.  Groups(NSGs) zabezpečení sítě můžete použít k řízení jaké IP adresy můžete přístup k vaší App Service Environment podle postupu popsaného tady [tom, jak řídit příchozí provoz ve službě App Service Environment](app-service-app-service-environment-control-inbound-traffic.md) a můžete také nakonfigurovat vaše App Service Environment s interním nástrojem pro vyrovnávání zatížení (ILB).  Tyto funkce lze také použít společně Pokud chcete omezit přístup pomocí skupiny Nsg k vaší ILB App Service Environment.

Při vytváření App Service Environment, vytvoří virtuální IP adresu ve vaší virtuální síti.  Existují dva typy virtuálních IP adres, externí i interní.  Při vytváření App Service Environment pomocí externí VIP vašich aplikací ve vaší App Service Environment budou přístupné přes internet směrovatelné IP adresu. Když vyberete interní vaší App Service Environment bude nakonfigurován s ILB a nebude se přímo přístup z Internetu.  App Service Environment ILB stále vyžaduje externí VIP, ale používá se pouze pro přístup k Azure správy a údržby.  

Během vytváření App Service Environment ILB zadejte subdomény používané App Service Environment ILB a bude nutné spravovat vlastní DNS subdomény, které zadáte.  Vzhledem k tomu, že nastavíte název subdomény, musíte taky ke správě certifikát používaný pro přístup k protokolu HTTPS.  Po vytvoření App Service Environment se výzva k zadání certifikátu.  Další informace o vytváření a používání App Service Environment ILB číst [interní pro vyrovnávání zatížení pomocí služby App Service Environment][ILBASE]. 

## <a name="portal"></a>Portál
Můžete spravovat a monitorovat služby App Service Environment pomocí uživatelského rozhraní na portálu Azure. Pokud máte App Service Environment, pak se pravděpodobně zobrazí symbol služby App Service na vaše bočním panelu. Tento symbol se používá k reprezentování prostředí App Service na portálu Azure:

![Symbol App Service Environment][1]

Chcete-li spustit uživatelské rozhraní, které jsou uvedeny všechny vaše prostředí App Service, můžete použít ikonu nebo vyberte na dvojitou šipku (">" symbol) v dolní části na bočním panelu vyberte prostředí App Service. Pokud vyberete jednu z ASEs uvedené, otevřete uživatelské rozhraní, které se používá ke sledování a správě ho.

![Uživatelské rozhraní pro monitorování a Správa služby App Service Environment][2]

Toto první okno obsahuje některé vlastnosti týkající se vaší App Service Environment, společně s metriky grafu na fond zdrojů. Některé vlastnosti, které jsou zobrazeny v **Essentials** bloku jsou i hypertextové odkazy, které se otevře okno, které je k ní přidružena. Například můžete vybrat **virtuální sítě** název, který otevře uživatelské rozhraní přidružené virtuální síť, která vaše App Service Environment běží v. **Plánů služby App Service** a **aplikace** každý otevřete okna, které obsahují tyto položky, které jsou ve vaší App Service Environment.  

### <a name="monitoring"></a>Monitorování
Grafy umožňují zobrazit různých metrik výkonu v každém fondu zdrojů. Front-endu fondu můžete monitorovat průměrné využití procesoru a paměti. Pro fondy pracovních procesů může sledovat množství, které se používá a množství, které je k dispozici.

Použití více App Service, které můžete provést plány pracovní procesy ve fondu pracovních procesů. Úlohy není distribuován stejným způsobem, jak se servery front-end, takže využití procesoru a paměti neposkytují mnohem cestě užitečné informace. Důležitější sledovat, kolik pracovních procesů, které jste už použili a jsou k dispozici – je obzvláště pokud spravujete tento systém ostatním uživatelům.  

Můžete taky všechny metriky, které lze sledovat v grafech nastavit výstrahy. Nastavení výstrah zde funguje stejně jako jinde v App Service. Výstrahu můžete nastavit od buď **výstrahy** uživatelského rozhraní část nebo z podrobnostem všechny metriky uživatelského rozhraní a výběrem **přidat výstraha**.

![Metriky uživatelského rozhraní][3]

Metriky, které byly právě popsané jsou metriky služby App Service Environment. Existují také metriky, které jsou k dispozici na úrovni plán služby App Service. Toto je, kde sledování procesoru a paměti umožňuje spoustu smysl.

V App Service Environment všechny plány služby App Service jsou vyhrazené plány služby App Service. To znamená, že jen aplikace, které jsou spuštěné v hostitelích přidělené, že plán služby App Service jsou aplikace v tomto plánu služby App Service. Chcete-li zobrazit podrobnosti na plán služby App Service, zprovoznit plán služby App Service ze všech seznamů v uživatelském rozhraní App Service Environment nebo z **plánů Procházet služby App Service** (které jsou uvedeny všechny z nich).   

### <a name="settings"></a>Nastavení
V okně App Service Environment není **nastavení** oddíl, který obsahuje několik důležitých funkcí:

**Nastavení** > **vlastnosti**: **nastavení** automaticky otevře se okno při nasazování okně vaší App Service Environment. V horní části **vlastnosti**. Existuje několik položek sem, které jsou redundantní co se zobrazí v **Essentials**, ale co je velmi užitečná je **virtuální IP adresy**, a také **odchozí IP adresy**.

![Okno nastavení a vlastností][4]

**Nastavení** > **IP adresy**: Když vytvoříte aplikaci IP Secure Sockets Layer (SSL) ve vašem App Service Environment, potřebujete adresu IP SSL. Chcete-li získat jeden, musí vaše App Service Environment adres IP protokolu SSL, které vlastní, které mohou být přiděleny. Když je vytvořen App Service Environment, má jednu IP SSL adresu pro tento účel, ale můžete přidat další. Není zpoplatněny pro další SSL IP adresy, jak je znázorněno v [služby App Service – ceny][AppServicePricing] (v oddílu na připojení SSL). Další cena je cena IP SSL.

**Nastavení** > **Front End fondu** / **fondy pracovních procesů**: každý z těchto oknech fondu prostředků nabízí možnost zobrazit informace pouze na tento fond zdrojů v Přidání poskytovat ovládací prvky plně škálování tento fond zdrojů.  

Základní okna pro každý fond zdrojů obsahuje graf s metriky pro tento fond zdrojů. Stejně jako s grafy v okně App Service Environment, můžete přejít do grafu a nastavit výstrahy podle potřeby. Nastavení v okně App Service Environment pro fond zdrojů pro konkrétní výstrahu provede stejnou akci jako provádění z fondu zdrojů. Z fondu pracovních procesů **nastavení** okno, máte přístup ke všem aplikacím, nebo které běží v tomto fondu pracovní plánů služby App Service.

![Nastavení fondu pracovní uživatelského rozhraní][5]

### <a name="portal-scale-capabilities"></a>Funkce portálu škálování
Existují tři operace škálování:

* Změna počtu IP adres App Service Environment, které jsou k dispozici pro použití protokolu SSL IP.
* Změna velikosti výpočtový prostředek, který se používá ve fondu zdrojů.
* Změna počtu výpočetní prostředky, které se používají ve fondu zdrojů, buď ručně, nebo prostřednictvím automatické škálování.

Na portálu existují tři způsoby řízení počtu serverů, které máte ve fondech zdrojů:

* Operace škálování v hlavním okně App Service Environment v horní části. Můžete provádět více škálování změny konfigurace fondů front-endu a pracovního procesu. Všechny jsou použity jako jediná operace.
* Operace ruční škálování z fondu zdrojů jednotlivých **škálování** okno, které je pod **nastavení**.
* Automatické škálování, který jste nastavili z fondu zdrojů jednotlivých **škálování** okno.

Pokud chcete používat operace škálování v okně App Service Environment, jezdcem na množství a uložte. Toto uživatelské rozhraní podporuje také změně velikosti.  

![Škálování uživatelského rozhraní][6]

Fond zdrojů konkrétní využít funkce ruční nebo automatické škálování, přejděte na **nastavení** > **Front End fondu** / **fondy pracovních procesů** jako vhodné. Potom otevře fondu, který chcete změnit. Přejděte na **nastavení** > **škálovat** nebo **nastavení** > **škálovat**. **Horizontální navýšení kapacity** okno vám umožňuje řídit množství instance. **Vertikálně navýšit kapacitu** vám umožňuje řídit velikost prostředků.  

![Nastavení škálování uživatelského rozhraní][7]

## <a name="fault-tolerance-considerations"></a>Důležité informace o odolnosti proti chybám
Můžete nakonfigurovat služby App Service Environment využívat až 55 celkový výpočetní prostředky. Tyto 55 výpočetní prostředky jenom 50 slouží k hostování zatížení. Důvodem je dva účely. Není k dispozici minimálně 2 front-end výpočetní prostředky.  Která zůstane až do 53 pro podporu přidělení fondu pracovních procesů. Aby bylo možné zajistit odolnost proti chybám, je potřeba mít na další výpočetní prostředek, který je přidělen podle následujících pravidel:

* Každý fond pracovních procesů musí alespoň 1 Další výpočtový prostředek, který není k dispozici pro přiřazení zatížení.
* Když množství výpočetní prostředky ve fondu pracovních procesů překročí určitou hodnotu, jiné výpočetních prostředků je vyžadována pro odolnost proti chybám. To tak není ve fondu front-endu.

V rámci žádného fondu jednoho pracovního jsou požadavky odolnosti proti chybám, pro danou hodnotu X zdroje přiřazené k fondu pracovních procesů:

* Pokud X je mezi 2 a 20, množství použitelné výpočetní prostředky, které můžete použít pro úlohy je X-1.
* Pokud X je 21 až 40, množství použitelné výpočetní prostředky, které můžete použít pro úlohy je X-2.
* Pokud X je mezi 41 a 53, množství použitelné výpočetní prostředky, které můžete použít pro úlohy je X-3.

Nároků má 2 front-end servery a 2 pracovní procesy.  S výše uvedené příkazy pak, zde je několik příkladů o vysvětlení:  

* Pokud máte 30 pracovních procesů v jednom fondu, můžete 28 z nich použít k hostování zatížení.
* Pokud máte 2 pracovní procesy v jednom fondu, můžete 1 použít k hostování zatížení.
* Pokud máte 20 pracovních procesů v jednom fondu, můžete 19 použít k hostování zatížení.  
* Pokud máte 21 pracovních procesů v jednom fondu, pak stále pouze 19 slouží k hostování zatížení.  

Je důležité aspekt odolnost proti chybám, ale musíte mít na paměti, jako je škálovat prahovou hodnotu pro určité. Pokud chcete zvýšit kapacitu přejdete z instancí, 20, pak přejděte na 22 nebo vyšší protože 21 nepřidává žádné další kapacitu. Platí to budete výše 40, kde je další číslo, které přidá kapacity 42.  

## <a name="deleting-an-app-service-environment"></a>Odstranění služby App Service Environment
Pokud chcete provést odstranění služby App Service Environment, jednoduše použijte **odstranit** akce v horní části okna služby App Service Environment. Když to uděláte, budete vyzváni k zadání názvu služby App Service Environment potvrďte, že Opravdu chcete provést. Všimněte si, že při odstranění služby App Service Environment, můžete odstranit veškerý obsah v něm také.  

![Odstranění služby App Service Environment uživatelského rozhraní][9]  

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [postup vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

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
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
