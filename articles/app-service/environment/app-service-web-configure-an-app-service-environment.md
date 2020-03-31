---
title: Konfigurace služby ASE v1
description: Konfigurace, správa a monitorování prostředí služby App Service v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687261"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurace prostředí služby App Service v1

> [!NOTE]
> Tento článek je o prostředí služby App Service v1.  K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
> 

## <a name="overview"></a>Přehled
Na vysoké úrovni azure app service prostředí se skládá z několika hlavních součástí:

* Výpočetní prostředky spuštěné v hostované službě Prostředí služby App Service Environment
* Úložiště
* Databáze
* Klasická virtuální síť Azure (V1) nebo Správce prostředků (V2) 
* Podsíť s hostovnou služby Prostředí služby App Service, která je v ní spuštěna

### <a name="compute-resources"></a>Výpočetní prostředky
Výpočetní prostředky se používají pro čtyři fondy prostředků.  Každé prostředí služby App Service (ASE) má sadu front-endů a tři možné fondy pracovních procesů. Není nutné používat všechny tři fondy pracovních procesů – pokud chcete, stačí použít jeden nebo dva.

Hostitelé ve fondech prostředků (front-endy a pracovníci) nejsou přímo přístupné klientům. Nelze použít protokol RDP (Remote Desktop Protocol) k připojení k nim, změnit jejich zřizování nebo jednat jako správce na ně.

