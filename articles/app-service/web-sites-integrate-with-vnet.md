---
title: Integrace aplikace s Azure Virtual Network – služba Azure App Service
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
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dcb128d8793e3438d87e728bde069d07c72cf97b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493031"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrujte svou aplikaci s Azure Virtual Network
Tento dokument popisuje funkci integrace virtuální sítě Azure App Service a jak ho nastavit s aplikacemi v [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Azure Virtual Networks] [ VNETOverview] (Vnet) umožňuje umístit řadu prostředků Azure v síti bez Internetu možnosti směrování.  

Azure App Service má dvě formy. 

1. Víceklientské systémy, které podporují celou škálu cenové plány s výjimkou izolovaný režim
2. App Service Environment (ASE), která nasadí do vaší virtuální sítě a podporuje s izolovanou cenou plánu aplikace

Tento dokument prochází dvě funkce integrace virtuální sítě, který je určen pro použití ve službě App Service pro více tenantů. Pokud je vaše aplikace v [služby App Service Environment][ASEintro], pak se už ve virtuální síti a nevyžaduje, použijte funkci integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti. Informace o všech síťových funkcí služby App Service, najdete v článku [síťové funkce služby App Service](networking-features.md)

Existují dva typy k funkci integrace virtuální sítě

1. Jedna verze umožňuje integraci s virtuálními sítěmi ve stejné oblasti. Tato forma funkci vyžaduje velikost podsítě ve virtuální síti ve stejné oblasti
2. Jiné verze umožňuje integraci s virtuálními sítěmi v jiných oblastech nebo s klasickými virtuálními sítěmi. Tato verze funkce vyžaduje nasazení brány virtuální sítě do virtuální sítě.

Aplikace lze použít pouze jednu formu funkci integrace virtuální sítě v čase. Pak otázkou je, které funkce byste měli použít. Můžete použít buď pro řadu věcí. Vymazat rozdíly ale jsou:

| Problém  | Řešení | 
|----------|----------|
| Chcete přístup adrese RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ve stejné oblasti | místní integrace virtuální sítě |
| Chcete dosáhnout klasickou virtuální síť nebo virtuální sítě v jiné oblasti | Brána vyžaduje integrace virtuální sítě |
| Chcete kontaktovat koncové body RFC 1918 přes ExpressRoute | místní integrace virtuální sítě |
| Chcete mít přístup k prostředkům napříč koncovými body služby | místní integrace virtuální sítě |

Ani jedna funkce vám umožní oslovit RFC 1918 adresy přes ExpressRoute. K tomu je nutné použít prostředí ASE teď.

Pomocí místní integrace virtuální sítě propojit vaši virtuální síť k místní nebo konfigurace koncových bodů služby. Která je oddělená konfiguraci sítě. Místní integrace virtuální sítě slouží jenom vaše aplikace provést volání mezi těmito typy připojení.

Bez ohledu na verzi použít integrace virtuální sítě poskytuje vaší webové aplikaci přístup k prostředkům ve vaší virtuální síti, ale nebude udělit příchozí privátní přístup do vaší webové aplikace z virtuální sítě. Přístup k webům privátní odkazuje na zpřístupnění aplikace jen z privátní sítě, jako z v rámci virtuální sítě Azure. Integrace virtuální sítě je pouze pro volání odchozí z vaší aplikace do virtuální sítě. 

Funkci integrace virtuální sítě:

* vyžaduje Standard, Premium nebo PremiumV2 cenový plán 
* podporuje TCP a UDP
* funguje s aplikacemi App Service a aplikace Function App

Existuje několik věcí, které integrace virtuální sítě nepodporuje, včetně:

* připojení na jednotku
* Integrace služby AD 
* NetBIOS

## <a name="regional-vnet-integration"></a>místní integrace virtuální sítě 

Při použití integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti jako vaše aplikace vyžaduje použití delegovaného podsíť s alespoň 32 adres. Podsíť se nedá použít na něco jiného. Z adresy v podsíti delegované se pošle odchozích volání z vaší aplikace. Při použití této verzi systému integrace virtuální sítě volání z adresy ve vaší virtuální síti. Pomocí adresy ve vaší virtuální síti umožňuje vaší aplikaci:

* volání do koncového bodu služby službám zabezpečeným přes službu
* přístup k prostředkům přes připojení ExpressRoute
* přístup k prostředkům ve virtuální síti připojeni k
* přístup k prostředkům v partnerské připojení, včetně připojení ExpressRoute

Tato funkce je ve verzi preview, ale podporují ji pro produkční úlohy s těmito omezeními:

* můžete přistupovat pouze adresy, které jsou v dokumentu RFC 1918 rozsahu. Toto jsou adresy 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 bloky adres.
* budete mít přístup k prostředkům napříč globální partnerské vztahy virtuálních sítí
* Nelze nastavit trasy na provoz přicházející z vaší aplikace do virtuální sítě
* Tato funkce je jen k dispozici novější jednotek škálování služby App Service, které podporují plány služby App Service PremiumV2.
* Tuto funkci nelze použít plán Isolated aplikace, které jsou ve službě App Service Environment
* Tato funkce vyžaduje nevyužité podsíť s alespoň 32 adres ve vaší virtuální síti správce prostředků.
* Aplikace a virtuální síť musí být ve stejné oblasti
* Jedna adresa se používá pro všechny instance plánu služby App Service. Protože se po přiřazení nelze změnit velikost podsítě, použijte podsíť, která může zahrnovat více než maximální měřítko. Možnost/27 s 32 adres je doporučená velikost, která by odpovídala plán služby App Service, který je škálovat, aby 20 instancí.
* Nelze odstranit virtuální síť, která integrované aplikace. Je nutné nejprve odebrat integrace 

Jak používat funkci integrace virtuální sítě pomocí virtuální sítě Resource Manageru ve stejné oblasti:

1. Přejděte na uživatelské rozhraní sítě na portálu. Pokud je vaše aplikace využívat nové funkce, se zobrazí možnost přidání virtuální sítě (preview).  

   ![Vyberte integrace virtuální sítě][6]

1. Vyberte **přidání virtuální sítě (preview)** .  

1. Vyberte virtuální síť Resource Manageru, kterou chcete integrovat a pak vytvořit novou podsíť nebo vyberte existující podsíť. Integrace trvá méně než minutu. Během integrace se aplikace restartuje.  Po dokončení integrace, zobrazí se podrobnosti na jsou integrované s virtuální sítí a banner v horní části stránky, dozvíte se, že tato funkce je ve verzi preview.

   ![Vyberte virtuální síť a podsíť][7]

Jakmile vaše aplikace je integrovaný s vaší virtuální sítě, bude používat stejný server DNS nakonfigurovaný s vaší virtuální sítě. 

Chcete-li vaše aplikace se odpojí z virtuální sítě, vyberte **odpojit**. Tato operace restartuje vaše webová aplikace. 

Novou funkci integrace virtuální sítě vám umožní použít koncové body služby.  Koncové body služby s vaší aplikací, použití nově zavedené integraci virtuální sítě pro připojení k vybrané virtuální síti a potom nakonfigurujte koncové body služby v podsíti, který jste použili pro integraci. 

### <a name="how-vnet-integration-works"></a>Jak funguje integrace virtuální sítě

Aplikace ve službě App Service jsou hostované na role pracovních procesů. Základní a vyšší cenové plány jsou vyhrazené plány hostování Pokud neexistují žádné další zákazníci pracovní postupy spouštěné ve stejné pracovní procesy. Integrace virtuální sítě funguje tak, že připojí virtuální rozhraní s adresami v podsíti delegovaný. Vzhledem k tomu, z adresa je ve vaší virtuální síti, má přístup k většinu toho, co v nebo prostřednictvím virtuální sítě stejně, jako by virtuální počítač ve vaší virtuální síti. Síťové implementaci se liší od spuštění virtuálního počítače ve vaší virtuální sítí a, který je proč některých síťových funkcí ještě nejsou k dispozici při použití této funkce.

![Integrace virtuální sítě](media/web-sites-integrate-with-vnet/vnet-integration.png)

