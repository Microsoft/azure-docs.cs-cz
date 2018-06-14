---
title: Příklad DMZ – sestavení DMZ k ochraně sítě s bránu Firewall, UDR a NSG | Microsoft Docs
description: Sestavení DMZ s bránou Firewall, uživatelem definované směrování (UDR) a skupiny zabezpečení sítě (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885242"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Příklad 3 – vytvoření DMZ k ochraně sítě s bránu Firewall, UDR a NSG
[Návrat na stránku osvědčené postupy zabezpečení hranic][HOME]

Tento příklad vytvoří DMZ s bránou firewall, čtyři servery windows, uživatele definované směrování, předávání IP adres a skupin zabezpečení sítě. Je také provede každou z relevantních příkazů, které poskytují podrobnější vysvětlení jednotlivých kroků. Je také části provoz scénář zajistit podrobné krok za krokem, jak se provoz pokračuje prostřednictvím vrstev obrany v hraniční síti. Nakonec v odkazy na části je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![DMZ obousměrně s hodnocení chyb zabezpečení, NSG a UDR][1]

## <a name="environment-setup"></a>Nastavení prostředí
V tomto příkladu je odběr, který obsahuje následující:

* Tři cloudové služby: "SecSvc001", "FrontEnd001" a "BackEnd001"
* Virtuální síť "CorpNetwork", s tři podsítě: "SecNet", "FrontEnd" a "Back-end"
* Virtuální zařízení sítě, v tomto příkladu bránu firewall, připojený k podsíti SecNet
* Windows Server, který představuje server webových aplikací ("IIS01")
* Dva windows servery, které představují aplikace zpět ukončení servery ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

V části odkazy níže je skript prostředí PowerShell, který bude vytvořit většinu prostředí popsané výše. Vytváření virtuálních počítačů a virtuálních sítí, i když provádějí ukázkový skript, nejsou podrobně popsané v tomto dokumentu.

K vytvoření prostředí:

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizovat název, umístění a IP adresy, které odpovídají danému scénáři)
2. Aktualizace uživatelské proměnné ve skriptu tak, aby odpovídaly prostředí, ve kterém je skript ke spouštění (odběry, názvy služeb atd.)
3. Spusťte skript v prostředí PowerShell

**Poznámka:**: oblasti označený ve skriptu PowerShell musí odpovídat oblasti označeny v souboru xml konfigurace sítě.

Po úspěšném spuštění skriptu mohou být přijata po skriptu takto:

1. Nastavit pravidla brány firewall, najdete v následující části s názvem: popis pravidla brány Firewall.
2. Volitelně můžete v části odkazy jsou dva skripty k nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ.

Jakmile se skript spustí úspěšně bránu firewall, která pravidla bude třeba provést, najdete v části s názvem: pravidla brány Firewall.

## <a name="user-defined-routing-udr"></a>Uživatelem definované směrování (UDR)
Ve výchozím nastavení následující systémové trasy, které jsou definované jako:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal je vždy prefix(es) definovaných adresních sítě vnet pro tuto konkrétní síť (ie se změní z virtuální sítě do virtuální sítě v závislosti na tom, jak je definována každý konkrétní virtuální sítě). Zbývající systémové trasy statické a výchozí jak je uvedeno výše.

Jako prioritu trasy se zpracovávají prostřednictvím metody nejdelší shody předpony (LPM), proto by nejvíce trasy v tabulce platí pro danou cílovou adresu.

Proto by provozu (třeba k serveru DNS01 10.0.2.4) určený pro místní síť (10.0.0.0/16) směrovat přes síť VNet do cíle z důvodu 10.0.0.0/16 trasy. Jinými slovy pro 10.0.2.4, 10.0.0.0/16 trasy, která je nejvíce trasy, i když 10.0.0.0/8 a 0.0.0.0/0 také může použít, ale vzhledem k tomu, že jsou menší konkrétní neovlivňují tento provoz. Proto provoz do 10.0.2.4 by mít dalšího směrování místní sítě vnet a jednoduše směrovat do cílového umístění.

Pokud se provoz určený pro 10.1.1.1 například, 10.0.0.0/16 trasy, která nebude platit, ale 10.0.0.0/8 by nejvíce konkrétní a provoz by to byl vyřazen ("černé dírkového"), protože dalšího směrování je Null. 

Pokud k některému z předpony hodnotu Null nebo VNETLocal předpony nepoužil, cíl, pak by postupujte podle nejméně specifická směrování, 0.0.0.0/0 a připojit k Internetu jako další segment a proto si Azure a internet okraj.

Pokud existují dva identické předpony v tabulce směrování, zde je v pořadí podle preference podle atributu "zdroje" trasy:

1. "VirtualAppliance" = trasu definovaná uživatelem ručně přidat do tabulky
2. "Brána VPN" = dynamické směrování protokolu BGP (při použití s hybridní sítě), přidal protokol dynamické sítě, tyto trasy v průběhu času mění jako protokol dynamické automaticky odráží změny v peered sítě
3. "Výchozí" = systémové trasy, místní virtuální síť a statické záznamy, jak je znázorněno v předchozí tabulce směrování.

> [!NOTE]
> Teď můžete použít uživatele definované směrování (UDR) s ExpressRoute a VPN Gateway vynutit odchozí a příchozí provoz směrovat na virtuální síťové zařízení (hodnocení chyb zabezpečení) mezi různými místy.
> 
> 

#### <a name="creating-the-local-routes"></a>Vytváření místní trasy
V tomto příkladu jsou potřeba dvou směrovacích tabulek, jeden pro každé podsítě front-endové a back-end. Každá tabulka je načtena s statické trasy, které jsou vhodné pro dané podsíti. Pro účely tohoto příkladu každá tabulka měla tři trasy:

1. Provozu místních podsítí s žádné další směrování definované umožňující provozu místních podsítí obejít bránu firewall
2. Virtuální síťový provoz s další směrování definovat jako bránu firewall, přepíše výchozí pravidlo, které umožňuje místní virtuální síť provoz směrovat přímo
3. Veškerý zbývající provoz (0/0) se na další směrování definovat jako bránu firewall

Po vytvoření směrovacích tabulek jsou vázány na podsítě. Pro podsíť Frontend směrovací tabulky, po vytvoření a vázaný k podsíti by měl vypadat takto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


V tomto příkladu se používají následující příkazy pro vytváření směrovací tabulka, přidejte trasu definovanou uživatelem a pak vytvořte vazbu tabulku směrování pro podsíť (Poznámka; všechny položky pod počínaje znak dolaru (např: $BESubnet) jsou uživatelem definované proměnné ve skriptu v části odkaz na tohoto dokumentu):

