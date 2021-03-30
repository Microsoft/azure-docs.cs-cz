---
title: Konfigurace pomocného mechanismu v1
description: Konfigurace, Správa a monitorování App Service Environment v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962583"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurace App Service Environment v1

> [!NOTE]
> Tento článek se týká App Service Environment v1.  Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Azure App Service Environment se na nejvyšší úrovni skládá z několika hlavních součástí:

* Výpočetní prostředky, které běží v hostované službě App Service Environment
* Storage
* Databáze
* Rozhraní Azure Classic (V1) nebo Správce prostředků (v2) Virtual Network (VNet) 
* Podsíť, ve které je spuštěná hostovaná služba App Service Environment

### <a name="compute-resources"></a>Výpočetní prostředky
Využijete výpočetní prostředky pro vaše čtyři fondy zdrojů.  Každý App Service Environment (pomocného programu) má sadu front-endu a tři možné fondy pracovních procesů. Nemusíte používat všechny tři fondy pracovních procesů – Pokud chcete, můžete použít jenom jednu nebo dvě.

Hostitelé ve fondech prostředků (front-endy a pracovní procesy) nejsou přímo přístupné pro klienty. K připojení k nim nemůžete použít protokol RDP (Remote Desktop Protocol) (RDP), měnit jejich zřizování nebo působit jako správce.

Můžete nastavit množství a velikost fondu zdrojů. V pomocném mechanismu služby máte čtyři možnosti velikosti, které jsou označené P1 až P4. Podrobnosti o těchto velikostech a jejich cenách najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).
Změna množství nebo velikosti se nazývá operace škálování.  V jednom okamžiku může probíhat jenom jedna operace škálování.

**Front-endy**: front-endy jsou koncové body HTTP/HTTPS pro vaše aplikace, které jsou uložené ve vašem pomocném mechanismu pro zápis. Nespouštíte úlohy na front-endy.

* Pomocného programu začíná dvěma P2s, což je dostatečné pro úlohy pro vývoj a testování a pro produkční úlohy nízké úrovně. Důrazně doporučujeme P3S pro středně náročné provozní úlohy.
* U středně velkých a velkých produkčních úloh doporučujeme, abyste měli aspoň čtyři P3sy, abyste zajistili, že budou při plánované údržbě spuštěny dostatečné front-endy. Naplánované aktivity údržby odeberou jeden front-end v čase. Tím se snižuje Celková dostupná kapacita front-endu během aktivit údržby.
* Pro front-endy může být zřízení trvat až hodinu. 
* Pro další škálovatelné ladění byste měli monitorovat procento využití procesoru, procentuální podíl paměti a aktivní požadavky na front-end fond. Pokud jsou procentuální hodnoty procesoru nebo paměti vyšší než 70% při spuštění P3S, přidejte další front-endy. Pokud je hodnota aktivní požadavky průměrně 15 000 až 20 000 požadavků za front-end, měli byste také přidat další front-endy. Celkovým cílem je udržet procento využití procesoru a paměti pod 70% a aktivní požadavky vychází z maximálního počtu požadavků 15 000 na front-end za sekundu, pokud používáte P3S.  

**Pracovní procesy**: pracovní procesy, kde se vaše aplikace skutečně spouštějí. Při horizontálním navýšení kapacity App Service plány používá pracovní procesy v přidruženém fondu pracovních procesů.

* Nemůžete okamžitě přidat pracovní procesy. Zřízení můžou trvat až hodinu.
* Škálování velikosti výpočetních prostředků pro každý fond bude trvat < 1 hodinu na jednu doménu aktualizace. V pomocném mechanismu služby je 20 aktualizačních domén. Pokud jste škálovat výpočetní velikost fondu pracovních procesů s 10 instancemi, může to trvat až 10 hodin, než se dokončí.
* Pokud změníte velikost výpočetních prostředků, které se používají ve fondu pracovních procesů, vygeneruje se studená spuštění aplikací, které jsou spuštěné v daném fondu pracovních procesů.

Nejrychlejší způsob, jak změnit velikost výpočetních prostředků fondu pracovních procesů, na kterém neběží žádné aplikace, je:

* Škálujte počet pracovních procesů na 2.  Minimální velikost zmenšení v portálu je 2. Zrušení přidělení instancí bude trvat několik minut. 
* Vyberte novou výpočetní velikost a počet instancí. Z tohoto místa bude trvat až 2 hodiny, než se dokončí.

Pokud vaše aplikace vyžadují větší velikost výpočetních prostředků, nemůžete využít výhod předchozích pokynů. Místo změny velikosti fondu pracovních procesů, který je hostitelem těchto aplikací, můžete další pracovní fond naplnit pracovníky požadované velikosti a přesunout své aplikace do tohoto fondu.

