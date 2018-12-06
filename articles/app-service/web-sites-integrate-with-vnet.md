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
ms.date: 11/12/2018
ms.author: ccompy
ms.openlocfilehash: e086c187129799e499c7ac057b1755a35f1f2327
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971143"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrujte svou aplikaci s Azure Virtual Network
Tento dokument popisuje funkci integrace virtuální sítě Azure App Service a ukazuje, jak ho nastavit s aplikacemi v [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Azure Virtual Networks] [ VNETOverview] (Vnet) umožňuje umístit řadu prostředků Azure v síti bez Internetu možnosti směrování. Tyto sítě můžou potom připojené k vaší místní sítí pomocí technologie VPN. 

Azure App Service má dvě formy. 

1. Víceklientské systémy, které podporují celou škálu cenové plány s výjimkou izolovaný režim
2. App Service Environment (ASE), která nasadí do vaší virtuální sítě. 

Tento dokument prochází funkci integrace virtuální sítě, která je určena pro použití ve službě App Service pro více tenantů.  Pokud je vaše aplikace v [služby App Service Environment][ASEintro], pak se už ve virtuální síti a nevyžaduje, použijte funkci integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti.

Integrace virtuální sítě poskytuje vaší webové aplikaci přístup k prostředkům ve vaší virtuální síti, ale nebude udělit soukromý přístup do vaší webové aplikace z virtuální sítě. Přístup k webům privátní odkazuje na zpřístupnění aplikace jen z privátní sítě, jako z v rámci virtuální sítě Azure. Přístup k privátním serveru je k dispozici pouze s ASE nakonfigurovaný s interní zatížení nástroje pro vyrovnávání (ILB). Podrobnosti o použití prostředí ILB ASE začínat článku: [vytváření a používání prostředí ILB ASE][ILBASE]. 

Integrace virtuální sítě se často používá k povolení přístupu z aplikace do databáze a webových služeb spuštěných ve virtuální síti. Integrace virtuální sítě není nutné vystavit veřejný koncový bod pro aplikace na virtuální počítač ale může používání privátních adres směrovatelný bez internet místo. 

Funkci integrace virtuální sítě:

* vyžaduje Standard, Premium nebo PremiumV2 cenový plán 
* funguje s Classic nebo virtuální síť Resource Manageru 
* podporuje TCP a UDP
* funguje s webových, mobilních a API apps a aplikace Function App
* umožňuje aplikaci připojit k virtuální síti jenom 1 najednou
* umožňuje až pět virtuálních sítí s být integrován se službami v plán služby App Service 
* Umožňuje stejné virtuální síti používané více aplikacemi v plán služby App Service
* vyžaduje bránu virtuální sítě, který je nakonfigurovaný s bodem pro síť VPN typu Site
* podporuje SLA 99,9 % z důvodu smlouvu SLA na bráně

Existuje několik věcí, které integrace virtuální sítě nepodporuje, včetně:

* připojení na jednotku
* Integrace služby AD 
* Rozhraní NetBios
* přístup privátního lokality
* přístup k prostředkům prostřednictvím ExpressRoute 
* přístup k prostředkům prostřednictvím koncových bodů služby 

### <a name="getting-started"></a>Začínáme
Tady jsou některé možnosti brát v úvahu před připojením vaší webové aplikace k virtuální síti:

* Cílová virtuální síť musí mít point-to-site VPN povolené s bránou založenou na směrování, předtím, než se dá připojit k aplikaci. 
* Virtuální síť musí být ve stejném předplatném jako služba Plan(ASP) vaší aplikace.
* Aplikace, které se integrují s virtuální sítě pomocí DNS, který je určen pro tuto virtuální síť.

## <a name="enabling-vnet-integration"></a>Povoluje se integrace virtuální sítě

Existuje nová verze funkci integrace virtuální sítě, která je ve verzi preview. Nezávisí na point-to-site VPN a také podporuje přístup k prostředkům přes ExpressRoute nebo koncové body služby. Další informace o nové funkce ve verzi preview, přejděte na konci tohoto dokumentu. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavit bránu ve vaší virtuální síti ###