Při zapnuté funkci integrace virtuální sítě, aplikace bude stále volání odchozí internetové stejnou cestou jako za normálních okolností. Odchozí adresy, které jsou uvedeny na vlastnosti portálu aplikace jsou stále adresy používané v aplikaci. Co jsou změny provedené u vaší aplikace, službám zabezpečeným přes službu volání koncového bodu služby, nebo RFC 1918 adres přejde do vaší virtuální sítě. 

Funkce se podporuje jenom jeden virtuální rozhraní za pracovního procesu.  Jeden virtuální rozhraní za pracovního procesu znamená, že jedno virtuální rozhraní za plán služby App Service. Všechny aplikace v rámci stejného plánu služby App Service můžete použít stejné integrace virtuální sítě, ale pokud potřebujete připojit k další síti, musíte vytvořit jiný plán služby App Service. Virtuální rozhraní použité není prostředek, který zákazníci mají přímý přístup k.

Vzhledem k povaze jak tato technologie funguje provoz, který se používá s integrace virtuální sítě není uveden v protokoly toku Network Watcher nebo skupiny zabezpečení sítě.  

## <a name="gateway-required-vnet-integration"></a>Brána vyžaduje integrace virtuální sítě 

Brána vyžaduje funkci integrace virtuální sítě:

* je možné se připojit k virtuálním sítím v libovolné oblasti se jejich Resource Manageru nebo klasických virtuálních sítí
* umožňuje aplikaci připojit k virtuální síti jenom 1 najednou
* umožňuje až pět virtuálních sítí s být integrován se službami v plán služby App Service 
* Umožňuje stejné virtuální síti používané více aplikacemi v plánu služby App Service bez dopadu na celkový počet, který mohou využívat plán služby App Service.  Pokud máte 6 aplikace s využitím stejné virtuální síti ve stejném plánu služby App Service, který se počítá jako 1 virtuální sítě používá. 
* vyžaduje bránu virtuální sítě, který je nakonfigurovaný s bodem pro síť VPN typu Site
* podporuje SLA 99,9 % z důvodu smlouvu SLA na bráně

Tato funkce nepodporuje:

* přístup k prostředkům prostřednictvím ExpressRoute 
* přístup k prostředkům prostřednictvím koncových bodů služby 

### <a name="getting-started"></a>Začínáme

Tady jsou některé možnosti brát v úvahu před připojením vaší webové aplikace k virtuální síti:

* Cílová virtuální síť musí mít point-to-site VPN povolené s bránou založenou na směrování, předtím, než se dá připojit k aplikaci. 
* Virtuální síť musí být ve stejném předplatném jako služba Plan(ASP) vaší aplikace.
* Aplikace, které se integrují s virtuální sítě pomocí DNS, který je určen pro tuto virtuální síť.

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavit bránu ve vaší virtuální síti ###

Pokud jste již bránu nakonfigurovanou adresu point-to-site, můžete přeskočit ke konfiguraci integrace virtuální sítě s vaší aplikací.  
Pokud chcete vytvořit bránu:

1. [Vytvořit podsíť brány] [ creategatewaysubnet] ve vaší virtuální síti.  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založené na směrování.

1. [Nastavit bod do lokality adresy][setp2saddresses]. Pokud brána není v základní SKU, pak musí se zakázat protokol IKEV2 v konfiguraci point-to-site a musí být vybrán SSTP. Adresní prostor musí být v dokumentu RFC 1918 bloky adres, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Pokud jste právě vytvořením brány pro použití s integrace virtuální sítě služby App, potom není potřeba nahrát certifikát. Vytvoření brány může trvat 30 minut. Nebude moct integrujte svou aplikaci s vaší virtuální síti, dokud se brána zřídí. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurace integrace virtuální sítě s vaší aplikací 

Povolení integrace virtuální sítě ve vaší aplikaci: 

1. Přejděte do vaší aplikace na webu Azure Portal a otevřete aplikaci nastavení a vyberte sítě > integrace virtuální sítě. Vaše prostředí ASP musí být ve standardním SKU nebo je lepší použít buď funkci integrace virtuální sítě. 
 ![Integrace virtuální sítě uživatelského rozhraní][1]

