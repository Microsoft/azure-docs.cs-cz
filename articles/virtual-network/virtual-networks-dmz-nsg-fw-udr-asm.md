---
title: DMZ příklad – vytvoření DMZ k ochraně sítě pomocí brány Firewall, směrování definovaného uživatelem a skupiny zabezpečení sítě | Dokumentace Microsoftu
description: Vytvoření DMZ pomocí brány Firewall, uživatelem definované směrování (UDR) a skupiny zabezpečení sítě (NSG)
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
ms.openlocfilehash: 9c2ebcfc376456f63896ebae8331136aff0cdb99
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119437"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Příklad 3 – vytvoření DMZ k ochraně sítě pomocí brány Firewall, směrování definovaného uživatelem a skupiny zabezpečení sítě
[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

V tomto příkladu bude vytvoření DMZ pomocí brány firewall, čtyři servery windows, směrování definovaného uživatele, předávání IP adres a skupin zabezpečení sítě. To vás také provede všechny relevantní příkazy zajistit lepší představu o jednotlivých kroků. Je také část provozu scénář k poskytování podrobné krok za krokem, jak se provoz pokračuje přes vrstev obrany v hraniční síti. Nakonec v odkazech oddíl je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![Obousměrné DMZ pomocí síťového virtuálního zařízení, skupiny zabezpečení sítě a směrování definovaného uživatelem][1]

## <a name="environment-setup"></a>Nastavení prostředí
V tomto příkladu je předplatné, které obsahuje následující:

* Tři cloudové služby: "SecSvc001", "FrontEnd001" a "BackEnd001"
* Virtuální síť "CorpNetwork" se třemi podsítěmi: "SecNet", "FrontEnd" a "Back-end"
* Síťové virtuální zařízení, v tomto příkladu bránu firewall, připojený k podsíti SecNet
* Windows Server, který představuje aplikační server web (dále jen "IIS01")
* Dva windows serverů, které představují zpět aplikaci ukončit servery ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

V části odkazy níže je skript prostředí PowerShell, který sestaví většinu prostředí, je popsáno výše. Vytváření virtuálních počítačů a virtuálních sítí, i když se provádí ukázkový skript, nejsou v podrobně popsány v tomto dokumentu.

Sestavení prostředí:

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizováno s názvy, umístění a IP adresy, které odpovídají situaci)
2. Aktualizace uživatelské proměnné ve skriptu, tak, aby odpovídaly prostředí, ve kterém je skript běžet před (předplatné, názvy služeb atd.)
3. Spusťte skript prostředí PowerShell

**Poznámka:** Oblast označeny Powershellového skriptu musí odpovídat oblasti označeny v souboru xml konfigurace sítě.

Po úspěšném spuštění skriptu bude může provést následující kroky pozálohovací skript:

1. Nastavení pravidel brány firewall, tento proces je popsán v následující části s názvem: Popis pravidla brány firewall.
2. Volitelně můžete v části odkazy jsou dva skripty pro nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ.

Po úspěšném spuštění skriptu bude brána firewall pravidla se musí dokončit, tento proces je popsán v části s názvem: Pravidla brány firewall.

## <a name="user-defined-routing-udr"></a>Uživatelem definované směrování (UDR)
Ve výchozím nastavení následující systémové trasy, které jsou definovány jako:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal je vždy předpony definovaných adresních virtuální sítě pro danou konkrétní síť (ie se změní z virtuální sítě k virtuální síti v závislosti na tom, jak je definované každá konkrétní virtuální síť). Zbývající systémové trasy, které jsou statické a výchozí jak je uvedeno výše.

Jako u Priorita trasy se zpracovávají prostřednictvím metody nejdelší shody předpony (LPM), proto by nejspecifičtější trasy v tabulce platí pro danou cílovou adresu.

Proto by se směroval provozu (třeba DNS01 server 10.0.2.4) určené pro místní síť (10.0.0.0/16) napříč virtuální sítě do konečného kvůli 10.0.0.0/16 trasy. Jinými slovy pro 10.0.2.4, trasy 10.0.0.0/16 je nejspecifičtější trasy, i když 10.0.0.0/8 a 0.0.0.0/0 také použít, ale protože jsou méně konkrétní neovlivňují tento provoz. Provoz do 10.0.2.4 by tedy mít dalším segmentem směrování místní virtuální sítě a jednoduše směrování do cíle.

Pokud se provoz určený pro 10.1.1.1 například 10.0.0.0/16 trasy se nedají použít, ale 10.0.0.0/8 by nejvíce specifické a provoz by to vyřadit ("black dírkového"), protože další segment směrování je Null. 

Pokud některý z předpony hodnotu Null nebo předpony VNETLocal neměli použít cíl, a měla by odpovídat po nejméně konkrétní směrování 0.0.0.0/0 a připojit k Internetu jako další segment směrování. proto si Azure pro internet edge.

Pokud existují dvě stejné předpony ve směrovací tabulce, je pořadí podle priority na základě atributu "zdroj" trasy:

1. "VirtualAppliance" = uživatelem definovaná trasa ručně přidané do tabulky
2. "Brána VPN" = dynamický směrovací protokol BGP (při použití s hybridní sítě), přidá dynamické síťový protokol, tyto trasy může v průběhu času měnit podle protokolu dynamické automaticky odráží změny v síti s navázaným partnerským vztahem
3. "Výchozí" = systémové trasy, místní virtuální sítě a statické položky, jak je znázorněno v předchozí tabulce směrování.

