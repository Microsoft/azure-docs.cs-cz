---
title: Integrace aplikace pomocí virtuální sítě Azure
description: Ukazuje, jak připojit aplikace v Azure App Service k nový nebo existující virtuální síť Azure
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 83f5c64926eb9b718463c415a5478af374245f31
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789203"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace pomocí virtuální sítě Azure
Tento dokument popisuje funkci integrace virtuální sítě Azure App Service a ukazuje, jak ho nastavit s aplikacemi ve [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Pokud jste obeznámeni s virtuální sítí Azure (virtuální sítě), toto je funkce, která umožňuje umístit mnoho vašich prostředků Azure v Internetu jiných routeable síti, která můžete řídit přístup ke. Tyto sítě můžete pak připojené k vaší místní sítě pomocí různých technologií, sítě VPN. Další informace o virtuálních sítí Azure, spusťte s informacemi, zde: [Přehled virtuálních sítí Azure][VNETOverview]. 

Azure App Service má dva formuláře. 

1. Víceklientské systémy, které podporují celou řadu cenových plánů
2. Funkci premium prostředí App Service (App Service Environment), která nasadí do virtuální sítě. 

Tento dokument projde integrace virtuální sítě a není App Service Environment. Pokud chcete získat další informace o funkci App Service Environment, spusťte s informacemi, zde: [App Service Environment ÚVOD][ASEintro].

Integrace virtuální sítě poskytuje vaší webové aplikaci přístup k prostředkům ve virtuální síti, ale nejsou udělena soukromý přístup do webové aplikace z virtuální sítě. Přístup privátního lokality odkazuje na zpřístupnění aplikace jenom z privátní sítě, jako z v rámci virtuální sítě Azure. Přístup privátního lokality je dostupná pouze s App Service Environment nakonfigurovaný s interní nástroj pro vyrovnávání zatížení (ILB). Podrobnosti o používání App Service Environment ILB, začínat článek zde: [vytváření a používání App Service Environment ILB][ILBASE]. 

Běžný scénář, kde byste použili integrace virtuální sítě se povoluje přístup z vaší webové aplikace do databáze nebo webové služby spuštěné na virtuálním počítači ve vaší virtuální síti Azure. Díky integraci virtuální síť nemusíte vystavit veřejný koncový bod pro aplikace na virtuální počítač ale může místo toho použít privátní adresy směrovatelné Internetu jiných výrobců. 

Funkce integrace virtuální sítě:

* vyžaduje standardní, Premium nebo izolované ceny plán 
* funguje s Classic nebo virtuální sítě Resource Manageru 
* podporuje TCP a UDP
* funguje s webové, mobilní aplikace API a funkce aplikace
* umožňuje aplikaci připojit k virtuální síti pouze 1 současně
* umožňuje až pět virtuálních sítí s integrovat v plán služby App Service 
* umožňuje stejnou virtuální síť má být používána více aplikacemi v plán služby App Service
* podporuje SLA 99,9 % kvůli smlouva SLA na bráně virtuální sítě

Existují některé věci, které virtuální sítě integrace nepodporuje, včetně:

* připojení na jednotku
* Integrace služby AD 
* Pro rozhraní NetBios
* přístup privátního lokality

### <a name="getting-started"></a>Začínáme
Zde jsou některé věci, třeba vzít v úvahu před připojením k virtuální síti vaší webové aplikace:

* Integrace virtuální sítě funguje pouze s aplikacemi ve **standardní**, **Premium**, nebo **izolovaná** ceny plánu. Pokud povolíte funkci a pak vertikálně váš plán služby App Service na nepodporované cenový plán aplikace ztratí připojení k virtuálním sítím používají. 
* Pokud cílový virtuální síti již existuje, musí mít point-to-site VPN povoleny v rámci brány dynamického směrování, než může být připojen k aplikaci. Pokud vaše brána je nakonfigurovaná se statickým směrováním, nelze povolit point-to-site virtuální privátní sítě (VPN).
* Virtuální sítě musí být ve stejném předplatném jako služba Plan(ASP) vaší aplikace.
* Pokud vaše brána už existuje s point-to-site povoleno a není v základní SKU, musí být IKEV2 zakázáno v konfiguraci point-to-site.
* Aplikace, které se integrují s virtuální sítě pomocí DNS, který je zadán pro tuto virtuální síť.
* Ve výchozím nastavení aplikace integrační pouze směrovat provoz do virtuální sítě podle tras, které jsou definovány ve vaší virtuální síti. 

## <a name="enabling-vnet-integration"></a>Povolení integrace virtuální sítě

Máte možnost připojovat vaše aplikace do nové nebo existující virtuální sítě. Pokud vytvoříte novou síť jako součást svoji integraci, kromě právě vytváření sítě VNet, je předem nakonfigurovaný pro vás brány dynamického směrování a je povoleno, přejděte na příkaz Site VPN. 

> [!NOTE]
> Konfigurace nového integrace virtuální sítě může trvat několik minut. 
> 
> 

Chcete-li povolit integraci virtuální sítě, otevřete v aplikaci nastavení a pak vyberte síť. Uživatelské rozhraní, které se otevře nabízí tři možnosti sítě. Tento průvodce bude jenom do integrace virtuální sítě, když hybridní připojení a prostředí App Service jsou popsány dále v tomto dokumentu. 

Pokud aplikace není ve správné cenách plán, uživatelské rozhraní umožňuje škálovat plán pro vyšší cenový plán, podle vašeho výběru.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Povolení integrace virtuální sítě s existující virtuální síť
Integrace rozhraní virtuální sítě můžete vybrat ze seznamu vaší virtuální sítě. Virtuální sítě Classic znamenat, že jsou například slovem "Klasickém" v závorkách vedle názvu virtuální sítě. Tento seznam je řazen tak, že virtuální sítě Resource Manager jsou uvedeny jako první. Na obrázku vidíte níže vidíte, že lze vybrat pouze jeden virtuální sítě. Tady je několik důvodů, virtuální sítě můžete šedě včetně:

* virtuální sítě je v jiné předplatné, který má přístup k účtu
* virtuální síť nemá bod do lokality povoleno
* virtuální síť nemá brány dynamického směrování

![][2]

Chcete-li povolit integraci jednoduše klikněte na virtuální sítě, které chcete integrovat. Po výběru virtuální sítě, aplikace automaticky restartovat, aby změny vstoupily v platnost. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Povolit bod do lokality v klasické virtuální sítě
Pokud virtuální síť nemá bránu ani má bod do lokality, budete muset nastavit první. Chcete-li to provést pro klasické virtuální síti, přejděte na [portál Azure] [ AzurePortal] a zprovoznit seznam virtuální Networks(classic). Zde klikněte na síť, kterou chcete integrovat a klikněte na pole velký pod názvem připojení k síti VPN Essentials. Tady můžete vytvořit váš bod do lokality VPN a i mějte ho vytvořit bránu. Po projít bodem k lokalitě s prostředím vytvoření brány je asi 30 minut, než je připraven. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Povolení bod do lokality ve virtuální síti Resource Manager
Konfigurace virtuální sítě Resource Manageru pomocí brány a bod do lokality, můžete použít buď PowerShell podle postupu uvedeného zde [konfigurace připojení typu Point-to-Site k virtuální síti pomocí prostředí PowerShell] [ V2VNETP2S] nebo pomocí portálu Azure podle postupu uvedeného zde [konfigurace připojení typu Point-to-Site k virtuální síti pomocí portálu Azure][V2VNETPortal]. Uživatelské rozhraní k provedení tato funkce není k dispozici. Všimněte si, že nepotřebujete k vytvoření certifikátů, aby bod konfigurace lokality. Ta se automaticky nakonfiguruje při připojení k virtuální síti vaší webové aplikace. 

### <a name="creating-a-pre-configured-vnet"></a>Vytvoření předem konfigurovaný virtuální sítě
Pokud chcete vytvořit novou virtuální síť, který je nakonfigurovaný s bránou a Point-to-Site, App Service sítě uživatelského rozhraní má možnost udělat, ale jenom pro virtuální sítě Resource Manageru. Pokud chcete vytvořit klasické virtuální sítě se brána a Point-to-Site, budete muset udělat to ručně přes uživatelské rozhraní sítě. 

Chcete-li vytvořit virtuální sítě Resource Manageru pomocí integrace rozhraní virtuální sítě, jednoduše vyberte **vytvořit novou virtuální síť** a zadejte:

* Název virtuální sítě
* Blok adres služby Virtual Network
* Název podsítě
* Blok adres podsítě
* Blok adres brány
* Blok adres point-to-site

Pokud chcete této virtuální sítě pro připojení k jiným sítím, pak byste neměli výběr prostor IP adres, který se překrývá s tyto sítě. 

> [!NOTE]
> Vytvoření virtuální sítě Resource Manageru s bránou trvá nějakých 30 minut a aktuálně Neintegruje virtuální sítě s vaší aplikací. Po vytvoření virtuální sítě s bránou potřebujete vraťte do vaší aplikace uživatelského rozhraní integrace virtuální sítě a vyberte nové sítě VNet.
> 
> 

![][3]

Virtuálních sítí Azure se obvykle vytvoří v rámci adres privátní sítě. Ve výchozím nastavení integrace virtuální sítě funkce směrovat všechny přenosy určené pro tyto rozsahy IP adres do virtuální sítě. Rozsahy privátních IP adres jsou:

* 10.0.0.0/8 – je to stejné jako 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 – je to stejné jako 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - Toto je stejný jako 192.168.0.0 - 192.168.255.255

Adresní prostor sítě VNet, je třeba zadat v notaci CIDR. Pokud jste obeznámeni s notaci CIDR, je metoda pro zadání bloky adres pomocí IP adresy a celé číslo, které představuje maska sítě. Jako rychlý přehled zvažte, že 10.1.0.0/24 by 256 adresy a 10.1.0.0/25 by 128 adresy. Adresu IPv4 s /32 by být právě 1 adresa. 

Pokud nastavíte informace o serveru DNS, pak, bude nastavena pro vaši virtuální síť. Po vytvoření virtuální sítě můžete upravit tyto informace z činnosti koncových uživatelů virtuální sítě. Pokud změníte DNS virtuální sítě, budete muset provést operaci synchronizace sítě.

Když vytvoříte klasické virtuální sítě pomocí integrace rozhraní virtuální sítě, vytvoří virtuální síť ve stejné skupině prostředků jako aplikace. 

## <a name="how-the-system-works"></a>Jak funguje v systému
Skrytě této funkce vytvoří nad technologie VPN Point-to-Site k připojení aplikace k virtuální síti. Aplikace v Azure App Service musí mít architektura víceklientské systému, který vylučuje zřizování aplikace přímo ve virtuální síti, jak se provádí s virtuálními počítači. Podle budovy na technologii point-to-site jsme omezit přístup k síti na pouze na virtuální počítač hostování aplikace. Přístup k síti je dále omezen na těchto hostitelích aplikace tak, aby vaše aplikace přístup jenom k sítě, které můžete nakonfigurovat je pro přístup. 

![][4]

Některé z dalších výhod služby App Service Environment jsou, které můžete použít na základě Dv2 pracovníci s až 14 GB paměti RAM. Další výhodou je, že je možné škálovat systému podle svých potřeb. Na rozdíl od více klientů prostředích, kde je omezený na 20 instancí vaší ASP v App Service Environment můžete škálovat instance až 100 ASP.

## <a name="managing-the-vnet-integrations"></a>Jednou z věcí, kterou poskytuje App Service Environment, který není v rámci integrace virtuální sítě je, že služby App Service Environment může fungovat s připojení VPN pomocí ExpressRoute.
Zatímco je, že některé použít případu překrývají, žádný z těchto funkcí můžete nahradit všechny ostatní. Zároveň budete vědět, jaké funkci používat, je vázaný na vašim potřebám. Pokud jste vývojář a jednoduše chcete spustit lokalitu v Azure a jeho přístup k databázi na pracovní stanici v rámci svého stolu, je ta nejjednodušší cesta používat hybridní připojení. Pokud jsou velké organizace, která chce velký počet webových vlastností v veřejnosti cloud a spravovat ve vlastní síti a potom chcete pomocí služby App Service Environment. 

![][5]

Pokud máte několik služby App Service hostované aplikace a jednoduše má přístup k prostředkům ve vaší virtuální síti, je tento způsob si integrace virtuální sítě. Kromě případů použití, jsou některé jednoduchost související aspekty. 

Pokud virtuální sítě je již připojen k síti na pracovišti, pak pomocí integrace virtuální sítě nebo služby App Service Environment je snadný způsob, jak využívat místních prostředků. Na druhé straně Pokud virtuální sítě není připojen k síti na pracovišti je mnohem víc zásahů, pokud chcete nastavit s2s VPN s vaší virtuální sítě ve srovnání s instalací HCM.

* Nad rámec funkční rozdíly, existuje jsou také ceny rozdíly.
* Funkce služby App Service Environment je prémiový nabídky služeb ale nabízí nejvíce sítě možnosti konfigurace kromě dalších funkcí. Integrace virtuální sítě můžete použít s Standard nebo Premium soubory ASP a je ideální pro bezpečné použití prostředky ve virtuální síti ze víceklientské služby App Service.
* Hybridní připojení se aktuálně závisí na BizTalk účtu, který má cenové úrovně, které spustit volné a potom získat progresivně nákladnější založenou na velikosti, které potřebujete. Pokud jde o práce v sítích s mnoha Přestože, neexistuje žádné jiné funkce, jako je hybridní připojení, která můžete povolit přístup k prostředkům do samostatných sítí a více než 100.
* Stav brány. - musí být jako vašich bran dolů z jakéhokoliv důvodu pak aplikace nemají přístup k prostředkům ve virtuální síti. 
* Adresní prostor sítě VNet - to je pro vaši virtuální síť adresní prostor IP adres. 
* Přejděte na webu adresní prostor – to je bod do lokality adresní prostor IP adres pro virtuální síť. Vaše aplikace zobrazí komunikace jako pocházející z jednoho z IP adresy v tento adresní prostor. 
* Lokality do lokality adresního prostoru - Site to Site VPN můžete použít pro připojení virtuální sítě k místním prostředkům nebo k jiné virtuální sítě. Pokud máte nakonfigurovaný a rozsahy IP definovaný s zde znázorňuje připojení k síti VPN.
* Servery DNS – Pokud máte nakonfigurované virtuální sítě, servery DNS, pak jsou zde uvedeny.
* IP adresy směrované do virtuální sítě - zde je uveden seznam IP adres, které virtuální sítě má směrování definované pro a zobrazit tyto adresy tady. 

Pouze operace, které můžete provést v zobrazení aplikace integrace vaší virtuální sítě je odpojit od sítě VNet je aktuálně připojen k vaší aplikace. To jednoduše klikněte na možnost Odpojit nahoře. Tato akce nezmění vaši virtuální síť. Virtuální sítě a jeho konfigurace, včetně brány zůstává beze změny. Pokud chcete poté odstranit virtuální síť, musíte nejprve odstranit prostředky v ní včetně brány. 

Zobrazení plánu služby App Service má několik dalších operací. Je také přístupný jinak než z aplikace. K dosažení rozhraní sítě ASP jednoduše otevřete uživatelské rozhraní ASP a přejděte dolů. Je prvek uživatelského rozhraní s názvem stav funkce sítě. Nabízí některé dílčí podrobnosti ohledně integrace vaší virtuální sítě. Kliknutím na toto uživatelské rozhraní, otevře se rozhraní stav funkce sítě. Pokud potom klikněte na "Kliknutím sem ke správě", otevře se uživatelské rozhraní, které jsou uvedené integrace virtuální sítě v této ASP.

![][6]

Umístění ASP je dobré pamatovat při vyhledávání v umístění virtuální sítě budete nástroj integrovat. Pokud virtuální sítě je v jiném umístění jsou mnohem pravděpodobnější zobrazíte problémy s latencí. 

Virtuální sítě integrovat je připomenutí na tom, kolik virtuálních sítí, že vaše aplikace jsou integrované s v této ASP a kolik můžete mít. 

Byly přidané podrobnosti najdete v každé virtuální síti VNet právě klikněte na virtuální síť se zajímáte. Kromě podrobností, které jste si poznamenali dříve můžete také zobrazit seznam aplikací v této ASP, které používají tuto virtuální síť. 

S ohledem na akce jsou dvě primární akce. První je možnost přidávat trasy, jež řídí odchozího provozu z vaší aplikace do virtuální sítě. Druhou akci, je schopnost synchronizovat certifikáty a informace o síti.

![][7]

**Směrování** jako si předtím poznamenali tras, které jsou definovány ve vaší virtuální síti se, co se používá pro směrují přenosy do virtuální sítě z vaší aplikace. Existují některé používá, když kde zákazníci chtějí odeslat další odchozí přenos z aplikace do virtuální sítě a je tato možnost je k dispozici. Co se stane provoz, poté, co je to na tom, jak zákazník nakonfiguruje své virtuální síti. 

**Certifikáty** stavu certifikátu odráží kontrolu prováděného App Service k ověření, že jsou stále vhodné certifikáty, které se používá pro připojení k síti VPN. Když je povolené integrace virtuální sítě, pokud je první integrace do ní ze všech aplikací v této ASP nejsou požadované výměny certifikáty pro zajištění zabezpečení připojení. Společně s certifikáty se nám získat konfiguraci serveru DNS, trasy a další podobné věci, které popisují sítě.
Pokud tyto certifikáty nebo informace o síti změní, budete muset klikněte na tlačítko "Synchronizace sítě". **Poznámka:**: Po kliknutí na tlačítko "Synchronizace síť", pak způsobit kratší výpadek připojení mezi vaší aplikace a virtuální síť. Při restartování aplikace není ztráty připojení může způsobit, že web není funkce správně. 

## <a name="accessing-on-premises-resources"></a>Přístup k místní prostředky
Jednou z výhod funkce integrace virtuální sítě je, že pokud virtuální sítě je připojený k místní síti pomocí sítě Site to Site VPN pak vaše aplikace může mít přístup k místním prostředkům z vaší aplikace. Tento postup vyžaduje, když budete muset aktualizovat bránu místní sítě VPN s trasy pro bod do lokality IP v rozsahu. Když Site to Site VPN je nejdřív nastavit pak skripty slouží ke konfiguraci ho měli nastavit trasy, včetně vašeho bodu k síti VPN lokality. Pokud přidáte bod k síti VPN lokality po vytvoření Site to Site VPN, budete muset ručně aktualizovat trasy. Podrobnosti o tom, jak to udělat se liší u každé brány a nejsou tady popsané. 

> [!NOTE]
> Funkce integrace virtuální sítě Neintegruje aplikace s virtuální sítě, který má bránu ExpressRoute. I když v je nakonfigurovaná brána ExpressRoute [koexistence režimu] [ VPNERCoex] integrace virtuální síť nefunguje. Pokud potřebujete přístup k prostředkům pomocí připojení ExpressRoute, pak můžete použít [App Service Environment][ASE], která se spouští ve vaší virtuální síti.
> 
> 

## <a name="pricing-details"></a>Podrobnosti o cenách
Existuje několik ceny drobné odlišnosti, kterých byste měli vědět, když používáte funkci integrace virtuální sítě. Existují 3 poplatky související s použitím této funkce:

* Cenová úroveň požadavků ASP
* Náklady na přenos dat
* Brána sítě VPN náklady.

Pro vaše aplikace, abyste mohli tuto funkci používat potřebují mít Standard nebo plán aplikační služby Premium. Další podrobnosti uvidíte na zde uvedené náklady: [App Service – ceny][ASPricing]. 

Vzhledem ke způsobu bod do sítě VPN Site jsou zpracovány, budete mít vždy zdarma pro odchozí data prostřednictvím připojení k virtuální síti integrace i v případě, že virtuální sítě je ve stejném datovém centru. Pokud chcete zobrazit, jaké jsou tyto poplatky, podívejte se sem: [přenosu informace o cenách datových][DataPricing]. 

Poslední položky jsou náklady na brány virtuální sítě. Pokud nepotřebujete brány na něco jiného, jako je například Site to Site VPN, pak platíte pro brány na podporu funkce integrace virtuální sítě. Podrobnosti jsou na těchto náklady zde: [ceny služby VPN Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>Řešení potíží
Sice snadno nastavit funkci, který neznamená, že budou vaše zkušenosti problém volné. Měli narazíte na problémy s přístupem k dispozici požadovaný koncový bod jsou některé nástroje, které můžete použít k testování připojení z konzoly pro aplikaci. Existují dvě konzoly prostředí, které můžete použít. Jedna je z konzoly Kudu a druhý je konzola, která můžete dosáhnout na portálu Azure. Abyste se dostali na konzole Kudu z vaší aplikace přejděte na Nástroje -> Kudu. Je to stejné nastavení jako chystáte [sitename]. scm.azurewebsites.net. Po, otevře se jednoduše přejděte na kartu konzoly ladění. Získat konzoly Azure portálu hostované potom z vaší aplikace, přejděte na Nástroje -> Konzola. 

#### <a name="tools"></a>Nástroje
Ping nástrojů, nslookup a tracert nebude fungovat přes konzolu z důvodu omezení zabezpečení. K vyplnění void zde byly dvou samostatných nástrojů, které jsou přidány. Chcete-li otestovat funkci DNS jsme přidali nástroje s názvem nameresolver.exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

Můžete zkontrolovat názvy hostitelů, které závisí aplikace na nameresolver. Tímto způsobem můžete otestovat, pokud máte nic nemá nakonfigurovaný s DNS nebo možná nemáte přístup k serveru DNS.

Další nástroj umožňuje otestovat připojení protokolu TCP na kombinaci hostitele a portu. Tento nástroj se nazývá tcpping.exe a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj tcpping zjistíte, pokud lze kontaktovat konkrétního hostitele a port. Jenom je zobrazují úspěch pokud: aplikace naslouchá na kombinace hostitele a portu, a je přístup k síti z vaší aplikace do zadaného hostitele a portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístup k virtuální síti hostovaným prostředkům
Existuje několik věcí, které může zabránit aplikaci v dosažení konkrétního hostitele a port. Ve většině případů je jedním z tři věci:

* **Je brána firewall způsobem** Pokud máte bránu firewall způsobem, se setkají vypršení časového limitu TCP. Který v tomto případě je 21 sekund. Použijte nástroj tcpping k testování připojení. Překročení časového limitu TCP dají kvůli celou řadu věcí mimo brány firewall, ale existuje spustit. 
* **DNS není dostupný** časový limit DNS je tři sekundy na jeden DNS server. Pokud máte dva servery DNS, je časový limit 6 sekund. Použijte nameresolver Pokud DNS funguje. Mějte na paměti, že nslookup nelze použít jako, který nepoužívá DNS jsou vaše virtuální síť nakonfigurované.
* **Neplatný rozsah P2S IP** bod do lokality rozsah IP adres musí být v dokumentu RFC 1918 rozsahy privátních IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Pokud rozsahu IP adres používá mimo který, nebude fungovat věcí. 

Pokud váš problém neobsahují odpověď na ty položky, vyhledejte první pro jednoduché třeba: 

* Zobrazit brány jako používán portálu?
* Certifikáty zobrazit, že je synchronizace?
* Bez provádění "Sítě synchronizace" v ovlivněné soubory ASP kdokoliv měnit síťovou konfiguraci? 

Pokud vaše brána je vypnutý, pak ho převeďte zálohování. Pokud vaše certifikáty nejsou synchronizované, přejděte do zobrazení ASP integrace vaší virtuální sítě a stiskněte tlačítko "Synchronizace sítě". Pokud máte podezření, že došlo ke změně provedené v konfiguraci virtuální sítě a jeho nebyl synchronizace by se vaše soubory ASP, pak přejděte do zobrazení ASP integrace vaší virtuální sítě a stiskněte tlačítko "Sítě synchronizace" těsně jako připomenutí, protože to způsobuje kratší výpadek s připojením k virtuální síti a aplikace. 

Pokud všechny, je v pořádku, budete muset prozkoumat trochu podrobněji:

* Jsou všechny ostatní aplikace pomocí integrace virtuální sítě pro přístup k prostředkům ve stejné virtuální síti? 
* Můžete přejít do konzoly aplikace a použít tcpping k dosažení žádné další prostředky ve vaší virtuální síti? 

Pokud platí některá z výše uvedených, pak je dobře vaše integrace virtuální sítě a problém je někde jinde. Toto je, kde získá za další problémy, protože neexistuje žádný jednoduchý způsob, jak zobrazit, proč nelze připojit hostitele a portu. Některé z příčin patří:

* máte bránu firewall na hostiteli brání přístupu k portu aplikace z bodu na rozsah IP lokality. Při překročení podsítě často vyžaduje veřejný přístup.
* Cílový hostitel je mimo provoz
* aplikace je mimo provoz
* jste měli nesprávné IP adresu nebo název hostitele
* vaše aplikace naslouchá na jiný port než jste očekávali. Zkontrolovat to můžete tak, že přejdete na tomto hostiteli a pomocí "netstat - aon" z příkazový řádek. To se dozvíte, co proces ID naslouchá na port. 
* Vaše skupiny zabezpečení sítě jsou nakonfigurované tak, že brání tomu, aby přístup do vaší aplikace hostitele a portu z bodu lokality rozsah IP

Mějte na paměti, že k rozsahu IP lokality, který vaše aplikace bude používat, musíte povolit přístup z celý rozsah nevíte jaké IP ve bodu. 

Další ladění krokům patří:

* Přihlaste se jiným virtuálním Počítačem ve virtuální síti a se pokoušejí připojit vaše prostředků hostitele a portu z ní. Existují některé nástroje ping TCP musí být pro tento účel můžete použít nebo i pokud můžete použít telnet. Účelem tady je jenom k určení, pokud je připojení k dispozici z jiných tohoto virtuálního počítače. 
* zprovoznit aplikace na jiný virtuální počítač a testovací přístup do tohoto hostitele a portu z konzoly z vaší aplikace.

#### <a name="on-premises-resources"></a>Místní prostředky ####
Pokud vaše aplikace nebudou moci připojit místní prostředky, je první věcí, které byste měli zkontrolovat, je-li se lze připojit prostředku ve vaší virtuální síti. Pokud na to, pokusí se kontaktovat místní aplikace z virtuálního počítače ve virtuální síti. Můžete použít protokol telnet nebo nástroj ping protokolu TCP. Pokud virtuální počítač nelze dosáhnout místnímu prostředku, zkontrolujte, zda že funguje připojení k síti VPN Site to Site. Pokud funguje, zkontrolujte si předtím poznamenali i místní konfiguraci brány a stav stejné operace. 

Teď Pokud hostované virtuální sítě virtuálních počítačů dosáhnout v místním systému, ale aplikace nemůže z důvodu bude jedním z následujících:

* trasy nejsou nakonfigurováni váš bod do lokality rozsahy IP ve vaší místní brány
* Vaše skupiny zabezpečení sítě, blokují přístup pro bod do lokality IP rozsahu
* vaše místní brány firewall, blokují přenosy z bodu na rozsah IP lokality
* Máte Route(UDR) definované uživatele ve vaší virtuální síti, která zabraňuje váš bod pro provoz lokality na základě přístup do místní sítě

## <a name="powershell-automation"></a>Automatizace prostředí PowerShell

Služby App Service můžete integrovat s virtuální síť Azure pomocí prostředí PowerShell. Připraveno ke spuštění skriptu, najdete v části [aplikaci v Azure App Service připojit k virtuální síti Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hybridní připojení a služby App Service Environment
Existují tři funkce, které umožňují přístup k prostředkům hostované virtuální sítě. Jsou to tyto:

* Integrace virtuální sítě
* Hybridní připojení
* Prostředí App Service

Hybridní připojení vyžaduje, abyste nainstalujte přenosového agenta názvem Manager(HCM) připojení hybridní ve vaší síti. HCM musí být schopni se připojit k Azure a také do vaší aplikace. Toto řešení je obzvláště skvělé z vzdálené sítě, například v místní síti nebo i jiného cloudu hostovaný sítě, protože nevyžaduje internet přístupném koncovém bodu. HCM lze spustit pouze v systému Windows a může mít až pět instancí spuštěných pro zajištění vysoké dostupnosti. Hybridní připojení, když pouze podporuje TCP a každý koncový bod HC musí odpovídat kombinaci konkrétní hostitele a portu. 

Funkce služby App Service Environment umožňuje spustit instanci služby Azure App Service ve vaší virtuální síti. To umožňuje vaší aplikace přístup k prostředkům ve vaší virtuální síti bez jakékoli dodatečné kroky. Některé z dalších výhod služby App Service Environment jsou, které můžete použít na základě Dv2 pracovníci s až 14 GB paměti RAM. Další výhodou je, že je možné škálovat systému podle svých potřeb. Na rozdíl od více klientů prostředích, kde je omezený na 20 instancí vaší ASP v App Service Environment můžete škálovat instance až 100 ASP. Jednou z věcí, kterou poskytuje App Service Environment, který není v rámci integrace virtuální sítě je, že služby App Service Environment může fungovat s připojení VPN pomocí ExpressRoute. 

Zatímco je, že některé použít případu překrývají, žádný z těchto funkcí můžete nahradit všechny ostatní. Zároveň budete vědět, jaké funkci používat, je vázaný na vašim potřebám. Příklad:

* Pokud jste vývojář a jednoduše chcete spustit lokalitu v Azure a jeho přístup k databázi na pracovní stanici v rámci svého stolu, je ta nejjednodušší cesta používat hybridní připojení. 
* Pokud jsou velké organizace, která chce velký počet webových vlastností v veřejnosti cloud a spravovat ve vlastní síti a potom chcete pomocí služby App Service Environment. 
* Pokud máte několik služby App Service hostované aplikace a jednoduše má přístup k prostředkům ve vaší virtuální síti, je tento způsob si integrace virtuální sítě. 

Kromě případů použití, jsou některé jednoduchost související aspekty. Pokud virtuální sítě je již připojen k síti na pracovišti, pak pomocí integrace virtuální sítě nebo služby App Service Environment je snadný způsob, jak využívat místních prostředků. Na druhé straně Pokud virtuální sítě není připojen k síti na pracovišti je mnohem víc zásahů, pokud chcete nastavit s2s VPN s vaší virtuální sítě ve srovnání s instalací HCM. 

Nad rámec funkční rozdíly, existuje jsou také ceny rozdíly. Funkce služby App Service Environment je prémiový nabídky služeb ale nabízí nejvíce sítě možnosti konfigurace kromě dalších funkcí. Integrace virtuální sítě můžete použít s Standard nebo Premium soubory ASP a je ideální pro bezpečné použití prostředky ve virtuální síti ze víceklientské služby App Service. Hybridní připojení se aktuálně závisí na BizTalk účtu, který má cenové úrovně, které spustit volné a potom získat progresivně nákladnější založenou na velikosti, které potřebujete. Pokud jde o práce v sítích s mnoha Přestože, neexistuje žádné jiné funkce, jako je hybridní připojení, která můžete povolit přístup k prostředkům do samostatných sítí a více než 100. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