1. Základní směrovací tabulky musí být nejprve vytvořen. Tento fragment kódu ukazuje vytvoření tabulky pro podsíť back-end. Ve skriptu je pro podsíť Frontend také vytvořit odpovídající tabulku.
   
     Nové AzureRouteTable-název $BERouteTableName.
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Po vytvoření směrovací tabulka se dá přidat trasy definované uživatelem konkrétní. V tomto uvádíme veškerý provoz (0.0.0.0/0) budou směrovány přes virtuální zařízení (proměnné, $VMIP [0], je sloužící k předávání IP adresu přiřadit při vytvoření virtuálního zařízení ve skriptu dříve). Ve skriptu se také vytvoří odpovídající pravidlo v tabulce front-endu.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. Výše uvedené položky trasy přepíše výchozí "0.0.0.0/0" trasu, ale stále existuje výchozí pravidlo 10.0.0.0/16 který by umožnil provoz v rámci virtuální sítě pro směrování přímo do cílového umístění, ne na virtuální síťové zařízení. Pro správné toto chování postupujte podle pravidla musí být přidaný.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. V tomto okamžiku je volbou má být provedeno. Pomocí výše uvedené dvě cesty bude směrovat veškerý provoz do brány firewall pro vyhodnocení, i provoz v rámci jedné podsíti. To může být požaduje, ale pokud chcete povolit přenosy v rámci jedné podsítě pro směrování místně bez zásahu bránu firewall jiného, mohou být přidány velmi konkrétní pravidlo. Tato trasa stavy, které libovolná adresa destine pro místní podsíti může právě směrovat existuje přímo (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Nakonec se do směrovací tabulky vytvořeny a naplněny s trasy definované uživatelem v tabulce musí nyní být vázána na podsíť. Ve skriptu je front-end směrovací tabulka také vázána podsítě front-endu. Zde je vazba skript pro podsíť back-end.
   
     Set-AzureSubnetRouteTable - VirtualNetworkName $VNetName.
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Předávání IP
Doprovodná funkce, která UDR, je předávání IP. Je toto nastavení na virtuální zařízení, která umožňuje přijímání dat adresovaných není konkrétně pro zařízení, a pak tento přenosu do konečného ultimate.

Jako příklad Pokud provoz z AppVM01 provede požadavek na server DNS01 UDR by směrovat to do brány firewall. S povoleno předávání IP přenosy dat pro cílový DNS01 (10.0.2.4) akceptovat zařízení (10.0.0.4) a potom předána do cílového ultimate (10.0.2.4). Bez předávání IP zapnuta brána Firewall nebude možné provoz přijímat zařízení Přestože směrovací tabulka má bránu firewall jako další segment. 

> [!IMPORTANT]
> Je důležité si pamatovat, abyste povolili předávání IP ve spojení s směrování definovaného uživatele.
> 
> 

Nastavení předávání IP adres je jeden příkaz a lze provést v okamžiku vytvoření virtuálního počítače. Pro tok tohoto příkladu fragmentu kódu je na konci skript a seskupuje UDR příkazy:

1. Volání instance virtuálního počítače, který v tomto případě je vaše virtuální zařízení brány firewall a povolení předávání IP adres (Poznámka; libovolnou položku v red počínaje znak dolaru (např: $VMName[0]) je uživatelem definované proměnné ve skriptu v části odkaz na tohoto dokumentu. Nula v hranatých závorkách [0], představuje první virtuální počítač v poli virtuálních počítačů pro ukázkový skript pracovat bez úprav, první virtuální počítač (VM 0) musí být bránu firewall):
   
     Get-AzureVM-název $VMName [0] - ServiceName $ServiceName [0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupina NSG vytvořené a pak načten s jedním pravidlem. Tato skupina je pak vázán pouze na podsítě front-endové a back-end (ne SecNet). Deklarativně se sestavuje následující pravidlo:

1. Přenosy dat (všechny porty) z Internetu do celý virtuální sítě (všechny podsítě) byl odepřen.

I když v tomto příkladu se používají skupiny Nsg, je hlavním účelem jako vrstva sekundární ochranu proti ruční chybné konfigurace. Chceme blokovat všechna příchozí provoz z Internetu do buď front-end nebo back-end podsítě, provoz by měl pouze procházet skrz SecNet podsítě do brány firewall (a pak v případě vhodné k front-end nebo back-end podsítě). Plus s pravidly UDR v místě, jakýkoli přenos, který zkontrolujte do podsítí front-end nebo back-end by přesměrováni se do brány firewall (díky UDR). Brána firewall by to zobrazit jako asymetrický toku a by vyřadit odchozí přenosy. Proto existují tři vrstvy zabezpečení, ochraně podsítě front-endu a back-end; 1) žádné otevřete koncové body na FrontEnd001 a BackEnd001 cloudových služeb, skupin Nsg 2), odepření přenosy z Internetu, 3) brána firewall vyřazování asymetrické provoz.

Jeden bod zajímavé týkající se skupina zabezpečení sítě v tomto příkladu je, že obsahuje pouze jedno pravidlo, viz následující obrázek, který je tak, aby odepřel internetové přenosy na celý virtuální síť, která bude zahrnovat podsítě zabezpečení. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Ale vzhledem k tomu, že NSG je vázaný jenom na podsítě front-endové a back-end, pravidlo není zpracován na provoz příchozí na podsíť. možnosti zabezpečení. Výsledkem je i když pravidla NSG uvádí žádné internetový provoz pro každou adresu, na virtuální síti, protože NSG se nikdy vázána na podsíť. možnosti zabezpečení, bude přenos na podsíť. možnosti zabezpečení.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Pravidla brány firewall
V bráně firewall předávání pravidla bude nutné vytvořit. Vzhledem k tomu, že brána firewall je blokování nebo předávání všechny vstupní, výstupní a intra-VNet provoz, je potřeba řada pravidla brány firewall. Navíc veškerý příchozí provoz, se setkají služby zabezpečení veřejnou IP adresu (na jiné porty), mají být zpracovány bránou firewall. Osvědčeným postupem je diagram logické toky před nastavením podsítě a pravidla brány firewall, aby se zabránilo přepracování později. Na následujícím obrázku je logický zobrazení pravidla brány firewall v tomto příkladu:

![Logickém zobrazení pravidla brány Firewall][2]

> [!NOTE]
> Založená na síti virtuální zařízení používá, se liší porty pro správu. V tomto příkladu, které se odkazuje Barracuda NextGen Firewall, který používá porty 22, 801 a 807. Najdete v dokumentaci výrobce zařízení najít přesnou porty používané ke správě zařízení používá.
> 
> 

### <a name="logical-rule-description"></a>Popis logické pravidla
Ve výše uvedeném logického diagramu se nezobrazí podsítě zabezpečení vzhledem k tomu, že brána firewall je pouze prostředků na této podsíti a tohoto diagramu se zobrazuje pravidla brány firewall a jak se logicky povolit nebo odepřít tok přenosů dat a ne skutečné směrované cesty. Také externí porty, vybraný pro provoz protokolu RDP jsou vyšší pohyboval porty (8014 – 8026) a nebyly vybrány poněkud vyrovnání v posledních dvou oktety místní IP adresu pro snazší čitelnost (například místní server adresu 10.0.1.4 je přidružen externí port 8014), ale může použít jakékoli vyšší-konfliktní porty.