1. Vyberte **přidat virtuální síť**. 
 ![Přidání integrace virtuální sítě][2]

1. Vyberte virtuální síť. 
  ![Vyberte virtuální síť][8]
  
Vaše aplikace se restartuje po tomto posledním kroku.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Jak brána vyžaduje funkci integrace virtuální sítě funguje

Brána požadované funkci integrace virtuální sítě je postavený na technologii point-to-site VPN. Technologie point-to-site omezuje přístup k síti na pouze na virtuální počítač hostující aplikaci. Aplikace jsou omezeny pouze směrovat provoz do Internetu přes hybridní připojení, nebo prostřednictvím integrace virtuální sítě. 

![Jak funguje integrace virtuální sítě][3]

## <a name="managing-vnet-integration"></a>Správa integrace virtuální sítě
Možnost připojit a odpojit virtuální sítě je na úrovni aplikace. Operace, které můžou ovlivnit integrace virtuální sítě mezi více aplikacemi jsou na úrovni plánu služby App Service. Z aplikace > sítě > portál integrace virtuální sítě, můžete získat podrobnosti o ve virtuální síti. Zobrazí podobné informace na úrovni prostředí ASP v ASP > sítě > portál integrace virtuální sítě, včetně toho, jaké aplikace v tomto plánu služby App Service využívají danou integraci.

 ![Podrobnosti virtuální sítě][4]

Informace, které máte k dispozici pro vás v uživatelském rozhraní integrace virtuální sítě je stejný mezi aplikací a portály ASP.

* Název virtuální sítě – odkazy na uživatelské rozhraní ve virtuální síti
* Umístění – odráží umístění sítě vnet. Integrace s virtuální sítí v jiném umístění, může způsobit problémy s latencí pro vaši aplikaci. 
* Stav certifikátu – odpovídá, pokud vaše certifikáty jsou synchronizované mezi plán služby App Service a vaší virtuální sítě.
* Stav brány - by měl být používáte brány vyžaduje integrace virtuální sítě, zobrazí se stav brány.
* Adresní prostor virtuální sítě – zobrazuje adresní prostor IP adres pro vaši virtuální síť. 
* Point-to-site adresního prostoru - ukazuje bod tak, aby lokality adresní prostor IP adres pro vaši virtuální síť. Při volání do vaší virtuální síti při použití požadované funkce brány, adresu z vaší aplikace bude jeden z těchto adres. 
* Site-to-site adresního prostoru - můžete použít VPN typu site-to-site k propojení vaší virtuální sítě k místním prostředkům nebo jinou virtuální sítí. Rozsahy IP adres definované pomocí tohoto připojení VPN se tady zobrazí.
* Servery DNS - ukazuje DNS servery nakonfigurované s vaší virtuální sítě.
* IP adresy směrované do virtuální sítě – zobrazuje bloky adres směrují lze přesměrovat provoz do vaší virtuální sítě 

Jedinou operací, které si můžete v zobrazení aplikace integrace vaší virtuální sítě je vaše aplikace se odpojí z aktuálně připojené k virtuální síti. Chcete-li vaše aplikace se odpojí z virtuální sítě, vyberte **odpojit**. Vaše aplikace se restartuje, pokud odpojíte z virtuální sítě. Odpojuje se nezmění vaši virtuální síť. Podsíť nebo brány se neodebere. Pokud chcete poté odstranit virtuální síť, musíte nejprve vaše aplikace se odpojí z virtuální sítě a odstranit prostředky, jako jsou brány v něm. 

K dosažení integrace rozhraní ASP virtuální sítě, otevřete uživatelské rozhraní ASP a vyberte **sítě**.  V části Integrace virtuální sítě, vyberte **klepnutím sem můžete nakonfigurovat** otevřete uživatelské rozhraní funkce stavu sítě.

![Informace o ASP integrace virtuální sítě][5]

Integrace rozhraní ASP virtuální sítě se zobrazí všechny virtuálních sítí, které jsou používány aplikací ve vaší ASP. Chcete-li zobrazit podrobnosti o každé virtuální síti, klikněte na virtuální sítě, které vás zajímají. Existují dvě akce, můžete tady provádět.