Pokud jste již bránu nakonfigurovanou adresu point-to-site, můžete přeskočit ke konfiguraci integrace virtuální sítě s vaší aplikací.  
Pokud chcete vytvořit bránu:

1. [Vytvořit podsíť brány] [ creategatewaysubnet] ve vaší virtuální síti.  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založené na směrování.

1. [Nastavit bod do lokality adresy][setp2saddresses]. Pokud brána není v základní SKU, pak musí se zakázat protokol IKEV2 v konfiguraci point-to-site a musí být vybrán SSTP. Adresní prostor musí být v jednom z následujících bloky adres:

* 10.0.0.0/8 – to znamená, že rozsah IP adres z 10.0.0.0 k 10.255.255.255
* 172.16.0.0/12 – to znamená, že rozsah IP adres z 172.16.0.0 k 172.31.255.255 
* 192.168.0.0/16 – to znamená, že rozsah IP adres z 192.168.0.0 k 192.168.255.255

Pokud není nutné jenom vytvořením brány pro použití s integrace virtuální sítě App Service, budete se nahrát certifikát. Vytvoření brány může trvat 30 minut. Nebude moct integrujte svou aplikaci s vaší virtuální síti, dokud se brána zřídí. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurace integrace virtuální sítě s vaší aplikací ###

Povolení integrace virtuální sítě ve vaší aplikaci: 

1. Přejděte do vaší aplikace na webu Azure Portal a otevřete aplikaci nastavení a vyberte sítě > integrace virtuální sítě. Škálovat vaše ASP na standardní SKU nebo vyšší, než budete moct nakonfigurovat integrace virtuální sítě. 
 ![Integrace virtuální sítě uživatelského rozhraní][1]

1. Vyberte **přidat virtuální síť**. 
 ![Přidání integrace virtuální sítě][2]

1. Vyberte virtuální síť. 
  ![Vyberte virtuální síť][8]
  
Vaše aplikace se restartuje po tomto posledním kroku.  

## <a name="how-the-system-works"></a>Jak funguje v systému
Funkci integrace virtuální sítě je postavený na technologii point-to-site VPN. Aplikace ve službě Azure App Service jsou hostované v systému s více tenanty, který vylučuje zřizování aplikace přímo ve virtuální síti. Technologie point-to-site omezuje přístup k síti na pouze na virtuální počítač hostující aplikaci. Aplikace jsou omezeny pouze směrovat provoz do Internetu přes hybridní připojení, nebo prostřednictvím integrace virtuální sítě. 

![Jak funguje integrace virtuální sítě][3]

## <a name="managing-the-vnet-integrations"></a>Správa integrace virtuální sítě
Možnost připojit a odpojit virtuální sítě je na úrovni aplikace. Operace, které můžou ovlivnit integrace virtuální sítě mezi více aplikacemi jsou na úrovni plánu služby App Service. Z aplikace UID můžete získat podrobnosti o ve virtuální síti. Stejné informace je zobrazen také na úrovni ASP. 

 ![Podrobnosti virtuální sítě][4]

Na stránce stav síťových funkcí můžete zobrazit, pokud vaše aplikace je připojená k virtuální síti. Pokud bránu vaší virtuální sítě je vypnutý z jakéhokoli důvodu, stav zobrazuje jako není připojený. 

Informace, které teď máte k dispozici v aplikaci, kterou úroveň uživatelského rozhraní integrace virtuální sítě je stejný jako podrobné informace, které získáte ze ASP. Tady jsou tyto položky:

* Název virtuální sítě – odkazy na uživatelské rozhraní ve virtuální síti
* Umístění – odráží umístění sítě vnet. Integrace s virtuální sítí v jiném umístění, může způsobit problémy s latencí pro vaši aplikaci. 
* Stav certifikátu – odpovídá, pokud vaše certifikáty jsou synchronizované mezi plán služby App Service a vaší virtuální sítě.
* Stav brány – vaše brány měli dolů z jakéhokoli důvodu pak vaše aplikace nemá přístup k prostředkům ve virtuální síti. 
* Adresní prostor virtuální sítě – zobrazuje adresní prostor IP adres pro vaši virtuální síť. 
* Point-to-site adresního prostoru - ukazuje bod tak, aby lokality adresní prostor IP adres pro vaši virtuální síť. Při volání do vaší virtuální sítě, bude jeden z těchto adres adresu z vaší aplikace. 
* Site-to-site adresního prostoru - můžete použít VPN typu site-to-site k propojení vaší virtuální sítě k místním prostředkům nebo jinou virtuální sítí. Rozsahy IP adres definované pomocí tohoto připojení VPN se tady zobrazí.
* Servery DNS - ukazuje DNS servery nakonfigurované s vaší virtuální sítě.
* IP adresy směrované do virtuální sítě – zobrazuje bloky adres směrují lze přesměrovat provoz do vaší virtuální sítě 

Jedinou operací, které si můžete v zobrazení aplikace integrace vaší virtuální sítě je vaše aplikace se odpojí z aktuálně připojené k virtuální síti. Chcete-li vaše aplikace se odpojí z virtuální sítě, vyberte **odpojit**. Vaše aplikace se restartuje, pokud odpojíte z virtuální sítě. Odpojuje se nezmění vaši virtuální síť. Virtuální síť a jeho konfigurace, včetně brány zůstávají beze změn. Pokud chcete poté odstranit virtuální síť, musíte nejprve odstranit prostředky v ní včetně brány. 

K dosažení integrace rozhraní ASP virtuální sítě, otevřete uživatelské rozhraní ASP a vyberte **sítě**.  V části Integrace virtuální sítě, vyberte **klepnutím sem můžete nakonfigurovat** otevřete uživatelské rozhraní funkce stavu sítě.

![Informace o ASP integrace virtuální sítě][5]

Integrace rozhraní ASP virtuální sítě se zobrazí všechny virtuálních sítí, které jsou používány aplikací ve vaší ASP. Můžete mít až 5 virtuální sítě připojené k libovolný počet aplikací v plánu služby App Service. Každá aplikace může mít pouze jeden nakonfigurovanou integraci. Chcete-li zobrazit podrobnosti o každé virtuální síti, klikněte na virtuální sítě, které vás zajímají. Existují dvě akce, můžete tady provádět.

* **Synchronizovat síť**. Síťové operace synchronizace zajišťuje, že jsou certifikáty a informace o síti synchronizované. Je-li přidat nebo změnit DNS virtuální sítě, je třeba provést **synchronizovat síť** operace. Tato operace restartuje všechny aplikace s využitím této virtuální síti.
* **Přidání tras** přidání trasy, se bude řídit odchozí provoz do vaší virtuální sítě.

**Směrování** trasy, které jsou definovány ve virtuální síti se používají ke směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud je nutné odeslat další odchozí provoz do virtuální sítě, můžete přidat tyto bloky adres tady.   

**Certifikáty** povolené při integrace virtuální sítě, se vyžaduje výměny certifikáty pro zajištění zabezpečení připojení. Spolu s certifikáty jsou konfigurace DNS, postupy a další podobné věci, které popisují sítě.
Pokud se změní certifikáty nebo informace o síti, budete muset klikněte na tlačítko "Synchronizovat síť". Po kliknutí na "Synchronizovat síť" způsobit výpadek připojení mezi vaší aplikací a virtuální síť. Když vaše aplikace nerestartuje, ztráta připojení by mohlo způsobit webu nebude fungovat správně. 

## <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům
Aplikace můžou přistupovat k místním prostředkům díky integraci s virtuálními sítěmi, které mají připojení site-to-site. Pro přístup k prostředkům na pracovišti, budete muset aktualizovat trasy brány místní sítě VPN s vaší bloky adres point-to-site. VPN typu site-to-site je nejprve nastavování, skripty používané k jeho konfiguraci byste trasy správně nastavený. Pokud chcete přidat adresy point-to-site, jakmile vytvoříte síť site-to-site VPN, musíte aktualizovat trasy ručně. Podrobnosti o tom, jak to udělat za bránu se liší a nejsou zde popsané. Také nemůže mít nakonfigurovaný s připojením VPN typu site-to-site protokolu BGP.