Můžete nastavit množství a velikost fondu zdrojů. Ve správě ase máte čtyři možnosti velikosti, které jsou označeny P1 až P4. Podrobnosti o těchto velikostech a jejich cenách najdete v [tématu Ceny služby App Service](https://azure.microsoft.com/pricing/details/app-service/).
Změna množství nebo velikosti se nazývá operace měřítka.  Současně může probíhá pouze jedna operace škálování.

**Front-endy**: Přední části jsou koncové body HTTP/HTTPS pro vaše aplikace, které jsou uloženy ve službě ASE. Nespouštějte úlohy na front-endu.

* ASE začíná dvěma P2s, což je dostačující pro vývoj a testování úloh a nízkoúrovňové produkční úlohy. Důrazně doporučujeme P3s pro střední až těžké produkční úlohy.
* Pro středně těžké až těžké produkční úlohy doporučujeme, abyste měli alespoň čtyři P3s, abyste zajistili, že při plánované údržbě běží dostatek front-endů. Plánované aktivity údržby sníží jeden front-end najednou. To snižuje celkovou dostupnou front-endovou kapacitu během činností údržby.
* Zajištění předních částek může trvat až hodinu. 
* Pro další škálování jemné doladění, měli byste sledovat procento procesoru, procento paměti a aktivní požadavky metriky pro front-end fondu. Pokud jsou procenta procesoru nebo paměti při spuštění P3s vyšší než 70 procent, přidejte další front-endy. Pokud hodnota Aktivní požadavky zprůměruje 15 000 až 20 000 požadavků na front-end, měli byste také přidat další front-endy. Celkovým cílem je udržet procenta procesoru a paměti pod 70 % a aktivní požadavky v průměru pod 15 000 požadavků na front-end při spuštění p3s.  

**Pracovníci**: Pracovníci jsou místo, kde vaše aplikace skutečně běží. Když navertíte své plány služby App Service, který využívá pracovníky v přidruženém fondu pracovníků.

* Nelze okamžitě přidávat pracovníky. Jejich zajištění může trvat až hodinu.
* Škálování velikosti výpočetního prostředku pro libovolný fond bude trvat < 1 hodinu na aktualizační doménu. V systému Služby ASE je 20 aktualizačních domén. Pokud jste škálovali výpočetní velikost fondu pracovních prostředků s 10 instancemi, může trvat až 10 hodin.
* Pokud změníte velikost výpočetních prostředků, které se používají ve fondu pracovních prostředků, způsobíte studené spuštění aplikací, které jsou spuštěny v tomto fondu pracovních prostředků.

Nejrychlejší způsob, jak změnit velikost výpočetního prostředku fondu pracovních prostředků, který neběží žádné aplikace, je:

* Zmenšete počet pracovníků na 2.  Minimální velikost zmenšení na portálu je 2. Bude trvat několik minut, než se vaše instance navrátí. 
* Vyberte novou výpočetní velikost a počet instancí. Odtud to bude trvat až 2 hodiny.

Pokud vaše aplikace vyžadují větší velikost výpočetního prostředku, nemůžete využít předchozí pokyny. Namísto změny velikosti fondu pracovních sil, který je hostitelem těchto aplikací, můžete naplnit jiný fond pracovních sil s pracovníky požadované velikosti a přesunout aplikace do tohoto fondu.

* Vytvořte další instance potřebné výpočetní velikosti v jiném fondu pracovních prostředků. Dokončení bude trvat až hodinu.
* Znovu přiřaďte plány služby App Service, které hostují aplikace, které potřebují větší velikost, do nově nakonfigurovaného fondu pracovních procesů. Jedná se o rychlou operaci, která by měla trvat méně než minutu.  
* Škálování na prvním fondu pracovních procesů, pokud už tyto nepoužívané instance nepotřebujete. Tato operace trvá několik minut.

**Automatické škálování**: Jedním z nástrojů, které vám můžou pomoct se správou spotřeby výpočetních prostředků, je automatické škálování. Automatické škálování můžete použít pro front-end ové nebo pracovní fondy. Můžete dělat věci, jako je zvýšení instance libovolného typu bazénu v dopoledních hodinách a snížit ji ve večerních hodinách. Nebo možná můžete přidat instance, kdy počet pracovníků, které jsou k dispozici ve fondu pracovníků klesne pod určitou prahovou hodnotu.

Pokud chcete nastavit pravidla automatického škálování kolem metriky fondu výpočetních prostředků, pak mějte na paměti, čas, který vyžaduje zřizování. Další podrobnosti o automatickéškálování prostředí služby App Service najdete v tématu [Jak nakonfigurovat automatické škálování v prostředí služby App Service][ASEAutoscale].

### <a name="storage"></a>Úložiště
Každá služba ASE je konfigurována s úložištěm o velikosti 500 GB. Tento prostor se používá ve všech aplikacích ve službě ASE. Tento úložný prostor je součástí služby ASE a momentálně nelze přepnout na využití úložného prostoru. Pokud provádíte úpravy směrování nebo zabezpečení virtuální sítě, musíte stále povolit přístup k Azure Storage – nebo služba ASE nemůže fungovat.

### <a name="database"></a>Databáze
Databáze obsahuje informace, které definují prostředí, stejně jako podrobnosti o aplikacích, které jsou spuštěny v něm. To je také součástí předplatného drženého Azure. Není to něco, s čím bys mohl manipulovat. Pokud provádíte úpravy směrování nebo zabezpečení virtuální sítě, musíte stále povolit přístup k SQL Azure – nebo služba ASE nemůže fungovat.

### <a name="network"></a>Network (Síť)
Virtuální síť, která se používá s vaší sekatou se může být ten, který jste provedli při vytváření ase nebo ten, který jste provedli dopředu. Při vytváření podsítě během vytváření služby ASE vynutí služby ASE být ve stejné skupině prostředků jako virtuální síť. Pokud potřebujete, aby se skupina prostředků používaná službou ASE lišila od skupiny virtuální sítě, musíte vytvořit službu ASE pomocí šablony správce prostředků.

Existují určitá omezení ve virtuální síti, která se používá pro službu ASE:

* Virtuální síť musí být místní virtuální síť.
* Musí existovat podsíť s 8 nebo více adres, kde je nasazena služby ASE.
* Po podsíť se používá k hostování ase, rozsah adres podsítě nelze změnit. Z tohoto důvodu doporučujeme, aby podsíť obsahuje alespoň 64 adres pro budoucí růst ase.
* V podsíti nemůže být nic jiného než ase.

Na rozdíl od hostované služby, která obsahuje službu ASE, [virtuální síť][virtualnetwork] a podsíť jsou pod uživatelským ovládacím prvkem.  Virtuální síť můžete spravovat prostřednictvím hlavního nastavení virtuální sítě nebo prostředí PowerShell.  Službu ASE lze nasadit ve virtuální síti Klasické nebo Správce prostředků.  Prostředí portálu a rozhraní API se mírně liší mezi virtuálními sítěmi Classic a Resource Manager, ale prostředí služby ASE je stejné.

Virtuální síť, která se používá k hostování služby ASE můžete použít buď privátní RFC1918 IP adresy nebo můžete použít veřejné IP adresy.  Pokud chcete použít rozsah IP adres, který není pokryt RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), pak je třeba vytvořit virtuální síť a podsíť, které mají být použity vaší ase před vytvořením ase.