V tomto příkladu budeme potřebovat 7 typy pravidel, tyto typy pravidel jsou popsány takto:

* Externí pravidla (pro příchozí provoz):
  1. Pravidlo brány firewall správy: Toto pravidlo přesměrování aplikace umožňuje přenos dat na porty správy zařízení virtuální sítě.
  2. Pravidla protokolu RDP (pro každý server systému windows): tyto čtyři pravidla (jeden pro každý server) vám umožní správu jednotlivých serverů prostřednictvím protokolu RDP. To může také seskupeny do jedno pravidlo v závislosti na možnosti síťového virtuální zařízení používá.
  3. Pravidla pro provoz aplikace: Existují dvě pravidla provoz aplikace první pro webový provoz front-endu a druhý pro přenosy back-end (např. webový server na datové vrstvě). Konfigurace tato pravidla se závisí na síťovou architekturu (kde jsou umístěné vaše servery) a provoz toků (směru tok přenosů dat a který porty se používají).
     * První pravidlo povolí provoz skutečné aplikaci připojit k serveru aplikace. Zatímco ostatní pravidla povolit pro zabezpečení, správy, atd., jsou pravidla aplikace co povolit externích uživatelů nebo služeb pro přístup k aplikace. V tomto příkladu je jednom webovém serveru na portu 80, proto jediné aplikace pravidlo firewallu přesměruje příchozí přenosy na externí IP adresu, na webové servery interní IP adresu. Relace přesměrovaného přenosy by se NAT i interního serveru.
     * Druhé pravidlo pro provoz aplikace je back-end pravidlo, kterým povolíte Webový Server, aby komunikoval s AppVM01 serveru (ale ne AppVM02) prostřednictvím libovolný port.
* Vnitřní pravidla (pro provoz intra-VNet)
  1. Odchozí do internetové pravidlo: Toto pravidlo povolí provoz od všech sítí, které mají být předána do vybraných sítí. Toto pravidlo je obvykle výchozí pravidlo už v bráně firewall, ale v zakázaném stavu. Toto pravidlo by měly být povoleny v tomto příkladu.
  2. Pravidlo DNS: Toto pravidlo umožňuje předat serveru DNS pouze provoz DNS (port 53). Toto pravidlo pro toto prostředí, které se většina provoz z front-endu na back-end je blokovaný, konkrétně umožňuje DNS z jakékoli místní podsítě.
  3. Pravidlo podsítě pro podsíť: Toto pravidlo se má povolit všechny servery v podsíti back-end připojení k libovolnému serveru na podsítě front end (ale nikoli naopak).
* Pohotovostního pravidlo (pro přenosy, které nesplňují výše uvedených možností):
  1. Všechny přenosy pravidlo odepřít: To by mělo být vždy poslední pravidlo (z hlediska priorita) a jako takový Pokud přenosy dat se nezdaří tak, aby odpovídaly některé z předchozích pravidel, které se zahodí tímto pravidlem. Toto je výchozí pravidlo a obvykle aktivaci žádné je obecně nutné provést změny.

> [!TIP]
> Na druhé pravidlo provoz aplikace jakéhokoli portu je povolen pro snadné tohoto příkladu, ve scénáři skutečné nejvíce konkrétní port a rozsahy adres se má použít pro snížení rizika útoku tohoto pravidla.
> 
> 

<br />

> [!IMPORTANT]
> Po vytvoření všech výše uvedených pravidel, je důležité zkontrolovat prioritu každé pravidlo zajistit provoz se povolí nebo zakáže podle potřeby. V tomto příkladu jsou pravidla v pořadí podle priority. Je snadné se uzamkne mimo bránu firewall kvůli nemá seřazené pravidla. Minimálně zkontrolujte, zda správy pro bránu firewall, samotné vždy absolutní pravidlo nejvyšší prioritou.
> 
> 

### <a name="rule-prerequisites"></a>Pravidla požadavků
Jeden předpoklad pro virtuální počítač spuštěný brány firewall jsou veřejné koncové body. Pro bránu firewall pro zpracování provozu je třeba otevřít odpovídající veřejné koncové body. Existují tři typy přenosů dat v tomto příkladu; Provoz protokolu RDP 1) provoz správy pro řízení brány firewall a pravidla brány firewall, 2) k řízení serverů se systémem windows a provoz 3) aplikace. Toto jsou tři sloupce typů přenosů v horní polovině logickém zobrazení pravidla brány firewall výše.

> [!IMPORTANT]
> Je zde klíče takeway nezapomeňte, že **všechny** provoz se odešlou přes bránu firewall. Proto vzdálené plochy k serveru IIS01, i když je počítač v cloudové službě Front End a na podsítě Front End, pro přístup k tomuto serveru jsme bude muset RDP do brány firewall na portu 8014 a potom povolit bránu firewall pro směrování požadavku protokolu RDP interně k portu RDP IIS01. Tlačítko "Připojit" portálu Azure nebude fungovat, protože neexistuje přímé cesta protokolu RDP na IIS01 (jde o můžete zobrazit na portálu). To znamená, že všechna připojení z Internetu bude služby zabezpečení a Port, například secscv001.cloudapp.net:xxxx (referenční dokumentace diagramu pro mapování portů externí, interní IP a Port).
> 
> 

Koncový bod lze otevřít buď při vytváření virtuálních počítačů a post sestavení, jak se provádí v ukázkový skript a znázorněném na tento fragment kódu (Poznámka; všechny položky počínaje znak dolaru (např: $VMName[$i]) je uživatelem definované proměnné ve skriptu v části odkaz na tohoto dokumentu. "$I" v hranatých závorkách [$i] představuje číslo pole konkrétní virtuální počítač v matici virtuálních počítačů):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

I když se zobrazují zde není jasně kvůli použití proměnných, ale koncové body jsou **pouze** otevřít v cloudové službě zabezpečení. Tím je zajištěno, že se zpracovává veškerý příchozí provoz (směrovat, NAT měl, vynechané) bránou firewall.

Klient správy bude potřeba nainstalovat na počítač pro správu brány firewall a vytvoření konfigurací potřeby. O tom, jak spravovat zařízení, najdete v článku dodavatele dokumentace z brány firewall (nebo jiné hodnocení chyb zabezpečení). Zbývající část tohoto tématu a v další části vytváření pravidel brány Firewall, bude popisují konfiguraci brány firewall, samostatně, prostřednictvím dodavatele správy klienta (tzn. ne portál Azure nebo PowerShell).