> [!NOTE]
> Funkci integrace virtuální sítě není integrovat aplikace s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [koexistence režimu] [ VPNERCoex] nefunguje integrace virtuální sítě. Pokud potřebujete přístup k prostředkům pomocí připojení ExpressRoute, pak můžete použít [služby App Service Environment][ASE], která se spouští ve vaší virtuální síti. 
> 
> 

## <a name="peering"></a>Partnerské vztahy
Integrace virtuální sítě můžete použít pro přístup k prostředkům ve virtuální sítě, které jsou k této virtuální síti připojeni k navázání partnerského vztahu. Můžete nakonfigurovat partnerský vztah pro práci s vaší aplikací:

1. Přidáte že připojení s partnerským vztahem ve virtuální síti vaše aplikace připojuje k. Při přidávání připojení s partnerským vztahem, povolte **povolit přístup k virtuální síti** a zkontrolujte **povolit přesměrovaný přenos** a **povolit průchod bránou**.
1. Přidáte připojení s partnerským vztahem k virtuální síti připojeni k ve virtuální síti, která se vytváří partnerské vztahy. Při přidávání připojení s partnerským vztahem na cílové virtuální sítě, povolit **povolit přístup k virtuální síti** a zkontrolujte **povolit přesměrovaný přenos** a **povolit vzdálené brány**.
1. Přejděte na plán služby App Service > sítě > virtuální síť integrace uživatelského rozhraní na portálu.  Vyberte vaše aplikace se připojí k virtuální síti. V části směrování přidejte rozsah adres virtuální sítě, je v partnerském vztahu s virtuální sítí, vaše aplikace je připojená k.  


## <a name="pricing-details"></a>Podrobnosti o cenách
Existují tři související poplatky za využívání funkci integrace virtuální sítě:

* Ceny za požadavky na úroveň ASP
* Náklady na přenos dat
* Náklady na brány sítě VPN.

Vaše aplikace musí být v Standard, Premium nebo plán služby PremiumV2 App Service. Můžete zobrazit další podrobnosti o těchto náklady na: [App Service – ceny][ASPricing]. 

Existuje poplatky za odchozí přenos dat i v případě, že virtuální sítě je ve stejném datovém centru. Tyto poplatky jsou popsány v [Data Transfer podrobnosti o cenách][DataPricing]. 

Se účtuje poplatek do brány virtuální sítě, která je požadována pro síť point-to-site VPN. Podrobnosti jsou na [ceny služby VPN Gateway] [ VNETPricing] stránky.

## <a name="troubleshooting"></a>Řešení potíží
Tato funkce je snadné nastavení, to ale neznamená, že vaše zkušenosti budou problém zdarma. By měl narazíte na problémy s přístupem k dispozici požadovaný koncový bod se některé nástroje, které lze použít k testování připojení z konzoly aplikace. Existují dvě konzoly, které můžete použít. Jeden je konzola Kudu a druhý je konzoly na webu Azure Portal. Otevřete konzoly Kudu z vaší aplikace, přejděte na Nástroje -> Kudu. To je stejný jako chystáte [název_serveru]. scm.azurewebsites.net. Po, která otevře, přejděte na kartu konzoly pro ladění. Zobrazíte konzolu Azure portal hostované potom z vaší aplikace přejděte na Nástroje -> Konzola. 

#### <a name="tools"></a>Nástroje
Nástroje **ping**, **nslookup** a **tracert** nebude fungovat přes konzolu z důvodu omezení zabezpečení. K vyplnění hodnoty typu void, dvou samostatných nástrojů přidat. Pokud chcete testovat funkce DNS, přidali jsme nástroj s názvem nameresolver.exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

Můžete použít **nameresolver** zkontrolovat názvy hostitelů, které vaše aplikace závisí. Tímto způsobem můžete otestovat Pokud nic s DNS chybně nakonfigurovaná nebo možná nebudete mít přístup k vašemu serveru DNS.

Další nástroj umožňuje otestovat pro připojení TCP ke kombinaci hostitele a port. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