> [!NOTE]
> Uživatelem definované směrování (UDR) teď můžete použít expressroute a místy bran VPN k vynucení odchozí a příchozí provoz směrovat na virtuální síťové zařízení (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Vytvoření místní trasy
V tomto příkladu jsou potřebné dvě směrovacích tabulek, jednu pro front-end a back-endové podsítě. Každá tabulka je načtena statické trasy, které jsou vhodné pro dané podsíti. Pro účely tohoto příkladu má každá tabulka tří způsobů:

1. Provozu místních podsítí s další směrování definované pro povolení provozu na místní podsíti obejít bránu firewall
2. Provoz virtuální sítě se další směrování definované jako brána firewall, tím se přepíše výchozí pravidlo, které umožňuje místní provoz virtuální sítě pro směrování přímo
3. Veškerý zbývající provoz (0/0) se další směrování definované jako bránu firewall

Po vytvoření směrovací tabulky jsou vázány na podsítě. Front-endové podsítě, směrovací tabulky, jednou vytvořit a vázaný k podsíti by měl vypadat takto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


V tomto příkladu se používají následující příkazy k vytvoření směrovací tabulky, přidejte trasu definovanou uživatelem a svážou směrovací tabulky k podsíti (Poznámka; všechny položky pod začíná znakem dolaru (např: $BESubnet) jsou uživatelem definované proměnné ze skriptu v referenční části tohoto dokumentu):

1. Nejprve musí být vytvořená v základní tabulce směrování. Tento fragment kódu ukazuje vytvoření objektu v tabulce pro podsíť back-endu. Ve skriptu příslušné tabulky se vytvoří také pro front-endové podsítě.
   
     Nové AzureRouteTable – název $BERouteTableName.
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Po vytvoření směrovací tabulky je možné přidat konkrétní uživatelsky definované trasy. V tomto uvádíme se budou směrovat veškerý provoz (0.0.0.0/0) přes toto virtuální zařízení (proměnnou $VMIP [0], se používá k předávání IP adresa přiřazená při vytváření virtuálního zařízení dříve ve skriptu). Ve skriptu se také vytvoří odpovídající pravidlo v tabulce front-endu.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. Výše uvedené položky trasy se přepíšou výchozí "0.0.0.0/0" trasy, ale výchozí pravidlo 10.0.0.0/16 stále existující která umožní provoz v rámci virtuální sítě pro směrování přímo do cíle a ne do síťového virtuálního zařízení. Pro správné toto chování, postupujte podle pravidel musí být přidán.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. V tomto okamžiku je volba má být provedeno. Pomocí výše uvedené dvě cesty bude směrovat veškerý provoz do brány firewall pro posouzení, dokonce i provoz v rámci jedné podsíti. To může být žádoucí, ale pro povolení provozu v rámci podsítě pro směrování místně bez zapojení bránu firewall jiného, velmi konkrétní pravidlo dá přidat. Tato trasa stavy, které libovolnou adresu destine pro stačí, když místní podsíti směrovat existuje přímo (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Nakonec se do směrovací tabulky vytvořena a naplněna trasy definované uživatelem v tabulce musí nyní být vázán na podsíť. Ve skriptu je dále vázané směrovací tabulka front-endu na front-endové podsítě. Tady je skript vazby pro podsíť back-endu.
   
     Set-AzureSubnetRouteTable - VirtualNetworkName $VNetName.
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Předávání IP
Funkce, která doprovodná uživatelem definovaná TRASA, je předávání IP adres. Toto je nastavení na virtuální zařízení, které umožňuje přijímání dat adresovaných konkrétně zkoumaly zařízení a potom je předejte tento provoz do konečného ultimate.

Například pokud provoz z AppVM01 učiní žádost vůči serveru DNS01 uživatelem definovaná TRASA by směrovat to do brány firewall. S povolené předávání IP adres provoz pro cíl DNS01 (10.0.2.4) přijal zařízení (10.0.0.4) a pak se předávají do konečného ultimate (10.0.2.4). Bez předávání IP adres v bráně Firewall povolen nebude se provoz přijímat zařízení i v případě, že směrovací tabulka má brána firewall jako další segment směrování. 

> [!IMPORTANT]
> Je důležité si pamatovat, abyste povolili předávání IP v kombinaci s směrování definovaného uživatele.
> 
> 

Nastavení předávání IP adres je jediným příkazem a můžete provést při vytváření virtuálního počítače. Tok v tomto příkladu fragment kódu je na konci skript a seskupenou s uživatelem definovaná TRASA příkazy:

1. Volání instance virtuálního počítače, který v tomto případě je vaše virtuální zařízení brány firewall a povolení předávání IP adres (Poznámka; libovolnou položku v red od znak dolaru (např: $VMName[0]) je uživatelem definované proměnné ze skriptu v referenční části tohoto dokumentu. Nula v hranatých závorkách [0] představuje první virtuální počítač v poli virtuálních počítačů pro ukázkový skript pro fungovat bez úprav, první virtuální počítač (VM 0) musí být brána firewall):
   
     Get-AzureVM – název $VMName [0] - ServiceName $ServiceName [0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupina NSG vytvořené a pak načíst s jedním pravidlem. Tato skupina je pak vázat pouze na podsítě front-endových a back-end (ne SecNet). Deklarativně se vytváří následující pravidlo:

1. Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (všechny podsítě) byl odepřen.

I když v tomto příkladu se používají skupiny zabezpečení sítě, je hlavním účelem jako sekundární vrstvy ochrany proti chybě ruční konfigurace. Chceme blokovat všechna příchozí provoz z Internetu do buď front-endu a back-endové podsítě, provoz by měl směrovat jenom prostřednictvím SecNet podsítě do brány firewall (a pak v případě potřeby k front-endu a back-endové podsítě). Navíc s pravidly směrování definovaného uživatelem na místě, jakýkoli přenos, který provedl na front-endu a back-endové podsítě by přesměrováni navýšení kapacity na bránu firewall (díky směrování definovaného uživatelem). Brána firewall by to vidí jako asymetrický toku a by vyřadit odchozí provoz. Proto existují tři vrstvy zabezpečení, ochraně front-endových a back-endové podsítě; (1) žádné otevřené koncové body na FrontEnd001 a BackEnd001 cloudových služeb, (2) skupiny Nsg odepření provozu z Internetu, 3) brána firewall vyřazování asymetrického provoz.

Jeden bod zajímavé týkající se skupina zabezpečení sítě v tomto příkladu je, že obsahuje jenom jedno pravidlo je uvedeno níže, což je pro odepření provozu internet celý virtuální sítě, která by obsahovat podsíť zabezpečení. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Ale vzhledem k tomu, že skupina zabezpečení sítě je vázaný jenom na front-endových a back-endové podsítě, pravidlo není zpracován za přenos příchozích zabezpečení podsítě. V důsledku toho i v případě, že pravidlo skupiny zabezpečení sítě říká žádné přenosy z Internetu na jakoukoli adresu ve virtuální síti, protože skupiny zabezpečení sítě se nikdy vázán na podsítě zabezpečení, budou směrovat provoz do podsítě zabezpečení.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Pravidla brány firewall
V bráně firewall pravidla předávání bude nutné vytvořit. Vzhledem k tomu, že brána firewall je provoz blokuje nebo předávání všech příchozích, odchozích a uvnitř virtuální sítě jsou potřeba více pravidel brány firewall. Veškerý příchozí provoz se navíc dostanou služby zabezpečení veřejnou IP adresu (na jiném portu), pro zpracování branou firewall. Osvědčeným postupem je diagram logické toky před nastavením podsítí a pravidla brány firewall, aby se zabránilo Přepracujte později. Na následujícím obrázku je logické zobrazení pravidla brány firewall v tomto příkladu:

![Logické zobrazení pravidel brány Firewall][2]

> [!NOTE]
> Podle síťového virtuálního zařízení používá, se liší portům pro správu. V tomto příkladu, který se odkazuje Barracuda NextGen Firewall, který používá porty 22, 801 a 807. Najdete v dokumentaci od výrobce zařízení najít přesnou portů používá ke správě zařízení používá.
> 
> 

### <a name="logical-rule-description"></a>Popis logické pravidla
Ve výše uvedeném logickém diagramu zabezpečení podsítě se nezobrazí, protože brána firewall je jediný zdroj v dané podsíti, a tento diagram zobrazuje pravidla brány firewall a jak jsou logicky povolují nebo odpírají přenosové toky a ne skutečné cesty směrované. Navíc externích portů vybrané pro provoz protokolu RDP jsou vyšší rozsah portů (8014 – 8026) a nebyly vybrány pro poněkud bylo v souladu s poslední dva oktety místní IP adresa, aby byly čitelnější (například adresa serveru místní 10.0.1.4 souvisí s externím portu 8014), ale může použít jakékoli vyšší nekonfliktní porty.

V tomto příkladu budeme potřebovat 7 typy pravidel, tyto typy pravidel jsou popsány následujícím způsobem:

* Externí pravidla (pro příchozí provoz):
  1. Pravidlo brány firewall správy: Toto pravidlo přesměrování aplikace umožňuje provoz procházel k portům pro správu síťového virtuálního zařízení.
  2. Pravidla protokolu RDP (pro každý server systému windows): Tyto čtyři pravidla (jeden pro každý server) vám umožní správu jednotlivých serverů pomocí protokolu RDP. To může také seskupeny do jednoho pravidla v závislosti na možnosti síťového virtuálního zařízení používá.
  3. Pravidla pro provoz aplikace: Existují dvě pravidla provoz aplikace, první pro webový provoz front-endu a druhé pro back-endu provozu (třeba webový server datové vrstvy). Konfigurace tato pravidla se závisí na síťovou architekturu (kde jsou umístěny servery) a provoz toků (směr přenosové toky a porty, které se používají).
     * První pravidlo bude umožňovat provoz aplikace skutečný přístup k serveru aplikace. Zatímco ostatní pravidla povolit pro zabezpečení, správy a podobně, jsou pravidla aplikace co umožňují externí uživatele nebo služby pro přístup k aplikace. V tomto příkladu je jednom webovém serveru na portu 80, tak jediné aplikace pravidlo firewallu přesměruje příchozí provoz na externí IP adresu, na webové servery interní IP adresu. Relace přesměrované přenosy by být NAT museli interní server.
     * Druhé pravidlo pro provoz aplikací je back-endu pravidlo pro povolení webového serveru ke komunikaci s AppVM01 serveru (ale ne AppVM02) přes libovolný port.
* Vnitřní pravidla (pro provoz uvnitř virtuální sítě)
  1. Odchozí internetové pravidlo: Toto pravidlo umožní provoz z libovolné sítě k předání do vybraných sítí. Toto pravidlo je obvykle výchozí pravidlo již v bráně firewall, ale v zakázaném stavu. Toto pravidlo musí být povolené pro účely tohoto příkladu.
  2. Pravidlo DNS: Toto pravidlo umožní provoz jenom DNS (port 53) k předání do serveru DNS. Toto pravidlo pro toto prostředí, které většina provoz z front-endu do back-endu blokovaný, konkrétně umožňuje DNS z jakékoli místní podsítě.
  3. Podsítě pro podsíť pravidlo: Toto pravidlo je povolit všechny servery v podsíti back-endu pro připojení k libovolnému serveru na front-endové podsítě (ale ne naopak).
* Odolné vůči selhání pravidla (pro provoz, který nesplňuje některé z výše):
  1. Všechny přenosy pravidlo odepřít: By měl vždy být konečné pravidlo (z hlediska prioritou) a proto pokud přenosem toky nepodaří shodují s některým z předchozích pravidel, které se zahodí tímto pravidlem. Toto je výchozí pravidlo a obvykle aktivovaná, nelze provádět žádné změny nejsou obvykle potřeba.

> [!TIP]
> Na druhé pravidlo provoz aplikace libovolný port je povolen pro snadné tohoto příkladu v reálné situaci nejspecifičtější port a rozsahy adres má použít k omezení možností útoku tohoto pravidla.
> 
> 

<br />

> [!IMPORTANT]
> Po vytvoření všech výše uvedených pravidel, je důležité si prioritu každé pravidlo k zajištění provozu se povolí nebo zakáže podle potřeby. V tomto příkladu jsou pravidla v pořadí podle priority. Je snadné být zablokována kvůli chybné seřazených pravidel brány firewall. Minimálně zajistěte, aby že správa brány firewall pro samotné je vždy absolutní pravidlo nejvyšší prioritou.
> 
> 

### <a name="rule-prerequisites"></a>Pravidla požadavků
Jeden požadavek pro virtuální počítač s brány firewall jsou veřejné koncové body. Pro bránu firewall pro zpracování provozu musí být otevřený odpovídající veřejné koncové body. Existují tři typy provozu v tomto příkladu; Provoz protokolu RDP (1) provoz správy ke správě brány firewall a pravidla brány firewall, 2) k řízení serverů se systémem windows a přenos 3) aplikace. Toto jsou tři sloupce typů přenosů v horní polovině logické zobrazení výše uvedená pravidla brány firewall.