Pokyny pro stažení klienta a připojení k Barracuda použité v tomto příkladu naleznete zde: [Barracuda NG správce](https://techlib.barracuda.com/NG61/NGAdmin)

Po přihlášení na bránu firewall, ale před vytvořením pravidel brány firewall, existují dvě třídy požadovaných objektů, které můžou vytváření pravidel snadněji; Objekty, síť a služby.

V tomto příkladu tři objekty pojmenované síti musí být definované (jeden pro podsítě front-endu a back-end podsíť, také síťového objektu pro IP adresu serveru DNS). Vytvořit síť s názvem; spouštění z řídicího panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v části Konfigurace provozní klikněte Ruleset, pak klikněte na tlačítko "Sítě" v nabídce objekty brány Firewall a pak v nabídce Upravit sítě klikněte na tlačítko Nový. Objekt sítě může být nyní vytvořen přidáním názvu a předponu:

![Vytvoření objektu front-endové síti][3]

Tím se vytvoří pojmenované sítě pro podsíť FrontEnd, podobně jako objekt měl být vytvořen v back-end podsíti. Nyní podsítě lze snadněji odkazovat podle názvu v pravidlech brány firewall.

Pro objekt serveru DNS:

![Vytvořit objekt serveru DNS][4]

V pravidle DNS později v dokumentu použije tento jeden odkaz na IP adresu.

Druhý požadované objekty jsou objekty služby. Toto bude reprezentovat porty pro připojení RDP pro každý server. Vzhledem k tomu, že existující objekt služby RDP je vázána výchozí port protokolu RDP, 3389, nové služby můžete vytvořit Pokud chcete povolit přenosy z externí porty (8014-8026). Nové porty nebylo možné přidat také do existující služba protokolu RDP, ale pro usnadnění ukázku, můžete vytvořit jednotlivých pravidel pro každý server. Chcete-li vytvořit nové pravidlo protokolu RDP pro server; spouštění z řídicího panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v provozní konfigurační oddíl klikněte na Ruleset, pak klikněte na tlačítko "Služby" v nabídce objekty brány Firewall, přejděte dolů v seznamu služeb a vyberte službu "RDP". Klikněte pravým tlačítkem a vyberte kopie, pak klikněte pravým tlačítkem a vyberte vložení. Je nyní objekt služby RDP Copy1, který lze upravovat. Klikněte pravým tlačítkem na RDP Copy1 a vyberte upravit, upravit objekt služby okno bude pop až, jak je vidět tady:

![Kopii výchozí pravidlo protokolu RDP][5]

Hodnoty lze upravit k reprezentaci služba protokolu RDP pro určitý server. Pro AppVM01 výše uvedené výchozí pravidlo protokolu RDP by měl být upraven tak, aby odrážela nový název služby, popis a externí portu RDP v diagramu obrázek 8 (Poznámka: porty jsou změnit z výchozích RDP 3389 k externí port používán pro tento konkrétní server, v případě AppVM01 externí Port je 8025) upravené služby jsou uvedeny níže :

![Pravidlo AppVM01][6]

Tento proces opakuje pro vytvoření služby protokolu RDP pro ostatní servery; AppVM02, DNS01 a IIS01. Vytvoření tyto služby budou vytvoření pravidla jednodušší a zřejmější v další části.

> [!NOTE]
> Služby protokolu RDP pro bránu Firewall není potřeba dvou důvodů; první 1) brána firewall virtuálního počítače je bitová kopie založenými na systému Linux tak, aby SSH se použije na port 22 pro správu virtuálních počítačů místo protokolu RDP a 2) port 22, a dva další správu porty jsou povolené v první pravidlo správy popsané dál umožňující možnosti připojení správy.
> 
> 

### <a name="firewall-rules-creation"></a>Vytvoření pravidla brány firewall
Existují tři typy pravidel brány firewall použité v tomto příkladu, všechny mají odlišné ikony:

Pravidlo aplikace přesměrování: ![přesměrování ikona aplikace][7]

Pravidlo NAT cílové: ![ikonu cílové NAT][8]

Pravidlo průchodu: ![předat ikonu][9]

Další informace o těchto pravidel lze najít na webu Barracuda.

Vytvořit následující pravidla (nebo ověřit existující výchozí pravidla), od řídicím panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v provozní konfiguraci oddíl, klikněte na Ruleset. Mřížka názvem, zobrazí "Hlavní pravidla" existující pravidla aktivní a deaktivované na tato brána firewall. V pravém horním rohu mřížce je malý, zelená "+" tlačítko, klepněte sem a vytvořit nové pravidlo (Poznámka: Brána firewall může "zamknout" změny, pokud se zobrazí tlačítko označené "Zamknout" a nelze vytvořit nebo upravit pravidla, klikněte na toto tlačítko "odemknutí" je sada pravidel a povolit úpravy). Pokud chcete upravit existující pravidlo, vyberte toto pravidlo, klikněte pravým tlačítkem a vyberte Upravit pravidlo.

Jakmile jsou pravidla vytvořit nebo upravit, musí být nabídnutých do brány firewall a pak se aktivuje, pokud to neuděláte pravidlo změny neprojeví. Nabízení a aktivace proces je popsán níže popisy pravidlo podrobnosti.

Jaké jsou specifikace každé pravidlo mohou provést pouze v tomto příkladu jsou popsány následovně:

* **Brány firewall pravidla správy**: Tato aplikace přesměrování pravidlo umožňuje předat porty správu zařízení virtuální sítě v tomto příkladu Barracuda NextGen Firewall provoz. Porty pro správu jsou 801, 807 a volitelně 22. Externí i interní porty jsou stejné (tj. žádné překlad port). Toto pravidlo, instalační program-MGMT-přístup, je výchozí pravidlo a povolena ve výchozím nastavení (Barracuda NextGen Firewall verze 6.1).
  
    ![Pravidlo brány firewall správy][10]

> [!TIP]
> Adresní prostor zdroje v tomto pravidle je existuje, pokud se ví, že rozsahy správy IP adres, snižuje tento obor by také omezit možnost útoku na porty správy.
> 
> 

* **Pravidla RDP**: pravidla NAT tyto cílové vám umožní správu jednotlivých serverů prostřednictvím protokolu RDP.
  Existují čtyři kritické pole, které jsou potřebné k vytvoření tohoto pravidla:
  
  1. Zdroj – chcete-li povolit RDP z libovolného místa, odkaz na "Žádný" se používá v poli zdroje.
  2. Služba – použít na příslušný objekt služby vytvořený v tomto případě "AppVM01 RDP", externí porty přesměrování na servery místní IP adresu a port 3386 (výchozí port protokolu RDP). Tato konkrétní pravidlo je pro přístup k protokolu RDP na AppVM01.
  3. Cíl – musí být *místního portu v bráně firewall*, "IP místní server DHCP 1" nebo eth0, pokud se používá statické IP adresy. Řadová číslovka (eth0, eth1 atd.), může lišit, pokud vaše síťové zařízení má více místní rozhraní. Toto je port brány firewall odesílá z (může být stejný jako přijímající port), skutečný směrované cíl je v poli cílového seznamu.
  4. Přesměrování – v této části informuje virtuální zařízení kde nakonec přesměrování tento provoz. Nejjednodušší přesměrování je umístit do cílového seznamu pole IP adresy a portu (volitelné). Pokud žádné port je používán cílový port na příchozí žádosti bude používat (ie žádné překlad), pokud je port určený port bude také NAT by spolu s IP adres.
     
     ![Pravidlo brány firewall protokolu RDP][11]
     
     Celkem čtyři pravidla RDP bude muset vytvořit: 
     
     | Název pravidla | Server | Služba | Cílového seznamu |
     | --- | --- | --- | --- |
     | RDP IIS01 |IIS01 |IIS01 PROTOKOLU RDP |10.0.1.4:3389 |
     | RDP DNS01 |DNS01 |DNS01 PROTOKOLU RDP |10.0.2.4:3389 |
     | RDP AppVM01 |AppVM01 |AppVM01 protokolu RDP |10.0.2.5:3389 |
     | RDP AppVM02 |AppVM02 |AppVm02 protokolu RDP |10.0.2.6:3389 |