**Tcpping** nástroj informuje, pokud můžete oslovit konkrétního hostitele a port. Jenom se zobrazí úspěch pokud: neexistuje aplikace naslouchá na kombinaci hostitele a port, a je přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístup k virtuální síti hostovala prostředky
Existuje mnoho věcí, které můžete aplikaci zabránit v dosažení konkrétního hostitele a port. Ve většině případů představuje jednu ze tří věcí:

* **Brána firewall je způsobem.** Pokud máte bránu firewall tak, jak se dostanou časového limitu protokolu TCP. Vypršení časového limitu TCP je v tomto případě 21 sekund. Použití **tcpping** nástroj k testování připojení. Vypršení časového limitu TCP může být mnoho věcí mimo brány firewall ale začít existuje. 
* **DNS není dostupný.** Časový limit DNS je tří sekund na serveru DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zobrazit, pokud DNS funguje. Mějte na paměti, že nslookup nelze použít jako, který nepoužívá DNS virtuální sítě se nakonfigurují.
* **Rozsah adres point-to-site je neplatný.** Rozsah IP adres point-to-site, musí být v dokumentu RFC 1918 rozsahy privátních IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Rozsah IP adres používá mimo, který, nebudou fungovat věci. 

Pokud váš problém není odpovědi na ty položky, podívejte se první pro jednoduché věci, jako jsou: 

* Zobrazit brány jako nahoru na portálu?
* Certifikáty zobrazit jako synchronizované?
* Aniž by "Synchronizace síť" v ovlivněných používalo kdokoli měnit síťovou konfiguraci? 

Pokud vaše brána je vypnutý, pak ho přeneste zálohování. Pokud vaše certifikáty nejsou synchronizované, přejděte do zobrazení ASP integrace vaší virtuální sítě a dosažení "Synchronizovat síť". Pokud máte podezření, že byl změny provedené v konfiguraci virtuální sítě, které se synchronizovaly s vaší používalo, pak klikněte na tlačítko "Synchronizovat síť".  Operace "Synchronizovat síť" restartuje všechny aplikace v prostředí ASP, které jsou integrovány v této virtuální síti. 

Pokud je to v pořádku, budete muset podrobněji trochu podrobněji:

* Existují nějaké jiné aplikace pomocí integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti? 
* Můžete přejít do konzoly aplikace a použít tcpping k dosažení jiných prostředků ve vaší virtuální síti? 

Pokud platí některá z výše uvedeného, pak vaše integrace virtuální sítě je v pořádku a problému je někde jinde. To je, ve kterém získá si více složité, protože neexistuje jednoduchý způsob zobrazíte, proč nelze kontaktovat hostitele: port. Mezi příčiny patří:

* máte bránu firewall na hostiteli brání přístupu k portu aplikace z bodu na webu rozsah IP adres. Překročení podsítě často vyžaduje veřejný přístup.
* Cílový hostitel je mimo provoz
* vaše aplikace je mimo provoz
* Máte nesprávné IP adresu nebo název hostitele
* vaše aplikace naslouchá na jiném portu, než jste očekávali. Odpovídá vaše ID procesu s naslouchajícího portu pomocí "netstat - aon" na hostitele koncového bodu. 
* Vaše skupiny zabezpečení sítě jsou nakonfigurovány tak, že zabránit přístupu k aplikaci hostitele a port z bodu lokality rozsah IP

Mějte na paměti, že nevíte jaký IP v rozsahu Point-to-Site IP adres, které vaše aplikace bude používat, musíte povolit přístup z celý rozsah. 

Další ladění postup je následující:

* připojení k virtuálnímu počítači ve virtuální síti a pokusí se kontaktovat hostitele: port prostředků z něj. K otestování pro přístup k protokolu TCP, použijte příkaz prostředí PowerShell **test-netconnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Otevřete aplikaci na virtuální počítač a otestujte přístup na daném hostiteli a portu z konzoly z aplikace

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud vaše aplikace nelze dosáhnout místní prostředek, zaškrtněte, pokud můžete oslovit prostředku z vaší virtuální sítě. Použití **test-netconnection** příkaz prostředí PowerShell a zkontrolujte pro přístup k protokolu TCP. Pokud se váš virtuální počítač nemůže připojit místnímu prostředku, ujistěte se, že je funkční připojení k síti VPN S2s. Pokud funguje, zkontrolujte stejné věci, které jste si předtím poznamenali a konfigurace místní brány a stav. 