* Vytvořte další instance potřebné výpočetní velikosti v jiném fondu pracovních procesů. Dokončení této akce může trvat až hodinu.
* Znovu přiřaďte plány App Service, které hostují aplikace, které vyžadují větší velikost pro nově nakonfigurovaný fond pracovních procesů. Toto je rychlá operace, kterou je třeba dokončit kratší dobu než minutu.  
* Pokud už nepotřebujete tyto nepoužívané instance, můžete škálovat první fond pracovních procesů. Dokončení této operace trvá několik minut.

Automatické **škálování**: jedním z nástrojů, které vám pomůžou spravovat spotřebu výpočetních prostředků, je automatické škálování. Automatické škálování můžete použít pro front-end nebo fondy pracovních procesů. Můžete provádět akce, jako je například zvýšení instancí libovolného typu fondu v ráno a jejich snížení večer. Případně můžete přidat instance, když počet pracovníků, kteří jsou k dispozici ve fondu pracovních procesů, klesne pod určitou prahovou hodnotu.

Pokud chcete nastavit pravidla automatického škálování na základě metrik fondu výpočetních prostředků, pamatujte na dobu, kterou zřizování vyžaduje. Další informace o automatickém škálování App Service prostředí najdete v tématu [Postup konfigurace automatického škálování v App Service Environment][ASEAutoscale].

### <a name="storage"></a>Storage
Každý pomocného programu je nakonfigurovaný s 500 GB úložiště. Toto místo se používá napříč všemi aplikacemi v pomocném formuláři. Tento prostor úložiště je součástí pomocného mechanismu řízení a momentálně se nedá přepnout na použití prostoru úložiště. Pokud provádíte úpravy směrování nebo zabezpečení vaší virtuální sítě, je potřeba, abyste stále povolili přístup k Azure Storage--nebo nemůžete funkci pomocného mechanismu nastavovat.

### <a name="database"></a>databáze
Databáze obsahuje informace definující prostředí a také podrobnosti o aplikacích, které jsou v něm spuštěné. Toto je moc součástí předplatného Azure. Nejedná se o to, že máte přímo možnost manipulovat. Pokud provádíte úpravy směrování nebo zabezpečení vaší virtuální sítě, je potřeba, abyste stále povolili přístup k SQL Azure--nebo nemůžete funkci pomocného mechanismu nastavovat.

### <a name="network"></a>Síť
Virtuální síť, která se používá u vašeho pomocného mechanismu, může být ta, kterou jste provedli při vytváření pomocného programu pro čtení nebo v případě, že jste ho udělali Když vytvoříte podsíť během vytváření pomocného mechanismu řízení, vynutí, aby se pomocného modulu nacházet ve stejné skupině prostředků jako virtuální síť. Pokud potřebujete, aby se skupina prostředků, kterou váš správce služby využívala, lišila od vaší virtuální sítě, je potřeba vytvořit správce prostředků pomocí šablony Resource Manageru.

Existují určitá omezení pro virtuální síť, která se používá pro pomocného mechanismu řízení:

* Virtuální síť musí být místní virtuální síť.
* Musí existovat podsíť s 8 nebo více adresami, ve kterých je nasazený pomocný objekt pro řízení.
* Po použití podsítě k hostování pomocného mechanismu řízení se rozsah adres podsítě nedá změnit. Z tohoto důvodu doporučujeme, aby podsíť obsahovala aspoň 64 adres, aby vyhovovala budoucímu nárůstu pomocného mechanismu.
* V podsíti nemůže být nic jiného, ale pomocného mechanismu.

Na rozdíl od hostované služby, která obsahuje pomocného uživatele, je [virtuální síť][virtualnetwork] a podsíť v rámci uživatelského ovládacího prvku.  Virtuální síť můžete spravovat pomocí Virtual Network uživatelského rozhraní nebo prostředí PowerShell.  Pomocí pomocného mechanismu může být nasazený v klasické nebo Správce prostředků virtuální síti.  Portál a prostředí API se mírně liší mezi klasickými a Správce prostředků virtuální sítě, ale prostředí pomocného uživatelského rozhraní je stejné.

Virtuální síť, která se používá k hostování pomocného mechanismu služby, může používat buď privátní IP adresy RFC1918, nebo může používat veřejné IP adresy.  Pokud chcete použít rozsah IP adres, který nepokrývá RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), musíte vytvořit virtuální síť a podsíť, kterou bude používat váš protokol pro čtení před vytvořením pomocného programu.