> [!TIP]
> Zužující dolů oboru pole zdrojové a služby se zmenší prostor pro útoky. Většina omezeným oborem, který vám umožní funkce je třeba použít.
> 
> 

* **Pravidla pro provoz aplikace**: existují dvě pravidla provoz aplikace první pro webový provoz front-endu a druhý pro přenosy back-end (např. webový server na datové vrstvě). Tato pravidla se závisí na síťovou architekturu (kde jsou umístěné vaše servery) a provoz toků (směru tok přenosů dat a který porty se používají).
  
    Nejprve popsané je pravidlo front-endu pro webový provoz:
  
    ![Pravidla brány firewall na webu][12]
  
    Toto pravidlo NAT cílové umožňuje přenos skutečné aplikaci připojit k serveru aplikace. Zatímco ostatní pravidla povolit pro zabezpečení, správy, atd., jsou pravidla aplikace co povolit externích uživatelů nebo služeb pro přístup k aplikace. V tomto příkladu je jednom webovém serveru na portu 80, proto jediné aplikaci pravidlo firewallu přesměruje příchozí přenosy na externí IP adresu, na webové servery interní IP adresu.
  
    **Poznámka:**: že neexistuje žádná portu přiřazené do pole cílového seznamu, proto příchozí port 80 (nebo 443 pro službu vybrané) se použije v přesměrování webového serveru. Pokud webový server naslouchá na jiný port, například port 8080, mohlo dojít k aktualizaci pole cílového seznamu k 10.0.1.4:8080 umožňující také přesměrování portu.
  
    Pravidlo pro provoz další aplikace je back-end pravidlo, kterým povolíte Webový Server, aby komunikoval s AppVM01 serveru (ale ne AppVM02) prostřednictvím jakékoli služby:
  
    ![Pravidlo brány firewall AppVM01][13]
  
    Toto pravidlo průchodu umožňuje jakýkoli server služby IIS na podsíť Frontend vás zastihnout AppVM01 (IP adresa 10.0.2.5) na libovolném portu pomocí libovolný protokol pro přístup k datům, které jsou potřebné pro webovou aplikaci.
  
    V tento snímek obrazovky "\<explicitní dest\>" se používá v poli cílové místo 10.0.2.5 jako cíl. To může být buď explicitní znázorněné nebo názvem objektu sítě (stejně jako ve požadavky pro DNS server). Toto je až správce brány firewall, která se použije metoda. Přidat Explict Desitnation 10.0.2.5, dvakrát klikněte na první prázdný řádek pod \<explicitní dest\> a v okně, které se zobrazí, zadejte adresu.
  
    Toto pravidlo předat není nutné žádné NAT vzhledem k tomu, že toto je interní provoz, takže metodu připojení může být nastaven na "Ne překládat pomocí SNAT".
  
    **Poznámka:**: zdrojový sítě v tomto pravidle je jakémukoli prostředku, na podsíť FrontEnd, pokud bude existovat pouze jedna nebo známé konkrétní počet webových serverů, prostředek objektu sítě by bylo možné vytvořit být konkrétnější těchto přesnou IP adresy místo celé podsítě front-endu.

> [!TIP]
> Toto pravidlo používá službu "Žádné" usnadnění ukázkovou aplikaci nastavit a používat, to také umožní ICMPv4 (ping) v jediné pravidlo. Je to ale není doporučený postup. Porty a protokoly ("služby") by měl být zúžit možné minimální, který umožňuje aplikaci operace redukovat prostor pro útok napříč tuto hranici.
> 
> 

<br />

> [!TIP]
> I když toto pravidlo zobrazuje odkaz na explicitní dest používá, je třeba použít jednotný přístup v rámci konfigurace brány firewall. Doporučuje se použít s názvem objektu sítě v rámci pro snazší čitelnost a podpoře. Explicitní dest tady je použita pouze k zobrazení metodu alternativní odkaz a se obecně nedoporučuje (hlavně u komplexní konfigurace).
> 
> 

* **Odchozí do internetové pravidlo**: předat toto pravidlo povolí provoz z jakékoli zdrojové síti mají být předána do vybrané cílové sítě. Toto pravidlo je výchozí pravidlo obvykle už v bráně firewall Barracuda NextGen, ale je v zakázaném stavu. Pravým tlačítkem myši na toto pravidlo můžete přístup k příkazu aktivovat pravidlo. Přidejte dva místní podsítě, které byly vytvořeny jako odkazy v části požadavků tohoto dokumentu ke zdrojovému atributu tohoto pravidla se změnilo pravidlo zobrazeny zde.
  
    ![Odchozí pravidlo brány firewall][14]
* **Pravidlo DNS**: předat toto pravidlo umožňuje předat serveru DNS pouze provoz DNS (port 53). Pro toto prostředí, které se většina provoz z front-endu na back-end je blokovaný konkrétně toto pravidlo umožňuje DNS.
  
    ![Pravidlo brány firewall DNS][15]
  
    **Poznámka:**: na této obrazovce snímek metoda připojení je součástí. Protože toto pravidlo je pro interní IP adresu pro interní IP adresu provoz, žádné NATing je vyžadován, tato metoda připojení nastavena na "Ne překládat pomocí SNAT" pro toto pravidlo průchodu.
* **Pravidlo podsítě pro podsíť**: předat toto pravidlo je výchozí pravidlo, které se aktivuje a upravit tak, aby povolit všechny servery v podsíti back-end připojení k libovolnému serveru na podsítě front end. Toto pravidlo je všechny interní provoz, takže metodu připojení může být nastaven na žádný překládat pomocí SNAT.
  
    ![Pravidlo brány firewall Intra-VNet][16]
  
    **Poznámka:**: zaškrtávací políčko obousměrný nastavení není kontrolován (ani je zaregistrováno většina pravidla), to je důležité pro toto pravidlo, umožňuje tato pravidla "jeden směrové", připojení lze inicializovat z back-end podsítě síť front-end, ale nikoli naopak. Pokud toto zaškrtávací políčko je zaškrtnuto, by toto pravidlo povolit obousměrný přenos, který je z našich logického diagramu není žádoucí.