* **Synchronizovat síť**. Síťové operace synchronizace je jenom pro brány závislé funkci integrace virtuální sítě. Provádění síťové operace synchronizace zajišťuje, že jsou certifikáty a informace o síti synchronizované. Je-li přidat nebo změnit DNS virtuální sítě, je třeba provést **synchronizovat síť** operace. Tato operace restartuje všechny aplikace s využitím této virtuální síti.
* **Přidání tras** přidání trasy, se bude řídit odchozí provoz do vaší virtuální sítě.

**Směrování** trasy, které jsou definovány ve virtuální síti se používají ke směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud je nutné odeslat další odchozí provoz do virtuální sítě, můžete přidat tyto bloky adres tady. Tato capabilty funguje pouze s bránou vyžaduje integrace virtuální sítě.

**Certifikáty** integrace virtuální sítě povolené vyžádání brány je požadovaná výměny certifikáty pro zajištění zabezpečení připojení. Spolu s certifikáty jsou konfigurace DNS, postupy a další podobné věci, které popisují sítě.
Pokud se změní certifikáty nebo informace o síti, budete muset klikněte na tlačítko "Synchronizovat síť". Po kliknutí na "Synchronizovat síť" způsobit výpadek připojení mezi vaší aplikací a virtuální síť. Když vaše aplikace nerestartuje, ztráta připojení by mohlo způsobit webu nebude fungovat správně. 

## <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům
Aplikace můžou přistupovat k místním prostředkům díky integraci s virtuálními sítěmi, které mají připojení site-to-site. Pokud používáte brány vyžaduje integrace virtuální sítě, je potřeba aktualizovat trasy brány místní sítě VPN s vaší bloky adres point-to-site. VPN typu site-to-site je nejprve nastavování, skripty používané k jeho konfiguraci byste trasy správně nastavený. Pokud chcete přidat adresy point-to-site, jakmile vytvoříte síť site-to-site VPN, musíte aktualizovat trasy ručně. Podrobnosti o tom, jak to udělat za bránu se liší a nejsou zde popsané. Nemůžete mít nakonfigurovaný s připojením VPN typu site-to-site protokolu BGP.

Neexistuje žádná další konfigurace požadované pro místní funkci integrace virtuální sítě k dosažení prostřednictvím vaší virtuální síti a k místnímu. Chcete-li propojit vaši virtuální síť k místní pomocí ExpressRoute nebo VPN typu site-to-site. 

> [!NOTE]
> Brána požadované funkci integrace virtuální sítě není integrovat aplikace s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [koexistence režimu] [ VPNERCoex] nefunguje integrace virtuální sítě. Pokud potřebujete přístup k prostředkům pomocí připojení ExpressRoute, můžete použít místní funkci integrace virtuální sítě nebo s [služby App Service Environment][ASE], která se spouští ve vaší virtuální síti. 
> 
> 

## <a name="peering"></a>Partnerské vztahy
Pokud používáte vytvoření partnerského vztahu se místní integrace virtuální sítě, není potřeba provádět žádnou další konfiguraci. 

Pokud používáte brány vyžaduje integrace virtuální sítě s využitím partnerského vztahu, je potřeba nakonfigurovat pár dalších položek. Můžete nakonfigurovat partnerský vztah pro práci s vaší aplikací:

1. Přidáte že připojení s partnerským vztahem ve virtuální síti vaše aplikace připojuje k. Při přidávání připojení s partnerským vztahem, povolte **povolit přístup k virtuální síti** a zkontrolujte **povolit přesměrovaný přenos** a **povolit průchod bránou**.
1. Přidáte připojení s partnerským vztahem k virtuální síti připojeni k ve virtuální síti, která se vytváří partnerské vztahy. Při přidávání připojení s partnerským vztahem na cílové virtuální sítě, povolit **povolit přístup k virtuální síti** a zkontrolujte **povolit přesměrovaný přenos** a **povolit vzdálené brány**.
1. Přejděte na plán služby App Service > sítě > virtuální síť integrace uživatelského rozhraní na portálu.  Vyberte vaše aplikace se připojí k virtuální síti. V části směrování přidejte rozsah adres virtuální sítě, je v partnerském vztahu s virtuální sítí, vaše aplikace je připojená k.  