Pokud hostovaný virtuální sítě virtuálních počítačů můžete oslovit místního systému, ale nelze vaši aplikaci a pak příčina je jedním z následujících důvodů:

* trasy nejsou konfigurované se váš bod do lokality rozsahy IP adres ve vaší místní brány
* Vaše skupiny zabezpečení sítě blokuje přístup pro rozsah IP Point-to-Site
* místní brány firewall, blokují přenosy z rozsahu Point-to-Site IP adres


## <a name="powershell-automation"></a>Automatizace Powershellu

Integrujte službu App Service pomocí Azure Virtual Network pomocí Powershellu. Připraveno ke spuštění skriptu, naleznete v tématu [aplikace ve službě Azure App Service se připojit ke službě Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hybrid Connections a služby App Service Environment
Existují tři funkce, které umožňují přístup k prostředkům hostované virtuální sítě. Jsou to tyto:

* Integrace virtuální sítě
* Hybridní připojení
* Prostředí App Service

Hybridní připojení vyžaduje instalaci přenosový agent volá Manager(HCM) hybridní připojení se ve vaší síti. HCM musí být schopný se připojit k Azure a také do vaší aplikace. Hybridní připojení nevyžaduje příchozí internetové dostupný koncový bod pro vzdálené sítě, jako je požadovaná pro připojení k síti VPN. HCM pouze běží na Windows a může mít až pět instancí spuštěných pro zajištění vysoké dostupnosti. Hybridní připojení podporuje jenom TCP ale a každý koncový bod hybridní připojení se musí shodovat na konkrétní port hostitele: kombinaci. 

Funkce App Service Environment umožňuje spustit instanci jednoho tenanta služby Azure App Service ve vaší virtuální síti. Pokud vaše aplikace nacházely ve službě App Service Environment, vaše aplikace přístupná prostředků ve vaší virtuální síti bez nějaké dodatečné kroky. S a služby App Service Environment vaše aplikace spouštět na výkonnější pracovních procesů a můžete škálovat až 100 instancí ASP. Služby App Service Environment fungovat se všemi sítě funkcí, včetně ExpressRoute a koncových bodů služby.  

Zatímco je, že některé používají případu překrývají, žádný z těchto funkcí můžete nahradit všechny ostatní. Vědět, jaké funkce pro použití se váže na vaše potřeby. Příklad:

* Pokud jako vývojář a chcete k provedení v Azure, která má přístup k databázi na pracovní stanici pod svým stolem, je nejsnazší používat hybridní připojení. 
* Pokud jste ve velkých organizacích s chce velký počet vlastností webu do veřejného cloudu a spravovat je ve vaší vlastní sítě a chcete přejít pomocí služby App Service Environment. 
* Pokud máte víc aplikací, které potřebují přístup k prostředkům ve vaší virtuální síti, integrace virtuální sítě je způsob přechodu. 

Při vaší virtuální sítě je již připojen k místní síti, pak pomocí integrace virtuální sítě nebo služby App Service Environment je snadný způsob, jak používat místní prostředky. Pokud svou virtuální síť není připojený k vaší místní síti, je mnohem vyšší mírou režie nastavení s2s VPN s vaší virtuální sítě ve srovnání s instalací HCM. 

Nad rámec funkční rozdíly, že jsou také cenové rozdíly. Funkce App Service Environment je na úrovni Premium nabídky služeb ale nabízí největší sítě možnosti konfigurace kromě dalších skvělých funkcí. Integrace virtuální sítě je možné s Standard nebo Premium používalo a je ideální pro bezpečné používání prostředků ve virtuální síti ze služby App Service pro více tenantů. Hybridní připojení se aktuálně závisí na BizTalk účtu, který má cenové úrovně, které začít zdarma a získejte postupně dražší, na základě doby, co potřebujete. Při rozhodování o práci napříč mnoha sítím ale, neexistuje žádná funkce jako Hybrid Connections, které vám umožní přístup k prostředkům v samostatných sítích i více než 100. 

## <a name="new-vnet-integration"></a>Nově zavedené integraci virtuální sítě ##

Existuje nová verze funkci integrace virtuální sítě, které nejsou závislé na technologii Point-to-Site VPN. Na rozdíl od existující funkce novou funkci ve verzi Preview bude fungovat s ExpressRoute a koncových bodů služby. 

Nová funkce je dostupná pouze z novější jednotky škálování Azure App Service. Pokud je možné škálovat na PremiumV2, pak jste v novější jednotka škálování služby App Service. Integrace rozhraní virtuální sítě na portálu vám sdělí, pokud vaše aplikace může využívat novou funkci integrace virtuální sítě. 

Nová verze je ve verzi Preview a má následující vlastnosti.

* Žádná brána je potřeba využít novou funkci integrace virtuální sítě
* Prostředky můžete přistupovat přes připojení ExpressRoute, bez jakékoli další konfigurace nad rámec integraci se službou ExpressRoute připojené virtuální sítě.
* Aplikace a virtuální síť musí být ve stejné oblasti
* Nová funkce vyžaduje nevyužité podsítě ve virtuální síti správce prostředků.
* Plán služby App Service musí být na plán Standard, Premium nebo PremiumV2
* Produkční úlohy nejsou podporovány na novou funkci, i když je ve verzi Preview
* Aplikace musí být v nasazení služby Azure App Service, který je schopen vertikální navýšení kapacity Premium v2.
* Novou funkci integrace virtuální sítě nefunguje pro aplikace ve službě App Service Environment.
* Nelze odstranit virtuální síť, která integrované aplikace.  
* Směrovací tabulky a globální partnerský vztah ještě nejsou k dispozici díky nově zavedené integraci virtuální sítě.  
* Jedna adresa se používá pro všechny instance plánu služby App Service. Protože se po přiřazení nelze změnit velikost podsítě, použijte podsíť, která může zahrnovat více než maximální měřítko. Možnost/27 s 32 adres je doporučená velikost, která by odpovídala plán služby App Service, který je škálovat, aby 20 instancí.  Můžete využívat koncový bod služby Zabezpečené prostředky díky nové funkci integrace virtuální sítě. Uděláte to tak, povolte koncové body služby v podsíti pro integrace virtuální sítě.

Chcete-li používat nové funkce:

1. Přejděte na uživatelské rozhraní sítě na portálu. Pokud je vaše aplikace využívat nové funkce, se zobrazí možnost využít novou funkci ve verzi preview.  

 ![Vyberte nový náhled integrace virtuální sítě][6]

1. Vyberte **přidání virtuální sítě (preview)**.  

1. Vyberte virtuální síť Resource Manageru, kterou chcete integrovat a pak vytvořit novou podsíť nebo vyberte existující podsíť. Integrace trvá méně než minutu. Během integrace se aplikace restartuje.  Po dokončení integrace, zobrazí se podrobnosti na jsou integrované s virtuální sítí a banner v horní části stránky, dozvíte se, že tato funkce je ve verzi preview.

 ![Vyberte virtuální síť a podsíť][7]

Aby vaše aplikace bude moct používat server DNS, který má nakonfigurovanou virtuální síť, vytvoření nastavení aplikace pro vaši aplikaci, ve kterém je WEBSITE_DNS_SERVER název a hodnota je IP adresa serveru.  Pokud máte sekundární server DNS, vytvořte další nastavení aplikace, kde je název WEBSITE_DNS_ALT_SERVER a hodnota je IP adresa serveru. 

Chcete-li vaše aplikace se odpojí z virtuální sítě, vyberte **odpojit**. Tato operace restartuje vaše webová aplikace. 

Novou funkci integrace virtuální sítě vám umožní použít koncové body služby.  Koncové body služby s vaší aplikací, použití nově zavedené integraci virtuální sítě pro připojení k vybrané virtuální síti a potom nakonfigurujte koncové body služby v podsíti, který jste použili pro integraci. 

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