* **Všechny přenosy pravidlo Odepřít**: to by mělo být vždy poslední pravidlo (z hlediska priorita), a jako takový Pokud selže tak, aby odpovídaly některé z přenosy podle předchozích pravidel se zahodí tímto pravidlem. Toto je výchozí pravidlo a obvykle aktivaci žádné je obecně nutné provést změny. 
  
    ![Pravidlo brány firewall Odepřít][17]

> [!IMPORTANT]
> Po vytvoření všech výše uvedených pravidel, je důležité zkontrolovat prioritu každé pravidlo zajistit provoz se povolí nebo zakáže podle potřeby. V tomto příkladu jsou pravidla v pořadí, ve kterém by se zobrazit v hlavní mřížky předávání pravidla v Barracuda správy klienta.
> 
> 

## <a name="rule-activation"></a>Aktivace pravidla
S ruleset upravit tak, aby specifikace diagramu logiku musí být nahrán do brány firewall a pak se aktivuje ruleset.

![Aktivace pravidla brány firewall][18]

V pravém horním rohu správy klienta jsou součástí clusteru tlačítka. Kliknutím na tlačítko "Změny odeslat" poslat upravené pravidla brány firewall a potom klikněte na tlačítko "Aktivovat".

S aktivace sada pravidel brány firewall pro tento příklad prostředí sestavení je dokončena.

## <a name="traffic-scenarios"></a>Provoz scénáře
> [!IMPORTANT]
> Nezapomeňte, že klíče takeway je **všechny** provoz se odešlou přes bránu firewall. Proto vzdálené plochy k serveru IIS01, i když je počítač v cloudové službě Front End a na podsítě Front End, pro přístup k tomuto serveru jsme bude muset RDP do brány firewall na portu 8014 a potom povolit bránu firewall pro směrování požadavku protokolu RDP interně k portu RDP IIS01. Tlačítko "Připojit" portálu Azure nebude fungovat, protože neexistuje přímé cesta protokolu RDP na IIS01 (jde o můžete zobrazit na portálu). To znamená, že všechna připojení z Internetu bude služby zabezpečení a Port, například secscv001.cloudapp.net:xxxx.
> 
> 

Pro tyto scénáře následující pravidla brány firewall musí být k dispozici:

1. Správa brány firewall
2. RDP na IIS01
3. RDP na DNS01
4. RDP na AppVM01
5. RDP na AppVM02
6. Provoz aplikace na webu
7. Provoz aplikace AppVM01
8. Odchozí do Internetu
9. Front-endu do DNS01
10. Přenosy mezi podsítěmi (back-endu front-endu pouze)
11. Odepřít vše

Ruleset skutečné brány firewall budou s největší pravděpodobností mít mnoho pravidla kromě těchto, pravidla na jakékoli dané brány firewall bude také obsahovat čísla jinou prioritu než ty, které jsou zde uvedeny. Tento seznam a přidružená čísla jsou zajistit relevance mezi právě tyto 11 pravidla a relativní Priorita jedna z nich. Jinými slovy; v bráně firewall skutečné "protokolu RDP na IIS01" může být číslo pravidla 5, ale dokud je nižší než pravidlo "Brány Firewall správy" a výše "Protokolu RDP na DNS01" pravidlo by zarovnat tak, aby tento seznam. V seznamu bude také pomáhají při níže scénáře povolení jako stručný výtah; například "Pravidlo FW 9 (DNS)". Také jako stručný výtah čtyři pravidla RDP souhrnně volaná, "pravidla RDP" Pokud scénář provoz nesouvisí s RDP.

Taky odvolat, že skupiny zabezpečení sítě jsou místní pro příchozí přenosy z Internetu v podsítích front-endové a back-end.

#### <a name="allowed-internet-to-web-server"></a>(Povoleno) Internet na webový Server
1. Internet uživatelské požadavky HTTP stránky z SecSvc001.CloudApp.Net (Internet čelí cloudové služby)
2. Cloudové služby předává přenos prostřednictvím otevřených koncových bodů na portu 80 k rozhraní brány firewall na 10.0.0.4:80
3. Žádné skupiny NSG přiřazené podsítě zabezpečení, tak pravidla NSG systému povolit provoz do brány firewall
4. Provoz dotkne interní IP adresu brány firewall (10.0.1.4)
5. Brány firewall zahájí zpracování pravidla:
   1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
   2. Nemusíte použít, přejděte k další pravidla FW pravidla 2 až 5 (RDP pravidla)
   3. FW pravidla 6 (aplikace: webové) použít, provoz je povolený, zařízení brány firewall NAT. jeho 10.0.1.4 (IIS01)
6. Podsíť Frontend zahájí zpracování příchozí pravidlo:
   1. Netýká NSG pravidlo 1 (bloku Internet) (Tento provoz byl NAT by bránou firewall, proto je zdrojovou adresu nyní brány firewall, která je v podsíti, zabezpečení a prohlížet podsíť Frontend NSG jako "místní" provoz a je proto povolen), přesunout do dalšího pravidla
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
7. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
8. IIS01 pokusy o zahájí relace FTP na AppVM01 v podsíti back-end
9. UDR trasy na podsíť Frontend umožňuje bráně firewall dalšího směrování
10. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
11. Brány firewall zahájí zpracování pravidla:
    1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
    2. Nemusíte použít, přejděte k další pravidla FW pravidlo 2 až 5 (RDP pravidla)
    3. FW pravidla 6 (aplikace: webové) není použít, přejděte k další pravidla
    4. FW pravidla 7 (aplikace: back-end) použít, provoz je povolený, brány firewall předává přenos do 10.0.2.5 (AppVM01)
12. Podsíť back-end zahájí zpracování příchozí pravidlo:
    1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
13. AppVM01 obdrží požadavek a inicializuje relaci a odpovídá
14. UDR trasy v podsíti back-end umožňuje bráně firewall dalšího směrování
15. Vzhledem k tomu, že neexistují žádná odchozí pravidla NSG na podsítě back-end odpovědi je povoleno
16. Protože to vrací provoz na navázanou relaci předává odpověď zpět do webového serveru (IIS01)
17. Podsíť frontend zahájí zpracování příchozí pravidlo:
    1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
18. Server služby IIS obdrží odpověď, dokončení transakce s AppVM01 a potom dokončí vytváření odpovědi HTTP, tato odpověď HTTP posílá žadatel
19. Vzhledem k tomu, že neexistují žádná odchozí pravidla NSG na podsítě front-endu odpovědi je povoleno
20. Odpověď HTTP dotkne brány firewall a vzhledem k tomu, že toto je odpověď na navázanou relaci NAT přijat branou firewall
21. Brána firewall pak přesměruje odpověď zpět do Internetu uživatele
22. Vzhledem k tomu, že neexistují žádná odchozí pravidla NSG nebo UDR směrování na front-endu podsítě, které je povoleno odpovědi a Internet uživatel obdrží požadované webové stránky.