Vzhledem k tomu, že tato funkce umístí Azure App Service do vaší virtuální sítě, znamená to, že vaše aplikace, které jsou hostované v přihlašování k síti, teď můžou přistupovat k prostředkům, které jsou k dispozici prostřednictvím služby ExpressRoute nebo virtuální privátní sítě (VPN) typu Site-to-site přímo. Aplikace, které jsou v App Service Environment, nevyžadují další síťové funkce pro přístup k prostředkům, které jsou k dispozici pro virtuální síť, která je hostitelem vašeho App Service Environment. To znamená, že nemusíte používat Integrace virtuální sítě ani Hybrid Connections k tomu, abyste se dostali k prostředkům v nebo připojení k vaší virtuální síti. Obě tyto funkce můžete i přesto použít, pokud chcete získat přístup k prostředkům v sítích, které nejsou připojené k virtuální síti.

Můžete například použít Integrace virtuální sítě pro integraci s virtuální sítí, která je ve vašem předplatném, ale není připojená k virtuální síti, ve které je váš přihlášený. I nadále můžete použít Hybrid Connections pro přístup k prostředkům, které jsou v jiných sítích, stejně jako obvykle.  

Pokud máte virtuální síť nakonfigurovanou pomocí sítě VPN ExpressRoute, měli byste si uvědomit některé z požadavků na směrování, které má pomocným mechanismem řízení. Existují některé konfigurace trasy definované uživatelem (UDR), které jsou nekompatibilní s pomocným mechanismem řízení. Další informace o spuštění pomocného mechanismu řízení ve virtuální síti s ExpressRoute najdete v tématu [spuštění App Service Environment ve virtuální síti s ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpečení příchozího provozu
Existují dvě primární metody řízení příchozího provozu do služby pomocného mechanismu.  Pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) můžete řídit, které IP adresy mají přístup k vašemu pomocnému objektu pro přístup, jak je popsáno zde, [jak řídit příchozí provoz v App Service Environment](app-service-app-service-environment-control-inbound-traffic.md) a můžete také nakonfigurovat své pomocného programu pomocí interního Load BALANCER (interního nástroje).  Tyto funkce se dají použít i v případě, že chcete omezit přístup pomocí skupin zabezpečení sítě pro pomocného programu interního nástroje.

Při vytváření pomocného mechanismu se vytvoří virtuální IP adresa ve vaší virtuální síti.  Existují dva typy VIP, externí a interní.  Když vytvoříte přístupový objekt s externí virtuální IP adresou, vaše aplikace ve vašem přihlašování k Internetu budou přístupné prostřednictvím IP adresy směrovatelné přes Internet. Když vyberete interní, váš přístupový protokol se nakonfiguruje s interního nástroje a nebude přímo přístupný z Internetu.  INTERNÍHO nástroje pomocného modulu pro správu pořád vyžaduje externí VIP, ale používá se pouze pro přístup ke správě a údržbě Azure.  

Během vytváření interního nástroje pomocného mechanismu vám poskytne subdoménu používanou službou interního nástroje pomocného mechanismu řízení a bude muset spravovat vlastní DNS pro subdoménu, kterou určíte.  Vzhledem k tomu, že jste nastavili název subdomény, musíte taky spravovat certifikát používaný pro přístup pomocí protokolu HTTPS.  Po vytvoření pomocného nástroje se zobrazí výzva k zadání certifikátu.  Další informace o vytváření a používání interního nástroje pomocného programu pro čtení [pomocí interní Load Balancer s App Service Environment][ILBASE]. 

## <a name="portal"></a>Portál
App Service Environment můžete spravovat a monitorovat pomocí uživatelského rozhraní v Azure Portal. Pokud máte přihlášený objekt pro řízení, pravděpodobně se na bočním panelu zobrazí symbol App Service. Tento symbol slouží k reprezentaci App Servicech prostředí v Azure Portal:

![Symbol App Service Environment][1]

Chcete-li otevřít uživatelské rozhraní se seznamem všech App Servicech prostředí, můžete použít ikonu nebo vybrat dvojitou šipku (">") v dolní části bočního panelu a vybrat App Service prostředí. Výběrem jednoho z uvedených služby ASE otevřete uživatelské rozhraní, které se používá k jeho monitorování a správě.

![Uživatelské rozhraní pro monitorování a správu App Service Environment][2]