> [!IMPORTANT]
> Klíče takeway zde má mějte na paměti, **všechny** přijde přenosů přes bránu firewall. Tak vzdálené plochy k serveru IIS01, i když je v cloudové službě Front End a na front-endové podsítě přístup k tomuto serveru jsme bude muset bránu firewall na portu 8014 pro protokol RDP a pak povolit bránu firewall pro směrování požadavku protokolu RDP interně k Por IIS01 protokolu RDP t. Tlačítko "Připojit" webu Azure portal nebude fungovat, protože není žádné přímé cestu protokolu RDP k IIS01 (jde o vidí portálu). To znamená, že všechna připojení z Internetu bude služba zabezpečení a Port, třeba secscv001.cloudapp.net:xxxx (referenční dokumentace diagramu pro mapování externí a interní IP adresa a Port).
> 
> 

Koncový bod můžete otevřít buď v době vytvoření virtuálního počítače nebo odeslat sestavení je v příkladu skript a níže v tomto fragmentu kódu (Poznámka; všechny položky začíná znakem dolaru (např: $VMName[$i]) je uživatelem definované proměnné ze skriptu v odkaz na oddíl n tohoto dokumentu. "$I" v závorkách [$i] představuje číslo pole konkrétního virtuálního počítače v poli virtuálních počítačů):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

I když není jasně zobrazen zde kvůli použití proměnné, ale koncové body jsou **pouze** otevřít v cloudové službě zabezpečení. Tím je zajištěno, že veškerý příchozí provoz probíhá (směrovat, NAT měli, vynechané) bránou firewall.

Klienta správy bude potřeba nainstalovat na počítači pro správu brány firewall a vytváření konfigurací potřeba. O správě zařízení najdete v článku dodavatele dokumentace ke službě z vaší brány firewall (nebo jiné síťové virtuální zařízení). Zbývající část této části a v další části vytváření pravidel brány Firewall, popisuje konfigurace, brány firewall prostřednictvím dodavatele správy klienta (tzn. ne Azure portal nebo Powershellu).