#### <a name="allowed-internet-rdp-to-backend"></a>(Povoleno) Internet protokolu RDP na back-end
1. Správce serveru na Internetu požadavky protokolu RDP relace AppVM01 prostřednictvím SecSvc001.CloudApp.Net:8025, kde 8025 je číslo portu přiřazeného uživatele pro pravidlo brány firewall "Protokolu RDP na AppVM01"
2. Cloudové služby předá provoz prostřednictvím otevřených koncových bodů na portu 8025 rozhraní brány firewall na 10.0.0.4:8025
3. Žádné skupiny NSG přiřazené podsítě zabezpečení, tak pravidla NSG systému povolit provoz do brány firewall
4. Brány firewall zahájí zpracování pravidla:
   1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
   2. FW pravidla 2 (RDP IIS) není použít, přejděte k další pravidla
   3. FW pravidla 3 (RDP DNS01) není použít, přejděte k další pravidla
   4. Použít FW pravidla 4 (RDP AppVM01), provoz je povolený, zařízení brány firewall NAT. jeho 10.0.2.5:3386 (portu RDP na AppVM01)
5. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Netýká NSG pravidlo 1 (bloku Internet) (Tento provoz byl NAT by bránou firewall, proto je zdrojovou adresu nyní brány firewall, která je v podsíti, zabezpečení a prohlížet podsíť back-end NSG jako "místní" provoz a je proto povolen), přesunout do dalšího pravidla
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
6. AppVM01 naslouchá pro provoz protokolu RDP a odpovídá
7. Žádná odchozí pravidla NSG použít výchozí pravidla a návratový provoz je povolený
8. UDR odchozí provoz směrovat na bránu firewall jako další segment
9. Protože to vrací provoz na navázanou relaci předává odpověď zpět do Internetu uživatele
10. Je povoleno relaci protokolu RDP.
11. AppVM01 vyzve k zadání názvu heslo uživatele

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povoleno) Webový Server DNS vyhledávání na serveru DNS
1. Webový Server, IIS01, požadavky datového kanálu v www.data.gov, ale musí pro překlad adres.
2. Konfigurace sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v podsíti back-end) jako primární server DNS, IIS01 odešle žádost DNS do DNS01
3. UDR odchozí provoz směrovat na bránu firewall jako další segment
4. Žádná odchozí pravidla NSG je vázána na podsíť Frontend, provoz je povolený
5. Brány firewall zahájí zpracování pravidla:
   1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
   2. Nemusíte použít, přejděte k další pravidla FW pravidlo 2 až 5 (RDP pravidla)
   3. Nemusíte použít, přejděte k další pravidla FW pravidla 6 a 7 (pravidla aplikace)
   4. FW pravidlo 8 (Internet k) není použít, přejděte k další pravidla
   5. Použít FW pravidlo 9 (DNS), provoz je povolený, brány firewall předává přenos do 10.0.2.4 (DNS01)
6. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
7. DNS server obdrží požadavek
8. DNS server nemá adresu do mezipaměti a požádá kořenový server DNS na Internetu
9. UDR odchozí provoz směrovat na bránu firewall jako další segment
10. Žádná odchozí pravidla NSG na podsítě back-end provoz je povolený.
11. Brány firewall zahájí zpracování pravidla:
    1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
    2. Nemusíte použít, přejděte k další pravidla FW pravidlo 2 až 5 (RDP pravidla)
    3. Nemusíte použít, přejděte k další pravidla FW pravidla 6 a 7 (pravidla aplikace)
    4. Použít pravidlo 8 FW (do Internetu), provoz je povolený, relace je překládat pomocí SNAT na kořenový server DNS na Internetu
12. Server DNS pro Internet odpoví, protože tato relace byla inicializována z brány firewall, odpovědi je přijímán bránou firewall
13. Protože se jedná navázanou relaci, brána firewall předává odpověď na inicializace serveru DNS01
14. Podsíť back-end zahájí zpracování příchozí pravidlo:
    1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
15. DNS server obdrží odpověď do mezipaměti a poté odpoví na počáteční žádost zpět na IIS01
16. UDR trasy v podsíti back-end umožňuje bráně firewall dalšího směrování
17. Neexistují žádná odchozí pravidla NSG na podsítě back-end, provoz je povolený
18. Toto je navázanou relaci v bráně firewall, odpověď se předá brány firewall zpět na server služby IIS
19. Podsíť frontend zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
    2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povoleno
20. IIS01 obdrží odpověď od DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Povoleno) Back-end serveru na server pro front-endu
1. Správce přihlášený k AppVM02 prostřednictvím protokolu RDP požádá o soubor přímo ze serveru IIS01 pomocí Průzkumníka souborů systému windows
2. UDR trasy v podsíti back-end umožňuje bráně firewall dalšího směrování
3. Vzhledem k tomu, že neexistují žádná odchozí pravidla NSG na podsítě back-end odpovědi je povoleno
4. Brány firewall zahájí zpracování pravidla:
   1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
   2. Nemusíte použít, přejděte k další pravidla FW pravidlo 2 až 5 (RDP pravidla)
   3. Nemusíte použít, přejděte k další pravidla FW pravidla 6 a 7 (pravidla aplikace)
   4. FW pravidlo 8 (Internet k) není použít, přejděte k další pravidla
   5. Není použít, přejděte k další pravidla FW pravidlo 9 (DNS)
   6. Použít pravidlo 10 FW (Intra-podsítě), provoz je povolený, brány firewall předá provoz 10.0.1.4 (IIS01)