Toto první okno zobrazuje některé vlastnosti vašeho pomocného programu spolu s grafem metriky na fond zdrojů. Některé vlastnosti, které jsou uvedeny v bloku **Essentials** , jsou také hypertextové odkazy, které otevřou okno, které je k němu přidruženo. Můžete například vybrat název **Virtual Network** a otevřít tak uživatelské rozhraní přidružené k virtuální síti, ve které je spuštěný váš pomocným mechanismem řízení. **App Service plány** a **aplikace** jednotlivých otevřených oken, které uvádějí tyto položky v pomocném mechanismu řízení.  

### <a name="monitoring"></a>Monitorování
Grafy vám umožní zobrazit v každém fondu zdrojů nejrůznější metriky výkonu. Pro front-end fond můžete monitorovat průměrný procesor a paměť. U fondů pracovních procesů můžete monitorovat množství, které se používá, a množství, které je k dispozici.

Více plánů App Service může využít pracovní procesy ve fondu pracovních procesů. Zatížení se nedistribuuje stejným způsobem jako u front-end serverů, takže využití procesoru a paměti nepřináší tolik, jak jsou užitečné informace. Je důležitější, abyste mohli sledovat, kolik pracovníků jste používali a jsou k dispozici – zejména pokud spravujete tento systém, aby ho ostatní používali.  

Pro nastavení výstrah můžete také použít všechny metriky, které lze v grafech sledovat. Nastavení výstrah v tomto případě funguje stejně jako jinde v App Service. Výstrahu můžete nastavit buď ze součásti uživatelského rozhraní **výstrahy** , nebo z přechodu do libovolného uživatelského rozhraní metriky a výběrem možnosti **Přidat výstrahu**.

![Uživatelské rozhraní metrik][3]

Právě popsaná metrika je App Service Environment metriky. K dispozici jsou také metriky, které jsou k dispozici na úrovni plánu App Service. To je místo, kde monitoruje procesor a paměť spoustu smyslů.

V pomocném mechanismu řízení jsou všechny plány App Service vyhrazené App Service plány. To znamená, že všechny aplikace, které jsou spuštěny na hostitelích přidělených tomuto App Service plánu, jsou aplikace v daném App Service plánu. Pokud chcete zobrazit podrobnosti o plánu App Service, zajistěte si App Service plán z libovolného seznamu v uživatelském rozhraní služby přihlašování nebo z části **procházet App Service plány** (které uvádí všechny).   

### <a name="settings"></a>Nastavení
V okně pomocného mechanismu se nachází oddíl **Nastavení** , který obsahuje několik důležitých možností:

**Nastavení**  >  **Vlastnosti**: okno **Nastavení** se automaticky otevře při otevření okna pomocného mechanismu. V horní části jsou **vlastnosti**. Tady je několik položek, které jsou redundantní na to, co vidíte v **Essentials**, ale to je velmi užitečné pro **virtuální IP adresu** i pro **odchozí IP adresy**.

![Okno nastavení a vlastnosti][4]

**Nastavení**  >  **IP adresy**: když v pomocném mechanismu služby vytvoříte aplikaci SSL (Secure Sockets Layer) protokolu IP, budete potřebovat IP SSL adresu. Abyste si ho mohli opatřit, vaše pomocného mechanismu potřebuje IP SSL adres, které vlastní, a dá se přidělit. Při vytvoření pomocného mechanismu pro tento účel má jednu IP SSL adresu, ale můžete přidat další. Pro další IP SSL adresy se účtuje poplatek, jak je uvedeno v článku o [cenách App Service][AppServicePricing] (v části o připojeních SSL). Další cenou je IP SSL cena.

**Nastavení**  >  Fond front- **Endu**  /  **Fondy pracovních procesů**: každá z těchto skupin fondů prostředků nabízí možnost zobrazovat informace pouze v tomto fondu zdrojů a poskytovat tak ovládací prvky pro kompletní škálování fondu zdrojů.  

Základní okno pro každý fond zdrojů poskytuje graf s metrikami pro daný fond zdrojů. Stejně jako u grafů z okna pomocného mechanismu můžete přejít do grafu a nastavit výstrahy podle potřeby. Nastavení výstrahy z okna pomocného mechanismu řízení pro určitý fond zdrojů má stejný stav jako z fondu zdrojů. V okně **Nastavení** fondu pracovních procesů máte přístup ke všem aplikacím nebo App Servicem plánům, které jsou spuštěné v tomto fondu pracovních procesů.

![Uživatelské rozhraní nastavení fondu pracovních procesů][5]

### <a name="portal-scale-capabilities"></a>Možnosti škálování portálu
Existují tři operace škálování:

* Změna počtu IP adres v přihlašování k systému, které jsou k dispozici pro IP SSL využití.
* Změna velikosti výpočetního prostředku, který se používá ve fondu zdrojů.
* Změna počtu výpočetních prostředků, které se používají ve fondu zdrojů, a to buď ručně, nebo prostřednictvím automatického škálování.