Pokyny pro stažení klienta a připojení k Barracuda použitý v tomto příkladu najdete tady: [Barracuda NG správce](https://techlib.barracuda.com/NG61/NGAdmin)

Po přihlášení na bránu firewall, ale před vytvořením pravidel brány firewall, existují dvě požadovaný objekt třídy, které umožňují vytváření pravidel snadněji; Objekty, sítě a služby.

V tomto příkladu tři objekty pojmenované sítě by měl být definované (jeden pro front-endovou podsíť a podsíť back-endu, také síťového objektu pro IP adresu serveru DNS). Pokud chcete vytvořit síť s názvem; spouští se na řídicím panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v části Konfigurace provozní klikněte sady pravidel, pak klikněte na tlačítko "Sítě" v nabídce objekty brány Firewall a pak klikněte na nový v nabídce Upravit sítě. Objekt sítě může být nyní vytvořen tak, že přidáte název a předpona:

![Vytvoření front-endové síti objektu][3]

Tím se vytvoří pojmenované sítě pro podsíť FrontEnd, podobně jako objekt měl být vytvořen pro do back-endové podsítě. Nyní podsítě můžete snadněji odkazovat podle názvu v pravidlech brány firewall.

Pro objekt Server DNS:

![Vytvoření objektu serveru DNS][4]

Tento jeden odkaz na IP adresy se používá v pravidle DNS později v dokumentu.

Druhý požadované objekty jsou objekty služby. Představují porty pro připojení RDP pro každý server. Protože existující objekt služby RDP je vázán na výchozí port protokolu RDP, 3389, nové služby můžete vytvořit umožňující provoz z externích portů (8014 8026). Nové porty může také být přidán do existující služby protokolu RDP, ale z důvodu snadnějšího ukázku, můžete vytvořit jednotlivé pravidlo pro každý server. Chcete-li vytvořit nové pravidlo protokolu RDP pro server. spuštění z řídicího panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v části Konfigurace provozní klikněte sady pravidel, pak klikněte na tlačítko "Služby" v nabídce objekty brány Firewall, přejděte dolů v seznamu služeb a vyberte službu "RDP". Klikněte pravým tlačítkem na a kopírovat, pak klikněte pravým tlačítkem a vyberte Vložit. Je teď RDP Copy1 objekt služby, které lze upravovat. Klikněte pravým tlačítkem na RDP Copy1 a vyberte upravit, upravit objekt služby objeví okno se až, jak je znázorněno zde:

![Kopii výchozí pravidlo protokolu RDP][5]

Hodnoty lze upravit k reprezentaci službu protokolu RDP pro konkrétní server. Pro AppVM01 výše uvedené výchozí pravidlo protokolu RDP by měl být upraven tak, aby odrážely nový název služby, popis a externí Port RDP v diagramu obrázek 8 (Poznámka: porty jsou změněny výchozí RDP 3389 pro externí port se používají pro tento konkrétní server v případě AppVM01 externí Port je 8025) upravenou službu jsou uvedené níže:

![Pravidlo AppVM01][6]

Tento proces opakuje k vytvoření služby protokolu RDP pro ostatní servery; AppVM02 DNS01 a IIS01. Vytvoření těchto služeb bude zjednodušit vytváření pravidla a zřetelnější v další části.

> [!NOTE]
> Pro dva důvody; není potřeba služby protokolu RDP pro bránu Firewall (1) první brány firewall virtuálního počítače je bitové kopie založené na Linuxu, takže budou použita SSH na portu 22 pro správu virtuálních počítačů místo protokolu RDP a (2) port 22, a dvě další porty pro správu jsou povoleny v první pravidlo správy popsaných níže a umožňují připojení správy.
> 
> 

### <a name="firewall-rules-creation"></a>Vytváření pravidel brány firewall
Existují tři typy v tomto příkladu používá pravidla brány firewall, všichni mají odlišné ikony:

Toto pravidlo přesměrování aplikace: ![Ikona aplikace pro přesměrování][7]

Pravidla NAT cíl: ![Určení ikony NAT][8]

Pravidlo Pass: ![Ikona pass][9]

Další informace o těchto pravidlech najdete na webu Barracuda.

K vytvoření následujících pravidel (nebo ověřit existující výchozí pravidla), od řídicího panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v provozní konfigurační oddíl, klikněte na sady pravidel. Mřížka volá se, "Hlavní pravidla" se zobrazí existující pravidla aktivní a deaktivované na tuto bránu firewall. V pravém horním rohu tuto mřížku je malá zelená "+" tlačítko, klepnutím na toto tlačítko Vytvořit nové pravidlo (Poznámka: vaše brána firewall může být "uzamčen" změny, pokud se zobrazí tlačítko označený "Lock" a nejde vytvořit nebo upravit pravidla, klikněte na toto tlačítko "odemknout" sady pravidel a  Povolte úpravy). Pokud chcete upravit stávající pravidlo, vyberte toto pravidlo, klikněte pravým tlačítkem a vyberte Upravit pravidlo.

Jakmile jsou pravidla vytvořit nebo upravit, musí být vloženo do brány firewall a pak se aktivuje, pokud tato podmínka není splněna pravidla změny projeví. Nabízená a aktivační proces je popsán níže popisy podrobnosti pravidla.

Specifika každé pravidlo předpokladem pro dokončení v tomto příkladu jsou popsány následujícím způsobem:

* **Správa pravidel brány firewall**: Toto pravidlo přesměrování aplikace umožňuje provoz procházel k portům pro správu síťové virtuální zařízení, v tomto příkladu Barracuda NextGen Firewall. Porty pro správu jsou 801, 807 a volitelně 22. Externí a interní porty jsou stejné (to znamená žádný port překlad). Toto pravidlo, nastavení-MGMT-přístupu, je výchozí pravidlo a povolena ve výchozím nastavení (Barracuda NextGen Firewall verze 6.1).
  
    ![Pravidlo brány firewall správy][10]

> [!TIP]
> Adresní prostor zdroje v tomto pravidle je, pokud jsou známé rozsahy správy IP adres, snižuje tohoto oboru by také omezení možností útoku k portům pro správu.
> 
> 

* **Pravidla protokolu RDP**:  Tato pravidla NAT cílové vám umožní správu jednotlivých serverů pomocí protokolu RDP.
  Existují čtyři kritické pole, které jsou potřeba k vytvoření tohoto pravidla:
  
  1. Zdroj – chcete-li povolit RDP z libovolného místa, odkaz na "Žádný" se používá ve zdrojovém poli.
  2. Služba – použít odpovídající objekt služby vytvořili dříve, v tomto případě "AppVM01 RDP", externích portů přesměrovat na místní servery IP adresu a port 3386 (výchozí port RDP). Toto zvláštní pravidlo je pro přístup protokolu RDP k AppVM01.
  3. Cíl – musí být *místní port v bráně firewall*, "Místní IP 1 DCHP" nebo eth0, pokud používáte statické IP adresy. Ordinální číslo (eth0, eth1 atd.), může lišit, pokud vaše síťové zařízení má několik místní rozhraní. Toto je port brány firewall odesílá z (může být stejná jako přijímající port), skutečné směrované cíl je v poli seznamu cílů.
  4. Přesměrování – v této části zjistí, virtuální zařízení kde nakonec přesměrovat provoz. Nejjednodušší přesměrování je umístit do cílového seznamu pole IP adresy a portu (volitelné). Pokud se port používá cílový port pro příchozí požadavek bude použít (ie žádný překlad), v případě, že port je určen port, který bude také NAT by spolu se IP adresa.
     
     ![Pravidlo brány firewall protokolu RDP][11]
     
     Celkem čtyři pravidla protokolu RDP, bude potřeba vytvořit: 
     
     | Název pravidla | Server | Služba | Seznam cílů |
     | --- | --- | --- | --- |
     | Protokol RDP IIS01 |IIS01 |IIS01 PROTOKOLU RDP |10.0.1.4:3389 |
     | Protokol RDP DNS01 |DNS01 |DNS01 PROTOKOLU RDP |10.0.2.4:3389 |
     | Protokol RDP AppVM01 |AppVM01 |AppVM01 protokolu RDP |10.0.2.5:3389 |
     | Protokol RDP AppVM02 |AppVM02 |AppVm02 protokolu RDP |10.0.2.6:3389 |

> [!TIP]
> Usnadnit působnosti polích zdroje a služby se omezení možností útoku. Nejvíce omezený obor, který vám umožní funkce by měla sloužit.
> 
> 

* **Pravidla pro provoz aplikace**: Existují dvě pravidla provoz aplikace, první pro webový provoz front-endu a druhé pro back-endu provozu (třeba webový server datové vrstvy). Tahle pravidla umožňují bude záviset na síťovou architekturu (kde jsou umístěny servery) a provoz toků (směr přenosové toky a porty, které se používají).
  
    Nejprve popsané je pravidlo front-endu pro webový provoz:
  
    ![Pravidlo brány firewall na Web][12]
  
    Toto pravidlo překladu adres cílové umožní provoz aplikace skutečný přístup k serveru aplikace. Zatímco ostatní pravidla povolit pro zabezpečení, správy a podobně, jsou pravidla aplikace co umožňují externí uživatele nebo služby pro přístup k aplikace. V tomto příkladu je jednom webovém serveru na portu 80, tak pravidlo brány firewall na jeden aplikace přesměruje příchozí provoz na externí IP adresu, na webové servery interní IP adresu.
  
    **Poznámka:**:, že neexistuje žádný port přiřazen do cílového seznamu pole, proto příchozí port 80 (nebo 443 pro vybrané služby) se použije při přesměrování na webovém serveru. Pokud webový server naslouchá na jiném portu, například port 8080, mohlo dojít k aktualizaci seznamu cílů pole k 10.0.1.4:8080 umožňující také přesměrování portu.
  
    Další pravidlo pro provoz aplikací je back-endu pravidlo pro povolení webového serveru ke komunikaci s AppVM01 serveru (ale ne AppVM02) prostřednictvím jakékoli služby:
  
    ![Pravidlo brány firewall AppVM01][13]
  
    Toto pravidlo Pass umožňuje jakýkoli server služby IIS na front-endové podsítě k dosažení AppVM01 (IP adresy 10.0.2.5) na jiný port pomocí libovolného protokolu pro přístup k datům vyžadované webovou aplikaci.
  
    Na tomto snímku obrazovky "\<explicitní dest\>" se používá v cílovém poli místo 10.0.2.5 jako cíl. To může být buď explicitní znázorněno nebo název objektu sítě (jak tomu bylo v rámci požadavků pro DNS server). To záleží na správce brány firewall, která bude použita metoda. Přidat explicitní Desitnation 10.0.2.5, dvakrát klikněte na první prázdný řádek pod \<explicitní dest\> a zadejte adresu v okně nastavení.
  
    S tímto pravidlem předat bez překladu adres je potřeba, protože se jedná interního provozu, takže způsob připojení může být nastaven na "Ne SNAT".
  
    **Poznámka:** Zdrojová síť v tomto pravidle je prostředek ve front-endové podsíti, pokud bude existovat pouze jeden, nebo známé určitý počet webových serverů, objektu sítě může být vytvořen prostředek na konkrétnější na těchto přesné IP adresy místo celé front-endové podsítě.

> [!TIP]
> Toto pravidlo používá službu "Žádná" pro usnadnění ukázkovou aplikaci nastavit a používat, to také umožní ICMPv4 (ping) v jediné pravidlo. Nicméně toto není doporučený postup. Porty a protokoly ("služby") by měl být zúžit na nejmenší možnou, který umožňuje aplikaci operace pro omezení možností útoku na této hranice.
> 
> 

<br />

> [!TIP]
> I když toto pravidlo se zobrazí odkaz na explicitní dest používá, v rámci konfigurace brány firewall by měla sloužit konzistentní přístup. Doporučuje se použít s názvem objektu sítě v průběhu pro čitelnější a podpoře. Explicitní dest zde slouží pouze k zobrazení o odkaz na alternativní metodu a se obecně nedoporučuje (zejména pro složité konfigurace).
> 
> 

* **Odchozí internetové pravidlo**: Toto pravidlo Pass vám umožní provoz z jakékoli zdrojové síti k předání do vybrané cílové sítě. Toto pravidlo je výchozí pravidlo obvykle již v bráně Barracuda NextGen firewall, ale je v zakázaném stavu. Pravým tlačítkem myši na pravidlo můžete přístup k příkazu aktivovat pravidlo. Přidejte dva místní podsítě, které byly vytvořeny jako odkazy v části požadavků tohoto dokumentu ke zdrojovému atributu tohoto pravidla byla změněna pravidla je vidět tady.
  
    ![Odchozí pravidla brány firewall][14]
* **Pravidlo DNS**: Toto pravidlo Pass umožní provoz jenom DNS (port 53) k předání do serveru DNS. Toto pravidlo pro toto prostředí, které většina provoz z front-endu do back-endu blokovaný, konkrétně umožňuje DNS.
  
    ![Pravidlo brány firewall na DNS][15]
  
    **Poznámka:** Na této obrazovce je v ceně snímek metodu připojení. Protože toto pravidlo je pro interní IP adresa na interní IP adresa provoz, žádné NATing je povinný, to metodu připojení je nastavena na "Ne SNAT" pro toto pravidlo Pass.
* **Podsítě pro podsíť pravidlo**: Toto pravidlo Pass je výchozí pravidlo, které byla aktivována a upravit tak, aby povolit všechny servery v podsíti back-endu pro připojení k libovolnému serveru na front-endové podsítě. Toto pravidlo je všechny vnitřní provoz, takže způsob připojení může být nastavena na No SNAT.
  
    ![Pravidlo brány firewall uvnitř virtuální sítě][16]
  
    **Poznámka:** Obousměrné zaškrtávací políčko není zaškrtnuté políčko (ani ho většina pravidel se změnami je), to je důležité pro toto pravidlo, je to kvůli tomu pravidlo "jednosměrně", připojení, nepůjdou z podsítě back-end k front-endovou síť, ale ne opačně. Pokud se toto zaškrtávací políčko zaškrtnuto, bude toto pravidlo povolit obousměrný provoz, který z našich Logický diagram není žádoucí.
* **Všechny přenosy pravidlo Odepřít**: By měl vždy být konečné pravidlo (z hlediska prioritou) a proto pokud přenosem toky nepodaří shodují s některým z předchozích pravidel, které se zahodí tímto pravidlem. Toto je výchozí pravidlo a obvykle aktivovaná, nelze provádět žádné změny nejsou obvykle potřeba. 
  
    ![Pravidlo brány firewall Odepřít][17]

> [!IMPORTANT]
> Po vytvoření všech výše uvedených pravidel, je důležité si prioritu každé pravidlo k zajištění provozu se povolí nebo zakáže podle potřeby. V tomto příkladu jsou pravidla v pořadí, ve kterém by se zobrazit v mřížce hlavní předávání pravidla v klientovi Barracuda Management.
> 
> 

## <a name="rule-activation"></a>Pravidlo aktivace
Pomocí pravidel upravit tak, aby specifikace diagram logiky musí být sady pravidel nahráli do brány firewall a pak se aktivuje.

![Aktivace pravidla brány firewall][18]

V pravém horním rohu okna správy klienta se cluster tlačítek. Klikněte na tlačítko "Odeslat změny" a odeslat upravenou pravidla brány firewall a potom klikněte na tlačítko "Aktivovat".

Díky aktivaci sady pravidel brány firewall prostředí sestavení tohoto příkladu je dokončena.

## <a name="traffic-scenarios"></a>Provoz scénáře
> [!IMPORTANT]
> Klíče takeway je si zapamatovat, že **všechny** přijde přenosů přes bránu firewall. Tak vzdálené plochy k serveru IIS01, i když je v cloudové službě Front End a na front-endové podsítě přístup k tomuto serveru jsme bude muset bránu firewall na portu 8014 pro protokol RDP a pak povolit bránu firewall pro směrování požadavku protokolu RDP interně k Por IIS01 protokolu RDP t. Tlačítko "Připojit" webu Azure portal nebude fungovat, protože není žádné přímé cestu protokolu RDP k IIS01 (jde o vidí portálu). To znamená, že všechna připojení z Internetu bude služba zabezpečení a Port, třeba secscv001.cloudapp.net:xxxx.
> 
> 

Pro tyto scénáře následující pravidla brány firewall by měla:

1. Správa brány firewall
2. Připojení RDP k IIS01
3. Připojení RDP k DNS01
4. Připojení RDP k AppVM01
5. Připojení RDP k AppVM02
6. Provoz aplikací na webu
7. Provoz aplikací na AppVM01
8. Odchozí do Internetu
9. Front-endu do DNS01
10. Provoz uvnitř podsítě (back-end k front-endu jenom)
11. Zamítnout vše

Sady pravidel brány firewall na skutečné pravděpodobně budou mít mnoho pravidel kromě těchto, pravidel v jakékoli dané brány firewall budou mít i čísla jinou prioritu než ty, které jsou tady uvedené. Tento seznam a přidružená čísla jsou zajištění relevance mezi právě tato jedenáct pravidla a relativní prioritu mezi nimi. Jinými slovy; skutečné bráně firewall "RDP k IIS01" může být pravidlo číslo 5, ale dokud je nižší než pravidel "Správa brány Firewall" a výše "RDP k DNS01" pravidlo by zarovnat tak tento seznam. V seznamu se také vám pomůže se následující scénáře umožní zkrácení; například "Pravidlo FW 9 (DNS)". Také jako stručný výtah čtyři pravidla protokolu RDP se souhrnně volat, "pravidla protokolu RDP" Při scénáři provoz nijak s RDP.

Také Připomínáme, že skupiny zabezpečení sítě jsou místní pro příchozí přenosy z Internetu na front-endových a back-endové podsítě.

#### <a name="allowed-internet-to-web-server"></a>(Povolena) Internet na webový Server
1. Internet uživatelské požadavky HTTP stránky SecSvc001.CloudApp.Net (Internet směřující cloudové služby)
2. Cloudové služby předává provoz přes otevřených koncových bodů na portu 80 na rozhraní brány firewall na 10.0.0.4:80
3. Žádnou skupinu zabezpečení sítě přiřazené podsítě zabezpečení, takže pravidla skupiny zabezpečení sítě systému povolení provozu do brány firewall
4. Provoz narazí na interní IP adresa brány firewall (10.0.1.4)
5. Brána firewall začíná zpracování pravidla:
   1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
   2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
   3. Pravidlo FW 6 (aplikace: Použít web), je povolený provoz, brány firewall NAT na 10.0.1.4 (IIS01)
6. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. Neplatí 1 pravidlo skupiny zabezpečení sítě (Internet bloku) (Tento provoz se NAT by bránou firewall, proto je zdrojovou adresu nyní brány firewall, která je v podsíti zabezpečení a vidět skupina NSG podsítě front-endu jako "místní" provoz a je proto povolený), přesunout na další pravidla
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
7. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
8. Inicializuje IIS01 pokusí relace FTP na AppVM01 na podsíť back-endu
9. Trasu UDR na front-endové podsítě je brána firewall dalšího segmentu směrování
10. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
11. Brána firewall začíná zpracování pravidla:
    1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
    2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
    3. Pravidlo FW 6 (aplikace: Web) nebude použít, přejděte k další pravidla
    4. Pravidlo FW 7 (aplikace: Vztahuje se back-end), je povolený provoz, brány firewall předává provoz na 10.0.2.5 (AppVM01)
12. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
    1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
13. AppVM01 obdrží požadavek a zahájí relace a reaguje
14. Trasu UDR v back-endové podsítě je brána firewall dalšího segmentu směrování
15. Protože je povolená žádná odchozí pravidla skupiny zabezpečení sítě na podsíť back-endu odpovědi
16. Protože toto vrací provoz na navázanou relaci brány předá odpověď zpět do webového serveru (IIS01)
17. Front-endové podsítě začíná zpracování příchozí pravidlo:
    1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
18. Server služby IIS obdrží odpověď, dokončí transakci AppVM01 a pak dokončí vytváření odpovědi HTTP, tato odpověď HTTP se pošle žadateli
19. Protože je povolená žádná odchozí pravidla skupiny zabezpečení sítě ve front-endové podsíti odpovědi
20. Odpověď HTTP narazí na bránu firewall a vzhledem k tomu, že jedná se o odpověď na navázanou relaci NAT přijat bránou firewall
21. Brána firewall pak přesměruje odpověď zpět do Internetu uživatele
22. Vzhledem k tomu, že neexistují žádné odchozí pravidla skupiny zabezpečení sítě nebo UDR směrování na front-endové podsíti, ve které odpovědi je povolený a uživatel Internet obdrží požadované webové stránky.

#### <a name="allowed-internet-rdp-to-backend"></a>(Povolena) Internetové připojení RDP k back-endu
1. Správce serveru k Internetu vyžaduje relaci RDP na AppVM01 prostřednictvím SecSvc001.CloudApp.Net:8025, kde je číslo portu přiřazené uživateli pro pravidlo brány firewall "RDP k AppVM01" 8025
2. Cloudová služba předává provoz přes otevřených koncových bodů na portu 8025 rozhraní brány firewall na 10.0.0.4:8025
3. Žádnou skupinu zabezpečení sítě přiřazené podsítě zabezpečení, takže pravidla skupiny zabezpečení sítě systému povolení provozu do brány firewall
4. Brána firewall začíná zpracování pravidla:
   1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
   2. FW pravidla 2 (protokol RDP IIS) není použít, přejděte k další pravidla
   3. FW pravidla 3 (protokol RDP DNS01) nebude použít, přejděte k další pravidla
   4. Použít FW pravidla 4 (protokol RDP AppVM01), je povolený provoz, brány firewall NAT na 10.0.2.5:3386 (port RDP na AppVM01)
5. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. Neplatí 1 pravidlo skupiny zabezpečení sítě (Internet bloku) (Tento provoz se NAT by bránou firewall, proto je zdrojovou adresu nyní brány firewall, která je v podsíti zabezpečení a vidět skupina NSG podsítě back-end jako "místní" provoz a je proto povolený), přesunout na další pravidla
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
6. AppVM01 naslouchá provozu protokolu RDP a reaguje
7. Žádná odchozí pravidla NSG použít výchozí pravidla a zpětný provoz je povolený.
8. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování
9. Protože toto vrací provoz na navázanou relaci brány předá odpověď zpět do Internetu uživatele
10. Povolené relace protokolu RDP
11. AppVM01 vyzve k zadání uživatelského jména hesla

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povolena) Webové vyhledávání serveru DNS na serveru DNS
1. Webový Server, IIS01, potřebám datového kanálu na www.data.gov, ale potřebám pro překlad adres.
2. Konfiguraci sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v back-endové podsíti) jako primární server DNS, IIS01 odešle žádosti DNS DNS01
3. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování
4. Žádná odchozí pravidla NSG je vázána na front-endové podsíti, je povolený provoz
5. Brána firewall začíná zpracování pravidla:
   1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
   2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
   3. Není použít, přejděte k další pravidlo FW pravidla 6 a 7 (pravidla pro aplikace)
   4. FW pravidlo 8 (do Internetu) nebude použít, přejděte k další pravidla
   5. Použít FW pravidlo 9 (DNS), je povolený provoz, brána firewall předává provoz na 10.0.2.4 (DNS01)
6. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
7. DNS server obdrží požadavek
8. DNS server nemá adresu do mezipaměti a požádá kořenový server DNS na Internetu
9. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování
10. Žádná odchozí pravidla skupiny zabezpečení sítě na podsíť back-endu provoz je povolený.
11. Brána firewall začíná zpracování pravidla:
    1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
    2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
    3. Není použít, přejděte k další pravidlo FW pravidla 6 a 7 (pravidla pro aplikace)
    4. Použít pravidlo 8 FW (do Internetu), je povolený provoz, relace je SNAT navýšení kapacity pro kořenový server DNS na Internetu
12. Reakce serveru DNS pro Internet, protože tuto relaci bylo zahájeno ze brána firewall, odpověď je přijat bránou firewall
13. Toto je navázanou relaci, brána firewall předá odpověď na původní server DNS01
14. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
    1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
15. DNS server obdrží odpověď do mezipaměti a pak odpovídá původní žádost zpět na IIS01
16. Trasu UDR v back-endové podsítě je brána firewall dalšího segmentu směrování
17. Neexistuje žádná odchozí pravidla skupiny zabezpečení sítě na podsíť back-endu, provoz je povolený
18. Toto je navázanou relaci v bráně firewall, odpověď se přesměrovávají pomocí brány firewall zpět na server služby IIS
19. Front-endové podsítě začíná zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz
20. IIS01 obdrží odpověď od DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Povolena) Back-end server k serveru front-endu
1. Správce přihlášený k AppVM02 přes protokol RDP, požadavek na soubor přímo ze serveru IIS01 prostřednictvím Průzkumníka souborů windows
2. Trasu UDR v back-endové podsítě je brána firewall dalšího segmentu směrování
3. Protože je povolená žádná odchozí pravidla skupiny zabezpečení sítě na podsíť back-endu odpovědi
4. Brána firewall začíná zpracování pravidla:
   1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
   2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
   3. Není použít, přejděte k další pravidlo FW pravidla 6 a 7 (pravidla pro aplikace)
   4. FW pravidlo 8 (do Internetu) nebude použít, přejděte k další pravidla
   5. Není použít, přejděte k další pravidlo FW pravidlo 9 (DNS)
   6. Použít pravidlo 10 FW (Intra-podsítě), je povolený provoz, brány firewall předává provoz 10.0.1.4 (IIS01)
5. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
   2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
6. Za předpokladu, že řádné ověření a autorizace, IIS01 tato služba požadavek přijme a reaguje
7. Trasu UDR na front-endové podsítě je brána firewall dalšího segmentu směrování
8. Protože je povolená žádná odchozí pravidla skupiny zabezpečení sítě ve front-endové podsíti odpovědi
9. Je to existující relaci v bráně firewall povolená tato odpověď a bránu firewall vrací odpověď AppVM02
10. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
    1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block Internet)
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený, zastavit zpracování pravidel NSG
11. AppVM02 obdrží odpověď

#### <a name="denied-internet-direct-to-web-server"></a>(Zakázaný) Internet s přímým přístupem k webovému serveru
1. Internet uživatel pokusí o přístup k webovému serveru, IIS01, přes službu FrontEnd001.CloudApp.Net
2. Protože je otevřeno žádné koncové body pro přenosy pomocí protokolu HTTP, to nebude projít Cloudovou službu a nebude připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncové body, skupiny zabezpečení sítě (Block Internet) ve front-endové podsíti by blokovaly tento provoz
4. Nakonec trasu UDR podsítě front-endu bude posílat veškerého odchozího provozu z IIS01 firewall jako další směrování, a brána firewall by to vidí jako asymetrický provoz a vyřadit odchozí odpovědi Thus existují aspoň tři nezávislá vrstev obrany mezi internet a IIS01 prostřednictvím jeho Cloudová služba, která brání neoprávněným neoprávněném přístupu.