## <a name="pricing-details"></a>Podrobnosti o cenách
Místní funkci integrace virtuální sítě má bez dalších poplatků nad rámec ASP cenové úrovně poplatky.

Existují tři související poplatky za využívání funkci integrace virtuální sítě vyžaduje bránu:

* ASP cenovou úroveň poplatky – vaše aplikace musí být v Standard, Premium nebo plán služby PremiumV2 App Service. Můžete zobrazit další podrobnosti o těchto náklady na: [Ceny služeb App Service][ASPricing]. 
* Náklady na přenos dat – tady je poplatek za odchozí přenos dat i v případě, že virtuální sítě je ve stejném datovém centru. Tyto poplatky jsou popsány v [Data Transfer podrobnosti o cenách][DataPricing]. 
* Náklady na VPN Gateway – Zde jsou náklady k bráně virtuální sítě, která je požadována pro síť point-to-site VPN. Podrobnosti jsou na [ceny služby VPN Gateway] [ VNETPricing] stránky.


## <a name="troubleshooting"></a>Řešení potíží
Tato funkce je snadné nastavení, to ale neznamená, že vaše zkušenosti budou problém zdarma. By měl narazíte na problémy s přístupem k dispozici požadovaný koncový bod se některé nástroje, které lze použít k testování připojení z konzoly aplikace. Existují dvě konzoly, které můžete použít. Jeden je konzola Kudu a druhý je konzoly na webu Azure Portal. Otevřete konzoly Kudu z vaší aplikace, přejděte na Nástroje -> Kudu. To je stejný jako chystáte [název_serveru]. scm.azurewebsites.net. Po, která otevře, přejděte na kartu konzoly pro ladění. Zobrazíte konzolu Azure portal hostované potom z vaší aplikace přejděte na Nástroje -> Konzola. 

#### <a name="tools"></a>Nástroje
Nástroje **ping**, **nslookup** a **tracert** nebude fungovat přes konzolu z důvodu omezení zabezpečení. K vyplnění hodnoty typu void, dvou samostatných nástrojů přidat. Pokud chcete testovat funkce DNS, přidali jsme nástroj s názvem nameresolver.exe. Syntaxe je následující:

    nameresolver.exe hostname [optional: DNS Server]

Můžete použít **nameresolver** zkontrolovat názvy hostitelů, které vaše aplikace závisí. Tímto způsobem můžete otestovat Pokud nic s DNS chybně nakonfigurovaná nebo možná nebudete mít přístup k vašemu serveru DNS. Zobrazí se server DNS, který vaše aplikace bude používat v konzole pohledem na proměnné prostředí WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

Další nástroj umožňuje otestovat pro připojení TCP ke kombinaci hostitele a port. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

**Tcpping** nástroj informuje, pokud můžete oslovit konkrétního hostitele a port. Jenom se zobrazí úspěch pokud: neexistuje aplikace naslouchá na kombinaci hostitele a port, a je přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístup k virtuální síti hostovala prostředky
Existuje mnoho věcí, které můžete aplikaci zabránit v dosažení konkrétního hostitele a port. Ve většině případů představuje jednu ze tří věcí:

* **Brána firewall je způsobem.** Pokud máte bránu firewall tak, jak se dostanou časového limitu protokolu TCP. Vypršení časového limitu TCP je v tomto případě 21 sekund. Použití **tcpping** nástroj k testování připojení. Vypršení časového limitu TCP může být mnoho věcí mimo brány firewall ale začít existuje. 
* **DNS není dostupný.** Časový limit DNS je tří sekund na serveru DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zobrazit, pokud DNS funguje. Mějte na paměti, že nslookup nelze použít jako, který nepoužívá DNS virtuální sítě se nakonfigurují. Pokud nedostupný, můžete mít bránu firewall nebo NSG blokují přístup do DNS, nebo může být mimo provoz.