Na portálu existují tři způsoby, jak řídit, kolik serverů máte ve fondech prostředků:

* Operace škálování z hlavního okna pomocného mechanismu řízení v horní části. Můžete provést několik změn konfigurace škálování front-endu a fondů pracovních procesů. Všechny jsou aplikovány jako jediná operace.
* Operace ručního škálování z okna **škálování** jednotlivých fondů prostředků, které se nachází v části **Nastavení**.
* Automatické škálování, které jste nastavili v okně **škálování** jednotlivých fondů prostředků.

Pokud chcete použít operaci škálování v okně pomocného mechanismu řízení, přetáhněte posuvník na požadované množství a uložte ho. Toto uživatelské rozhraní také podporuje změnu velikosti.  

![Škálování uživatelského rozhraní][6]

Pokud chcete používat funkce ručního nebo automatického škálování v konkrétním fondu zdrojů, v případě potřeby použijte možnost **Nastavení**  >    /  **fondy pracovních procesů** fondu front-endu. Pak otevřete fond, který chcete změnit. Přejít na **Nastavení**  >  **horizontální**   >  **navýšení kapacity nebo navýšení kapacity** nastavení. Okno **horizontálního** navýšení kapacity umožňuje řídit množství instancí. **Horizontální navýšení kapacity** umožňuje řídit velikost prostředků.  

![Uživatelské rozhraní nastavení škálování][7]

## <a name="fault-tolerance-considerations"></a>Požadavky na odolnost proti chybám
Můžete nakonfigurovat App Service Environment pro použití až 55 celkových výpočetních prostředků. Z těchto 55 výpočetních prostředků lze použít pouze 50 pro hostování úloh. Důvodem je dvojí. Je k dispozici minimálně 2 výpočetní prostředky front-endu.  To ponechá až 53 pro podporu přidělení pracovního fondu. Aby se zajistila odolnost proti chybám, musíte mít další výpočetní prostředek, který je přidělený podle následujících pravidel:

* Každý pracovní fond potřebuje aspoň jeden další výpočetní prostředek, který není k dispozici pro přiřazení úlohy.
* Pokud množství výpočetních prostředků ve fondu pracovních procesů překročí určitou hodnotu, je potřeba pro odolnost proti chybám potřebovat jiný výpočetní prostředek. Nejedná se o případ ve fondu front-endu.

V rámci libovolného jednoho pracovního fondu jsou požadavky na odolnost proti chybám, které se týkají dané hodnoty X prostředků přiřazených k fondu pracovních procesů:

* Pokud X je mezi 2 a 20, množství použitelných výpočetních prostředků, které můžete použít pro úlohy, je X-1.
* Pokud X je mezi 21 a 40, množství použitelných výpočetních prostředků, které můžete použít pro úlohy, je X-2.
* Pokud X je mezi 41 a 53, množství použitelných výpočetních prostředků, které můžete použít pro úlohy, je X-3.

Minimální nároky mají 2 servery front-end a dva pracovní procesy.  Výše uvedené příkazy pak představují několik příkladů k objasnění:  

* Pokud máte v jednom fondu 30 pracovních procesů, můžete k hostování úloh použít 28.
* Pokud máte 2 pracovní procesy v jednom fondu, pak je možné použít 1 k hostování úloh.
* Pokud máte v jednom fondu 20 pracovních procesů, můžete k hostování úloh použít 19.  
* Pokud máte v jednom fondu 21 pracovních procesů, můžete k hostování úloh použít jenom 19.  

Aspekt odolnosti proti chybám je důležitý, ale při škálování nad rámec určitých prahových hodnot je potřeba mít na paměti. Pokud chcete přidat větší kapacitu z 20 instancí, pokračujte na 22 nebo vyšší, protože 21 nepřidá žádnou další kapacitu. Totéž platí výše 40, kde další číslo, které přičítá kapacitu, je 42.  

## <a name="deleting-an-app-service-environment"></a>Odstranění App Service Environment
Pokud chcete odstranit App Service Environment, jednoduše použijte akci **Odstranit** v horní části okna App Service Environment. Když to uděláte, zobrazí se výzva k zadání názvu App Service Environment, abyste se ujistili, že to opravdu chcete. Všimněte si, že při odstranění App Service Environment odstraníte i veškerý obsah v něm obsažené.  

![Odstranění uživatelského rozhraní App Service Environment][9]  

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat s App Service prostředími, přečtěte si téma [vytvoření App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

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
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md