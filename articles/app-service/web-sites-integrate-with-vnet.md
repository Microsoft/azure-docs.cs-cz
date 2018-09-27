---
title: Integrace aplikace s Azure Virtual Network
description: Ukazuje, jak připojit aplikaci ve službě Azure App Service pro novou nebo existující virtuální síť Azure
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5eab09d5dffe16517e8c18eb0281716618ca0286
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166206"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrujte svou aplikaci s Azure Virtual Network
Tento dokument popisuje funkci integrace virtuální sítě Azure App Service a ukazuje, jak ho nastavit s aplikacemi v [služby Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Pokud nejste obeznámeni s Azure Virtual Networks (Vnet), jedná se o funkci, která umožňuje umístit řadu prostředků Azure, které řídí přístup k síti možnosti směrování Internetu jiných. Potom se dá propojit tyto sítí na vaše místní sítě pomocí různých technologií VPN. Další informace o Azure Virtual Network, začněte s tyto informace tady: [Přehled služby Azure Virtual Network][VNETOverview]. 

Azure App Service má dvě formy. 

1. Víceklientské systémy, které podporují celou škálu cenové plány
2. Funkce premium služby App Service Environment (ASE), která nasadí do vaší virtuální sítě. 

Tento dokument se odkazuje prostřednictvím integrace virtuální sítě a nikoli služby App Service Environment. Pokud chcete získat další informace o funkci služby ASE, začněte s tyto informace tady: [zavedení služby App Service Environment][ASEintro].

Integrace virtuální sítě poskytuje vaší webové aplikaci přístup k prostředkům ve vaší virtuální síti, ale nejsou udělena soukromý přístup do vaší webové aplikace z virtuální sítě. Přístup k webům privátní odkazuje na zpřístupnění aplikace jen z privátní sítě, jako z v rámci virtuální sítě Azure. Přístup k privátním serveru je k dispozici pouze s ASE nakonfigurovaný s interní zatížení nástroje pro vyrovnávání (ILB). Podrobnosti o použití prostředí ILB ASE začínat článku: [vytváření a používání prostředí ILB ASE][ILBASE]. 

Běžný scénář, kde můžete využít integrace virtuální sítě je povolení přístupu z vaší webové aplikace k databázi nebo webové služby spuštěné na virtuálním počítači ve službě Azure virtual network. Integrace virtuální sítě není nutné vystavit veřejný koncový bod pro aplikace na virtuální počítač ale může používání privátních adres směrovatelný bez internet místo. 

Funkci integrace virtuální sítě:

* vyžaduje Standard, Premium nebo izolované cenový plán 
* funguje s Classic nebo virtuální síť Resource Manageru 
* podporuje TCP a UDP
* funguje s webových, mobilních a API apps a aplikace Function App
* umožňuje aplikaci připojit k virtuální síti jenom 1 najednou
* umožňuje až pět virtuálních sítí s být integrován se službami v plán služby App Service 
* Umožňuje stejné virtuální síti používané více aplikacemi v plán služby App Service
* podporuje Brána virtuální sítě 99,9 % podle smlouvy SLA kvůli smlouvě SLA

Existuje několik věcí, které integrace virtuální sítě nepodporuje, včetně:

* připojení na jednotku
* Integrace služby AD 
* Rozhraní NetBios
* přístup privátního lokality

### <a name="getting-started"></a>Začínáme
Tady jsou některé možnosti brát v úvahu před připojením vaší webové aplikace k virtuální síti:

* Integrace virtuální sítě funguje jenom s aplikacemi v **standardní**, **Premium**, nebo **izolované** cenový plán. Pokud povolíte tuto funkci a pak škálovat plán služby App Service na nepodporované cenový plán vaše aplikace ztratí připojení k virtuálním sítím, které využívají. 
* Pokud cílová virtuální síť už existuje, musí mít VPN point-to-site předtím, než se dá připojit k aplikaci povoleno pomocí brány dynamického směrování. Pokud vaše brána je nakonfigurovaná s statické směrování, nelze povolit point-to-site virtuální privátní sítě (VPN).
* Virtuální síť musí být ve stejném předplatném jako služba Plan(ASP) vaší aplikace.
* Pokud již existuje brána s point-to-site povolena a není v základní SKU, musí se zakázat protokol IKEV2 v konfiguraci point-to-site.
* Aplikace, které se integrují s virtuální sítě pomocí DNS, který je určen pro tuto virtuální síť.
* Ve výchozím nastavení integrace aplikací pouze směrování provozu do vaší virtuální sítě na základě tras, které jsou definovány ve virtuální síti. 

## <a name="enabling-vnet-integration"></a>Povoluje se integrace virtuální sítě

Máte možnost se připojit vaši aplikaci do nové nebo existující virtuální sítě. Pokud vytvoříte novou síť jako součást vaší integrace, kromě vytvoření virtuální sítě, bránu dynamického směrování je předem nakonfigurovaný pro vás a je povolená síť Point-to-Site VPN. 

> [!NOTE]
> Konfigurace nově zavedené integraci virtuální sítě může trvat několik minut. 
> 
> 

Povolení integrace virtuální sítě, otevřete aplikaci nastavení a vyberte síť. Uživatelské rozhraní, které se otevře nabízí tři možnosti sítě. Tato příručka se pouze data přesunou do integrace virtuální sítě však Hybrid Connections a služby App Service Environment jsou popsány dále v tomto dokumentu. 

Pokud vaše aplikace není ve správné cenový plán, uživatelské rozhraní umožňuje škálovat plán vyšší cenového plánu podle vašeho výběru.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Povoluje se integrace virtuální sítě pomocí existující virtuální sítě
Integrace rozhraní virtuální sítě vám umožní vybrat ze seznamu vaše virtuální sítě. Klasické virtuální sítě znamenat, že jsou například slovo "Klasickém" v závorkách vedle názvu virtuální sítě. Seznam je seřazen tak, aby virtuální sítě Resource Manageru se zobrazují jako první. Na obrázku níže vidíte, že lze vybrat pouze jednu virtuální síť. Existuje několik důvodů, že virtuální síť můžete šedě včetně:

* virtuální síť je v jiném předplatném, které má váš účet přístup k
* virtuální síť nemá Point-to-Site povoleno
* virtuální síť nemá bránu dynamického směrování

![][2]

Chcete-li povolit integraci modulu, klikněte na virtuální síť, kterou chcete integrovat s. Jakmile vyberete virtuální síť, aplikace se automaticky restartuje se změny projevily. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Povolit Point-to-Site v klasické virtuální sítě

Pokud svou virtuální síť nemá bránu ani má Point-to-Site, budete muset nastavit první. Konfigurace brány pro klasické virtuální síti, přejděte na portál a otevřete seznam virtuální Networks(classic). Vyberte síť, kterou chcete integrovat. Vyberte připojení k síti VPN. Z tohoto místa můžete vytvořit váš bod na serveru VPN a dokonce i ho vytvořit bránu. Brány trvá přibližně 30 minut aktivován.

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Umožňující Point-to-Site ve virtuální síti správce prostředků
Konfigurace virtuální sítě Resource Manageru pomocí brány a Point-to-Site, můžete použít buď PowerShell podle postupu uvedeného zde [konfigurace připojení typu Point-to-Site k virtuální síti pomocí Powershellu] [ V2VNETP2S] nebo pomocí webu Azure portal podle postupu uvedeného zde [konfigurace připojení typu Point-to-Site k virtuální síti pomocí webu Azure portal][V2VNETPortal]. Uživatelské rozhraní k provedení této funkce není k dispozici. Není nutné vytvářet certifikáty pro konfigurace Point-to-Site. Certifikáty se automaticky vytvoří při připojení vaší webové aplikace k virtuální síti pomocí portálu. 

### <a name="creating-a-pre-configured-vnet"></a>Vytvoření předem nakonfigurované virtuální sítě
Pokud chcete vytvořit novou virtuální síť, která má nakonfigurovanou bránu a Point-to-Site, uživatelské rozhraní sítě služby App Service podporuje přechod do režimu to ale jenom pro virtuální síť Resource Manageru. Pokud chcete vytvořit klasickou virtuální síť s bránou a Point-to-Site, budete muset ručně to provést prostřednictvím uživatelského rozhraní sítě. 

Chcete-li vytvořit virtuální síť Resource Manageru prostřednictvím uživatelského rozhraní integrace virtuální sítě, vyberte **vytvořit novou virtuální síť** a poskytnout:

* Název virtuální sítě
* Blok adres služby Virtual Network
* Název podsítě
* Blok adres podsítě
* Blok adres brány
* Blok adres point-to-site

Pokud chcete tato virtuální síť připojila k jiným sítím, pak byste se měli vyhnout výběr adresní prostor IP adres, který se překrývá s těmito sítěmi. 

> [!NOTE]
> Vytvoření virtuální sítě Resource Manageru pomocí brány trvá přibližně 30 minut a aktuálně Neintegruje virtuální sítě s vaší aplikací. Po vytvoření virtuální sítě s bránou, budete muset vrátit do své aplikace uživatelského rozhraní integrace virtuální sítě a vyberte novou virtuální síť.
> 
> 

![][3]

Virtuálních sítí Azure se obvykle vytvoří v rámci adres privátní sítě. Funkce ve výchozím nastavení integrace virtuální sítě směruje veškerý provoz směřující do vaší virtuální sítě pro tyto rozsahy IP adres. Rozsahy privátních IP adres jsou:

* 10.0.0.0/8 – to je stejný jako 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 – to je stejný jako 172.16.0.0 – 172.31.255.255 
* 192.168.0.0/16 – to je stejný jako 192.168.0.0 - 192.168.255.255

Adresní prostor virtuální sítě je nutné zadat v notaci CIDR. Pokud nejste obeznámeni s notaci CIDR, představuje metodu pro určení bloky adres pomocí IP adresy a celé číslo, které představuje maska sítě. Jako rychlý odkaz vezměte v úvahu, že 10.1.0.0/24 by 256 adres a 10.1.0.0/25 by 128 adres. Adresu IPv4 s /32 by jenom 1 adresu. 

Pokud tady nastavíte, informace o serveru DNS, který je nastavte pro vaši virtuální síť. Po vytvoření virtuální sítě můžete upravit tyto informace z virtuální sítě uživatelské prostředí. Pokud změníte DNS virtuální sítě, budete muset provést operaci synchronizace sítě.

Když vytvoříte klasické virtuální sítě pomocí integrace rozhraní virtuální sítě, vytvoří virtuální síť ve stejné skupině prostředků jako aplikace. 

## <a name="how-the-system-works"></a>Jak funguje v systému
Na pozadí postavená tato funkce technologie sítě VPN Point-to-Site k připojení aplikace k virtuální síti. Aplikace ve službě Azure App Service mají architekturu systému s více tenanty, která vylučuje zřizování aplikace přímo ve virtuální síti, jak se provádí s virtuálními počítači. Vytvořením point-to-site technologie jsme omezení síťového přístupu k pouze na virtuální počítač hostující aplikaci. Přístup k síti je dále omezen na hostitelích aplikace tak, aby vaše aplikace můžou přistupovat pouze k sítě, které můžete konfigurovat jejich přístupu k. 

![][4]

Pokud jste nenakonfigurovali DNS server s virtuální sítí, musíte použít IP adresy k dosažení prostředků ve virtuální síti vaší aplikace. Při použití IP adresy, mějte na paměti, že je hlavní výhodou tato funkce umožňuje používání privátních adres v rámci vaší privátní sítě. Pokud nastavíte, aby vaše aplikace až po použití veřejné IP adresy pro jeden z vašich virtuálních počítačů a potom nepoužíváte funkci integrace virtuální sítě a komunikují přes internet.

## <a name="managing-the-vnet-integrations"></a>Správa integrace virtuální sítě
Možnost připojit a odpojit virtuální sítě je na úrovni aplikace. Operace, které můžou ovlivnit integrace virtuální sítě mezi více aplikacemi jsou na úrovni ASP. Z uživatelského rozhraní, které se zobrazí na úrovni aplikace můžete získat podrobnosti o ve virtuální síti. Většina stejné informace je zobrazen také na úrovni ASP. 

![][5]

Na stránce stav síťových funkcí můžete zobrazit, pokud vaše aplikace je připojená k virtuální síti. Pokud bránu vaší virtuální sítě je vypnutý z jakéhokoli důvodu, pak toto zobrazí jako není připojený. 

Informace, které teď máte k dispozici v aplikaci, kterou úroveň uživatelského rozhraní integrace virtuální sítě je stejný jako podrobné informace, které získáte ze ASP. Tady jsou tyto položky:

* Název virtuální sítě – tento odkaz otevře virtuální síť Azure uživatelského rozhraní
* Umístění, to odpovídá umístění sítě vnet. Je možné integrovat s virtuální sítí v jiném umístění.
* Stav certifikátu – jsou certifikáty používané k zabezpečení připojení VPN mezi aplikací a virtuální síť. To odpovídá testu k zajištění, že jsou synchronizované.
* Stav brány – vaše brány měli dolů z jakéhokoli důvodu pak vaše aplikace nemá přístup k prostředkům ve virtuální síti. 
* Adresní prostor virtuální sítě – to je pro vaši virtuální síť adresní prostor IP adres. 
* Point-to-Site adresní prostor – to je bod tak, aby lokality IP adresní prostor sítě vnet. Vaše aplikace zobrazuje komunikace jako pocházející z jednoho z IP adresy v tento adresní prostor. 
* Lokality do lokality adresního prostoru - Site to Site VPN umožňuje propojit vaši virtuální síť k místním prostředkům nebo k druhé virtuální sítě. Pokud máte nakonfigurovaný a rozsahy IP adres definované pomocí zde ukazuje připojení k síti VPN.
* Servery DNS – Pokud máte serverům DNS nakonfigurovaným s vaší virtuální síti, pak jsou zde uvedeny.
* IP adresy směrovat do virtuální sítě – zde jsou seznam IP adres, které má směrování definované pro vaše virtuální síť a zobrazit tyto adresy tady. 

Jedinou operací, které si můžete v zobrazení aplikace integrace vaší virtuální sítě je vaše aplikace se odpojí z aktuálně připojené k virtuální síti. Chcete-li vaše aplikace se odpojí z virtuální sítě, odpojte v horní části. Tato akce nezmění vaši virtuální síť. Virtuální síť a jeho konfigurace, včetně brány zůstávají beze změn. Pokud chcete poté odstranit virtuální síť, musíte nejprve odstranit prostředky v ní včetně brány. 

Zobrazení plánu služby App Service má několik dalších operací. To je také přistupovat jinak než z aplikace. Otevřete rozhraní ASP sítě, otevřete uživatelské rozhraní ASP a přejděte dolů. Vyberte "Stav síťových funkcí" otevřete uživatelské rozhraní funkce stavu sítě. Vyberte "Kliknutím sem můžete spravovat" do seznamu integrace virtuální sítě v tomto prostředí ASP.

![][6]

Umístění ASP je dobré pamatovat i při pohledu na umístění virtuální sítě, kterými budete nástroj integrovat. Pokud virtuální síť je v jiném umístění jsou mnohem pravděpodobnější problémy s latencí. 

Integrovaná s virtuálními sítěmi je připomenutí na tom, kolik virtuálních sítí, že vaše aplikace jsou integrované s v tomto ASP a kolik máte. 

Chcete-li zobrazit byly přidané podrobnosti o každé virtuální síti, stačí klikněte na virtuální sítě, které vás zajímají. Kromě podrobností, které jste si poznamenali dříve můžete také zobrazit seznam aplikací v tomto prostředí ASP, které používají tuto virtuální síť. 

S ohledem na akce jsou dvě primární akce. První je schopnost přidejte trasy, které řídí přenosů z vaší aplikace do virtuální sítě. Druhou akci je schopnost synchronizovat certifikáty a informace o síti.

![][7]

**Směrování** jak bylo uvedeno dříve, tras, které jsou definovány ve virtuální síti se, co se používá pro směrování provozu do vaší virtuální sítě z vaší aplikace. Existují některé používá v případě, že pokud zákazníci chtějí odeslat další odchozí přenos z aplikace do virtuální sítě a pro ně tato funkce je k dispozici. Co se stane na provoz, který po až po tom, jak zákazník nakonfiguruje své virtuální síti. 

**Certifikáty** The stav certifikátu odráží kontrola prováděného služby App Service k ověření, že jsou pořád dobré certifikáty, které se používá pro připojení k síti VPN. Když integrace virtuální sítě povolené, pokud je první integrace do ní z jakékoli aplikace v tomto prostředí ASP nejsou požadované výměny certifikáty pro zajištění zabezpečení připojení. Spolu s certifikáty získáme konfiguraci serveru DNS, cesty a další podobné věci, které popisují sítě.
Pokud tyto certifikáty nebo informace o síti se změní, budete muset kliknout na "Synchronizovat síť". **Poznámka:**: po klepnutí na tlačítko "Synchronizovat síť", pak způsobit výpadek připojení mezi vaší aplikací a virtuální síť. Když vaše aplikace nerestartuje, ztráta připojení by mohlo způsobit webu nebude fungovat správně. 

## <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům
Jednou z výhod funkci integrace virtuální sítě je, že pokud vaše virtuální sítě je připojený k vaší místní síti prostřednictvím sítě Site to Site VPN se vaše aplikace může mít přístup k místním prostředkům z vaší aplikace. Aby to fungovalo v případě, že budete muset aktualizovat bránu místní sítě VPN s trasy pro vaše IP adresa Point-to-Site v rozsahu. Při S2s VPN nejprve nastavit skripty používané k jeho konfiguraci by měl nastavit cesty včetně vaši síť Point-to-Site VPN. Přidáte-li po vytvoření S2s VPN Point-to-Site VPN, budete muset aktualizovat trasy ručně. Podrobnosti o tom, jak to udělat za bránu se liší a nejsou zde popsané. 

> [!NOTE]
> Funkci integrace virtuální sítě nejde integrovat do aplikace virtuální síť, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [koexistence režimu] [ VPNERCoex] nefunguje integrace virtuální sítě. Pokud potřebujete přístup k prostředkům pomocí připojení ExpressRoute, pak můžete použít [služby App Service Environment][ASE], která se spouští ve vaší virtuální síti.
> 
> 

## <a name="pricing-details"></a>Podrobnosti o cenách
Existuje několik ceny drobné rozdíly, které byste měli vědět, když používáte funkci integrace virtuální sítě. Existují 3 související poplatky za využívání této funkce:

* Ceny za požadavky na úroveň ASP
* Náklady na přenos dat
* Náklady na brány sítě VPN.

Pro vaše aplikace, abyste mohli tuto funkci používat musí být v plánu App Service Premium nebo Standard. Můžete zobrazit další podrobnosti o těchto náklady na: [App Service – ceny][ASPricing]. 

Z důvodu zpracování VPN typu Point-to-Site vždy máte poplatky za odchozí data přes připojení integrace virtuální sítě i v případě, že virtuální sítě je ve stejném datovém centru. Pokud chcete zobrazit, co jsou tyto poplatky, podívejte se sem: [Data Transfer podrobnosti o cenách][DataPricing]. 

Poslední položka stojí brány virtuální sítě. Pokud nepotřebujete brány na něco jiného, jako je například Site to Site VPN, pak platíte za brány podporují funkci integrace virtuální sítě. Na náklady, tady jsou podrobnosti: [ceny služby VPN Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>Řešení potíží
Tato funkce je snadné nastavení, to ale neznamená, že vaše zkušenosti budou problém zdarma. By měl narazíte na problémy s přístupem k dispozici požadovaný koncový bod se některé nástroje, které lze použít k testování připojení z konzoly aplikace. Existují dvě prostředí konzoly, která vám pomůže. Jedna je z konzoly Kudu a druhý je konzola, která můžete oslovit na webu Azure Portal. Chcete-li získat ke konzole Kudu z vaší aplikace přejděte na Nástroje -> Kudu. To je stejný jako chystáte [název_serveru]. scm.azurewebsites.net. Po, která otevře, přejděte na kartu konzoly pro ladění. Zobrazíte konzolu Azure portal hostované potom z vaší aplikace přejděte na Nástroje -> Konzola. 

#### <a name="tools"></a>Nástroje
Nástroje **ping**, **nslookup** a **tracert** nebude fungovat přes konzolu z důvodu omezení zabezpečení. K vyplnění void zde byly dvou samostatných nástrojů přidat. Pokud chcete otestovat funkci DNS jsme přidali nástroj s názvem nameresolver.exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

Můžete použít **nameresolver** zkontrolovat názvy hostitelů, které vaše aplikace závisí. Tímto způsobem můžete otestovat Pokud nic s DNS chybně nakonfigurovaná nebo možná nebudete mít přístup k vašemu serveru DNS.

Další nástroj umožňuje otestovat pro připojení TCP ke kombinaci hostitele a port. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

**Tcpping** nástroj informuje, pokud můžete oslovit konkrétního hostitele a port. Jenom se zobrazí úspěch pokud: neexistuje aplikace naslouchá na kombinaci hostitele a port, a je přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístup k virtuální síti hostovala prostředky
Existuje mnoho věcí, které můžete aplikaci zabránit v dosažení konkrétního hostitele a port. Ve většině případů představuje jednu ze tří věcí:

* **Je brána firewall tak, jak** Pokud máte bránu firewall tak, jak se dostanou časového limitu protokolu TCP. Který v tomto případě je 21 sekund. Použití **tcpping** nástroj k testování připojení. Vypršení časového limitu TCP může být mnoho věcí mimo brány firewall ale začít existuje. 
* **DNS není dostupný** DNS vypršení časového limitu je tří sekund na serveru DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zobrazit, pokud DNS funguje. Mějte na paměti, že nslookup nelze použít jako, který nepoužívá DNS virtuální sítě se nakonfigurují.
* **Neplatný rozsah P2S IP** bod tak, aby lokality rozsah IP adres musí být v dokumentu RFC 1918 rozsahy privátních IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Rozsah IP adres používá mimo, který, nebudou fungovat věci. 

Pokud váš problém není odpovědi na ty položky, podívejte se první pro jednoduché věci, jako jsou: 

* Zobrazit brány jako nahoru na portálu?
* Certifikáty zobrazit jako synchronizované?
* Aniž by "Synchronizace síť" v ovlivněných používalo kdokoli měnit síťovou konfiguraci? 

Pokud vaše brána je vypnutý, pak ho přeneste zálohování. Pokud vaše certifikáty nejsou synchronizované, přejděte do zobrazení ASP integrace vaší virtuální sítě a dosažení "Synchronizovat síť". Pokud máte podezření, že došlo ke změně vaše konfigurace virtuální sítě a nebyl synchronizace by se používalo, pak přejděte do zobrazení ASP integrace vaší virtuální sítě a přístupů "Sítě synchronizace" těsně Připomínáme, protože to způsobuje výpadek připojení k virtuální síti a vaše aplikace. 

Pokud je to v pořádku, budete muset podrobněji trochu podrobněji:

* Existují nějaké jiné aplikace pomocí integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti? 
* Můžete přejít do konzoly aplikace a použít tcpping k dosažení jiných prostředků ve vaší virtuální síti? 

Pokud platí některá z výše uvedeného, pak vaše integrace virtuální sítě je v pořádku a problému je někde jinde. To je, ve kterém získá si více složité, protože neexistuje jednoduchý způsob zobrazíte, proč nelze kontaktovat hostitele: port. Mezi příčiny patří:

* máte bránu firewall na hostiteli brání přístupu k portu aplikace z bodu na webu rozsah IP adres. Překročení podsítě často vyžaduje veřejný přístup.
* Cílový hostitel je mimo provoz
* vaše aplikace je mimo provoz
* Máte nesprávné IP adresu nebo název hostitele
* vaše aplikace naslouchá na jiném portu, než jste očekávali. Můžete zkontrolovat tak, že přejdete na tomto hostiteli a pomocí "netstat - aon" z příkazového řádku. To se dozvíte, jaký proces ID naslouchá na port. 
* Vaše skupiny zabezpečení sítě jsou nakonfigurovány tak, že zabránit přístupu k aplikaci hostitele a port z bodu lokality rozsah IP

Mějte na paměti, že nevíte jaký IP v rozsahu Point-to-Site IP adres, které vaše aplikace bude používat, musíte povolit přístup z celý rozsah. 

Další ladění postup je následující:

* připojení k virtuálnímu počítači ve virtuální síti a pokusí se kontaktovat hostitele: port prostředků z něj. K otestování pro protokol TCP přístup, použijte příkaz prostředí PowerShell **test-netconnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Otevřete aplikaci na virtuální počítač a otestujte přístup na daném hostiteli a portu z konzoly z aplikace

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud vaše aplikace nelze dosáhnout místní prostředek, zaškrtněte, pokud můžete oslovit prostředku z vaší virtuální sítě. Použití **test-netconnection** k tomu příkaz prostředí PowerShell. Pokud se váš virtuální počítač nemůže připojit místnímu prostředku, ujistěte se, že je funkční připojení k síti VPN S2s. Pokud funguje, zkontrolujte stejné věci, které jste si předtím poznamenali a konfigurace místní brány a stav. 

Pokud hostovaný virtuální síť virtuálního počítače můžete dosáhnout místního systému, ale aplikace nemůže z důvodu je jedním z následujících akcí:

* trasy nejsou konfigurované se váš bod do lokality rozsahy IP adres ve vaší místní brány
* Vaše skupiny zabezpečení sítě blokuje přístup pro rozsah IP Point-to-Site
* místní brány firewall, blokují přenosy z rozsahu Point-to-Site IP adres
* máte ve virtuální síti, která zabrání dosažení vaši místní síť Point-to-Site na základě provozu uživatele definované trasu (udr)

## <a name="powershell-automation"></a>Automatizace Powershellu

Integrujte službu App Service pomocí Azure Virtual Network pomocí Powershellu. Připraveno ke spuštění skriptu, naleznete v tématu [aplikace ve službě Azure App Service se připojit ke službě Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hybrid Connections a služby App Service Environment
Existují tři funkce, které umožňují přístup k prostředkům hostované virtuální sítě. Jsou to tyto:

* Integrace virtuální sítě
* Hybridní připojení
* Prostředí App Service

Hybridní připojení vyžaduje instalaci přenosový agent volá Manager(HCM) hybridní připojení se ve vaší síti. HCM musí být schopný se připojit k Azure a také do vaší aplikace. Toto řešení je zvlášť skvělé ze vzdálené sítě, jako je například v místní síti nebo dokonce pro jiný cloud hostované sítě, protože ta není nutné internet přístupném koncovém bodu. HCM pouze běží na Windows a může mít až pět instancí spuštěných pro zajištění vysoké dostupnosti. Hybridní připojení podporuje jenom TCP ale a každý koncový bod hybridní připojení se musí shodovat na konkrétní port hostitele: kombinaci. 

Funkce App Service Environment umožňuje spustit instance služby Azure App Service ve vaší virtuální síti. Díky tomu vaše aplikace přístup k prostředkům ve vaší virtuální síti bez nějaké dodatečné kroky. Některé z dalších výhod služby App Service Environment jsou, které můžete použít pracovních procesů na základě Dv2 s až 14 GB paměti RAM. Další výhodou je, že systém podle potřeby je možné škálovat. Na rozdíl od víceklientského prostředí, ve kterém je omezený na 20 instancí vaší ASP ve službě ASE můžete škálovat až 100 instancí ASP. Jednou z věcí, které získáte pomocí služby ASE, nemusí se integrace virtuální sítě je, že služby App Service Environment může fungovat s ExpressRoute VPN. 

Zatímco je, že některé používají případu překrývají, žádný z těchto funkcí můžete nahradit všechny ostatní. Vědět, jaké funkce pro použití se váže na vaše potřeby. Příklad:

* Pokud jako vývojář a chcete do lokality v Azure a jeho přístup k databázi na pracovní stanici pod svým stolem, je nejsnazší používat hybridní připojení. 
* Pokud jste ve velkých organizacích s chce velký počet vlastností webu do veřejného cloudu a spravovat je ve vaší vlastní sítě a chcete přejít pomocí služby App Service Environment. 
* Pokud máte víc aplikací, které potřebují přístup k prostředkům ve vaší virtuální síti, integrace virtuální sítě je způsob přechodu. 

Pokud svou virtuální síť je již připojen k místní síti, pak pomocí integrace virtuální sítě nebo služby App Service Environment je snadný způsob, jak používat místní prostředky. Pokud svou virtuální síť není připojený k vaší místní síti, je mnohem vyšší mírou režie nastavení s2s VPN s vaší virtuální sítě ve srovnání s instalací HCM. 

Nad rámec funkční rozdíly, že jsou také cenové rozdíly. Funkce App Service Environment je na úrovni Premium nabídky služeb ale nabízí největší sítě možnosti konfigurace kromě dalších skvělých funkcí. Integrace virtuální sítě je možné s Standard nebo Premium používalo a je ideální pro bezpečné používání prostředků ve virtuální síti ze služby App Service pro více tenantů. Hybridní připojení se aktuálně závisí na BizTalk účtu, který má cenové úrovně, které začít zdarma a získejte postupně dražší, na základě doby, co potřebujete. Při rozhodování o práci napříč mnoha sítím ale, neexistuje žádná funkce jako Hybrid Connections, které vám umožní přístup k prostředkům v samostatných sítích i více než 100. 

## <a name="new-vnet-integration"></a>Nově zavedené integraci virtuální sítě ##

Existuje nová verze funkci integrace virtuální sítě, která nezávisí na technologie sítě VPN Point-to-Site. Tato nová verze je ve verzi Preview. Novou funkci integrace virtuální sítě má následující vlastnosti.

- Nová funkce vyžaduje nevyužité podsítě ve virtuální síti správce prostředků
- Jedna adresa se používá pro všechny instance plánu služby App Service. Protože se po přiřazení nelze změnit velikost podsítě, použijte podsíť, která může zahrnovat více než maximální měřítko. Možnost/27 s 32 adres je doporučená velikost, která by odpovídala plán služby App Service, který je škálovat, aby 20 instancí.
- Můžete využívat koncový bod služby Zabezpečené prostředky díky nové funkci integrace virtuální sítě. Povolení přístupu z podsítě přiřazené vaší aplikaci ke konfiguraci koncových bodů služby s vaší aplikací
- Prostředky můžete přistupovat přes připojení ExpressRoute, bez jakékoli další konfigurace
- Žádná brána je potřeba využít novou funkci integrace virtuální sítě
- Plán služby App Service musí být na plán Standard, Premium nebo PremiumV2
- Nová funkce je dostupná pouze z novější jednotky škálování Azure App Service. Na portálu vám sdělí, pokud vaše aplikace může využívat novou funkci integrace virtuální sítě. 
- Aplikace a virtuální síť musí být ve stejné oblasti

Novou funkci integrace virtuální sítě je zpočátku k dispozici pouze v oblastech severní Evropa a východní USA.


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