Pokud tyto položky není odpovědi na problémy, podívejte se první pro takové věci, jako jsou: 

**místní integrace virtuální sítě**
* je vaše cílové adresy RFC 1918
* je k dispozici o NSG blokují výchozí přenos dat z vaší podsítě integrace
* Pokud přes ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zálohování do Azure Pokud dosáhnete koncových bodů ve virtuální síti, ale ne místně, to je vhodné zkontrolovat.

**Brána vyžaduje integrace virtuální sítě**
* je rozsah adres point-to-site v dokumentu RFC 1918 rozsahů (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Zobrazit brány jako nahoru na portálu? Pokud vaše brána je vypnutý, pak ho přeneste zálohování.
* Certifikáty zobrazit stav synchronizace nebo máte podezření, že byla změněna konfigurace sítě?  Pokud vaše certifikáty nejsou synchronizované nebo máte podezření, že byl změny provedené v konfiguraci virtuální sítě, které se synchronizovaly s vaší používalo, pak klikněte na tlačítko "Synchronizovat síť".
* Pokud přes ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zálohování do Azure Pokud dosáhnete koncových bodů ve virtuální síti, ale ne místně, to je vhodné zkontrolovat.

Ladění síťové potíže představuje výzvu, protože existuje nemůžete zobrazit, co blokuje přístup na konkrétní port hostitele: kombinaci. Mezi příčiny patří:

* máte bránu firewall na hostiteli brání přístupu k portu aplikace z bodu na webu rozsah IP adres. Překročení podsítě často vyžaduje veřejný přístup.
* Cílový hostitel je mimo provoz
* vaše aplikace je mimo provoz
* Máte nesprávné IP adresu nebo název hostitele
* vaše aplikace naslouchá na jiném portu, než jste očekávali. Odpovídá vaše ID procesu s naslouchajícího portu pomocí "netstat - aon" na hostitele koncového bodu. 
* Vaše skupiny zabezpečení sítě jsou nakonfigurovány tak, že zabránit přístupu k aplikaci hostitele a port z bodu lokality rozsah IP

Mějte na paměti, jakou adresu vaší aplikace bude používat ve skutečnosti neznáte. Může to být libovolnou adresu v integraci podsítě nebo point-to-site rozsah adres, proto je potřeba povolit přístup z celý rozsah adres. 

Další ladění postup je následující:

* připojení k virtuálnímu počítači ve virtuální síti a pokusí se kontaktovat hostitele: port prostředků z něj. K otestování pro přístup k protokolu TCP, použijte příkaz prostředí PowerShell **test-netconnection**. Syntaxe je následující:

      test-netconnection hostname [optional: -Port]

* Otevřete aplikaci na virtuální počítač a otestujte přístup k tomuto hostiteli a portu z konzoly z aplikace pomocí **tcpping**

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud vaše aplikace nelze dosáhnout místní prostředek, zaškrtněte, pokud můžete oslovit prostředku z vaší virtuální sítě. Použití **test-netconnection** příkaz prostředí PowerShell a zkontrolujte pro přístup k protokolu TCP. Pokud se váš virtuální počítač nemůže připojit místnímu prostředku, vaše připojení VPN nebo ExpressRoute nemusí být správně nakonfigurované.

Pokud hostovaný virtuální sítě virtuálních počítačů můžete oslovit místního systému, ale nelze vaši aplikaci a pak příčina je jedním z následujících důvodů:

* trasy nejsou konfigurované se podsíť nebo přejděte na web rozsahy adres ve vaší místní brány
* Vaše skupiny zabezpečení sítě blokuje přístup pro rozsah IP Point-to-Site
* místní brány firewall, blokují přenosy z rozsahu Point-to-Site IP adres
* Pokoušíte se dosažení RFC 1918 adresy pomocí místní funkci integrace virtuální sítě


## <a name="powershell-automation"></a>Automatizace Powershellu

Integrujte službu App Service pomocí Azure Virtual Network pomocí Powershellu. Připraveno ke spuštění skriptu, naleznete v tématu [aplikace ve službě Azure App Service se připojit ke službě Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