#### <a name="denied-internet-to-backend-server"></a>(Zakázaný) Internet back-end server
1. Internet uživatel pokusí přistoupit k souboru na AppVM01 přes službu BackEnd001.CloudApp.Net
2. Protože je otevřeno žádné koncové body pro sdílenou složku, to nebude předejte Cloudovou službu a nebude připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncové body, skupiny zabezpečení sítě (bloku Internetu) by blokovaly tento provoz
4. Nakonec trasu UDR bude posílat veškerého odchozího provozu z AppVM01 firewall jako další směrování, a brána firewall by to vidí jako asymetrický provoz a vyřadit odchozí odpovědi proto existují aspoň tři nezávislá vrstev obrany mezi Internetem a AppVM01 prostřednictvím jeho Cloudová služba, která brání neoprávněným neoprávněném přístupu.

#### <a name="denied-frontend-server-to-backend-server"></a>(Zakázaný) Front-endový server back-end server
1. Předpokládejme IIS01 došlo k napadení, běží škodlivý kód pokusu skenovací servery back-endové podsítě.
2. Trasu UDR podsítě front-endu bude posílat veškerého odchozího provozu z IIS01 bránu firewall jako další segment směrování. To je něco, co může změnit ohrožení zabezpečení virtuálních počítačů.
3. Brána firewall by zpracovat provoz, pokud se požadavek na AppVM01 nebo serveru DNS pro vyhledávání DNS, které může potenciálně být povolený provoz bránou firewall (z důvodu FW pravidla 7 a 9). Veškerý ostatní provoz by být blokovány FW pravidlo 11 (Zamítnout vše).
4. Pokud rozšířeného zjišťování hrozeb byl povolen v bráně firewall (který není zahrnuté v tomto dokumentu, najdete v dokumentaci dodavatele pro vaše konkrétní síťové zařízení advanced threat možnosti), dokonce i provoz, který by bylo možné pravidly základní předávání popsané v tomto dokumentu by mohla zabránit, pokud provoz obsažené známé podpisy nebo vzorce, které příznak pravidlo pokročilé hrozby.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Zakázaný) Vyhledávání DNS pro Internet na serveru DNS
1. Internet uživatel se pokusí vyhledat interní DNS záznam na DNS01 prostřednictvím BackEnd001.CloudApp.Net služby 
2. Protože je otevřeno žádné koncové body pro provoz DNS, to nebude projít Cloudovou službu a by připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncových bodů, pravidlo skupiny zabezpečení sítě (Block Internet) na front-endové podsítě by blokovaly tento provoz
4. Nakonec trasu UDR podsíť back-endu bude posílat veškerého odchozího provozu z DNS01 firewall jako další směrování, a brána firewall by to vidí jako asymetrický provoz a vyřadit odchozí odpovědi Thus existují aspoň tři nezávislá vrstev obrany mezi Internet a DNS01 prostřednictvím jeho Cloudová služba, která brání neoprávněným neoprávněném přístupu.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Zakázaný) Internetu, aby přístup k SQL přes bránu Firewall
1. Internet uživatel požádá o SQL data z SecSvc001.CloudApp.Net (Internet směřující cloudové služby)
2. Protože je otevřeno žádné koncové body pro server SQL, to nebude předat Cloudovou službu a by kontaktovat bránu firewall
3. Pokud z nějakého důvodu otevřelo koncových bodů SQL, brána firewall by zahájit zpracování pravidla:
   1. Není použít, přejděte k další pravidlo FW Rule 1 (FW Mgmt)
   2. FW pravidla 2 – 5 (protokol RDP pravidla) nemusíte použít, přejděte k další pravidla
   3. FW pravidla 6 a 7 (pravidla aplikací) nemusíte použít, přejděte k další pravidla
   4. FW pravidlo 8 (do Internetu) nebude použít, přejděte k další pravidla
   5. Není použít, přejděte k další pravidlo FW pravidlo 9 (DNS)
   6. FW pravidlo 10 (Intra-podsítě) nebude použít, přejděte k další pravidla
   7. Použít pravidla FW 11 (Zamítnout vše), provoz je blokované, zastavte pravidla zpracování

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní skript a konfigurace sítě
Úplná skript uložte v souboru skriptu prostředí PowerShell. Konfigurace sítě uložte do souboru s názvem "NetworkConf2.xml".
Podle potřeby upravte proměnné definované uživatelem. Spusťte skript a potom postupujte podle pokynů nastavení pravidla brány Firewall výše.