Vzhledem k tomu, že tato funkce umístí službu Azure App Service do vaší virtuální sítě, znamená to, že vaše aplikace, které jsou hostované ve vaší službě ASE, teď mají přístup k prostředkům, které jsou dostupné prostřednictvím expressroute nebo virtuálních privátních sítí (VIRTUÁLNÍ SÍTĚ) přímo. Aplikace, které jsou v prostředí služby App Service, nevyžadují další síťové funkce pro přístup k prostředkům dostupným ve virtuální síti, která je hostitelem prostředí služby App Service. To znamená, že není nutné používat integrace virtuální sítě nebo hybridní připojení se dostat k prostředkům ve virtuální síti nebo připojení k virtuální síti. Obě tyto funkce můžete stále používat pro přístup k prostředkům v sítích, které nejsou připojeny k vaší virtuální síti.

Například můžete použít integraci virtuální sítě k integraci s virtuální sítí, která je ve vašem předplatném, ale není připojen k virtuální síti, ve které se vaše sekcí se nachází. Hybridní připojení můžete také použít pro přístup k prostředkům, které jsou v jiných sítích, stejně jako obvykle.  

Pokud máte virtuální síť nakonfigurovanou pomocí technologie ExpressRoute VPN, měli byste si být vědomi některých potřeb směrování, které služba ASE má. Existují některé uživatelem definované konfigurace trasy (UDR), které nejsou kompatibilní se službou ASE. Další podrobnosti o spuštění služby ASE ve virtuální síti s ExpressRoute najdete [v tématu Spuštění prostředí služby App Service ve virtuální síti s ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpečení příchozího provozu
Existují dvě primární metody pro řízení příchozího provozu do služby ASE.  Pomocí skupin zabezpečení sítě (NSG) můžete řídit, jaké IP adresy mají přístup k vaší službě ASE, jak je popsáno zde [Jak řídit příchozí provoz v prostředí služby App Service](app-service-app-service-environment-control-inbound-traffic.md) a můžete také nakonfigurovat službu ASE pomocí interního vyrovnávání zatížení (ILB).  Tyto funkce lze také použít společně, pokud chcete omezit přístup pomocí nsg s ase na vaše Služby ASE ILB.

Když vytvoříte sedá, vytvoří virtuální ip ve vaší virtuální síti.  Existují dva typy VIP, externí a interní.  Když vytvoříte službu ASE s externí minoutelnou službou VIP, budou vaše aplikace ve službě ASE přístupné prostřednictvím internetové ip adresy. Když vyberete Interní, vaše služby ASE budou nakonfigurovány s ILB a nebudou přímo přístupné z internetu.  Služba ASE ILB stále vyžaduje externí virtuální ip adresu, ale používá se jenom pro správu Azure a přístup k údržbě.  

Při vytváření služby ASE ilb zadáte subdoménu používanou službou ASE ILB a budete muset spravovat vlastní SLUŽBU DNS pro zadanou subdoménu.  Protože nastavíte název subdomény, musíte také spravovat certifikát používaný pro přístup https.  Po vytvoření služby ASE budete vyzváni k zadání certifikátu.  Další informace o vytváření a používání služby ASE služby ILB najdete [na čtení pomocí interního vytápěče zatížení v prostředí služby App Service][ILBASE]. 

## <a name="portal"></a>Portál
Prostředí služby App Service můžete spravovat a monitorovat pomocí ui na webu Azure Portal. Pokud máte službu ASE, pak se pravděpodobně zobrazí symbol služby App Service na postranním panelu. Tento symbol se používá k reprezentaci prostředí služby App Service na webu Azure Portal:

![Symbol prostředí služby App Service][1]

Chcete-li otevřít uhlavní počítač se seznamem všech prostředí služby App Service, můžete použít ikonu nebo vybrat dvojitou šipku (symbol ">") v dolní části postranního panelu a vybrat prostředí služby App Service. Výběrem jedné z uvedených ses otevřete ui, který se používá ke sledování a správě.

![UI pro monitorování a správu prostředí služby App Service][2]

Toto první okno zobrazuje některé vlastnosti služby ASE spolu s metrikovým grafem pro každý fond prostředků. Některé vlastnosti, které jsou zobrazeny v bloku **Essentials,** jsou také hypertextové odkazy, které otevřou okno, které je k němu přidruženo. Můžete například vybrat název **virtuální sítě** a otevřít uživatelské prostředí přidružené k virtuální síti, ve které je spuštěna vaše ase. **Plány služby App Service** a **aplikace** každý otevřít okna, které uvádějí tyto položky, které jsou ve vaší službě ASE.  

### <a name="monitoring"></a>Monitorování
Grafy umožňují zobrazit různé metriky výkonu v každém fondu zdrojů. Pro front-endového fondu můžete sledovat průměrný procesor a paměť. U fondů pracovních procesů můžete sledovat množství, které se používá, a množství, které je k dispozici.

Více plánů služby App Service můžete využít pracovníky ve fondu pracovních sil. Úloha není distribuována stejným způsobem jako u serverů front-end, takže využití procesoru a paměti neposkytují mnoho užitečných informací. Je důležitější sledovat, kolik pracovníků jste použili a jsou k dispozici – zejména pokud spravujete tento systém pro ostatní uživatele.  

Můžete také použít všechny metriky, které lze sledovat v grafech k nastavení výstrah. Nastavení výstrah zde funguje stejně jako jinde ve službě App Service. Můžete nastavit výstrahu buď z části **upozornění** uj., nebo z vrtání do libovolnémetriky uI a výběrem **Přidat výstrahu**.

![UI metriky][3]

Metriky, které byly právě diskutovány, jsou metriky prostředí služby App Service. Existují také metriky, které jsou k dispozici na úrovni plánu služby App Service. To je místo, kde sledování CPU a paměti dává velký smysl.

Ve službě ASE jsou všechny plány služby App Service vyhrazené plány služby App Service. To znamená, že jediné aplikace, které jsou spuštěny na hostitelích přidělených tomuto plánu služby App Service, jsou aplikace v tomto plánu služby App Service. Chcete-li zobrazit podrobnosti o plánu služby App Service, zaregistrujte svůj plán služby App Service z libovolného seznamu v umítce služby ASE nebo z **plánů Procházet službu aplikace** (která obsahuje všechny z nich).   

### <a name="settings"></a>Nastavení
V okně služby ASE je oddíl **Nastavení,** který obsahuje několik důležitých funkcí:

**Settings** > **Vlastnosti nastavení**: Okno **Nastavení** se automaticky otevře při zobrazení okna služby ASE. V horní části je **vlastnosti**. Existuje celá řada položek, které jsou nadbytečné na to, co vidíte v **Essentials**, ale co je velmi užitečné, je **virtuální IP adresa**, stejně jako odchozí IP **adresy**.

![Nastavení okna a vlastností][4]

**Nastavení** > **IP adres**: Při vytváření aplikace SSL (IP Secure Sockets Layer) ve vaší ase potřebujete adresu IP SSL. Chcete-li získat jeden, vaše ASE potřebuje IP SSL adresy, které vlastní, které mohou být přiděleny. Při vytvoření je vytvořena ase má jednu adresu IP SSL pro tento účel, ale můžete přidat další. Za další adresy IP SSL se účtuje poplatek, jak je znázorněno v [cenách služby App Service][AppServicePricing] (v části o připojeních SSL). Dodatečná cena je cena IP SSL.

**Nastavení** > **fondů pracovních procesů****front-end:** / Každý z těchto nožů fondu zdrojů nabízí možnost zobrazit informace pouze ve fondu prostředků a kromě toho poskytuje ovládací prvky pro úplné škálování tohoto fondu prostředků.  

Základní okno pro každý fond zdrojů poskytuje graf s metrikami pro tento fond zdrojů. Stejně jako u grafů z okna ase, můžete jít do grafu a nastavit výstrahy podle potřeby. Nastavení výstrahy z okna ase pro konkrétní fond prostředků dělá totéž jako dělat to z fondu zdrojů. Z okna **Nastavení** fondu pracovních pracovníků máte přístup ke všem plánům aplikací nebo služby App Service, které jsou spuštěny v tomto fondu pracovních procesů.

![UI nastavení fondu pracovních pracovníků][5]

### <a name="portal-scale-capabilities"></a>Možnosti škálování portálu
Existují tři operace škálování:

* Změna počtu adres IP v prostředí Služby ASE, které jsou k dispozici pro využití protokolu IP SSL.
* Změna velikosti výpočetního prostředku, který se používá ve fondu prostředků.
* Změna počtu výpočetních prostředků, které se používají ve fondu prostředků, ručně nebo pomocí automatického škálování.

Na portálu existují tři způsoby, jak řídit, kolik serverů máte ve fondech prostředků:

* Operace v měřítku z hlavního okna ASE v horní části. Můžete provést více změn konfigurace škálování front-endu a fondů pracovních procesů. Všechny jsou použity jako jedna operace.
* Ruční operace škálování z jednotlivých zdrojů fondu **měřítko** okno, který je v části **Nastavení**.
* Automatické škálování, které nastavíte z okna **škálování** jednotlivých prostředků fondu.

Chcete-li použít operaci měřítka v noži ase, přetáhněte jezdec na požadované množství a uložte. Toto ui také podporuje změnu velikosti.  

![Měřítko ui][6]

Chcete-li použít možnosti ručního nebo automatického škálování v určitém fondu prostředků, přejděte podle potřeby do **fondu** > **pracovních procesů** **front-end ového fondu.** /  Pak otevřete fond, který chcete změnit. Přejděte na **Měřítko** > **nastavení nebo** **Navýšit kapacitu** **nastavení** > . Okno **Horizontální navýšení kapacity** umožňuje řídit množství instance. **Škálování nahoru** umožňuje řídit velikost prostředků.  

![UI nastavení měřítka][7]

## <a name="fault-tolerance-considerations"></a>Aspekty odolnosti proti chybám
Prostředí služby App Service můžete nakonfigurovat tak, aby využívalo až 55 výpočetních prostředků. Z těchto 55 výpočetních prostředků lze k hostování úloh použít pouze 50. Důvod je dvojí. K dispozici jsou minimálně 2 výpočetní prostředky front-endu.  To ponechává až 53 na podporu přidělení fondu pracovníků. Chcete-li poskytnout odolnost proti chybám, musíte mít další výpočetní prostředek, který je přidělen podle následujících pravidel:

* Každý fond pracovních prostředků potřebuje alespoň 1 další výpočetní prostředek, který není k dispozici pro přiřazení pracovního vytížení.
* Když množství výpočetních prostředků ve fondu pracovních prostředků přejde nad určitou hodnotu, pak je pro odolnost proti chybám vyžadován jiný výpočetní prostředek. To není případ v front-end bazénu.

V rámci jednoho fondu pracovních procesů jsou požadavky odolnosti proti chybám, že pro danou hodnotu prostředků X přiřazených fondu pracovních pracovníků:

* Pokud X je mezi 2 a 20, množství použitelných výpočetních prostředků, které můžete použít pro úlohy je X-1.
* Pokud X je mezi 21 a 40, množství použitelných výpočetních prostředků, které můžete použít pro úlohy je X-2.
* Pokud X je mezi 41 a 53, množství použitelných výpočetních prostředků, které můžete použít pro úlohy je X-3.

Minimální nároky mají 2 front-end servery a 2 pracovníky.  S výše uvedenými prohlášeními pak, zde je několik příkladů objasnit:  

* Pokud máte 30 pracovníků v jednom fondu, pak 28 z nich lze použít k hostování úloh.
* Pokud máte 2 pracovníky v jednom fondu, pak 1 lze použít k hostování úloh.
* Pokud máte 20 pracovníků v jednom fondu, pak 19 lze použít k hostování úloh.  
* Pokud máte 21 pracovníků v jednom fondu, pak stále pouze 19 lze použít k hostování úloh.  

Aspekt odolnosti proti chybám je důležitý, ale musíte to mít na paměti, když se škálujete nad určité prahové hodnoty. Pokud chcete přidat další kapacitu z 20 instancí, přejděte na 22 nebo vyšší, protože 21 nepřidává žádnou další kapacitu. Totéž platí nad 40, kde další číslo, které přidává kapacitu, je 42.  

## <a name="deleting-an-app-service-environment"></a>Odstranění prostředí služby App Service
Pokud chcete odstranit prostředí služby App Service, pak jednoduše použijte akci **Odstranit** v horní části okna Prostředí služby Aplikace. Když toto provedete, budete vyzváni k zadání názvu prostředí služby App Service a potvrďte, že to opravdu chcete provést. Všimněte si, že když odstraníte prostředí služby App Service, odstraníte také veškerý obsah v něm.  

![Odstranění nového prostředí služby App Service][9]  

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, přečtěte si informace [o tom, jak vytvořit prostředí služby App Service](app-service-web-how-to-create-an-app-service-environment.md).

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