5. Podsíť frontend zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
6. Za předpokladu, že správné ověření a autorizaci, IIS01 přijme žádost a odpovídá
7. UDR trasy na podsíť Frontend umožňuje bráně firewall dalšího směrování
8. Vzhledem k tomu, že neexistují žádná odchozí pravidla NSG na podsítě front-endu odpovědi je povoleno
9. Protože se jedná o existující relaci v bráně firewall této odpovědi je povolen a bránu firewall vrátí odpověď na AppVM02
10. Back-end podsíť zahájí zpracování příchozí pravidlo:
    1. Není použít, přejděte k další pravidla NSG pravidlo 1 (bloku Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
11. AppVM02 obdrží odpověď

#### <a name="denied-internet-direct-to-web-server"></a>(Byl odepřen) Internet přímo na webovém serveru
1. Uživatel Internetu pokusí o přístup k webovému serveru, IIS01, prostřednictvím služby FrontEnd001.CloudApp.Net
2. Vzhledem k tomu, že jsou pro přenos HTTP otevřené žádné koncové body, se nebude předávat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, by tento provoz blokovat NSG (bloku Internet) na podsíť Frontend
4. Nakonec trasy UDR podsítě front-endu byste odesílali všechny odchozí přenosy z IIS01 do brány firewall jako další segment a bránu firewall by najdete jako asymetrický provoz a vyřadit odchozí odpovědi proto existují aspoň tři nezávislá vrstev obrany mezi internet a IIS01 přes jeho Cloudová služba, která brání Neautorizováno nevhodných přístupu.

#### <a name="denied-internet-to-backend-server"></a>(Byl odepřen) Internet back-end server
1. Internet uživatel pokusí přistoupit k souboru na AppVM01 prostřednictvím služby BackEnd001.CloudApp.Net
2. Vzhledem k tomu, že jsou pro sdílené složky otevřené žádné koncové body, se nebude předat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, by tento provoz blokovat NSG (bloku Internet)
4. Nakonec UDR trasy, která byste odesílali všechny odchozí přenosy z AppVM01 do brány firewall jako další segment a bránu firewall by najdete jako asymetrický provoz a vyřadit odchozí odpovědi proto existují aspoň tři nezávislá vrstev obrany mezi internet a AppVM01 přes jeho Cloudová služba, která brání Neautorizováno nevhodných přístupu.

#### <a name="denied-frontend-server-to-backend-server"></a>(Byl odepřen) Front-endu serveru back-end server
1. Předpokládejme, IIS01 došlo k ohrožení a běží škodlivý kód pokusu o zjištění podsíť back-end servery.
2. Trasy UDR podsítě front-endu byste odesílali všechny odchozí přenosy z IIS01 do brány firewall jako další segment. To však není něco, co může být změněna ohrožené virtuální počítač.
3. Brána firewall by zpracovat provoz, pokud se požadavek na AppVM01 nebo na server DNS pro vyhledávání DNS, které provoz může být potenciálně povolený bránou firewall (z důvodu FW pravidla 7 a 9). Všechny ostatní přenosy by se zablokovaly podle FW pravidlo 11 (odmítnout vše).
4. Pokud rozšířené detekce hrozeb byl povolen v bráně firewall (který není zahrnuté v tomto dokumentu, najdete v dokumentaci dodavatele pro vaše konkrétní síťové zařízení advanced threat možnosti), i provoz, který bude mít možnost pravidly základní předávání popisovaný v tomto dokumentu může zabránit, pokud provoz obsažené známé podpisům a vzorů, které příznak pravidlo rozšířené hrozba.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Byl odepřen) Vyhledávání DNS pro Internet na serveru DNS
1. Internet uživatel se pokusí vyhledat interní DNS záznam na DNS01 BackEnd001.CloudApp.Net pomocí služby 
2. Vzhledem k tomu, že jsou pro přenosy DNS otevřené žádné koncové body, se nebude předávat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, by tento provoz blokovat pravidla NSG (bloku Internet) na podsíť Frontend
4. Nakonec směrování back-endu podsíť UDR byste odesílali všechny odchozí přenosy z DNS01 do brány firewall jako další segment a bránu firewall by najdete jako asymetrický provoz a vyřadit odchozí odpovědi proto existují aspoň tři nezávislá vrstev obrany mezi internet a DNS01 přes jeho Cloudová služba, která brání Neautorizováno nevhodných přístupu.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Byl odepřen) Internetu, aby SQL přístup přes bránu Firewall
1. Internet uživatel požádá o dat SQL z SecSvc001.CloudApp.Net (Internet čelí cloudové služby)
2. Vzhledem k tomu, že nejsou otevřené pro SQL koncové body, se nebude předat cloudové služby a nebude kontaktovat bránu firewall
3. Pokud z nějakého důvodu byly otevřené koncové body SQL, brány firewall se začne zpracování pravidla:
   1. Není použít, přejděte k další pravidla FW pravidla 1 (FW Mgmt)
   2. Nemusíte použít, přejděte k další pravidla FW pravidla 2 až 5 (RDP pravidla)
   3. Nemusíte použít, přejděte k další pravidla FW pravidla 6 a 7 (pravidla pro aplikace)
   4. FW pravidlo 8 (Internet k) není použít, přejděte k další pravidla
   5. Není použít, přejděte k další pravidla FW pravidlo 9 (DNS)
   6. FW pravidlo 10 (Intra-podsítě) není použít, přejděte k další pravidla
   7. Použít FW pravidlo 11 (odmítnout vše), provoz je zpracování blokované, zastavení pravidla

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní skript a konfiguraci sítě
Uložte úplné skript v souboru skriptu prostředí PowerShell. Uložte konfiguraci sítě do souboru s názvem "NetworkConf2.xml".
Podle potřeby změňte proměnné definované uživatelem. Spusťte skript a potom postupujte podle pokynů instalace pravidlo brány Firewall výše.

#### <a name="full-script"></a>Úplné skriptu
Tento skript bude na základě uživatelsky definované proměnných:

1. Připojení k předplatnému Azure
2. Vytvořit nový účet úložiště
3. Vytvořit novou virtuální síť a tři podsítě, jak jsou definovány v souboru konfigurace sítě
4. Sestavení pět virtuálních počítačů brány firewall 1 a 4 windows server virtuálních počítačů
5. Konfigurace, včetně UDR:
   1. Vytváření dva nové směrovací tabulky
   2. Přidání tras do tabulky
   3. Vytvořit vazbu tabulky k příslušné podsítě
6. Povolení předávání IP na hodnocení chyb zabezpečení
7. Konfigurace, včetně NSG:
   1. Vytváření skupina NSG
   2. Přidávání pravidla
   3. Vytvoření vazby skupinu NSG na příslušné podsítě

Tento skript prostředí PowerShell je vhodné spustit místně na Internetu připojený počítač nebo server.

> [!IMPORTANT]
> Když tento skript se spustí, může být upozornění nebo ostatní informační zprávy, které pop v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou problém.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Soubor konfigurace sítě
Uložte tento soubor xml s aktualizované umístění a přidat odkaz na tohoto souboru do $NetworkConfigFile proměnné ve skriptu výše.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Ukázkové skripty aplikace
Pokud chcete nainstalovat ukázkovou aplikaci pro toto a další příklady hraniční sítě, jednu bylo zadáno na následující odkaz: [ukázkový skript aplikace][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ obousměrně s hodnocení chyb zabezpečení, NSG a UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logickém zobrazení pravidla brány Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Vytvoření objektu front-endové síti"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Vytvořit objekt serveru DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopii výchozí pravidlo protokolu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Pravidlo AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ikona aplikace přesměrování"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ikona cílové NAT"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ikona průchodu"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Pravidlo brány firewall správy"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Pravidlo brány firewall protokolu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Pravidla brány firewall na webu"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Pravidlo brány firewall AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Odchozí pravidlo brány firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Pravidlo brány firewall DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Pravidlo brány firewall Intra-VNet"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Pravidlo brány firewall Odepřít"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktivace pravidla brány firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