#### <a name="full-script"></a>Úplná skript
Tento skript bude založené na proměnné definované uživatelem:

1. Připojení k předplatnému Azure
2. Vytvoření nového účtu úložiště
3. Vytvořit novou virtuální síť a tři podsítě, jak jsou definovány v souboru konfigurace sítě
4. Sestavení pět virtuálních počítačů; brány firewall 1 a 4 windows server virtuálních počítačů
5. Konfigurace, včetně směrování definovaného uživatelem:
   1. Vytváří se dvě nové směrovací tabulky
   2. Přidání tras do tabulek
   3. Vytvoření vazby tabulky do příslušné podsítě
6. Povolit předávání IP na síťové virtuální zařízení
7. Konfigurace, včetně skupiny zabezpečení sítě:
   1. Vytvoření NSG
   2. Přidání pravidla
   3. Vytvoření vazby skupiny zabezpečení sítě na příslušné podsítě

Tento skript Powershellu je vhodné spustit místně na, že připojení Internetu, počítač nebo server.

> [!IMPORTANT]
> Když se skript spouští, může být upozornění nebo ostatní informační zprávy, které se objeví v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou znepokojení.
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
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
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
Uložte tento soubor xml s aktualizované umístění a přidání odkazu do tohoto souboru $NetworkConfigFile proměnné ve skriptu výše.

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

#### <a name="sample-application-scripts"></a>Ukázky skriptů aplikace
Pokud chcete nainstalovat ukázkovou aplikaci pro tuto a další příklady hraniční sítě, jednu byl poskytnut na následující odkaz: [Ukázkový skript aplikace][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Obousměrné DMZ pomocí síťového virtuálního zařízení, skupiny zabezpečení sítě a směrování definovaného uživatelem"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logické zobrazení pravidel brány Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Vytvoření front-endové síti objektu"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Vytvoření objektu serveru DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopii výchozí pravidlo protokolu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Pravidlo AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ikona aplikace pro přesměrování"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Určení ikony NAT"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ikona pass"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Pravidlo brány firewall správy"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Pravidlo brány firewall protokolu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Pravidlo brány firewall na Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Pravidlo brány firewall AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Odchozí pravidla brány firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Pravidlo brány firewall na DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Pravidlo brány firewall uvnitř virtuální sítě"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Pravidlo brány firewall Odepřít"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktivace pravidla brány firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
