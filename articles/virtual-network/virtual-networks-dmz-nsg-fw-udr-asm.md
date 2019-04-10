---
title: Příklad hraniční sítě – ochrana sítě se hraniční síti, který se skládá z brány firewall, směrování definovaného uživatelem a skupin zabezpečení sítě | Dokumentace Microsoftu
description: Vytvářejte hraniční síti (označované také jako DMZ) s bránou firewall, uživatelem definované směrování (UDR) a skupiny zabezpečení sítě (Nsg).
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
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426524"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Příklad 3: Vytvořit hraniční síť k ochraně sítě s bránou firewall, směrování definovaného uživatelem a skupin zabezpečení sítě

[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

V tomto příkladu vytvoříte hraniční síti (označované také jako DMZ, demilitarizovaná zóna a monitorovaná podsíť). V příkladu implementuje bránu firewall, čtyři servery Windows, směrování definovaného uživatelem (UDR), předávání IP a skupiny zabezpečení sítě (Nsg). Tento článek vás provede všechny relevantní příkazy zajistit lepší představu o jednotlivých kroků. Scénář část provozu také vysvětluje do hloubky, jak pokračuje provoz přes vrstev obrany v hraniční síti. A konečně odkazy na oddíl obsahuje kód a pokyny pro toto prostředí sestavení, takže můžete otestovat a experimentovat s různými scénáři.

![Obousměrné hraniční sítě pomocí síťového virtuálního zařízení, skupiny zabezpečení sítě a směrování definovaného uživatelem][1]

## <a name="environment-setup"></a>Nastavení prostředí

Tento příklad používá předplatné, které obsahuje následující součásti:

* Tři cloudové služby: SecSvc001 FrontEnd001 a BackEnd001
* Virtuální síť (CorpNetwork) se třemi podsítěmi: SecNet front-endu a back-endu
* Síťové virtuální zařízení: Brána firewall připojený k podsíti SecNet
* Windows server, který představuje server webových aplikací: IIS01
* Dva servery Windows, které představují aplikace back-end serverů: AppVM01 AppVM02
* Windows server, který představuje DNS server: DNS01

[Odkazuje na část](#references) obsahuje Powershellový skript, který vytváří většinu prostředí, je zde popsáno. Tento článek není jinak poskytují podrobné pokyny pro vytváření virtuálních sítí a virtuální počítače (VM).

Sestavení prostředí:

1. Uložte soubor XML konfigurace síťových součástí [části](#references). Budete muset aktualizovat s názvy, umístění a IP adresy, které odpovídají situaci.
1. Aktualizujte uživatelské proměnné ve skriptu úplné tak, aby odpovídaly konkrétní prostředí (například předplatná, názvy služeb a tak dále).
1. Spusťte skript prostředí PowerShell.

> [!NOTE]
> Oblast zadali ve skriptu prostředí PowerShell musí odpovídat oblasti zadaný v souboru XML konfigurace sítě.

Po úspěšném spuštění skriptu, proveďte následující kroky:

1. Nastavení pravidel brány firewall. Zobrazit [pravidla brány firewall](#firewall-rules) oddílu.
1. Volitelně použijte dva skripty v části odkazy na nastavení webové aplikace na webovém serveru a aplikačního serveru umožňující testování této konfigurace DMZ.

## <a name="user-defined-routing"></a>Uživatelem definované směrování

Ve výchozím nastavení následující systémové trasy, které jsou definovány jako:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal je vždy předpony adres definované pro tuto konkrétní virtuální síť. Například se změní z virtuální sítě k virtuální síti v závislosti na tom, jak definuje každá konkrétní virtuální síť. Zbývající systémové trasy, které jsou statické a výchozí jak je znázorněno.

Jako u Priorita trasy zpracovávají prostřednictvím metody nejdelší shody předpony (LPM). Proto nejspecifičtější trasy v tabulce platí pro danou cílovou adresu.

Proto se provoz určený pro server jako DNS01 (10.0.2.4) na místní sítě (10.0.0.0/16) prochází přes virtuální síť kvůli 10.0.0.0/16 trasy.  Pro 10.0.2.4 je trasa 10.0.0.0/16 nejspecifičtější trasy. Toto pravidlo aplikuje, i když 10.0.0.0/8 a 0.0.0.0/0 může být také použít. Jsou specifické pro less, ale tak, že nemají vliv na provoz. Provoz do 10.0.2.4 má místní virtuální sítě jako jeho další segment směrování tak se směruje do cíle.

Například 10.0.0.0/16 trasy neplatí pro provoz, který je určený pro 10.1.1.1. Systémová trasa 10.0.0.0/8 není nejvíce specifické, tak, aby zahodí přenos, nebo "black dírkového" protože další segment směrování je Null.

Pokud cíl neplatí pro některý z předpony hodnotu Null nebo předpony VNETLocal, provoz následuje nejméně specifickými trasy (0.0.0.0/0). Přesměruje ho navýšení kapacity na Internetu jako další segment směrování a mimo Azure pro internet edge.

Pokud existují dvě stejné předpony ve směrovací tabulce, pořadí podle priority podle postupu zdrojový atribut:

1. VirtualAppliance: Uživatelem definované trasy ručně přidat do tabulky.
1. VPNGateway: Dynamické směrování (BGP při použití s hybridní sítě) se přidal protokol dynamické sítě. Tyto trasy může postupem času změnit jako protokol dynamické automaticky odráží změny v síti s navázaným partnerským vztahem.
1. Výchozí: Systémové trasy, místní virtuální sítě a statické položky, jak je vidět ve směrovací tabulce výše.

> [!NOTE]
> Uživatelem definované směrování (UDR) můžete nyní použít ExpressRoute a VPN Gateway pro vynucení provozu odchozí a příchozí místy bude směrovat na virtuální síťové zařízení (NVA).

### <a name="create-local-routes"></a>Vytvořit místní trasy

Tento příklad používá dva směrovacích tabulek, jednu pro front-end a back endové podsítě. Každá tabulka je načtena statické trasy, které jsou vhodné pro dané podsíti. Pro účely tohoto příkladu má každá tabulka tří způsobů:

1. Provozu místních podsítí s žádné další segment směrování definované. Tato trasa umožňuje provozu místních podsítí obejít bránu firewall.
2. Virtuální síťový provoz s dalším segmentem směrování definované jako brána firewall. Tato trasa přepisuje výchozí pravidlo, které umožňuje místní přenosy virtuální sítě pro směrování přímo.
3. Všechny zbývající provoz (0/0) s dalším segmentem směrování definované jako brána firewall.

Po vytvoření směrovací tabulky jsou vázány na podsítě. Front-endové podsítě směrovací tabulky by měl vypadat:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Tento příklad používá následující příkazy k vytvoření směrovací tabulky, přidejte trasy definované uživatelem a svážou směrovací tabulky k podsíti. Položky, které začínají `$`, jako například `$BESubnet`, jsou uživatelem definované proměnné ze skriptu v referenční části.

1. Nejprve vytvořte základní směrovací tabulky. Následující fragment kódu vytvoří tabulku pro back endové podsítě. Úplná skript také vytvoří odpovídající tabulku pro front-endové podsítě.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Po vytvoření směrovací tabulky, můžete přidat konkrétní trasy definované uživatelem. Následující fragment kódu určuje, že veškerý provoz (0.0.0.0/0) prochází přes virtuální zařízení. Proměnná `$VMIP[0]` slouží k předávání IP adresu, přiřadit při vytváření virtuálního zařízení dříve ve skriptu. Úplná skript také vytvoří odpovídající pravidlo v tabulce front-endu.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Předchozí položka trasa přepisuje výchozí trasu "0.0.0.0/0", ale výchozí pravidlo 10.0.0.0/16 stále umožňuje provoz v rámci virtuální sítě pro směrování přímo do cíle a ne do síťového virtuálního zařízení. Chcete-li toto chování, budete muset přidat následující pravidla:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. V tomto okamžiku je nutné provést výběr. Dvě předchozí pravidla směrování veškerého provozu do brány firewall pro posouzení, včetně provozu v rámci jedné podsítě. Toto chování může být vhodné. Pokud to neuděláte, ale můžete povolit provoz v rámci podsítě pro směrování místně bez zapojení brány firewall. Přidat třetí, příslušné pravidlo, které přímo směruje libovolnou adresu určený pro místní podsítě (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Nakonec po do směrovací tabulky je vytvořena a naplněna trasy definované uživatelem, budete muset vytvořit vazbu tabulky k podsíti. Následující fragment kódu vytvoří vazbu v tabulce pro podsíť back-end. Úplná skript také sváže front-endu směrovací tabulku s front-endové podsítě.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Předávání IP

Předávání IP je funkce, která doprovodná uživatelem definovaná TRASA. Toto nastavení na virtuální zařízení umožňuje přijímání dat adresovaných není na zařízení a potom je předejte tento provoz do konečného ultimate.

Pokud přenos z AppVM01 odešle požadavek na DNS01 server, směrování definovaného uživatelem směruje provoz do brány firewall. Povolené předávání IP adres provoz s cílem DNS01 (10.0.2.4) se přijal zařízení brány firewall (10.0.0.4) a pak se předávají do konečného ultimate (10.0.2.4). Bez předávání IP na bránu firewall povolená, provoz neakceptuje zařízení i v případě, že směrovací tabulka má brána firewall jako další segment směrování.

> [!IMPORTANT]
> Nezapomeňte povolit předávání IP v kombinaci s uživatelsky definované směrování.

Při vytváření virtuálního počítače můžete pomocí jediného příkazu povolené předávání IP. Volání instance virtuálního počítače, které je vaše virtuální zařízení brány firewall a povolení předávání IP. Pamatujte, že položky červenou barvou, které začínají `$`, třeba `$VMName[0]`, jsou uživatelem definované proměnné ze skriptu v referenční části tohoto dokumentu. Nula v závorkách, `[0]`, představuje první virtuální počítač v poli virtuálních počítačů. Ukázkový skript fungovat bez úprav musí být prvním virtuálním počítači (VM 0) brána firewall. Úplná skript je fragment kódu odpovídající kód seskupený pomocí příkazů uživatelem definovaná TRASA poblíž konce.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

V tomto příkladu můžete vytvářet skupiny zabezpečení sítě (NSG) a pak ji zavede s jedním pravidlem. Příklad poté vytvoří vazbu NSG pouze podsítě front-endu a back-end (ne SecNet). Pravidlo, které můžete načíst do skupiny zabezpečení sítě je následujícím způsobem:

* Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (všechny podsítě) byl odepřen.

I když v tomto příkladu se používají skupiny zabezpečení sítě, jejich hlavním účelem je jako sekundární vrstvy ochrany proti chybě ruční konfigurace. Chcete blokovat veškerý příchozí provoz z Internetu do části front end a back endové podsítě. Provoz by měl směrovat jenom prostřednictvím SecNet podsíť pro bránu firewall, po jejímž uplynutí by měla získat pouze odpovídající provoz směruje do části front end a back endové podsítě. Kromě toho pravidla směrování definovaného uživatelem směrovat veškerý provoz, kterou půjde používat části front end a back endové podsítě do brány firewall. Brána firewall vnímá jako asymetrický toku a zahodí odchozí provoz.

Tři vrstvy zabezpečení chránit front-endovými a back endové podsítě:

1. Žádné otevřené koncové body na FrontEnd001 a BackEnd001 cloudové služby
1. Skupiny Nsg zakážou provoz z Internetu
1. Zahodí asymetrického provozu

Zajímavé bod týkající se skupiny zabezpečení sítě v tomto příkladu je, aby obsahoval pouze jedno pravidlo je uvedeno níže. Toto pravidlo blokuje přenosy z Internetu do celé virtuální sítě, včetně zabezpečení podsítě.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Vzhledem k tomu, že skupina zabezpečení sítě je vázaný jenom na front-endovými a back endové podsítě, pravidlo neplatí pro podsítě zabezpečení příchozí provoz. V důsledku toho proudí do podsítě zabezpečení.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Pravidla brány firewall

Je třeba vytvořit pravidla pro předávání v bráně firewall. Vzhledem k tomu, že brána firewall blokuje nebo veškerý provoz příchozích, odchozích a intra virtual network, je třeba mnoho pravidel brány firewall. Brána firewall má také zpracovat veškerý příchozí provoz na veřejnou IP adresu služby zabezpečení (na jiném portu). Abyste nemuseli všechno předělávat později, dodržíte osvědčený postup pomocí diagramů logické toky před nastavením podsítě a pravidla brány firewall. Na následujícím obrázku je logické zobrazení pravidla brány firewall v tomto příkladu:

![Logické zobrazení pravidel brány Firewall][2]

> [!NOTE]
> Porty pro správu se liší v závislosti na síťové virtuální zařízení. Tento příklad ukazuje Barracuda NextGen Firewall, která používá porty 22, 801 a 807. Najdete v dokumentaci od výrobce zařízení najít přesnou porty pro správu zařízení.

### <a name="logical-rule-description"></a>Popis logické pravidla

Výše uvedeném logickém diagramu nezobrazí zabezpečení podsítě, protože brána firewall je jediný zdroj v příslušné podsíti. Tento diagram znázorňuje pravidla brány firewall, jak jsou logicky povolují nebo odpírají přenosové toky, ale nikoli skutečné směrovat cestu. Vyšší rozsahové porty (8014 – 8026), aby byly čitelnější, aby bylo v souladu s poslední dva oktety místních IP adres zvolené jsou také externích portů vybraná pro přenosy protokolu vzdálené plochy (RDP). Adresa serveru místní 10.0.1.4 například souvisí s externím portu 8014. Můžete ale použít jakýkoli nekonfliktní vyšší.

V tomto příkladu potřebujete následující typy pravidel:

* Externí pravidla pro příchozí provoz:
  1. Pravidlo brány firewall správy: povoluje přenos dat k portům pro správu síťového virtuálního zařízení.
  2. Pravidla protokolu RDP pro každý server systému windows: umožňuje správu jednotlivé servery pomocí protokolu RDP.  V závislosti na možnosti síťového virtuálního zařízení je možné seskupit do jednoho pravidla.
  3. Pravidla pro provoz aplikace: jednu pro front-end webového provozu a jeden pro provoz back-end (například webový server na datové vrstvě). Konfigurace pravidla závisí na síťové architektury a provoz toky.

     * První pravidlo povoluje provoz aplikace skutečný přístup k serveru aplikace. Na rozdíl od pravidla pro zabezpečení, správy a tak dále pravidla aplikací povolit externí uživatele nebo služby pro přístup k aplikace. V tomto příkladu má jeden webový server na portu 80, což umožňuje jediné aplikace pravidlo firewallu pro přesměrování přenosu dat, který je určený pro externí IP adresu místo něj směrovat na webový server interní IP adresu. Relace přesměrované přenosy při přemapování při překladu síťových adres do interního serveru.
     * Druhé pravidlo pro provoz aplikací je back endové pravidlo pro povolení webového serveru pro směrování provozu na AppVM01 server, ale ne k serveru AppVM02 použít libovolný port.

* Vnitřní pravidla pro provoz intra virtual network:
  1. Odchozí internetové pravidlo: umožňuje přenos z libovolné sítě k předání do vybraných sítí. Toto pravidlo je obvykle výchozí bránu firewall, ale v zakázaném stavu. Povolte toto pravidlo pro účely tohoto příkladu.
  2. Pravidlo DNS: povoluje pouze provoz DNS (port 53) k předání do serveru DNS. Pro toto prostředí je blokován většiny provozu z front-endu do back-endu. Toto pravidlo umožňuje konkrétně DNS z jakékoli místní podsítě.
  3. Podsítě pro podsíť pravidlo: Povolí jakýkoli server v back endové podsítě pro připojení k libovolnému serveru na front-endové podsítě, ale ne opačně.

* Odolné vůči selhání pravidlo pro provoz, který nesplňuje některé z výše uvedených kritérií:
  1. Všechny přenosy pravidlo odepřít: vždy konečné pravidlo z hlediska priority. Pokud se shodují s některým z předchozích pravidel se nepovede tok přenosů, toto pravidlo blokuje se. Je výchozí pravidlo. Vzhledem k tomu obvykle dojde, nejsou potřeba žádné úpravy.

> [!TIP]
> V druhé pravidlo provozu aplikace a smí jakýkoli port pro zjednodušení tento příklad. V reálné situaci by měl použít konkrétní port a rozsahy adres pro omezení možností útoku tohoto pravidla.


> [!IMPORTANT]
> Po vytvoření pravidla, je důležité, abyste si přečetli prioritu každé pravidlo k zajištění, že provoz je povolený nebo zakázaný podle potřeby. V tomto příkladu jsou pravidla v pořadí podle priority. Je snadné nezůstanete zamknutí mimo bránu firewall, pokud jsou správně seřazený pravidla. Nezapomeňte nastavit pravidlo brány firewall správy jako absolutní nejvyšší prioritou.

### <a name="rule-prerequisites"></a>Pravidla požadavků

Veřejné koncové body jsou požadovány pro virtuální počítač s bránu firewall. Tyto veřejné koncové body musí být otevřený, aby brána firewall může zpracovat provoz. Existují tři typy provozu v tomto příkladu:

1. Provoz správy řídit brány firewall a pravidla brány firewall
1. Provoz protokolu RDP k řízení serverů windows
1. Provoz aplikací

V horní polovině brány firewall pravidla výše uvedeném logickém diagramu se zobrazí typy přenosů.

> [!IMPORTANT]
> Nezapomeňte, že *všechny* datové přenosy přicházejí firewallem. K připojení k vzdálené plochy serveru IIS01 budete muset připojit do brány firewall na portu 8014 a následné povolení brány firewall pro směrování požadavku protokolu RDP interně k portu IIS01 RDP. Azure portal **připojit** tlačítko nebude fungovat, protože neexistuje žádné přímé cestu protokolu RDP k IIS01 nejdál, co můžete vidět na portálu. Zabezpečení a port (například secscv001.cloudapp.net:xxxx) jsou všechna připojení z Internetu. Odkaz na výše uvedené diagram mapování portů externí a interní IP adresy a portu.

Koncový bod můžete otevřít v době vytvoření virtuálního počítače nebo po sestavení. Ukázkový skript a následující fragment kódu otevřete koncový bod po vytvoření virtuálního počítače.

Uvědomte si, že položek, které začínají řetězcem `$`, jako například `$VMName[$i]`, jsou uživatelem definované proměnné ze skriptu v referenční části. `[$i]` Představuje číslo pole konkrétního virtuálního počítače v poli virtuálních počítačů.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

I když ho je zobrazený není jasně zde kvůli proměnné, byste měli otvírat jenom koncové body v cloudové službě zabezpečení. Toto opatření pomáhá zajistit, že brána firewall zpracovává veškerý příchozí provoz, ať už má směrovat, přemapování při překladu síťových adres nebo vyřadit.

Instalace správy klienta na počítači pro správu brány firewall a vytvořte nezbytné konfigurace. Podrobnosti o tom, jak spravovat bránu firewall nebo jiné síťové virtuální zařízení najdete v dokumentaci od výrobce. Zbytek tohoto oddílu i na **vytváření pravidel brány Firewall** části popisují konfiguraci brány firewall. Pomocí klienta správy od dodavatele, není webu Azure portal nebo Powershellu.

Přejděte na [Barracuda NG správce](https://techlib.barracuda.com/NG61/NGAdmin) stáhnout klienta pro správu a zjistěte, jak se připojit k bráně firewall Barracuda.

Poté, co jste přihlášeni bránu firewall, definujte objekty sítě a služby před vytvořením pravidel brány firewall. Tyto dvě třídy požadovaný objekt může být vytvoření pravidel jednodušší.

V tomto příkladu definujte pro všechny tři objekty pojmenované sítě:

* Front-endové podsítě
* Back endové podsítě
* IP adresa serveru DNS

Chcete-li vytvořit síť s názvem na řídicím panelu Správce NG Barracuda klienta:

1. Přejděte **karta konfigurace**.
1. Vyberte **Ruleset** v **provozní konfiguraci** oddílu
1. Vyberte **sítě** pod **objekty brány Firewall** nabídky.
1. Vyberte **nový** v **upravit sítě** nabídky.
1. Vytvoření objektu sítě tak, že přidáte název a předpona:

   ![Vytvoření front-ed síťového objektu][3]

V předchozích krocích vytvořit síť s názvem pro front-endové podsítě. Vytvoří objekt podobné pro back endové podsítě. Nyní podsítě můžete snadněji odkazovat podle názvu v pravidlech brány firewall.

Pro objekt server DNS:

![Vytvoření objektu serveru DNS][4]

Tento jeden odkaz na IP adresy se používá v pravidlu DNS později v dokumentu.

Další objekt třídy obsahuje objekty služby, které představují porty pro připojení RDP pro každý server. Existující objekt služby RDP je vázán na výchozí port RDP 3389. Proto můžete vytvářet nové služby umožňující provoz z externích portů (8014 8026). Můžete také přidat nové porty na existující službu protokolu RDP. Z důvodu snadnějšího ukázku, však může provádět jednotlivých pravidel pro každý server. Chcete-li vytvořit nové pravidlo protokolu RDP pro server na řídicím panelu Správce NG Barracuda klienta:

1. Přejděte **karta konfigurace**.
1. Vyberte **Ruleset** v **provozní konfiguraci** oddílu.
1. Vyberte **služby** pod **objekty brány Firewall** nabídky.
1. Přejděte dolů v seznamu služeb a vyberte **RDP**.
1. Klikněte pravým tlačítkem na Kopírovat, klikněte pravým tlačítkem myši a zvolte Vložit.
1. Je teď RDP Copy1 objekt služby, které lze upravovat. Klikněte pravým tlačítkem na **RDP Copy1** a vyberte **upravit**.
1. **Upravit objekt služby** zobrazí okno až, jak je znázorněno zde:

   ![Kopii výchozí pravidlo protokolu RDP][5]

1. Upravte hodnoty tak, aby představují službu protokolu RDP pro konkrétní server. Pro AppVM01, výchozí pravidlo protokolu RDP by měl být upraven tak, aby odrážely novou službu **název**, **popis**a externí Port RDP v diagramu obrázek 8. Uvědomte si, že porty jsou změnit z výchozích RDP 3389 pro externí port pro tento konkrétní server. Například je externí port pro AppVM01 8025. Pravidlo upravené služby je znázorněna zde:

   ![Pravidlo AppVM01][6]

Opakujte tento postup k vytvoření služby protokolu RDP pro ostatní servery: AppVM02 DNS01 a IIS01. Tyto služby zjednodušit pravidla v další části, chcete-li vytvořit a zřetelnější.

> [!NOTE]
> Službě protokolu RDP pro bránu firewall není nutné, protože brána firewall virtuálního počítače je linuxových imagí, takže SSH se používá na portu 22 pro správu virtuálních počítačů místo protokolu RDP. Také port 22 a dvě další porty jsou povolené pro připojení pro správu. Zobrazit **pravidlo brány Firewall správy** v další části.

### <a name="firewall-rules-creation"></a>Vytváření pravidel brány firewall

Existují tři typy pravidel brány firewall použitý v tomto příkladu, vše odlišné ikony:

Pravidlo pro přesměrování aplikace: ![Ikona aplikace pro přesměrování][7]

Cílové pravidlo překladu adres: ![Určení ikony NAT][8]

Pravidlo pass: ![Ikona pass][9]

Další informace o těchto pravidlech najdete na webu Barracuda.

K vytvoření následujících pravidel nebo ověřit existující výchozí pravidla:

1. Z řídicího panelu Barracuda NG správce klienta, přejděte **konfigurace** kartu.
1. V **provozní konfiguraci** vyberte **Ruleset**.
1. **Hlavní pravidla** mřížky se zobrazí existující aktivní a deaktivovat pravidla pro tuto bránu firewall. Vyberte zelené **+** v pravém horním rohu a vytvořte nové pravidlo. Pokud vaše brána firewall je uzamčen pro změny, zobrazí se tlačítko označené **Zámek** a nejde vytvořit nebo upravit pravidla. Vyberte **Zámek** tlačítko k odemknutí sady pravidel a povolit úpravy. Klikněte pravým tlačítkem na pravidla, které chcete upravit a vyberte **upravit pravidlo**.

Po vytvoření nebo úpravě všechna pravidla, push do brány firewall a jejich aktivaci. V opačném případě pravidlo změny se projeví. Nabízená a aktivační proces je popsán v [pravidlo aktivace](#rule-activation).

Tady jsou podrobnosti každé pravidlo předpokladem pro dokončení v tomto příkladu:

* **Pravidlo brány firewall správy**: Toto pravidlo přesměrování aplikace umožňuje provoz procházel k portům pro správu síťové virtuální zařízení, v tomto příkladu Barracuda NextGen Firewall. Porty pro správu jsou 801 807 a volitelně 22. Externí a interní porty jsou stejné bez překladu portu. Toto pravidlo se nazývá nastavení-MGMT-přístupu. Výchozí pravidlo a povolena ve výchozím nastavení Barracuda NextGen Firewall verze 6.1.
  
    ![Pravidlo brány firewall správy][10]

  > [!TIP]
  > Adresní prostor zdroje v tomto pravidle je **jakékoli**. Pokud jsou známé rozsahy správy IP adres, snižuje tento obor také snižuje požadavek pro možný útok k portům pro správu.

* **Pravidla protokolu RDP**:  Tato pravidla NAT cílové povolit správu jednotlivých serverů pomocí protokolu RDP. Kritické pole pro tato pravidla jsou:
  * Zdroj. Chcete-li povolit RDP z libovolného místa, použijte odkaz **jakékoli** ve zdrojovém poli.
  * Služba. Pomocí objektu služby protokolu RDP, který jste vytvořili dříve: **AppVM01 RDP**. Externí porty přesměrovat místní IP adresa serveru a na výchozí port RDP 3386. Toto zvláštní pravidlo je pro přístup protokolu RDP k AppVM01.
  * Cíl. Použijte místní port v bráně firewall: **Místní IP DHCP 1** nebo **eth0** Pokud používáte statické IP adresy. Ordinální číslo (eth0 eth1 a tak dále), mohou lišit, pokud vaše síťové zařízení má několik místní rozhraní. Brána firewall používá tento port pro odesílání a může být stejný jako přijímající port. Skutečné směrované cíl je ve **cílového seznamu** pole.
  * Přesměrování. Konfigurace virtuálního zařízení zjistit, kde nakonec přesměrovat provoz. Nejjednodušší přesměrování je umístit IP adresu do pole cílového seznamu. Můžete zadat také port, a překladu adres bude přesměrována portu a IP adresu. Pokud port neurčíte, virtuální zařízení používá cílový port pro příchozí požadavek.

    ![Pravidlo brány firewall protokolu RDP][11]

    Vytvořte čtyři pravidla protokolu RDP:

    | Název pravidla | Server | Služba | Seznam cílů |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 protokolu RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Zúžení rozsah pole zdroje a služby snižuje možný útok. Použijte nejvíc omezenou obor, který umožňuje funkce.

* **Pravidla pro provoz aplikace**: Existují dvě pravidla pro provoz aplikace. Jedna je front-endový webový provoz. Druhá zahrnuje provoz back-end jako webový server datové vrstvy. Tato pravidla jsou závislé na síťové architektury a provozu toky.
  
  * Front-endové pravidlo pro webový provoz:
  
    ![Pravidlo brány firewall na Web][12]
  
    Toto pravidlo překladu adres cílové umožňuje přenos aplikace skutečný kontaktovat server aplikace. Na rozdíl od pravidel pro zabezpečení, řízení a etcetera pravidla aplikací povolit externí uživatele nebo služby pro přístup k aplikace. V tomto příkladu má jeden webový server na portu 80, což umožňuje jediné aplikace pravidlo firewallu pro přesměrování přenosu dat, který je určený pro externí IP adresu místo něj směrovat na webový server interní IP adresu. Relace přesměrované přenosy při přemapování při překladu síťových adres do interního serveru.

    > [!NOTE]
    > Neexistuje žádný port přiřadit **cílového seznamu** pole. Díky tomu se příchozí port 80 (nebo 443 pro vybrané služby) se používá v přesměrování webového serveru. Pokud webový server naslouchá na jiném portu, jako je 8080, můžete aktualizovat pole cílového seznamu k 10.0.1.4:8080 umožňující také přesměrování portu.
  
  * Back endové pravidlo umožňuje webovému serveru ke komunikaci s AppVM01 serveru, ale ne AppVM02 prostřednictvím **jakékoli** služby:
  
    ![Pravidlo brány firewall AppVM01][13]
  
    Toto pravidlo pass umožňuje jakýkoli server služby IIS na front-endovou podsíť k dosažení AppVM01 (10.0.2.5) přes libovolný port, tak, aby data přístupná webové aplikace pomocí libovolného protokolu.
  
    Na tomto snímku obrazovky `<explicit-dest>` je používán **cílové** pole místo 10.0.2.5 jako cíl. Můžete zadat IP adresu explicitně, jak je znázorněno na snímku obrazovky. Můžete také použít pojmenovaný objekt sítě stejně jako v rámci požadavků pro DNS server. Správce brány firewall může rozhodnout jakou metodu použít. Chcete-li přidat 10.0.2.5 jako explicitní určení, dvakrát klikněte na první prázdný řádek pod `<explicit-dest>` a zadejte adresu v dialogovém okně, které se otevře.
  
    S tímto pravidlem pass bez překladu adres je potřeba, protože zpracovává vnitřní provoz. Můžete nastavit **metodu připojení** k `No SNAT`.
  
    > [!NOTE]
    > Zdrojová síť v tomto pravidle je prostředek na front-endové podsítě, pokud existuje jenom jeden. Pokud vaše Architektura určuje známé počet webových serverů, můžete vytvořit prostředek objektu sítě na konkrétnější na těchto přesné IP adresy místo celé front-endové podsítě.

    > [!TIP]
    > Toto pravidlo používá službu **jakékoli** usnadnit ukázkovou aplikaci nastavit a používat. To umožňuje ICMPv4 (ping) v jediné pravidlo. Chcete-li omezit možnosti útoku surface hranice, doporučujeme však omezení porty a protokoly služeb na minimum, je to možné, díky kterým můžou aplikace operace.

    > [!TIP]
    > I když se používá tento příklad pravidla `<explicit-dest>` odkaz, byste měli použít konzistentní přístup v rámci konfigurace brány firewall. Je doporučeno použití pojmenovaný objekt sítě pro snazší čitelnost a podpoře. `<explicit-dest>` Zobrazuje, tady je jenom zobrazíte alternativní referenční metody. Není doporučeno obecně, zejména pro složité konfigurace.

* **Odchozí internetové pravidlo**: Toto pravidlo passu umožňuje přenosy z jakékoli zdrojové síti k předání do vybrané cílové sítě. Barracuda NextGen firewall toto pravidlo má obvykle "on", ve výchozím nastavení, ale v zakázaném stavu. Klikněte pravým tlačítkem na toto pravidlo pro přístup k **aktivovat pravidlo** příkazu. Upravte pravidlo ukazuje snímek obrazovky přidání objektů sítě pro back-end a front-endové podsítě ke zdrojovému atributu tohoto pravidla. Tyto objekty sítě jste vytvořili v části požadavků tohoto článku.
  
    ![Odchozí pravidla brány firewall][14]

* **Pravidlo DNS**: Toto pravidlo pass umožní provoz jenom DNS (port 53) k předání do serveru DNS. Pro toto prostředí většina provoz z front-endu do back-endu blokovaný, tak tato pravidla umožňují provoz DNS.
  
    ![Pravidlo brány firewall na DNS][15]
  
    > [!NOTE]
    > **Metodu připojení** je nastavena na `No SNAT` vzhledem k tomu, že toto pravidlo je pro interní IP adresu do interního provozu IP adres a žádné přesměrování prostřednictvím překladu adres je povinné.

* **Podsítě pro podsíť pravidlo**: Toto výchozí pravidlo pass byla aktivována a upravit tak, aby povolit všechny servery v back endové podsítě pro připojení k libovolnému serveru na front-endové podsítě. Toto pravidlo pouze coves interního provozu proto **metodu připojení** může být nastaven na `No SNAT`.

    ![Pravidlo brány firewall uvnitř virtuální sítě][16]
  
    > [!NOTE]
    > **Obousměrné** tak toto pravidlo platí pouze v jednom směru není zde zaškrtnuté políčko. Připojení lze inicializovat z back endové podsítě front-endovou síť, ale ne opačně. Pokud toto políčko byly vybrány, by toto pravidlo povolit obousměrný provoz, který jsme jste zadali jako nežádoucí v našich Logický diagram.

* **Všechny přenosy pravidlo Odepřít**: Toto pravidlo musí být vždy konečné pravidlo z hlediska priority. Pokud tok provozu neodpovídá žádné z předchozích pravidel, tohoto pravidla způsobí, že ho chcete vyřadit. Toto pravidlo je často aktivovaná ve výchozím nastavení, takže nejsou potřeba žádné úpravy.
  
    ![Pravidlo brány firewall Odepřít][17]

> [!IMPORTANT]
> Po vytvoření všech předchozích pravidel zkontrolujte prioritu každé pravidlo, aby provoz je povolený nebo zakázaný podle potřeby. V tomto příkladu pravidla jsou uvedeny v pořadí, ve kterém by se zobrazit v hlavní mřížky Barracuda správy klienta z pravidla předávání.

## <a name="rule-activation"></a>Pravidlo aktivace

Poté, co jste změnili sadu pravidel, která splňují požadavky diagram logiky, budete muset nahrát sady pravidel brány firewall a pak sadu aktivujte.

![Aktivace pravidla brány firewall][18]

Podívejte se v pravém horním rohu okna správy klienta a vyberte **odeslat změny** nahrát upravené pravidla brány firewall. Vyberte **aktivovat**.

Po aktivaci sady pravidel brány firewall na tomto ukázkovém prostředí je dokončena.

## <a name="traffic-scenarios"></a>Provoz scénáře

> [!IMPORTANT]
> Nezapomeňte, že *všechny* datové přenosy přicházejí firewallem. K připojení k vzdálené plochy serveru IIS01 budete muset připojit do brány firewall na portu 8014 a následné povolení brány firewall pro směrování požadavku protokolu RDP interně k portu IIS01 RDP. Azure portal **připojit** tlačítko nebude fungovat, protože neexistuje žádné přímé cestu protokolu RDP k IIS01 nejdál, co můžete vidět na portálu. Zabezpečení a port (například secscv001.cloudapp.net:xxxx) jsou všechna připojení z Internetu. Odkaz na výše uvedené diagram mapování portů externí a interní IP adresy a portu.

Pro tyto scénáře následující pravidla brány firewall by měla:

1. Správa brány firewall (FW Mgmt)
2. Připojení RDP k IIS01
3. Připojení RDP k DNS01
4. Připojení RDP k AppVM01
5. Připojení RDP k AppVM02
6. Provoz aplikací na webu
7. Provoz aplikací na AppVM01
8. Odchozí do Internetu
9. Front-endu DNS01
10. Provoz uvnitř podsítě (back-end k front-endu jenom)
11. Zamítnout vše

Vaše sada pravidel brány firewall na skutečné bude pravděpodobně vyžadovat další pravidla od těch v tomto příkladu. Jsou to pravděpodobně mají také různé priority čísla. By měla odkazovat na tomto seznamu a přidružená čísla pro jejich relativní priority k sobě navzájem. Například "RDP k IIS01" pravidlo může být pravidlo číslo 5 na skutečné bránu firewall, ale pokud jako jeho níže "brány Firewall pro správu" a výše "RDP k DNS01" pravidlo, sada zarovná tak tento seznam. Tento seznam také pomáhá zjednodušit pokyny pro scénáře, které následují. Například "pravidlo brány Firewall 9 (DNS)." Mějte na paměti, že čtyři pravidla protokolu RDP se společně nazývají "pravidla protokolu RDP" Při scénáři provoz nijak s RDP.

Také Připomínáme, že skupiny zabezpečení sítě (Nsg) se pro příchozí přenosy z Internetu na front-endovými a back endové podsítě.

### <a name="allowed-internet-to-web-server"></a>(Povolena) Internet na webový server

1. Uživatel s internet stránka HTTP žádosti z SecSvc001.CloudApp.Net (přístupem k Internetu cloudové služby).
1. Cloudové služby předává do rozhraní brány firewall na 10.0.0.4:80 provoz přes otevřených koncových bodů na portu 80.
1. Žádnou skupinu zabezpečení sítě je přiřazen k zabezpečení podsítě, takže pravidla NSG systému povolení provozu do brány firewall.
1. Provoz narazí na vnitřní IP adresu brány firewall (10.0.1.4).
1. Brána firewall provádí zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidla brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidlo brány firewall 6 (aplikace: Vztahuje se web). Provoz je povolený. Brána firewall přesměrovává přenosy prostřednictvím překladu adres do 10.0.1.4 (IIS01).
1. Front-endové podsítě provádí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Brána firewall přesměrovány tento provoz prostřednictvím překladu adres, proto teď zdrojovou adresu brány firewall. Vzhledem k tomu, že brána firewall je v podsíti zabezpečení a zobrazí se jako místní provoz do front-endové podsítě skupiny zabezpečení sítě, provoz je povolený. Přesunout na další pravidla.
   1. Výchozí pravidla NSG povolit podsítě pro podsíť provozu tak, že tento provoz je povolený. Zastavte zpracování pravidel NSG.
1. IIS01 naslouchá pro webový provoz. Tento požadavek obdrží a spustí zpracování požadavku.
1. IIS01 pokusí o zahájení relace FTP na AppVM01 na back endové podsítě.
1. Trasu UDR na front-endové podsítě je brána firewall další segment směrování.
1. Nejsou žádná odchozí pravidla na front-endové podsítě, aby provoz.
1. Brána firewall začíná zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidla brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidlo brány firewall 6 (aplikace: Webu) se nevztahuje. Přesunout na další pravidla.
   1. Pravidlo brány firewall 7 (aplikace: back-end) nevztahuje. Provoz je povolený. Brána firewall předává provoz na 10.0.2.5 (AppVM01).
1. Back endové podsítě provádí zpracování příchozí pravidlo:
    1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Přesunout na další pravidla.
    1. Povolit výchozí pravidla NSG podsítě pro podsíť provozu. Provoz je povolený. Zastavte zpracování pravidel NSG.
1. AppVM01 obdrží požadavek, zahájí relace a odpovídá.
1. Trasu UDR v back endové podsítě je brána firewall další segment směrování.
1. Nejsou žádná odchozí pravidla NSG na back endové podsítě, aby odpovědi.
1. Protože vrací provoz na navázanou relaci, brána firewall předá odpověď zpět do webového serveru (IIS01).
1. Front-endové podsítě provádí zpracování příchozí pravidlo:
    1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Přesunout na další pravidla.
    1. Výchozí pravidla NSG povolit podsítě pro podsíť provozu tak, že tento provoz je povolený. Zastavte zpracování pravidel NSG.
1. Server služby IIS obdrží odpověď a dokončení transakce s AppVM01. Potom server dokončí vytváření odpovědi HTTP a pošle žadateli.
1. Nejsou žádná odchozí pravidla NSG na front-endové podsítě, aby odpovědi.
1. Odpověď HTTP narazí na bránu firewall. Protože je odpověď na navázanou relaci NAT, brána firewall ji přijímá.
1. Brána firewall přesměruje odpověď zpět do Internetu uživatele.
1. Aby odpovědi nejsou žádná odchozí pravidla skupiny zabezpečení sítě nebo UDR směrování na front-endové podsítě. Internet uživateli se zobrazí požadované webové stránky.

### <a name="allowed-internet-rdp-to-back-end"></a>(Povolena) Internet RDP k back-endu

1. Správce serveru na Internetu požádá o relaci RDP na AppVM01 prostřednictvím SecSvc001.CloudApp.Net:8025. 8025 je číslo portu přiřazené uživatele pro pravidlo brány firewall 4 (protokol RDP AppVM01).
1. Cloudová služba předává provoz přes otevřených koncových bodů na portu 8025 rozhraní brány firewall na 10.0.0.4:8025.
1. Žádnou skupinu zabezpečení sítě je přiřazen k zabezpečení podsítě, takže pravidla skupiny zabezpečení sítě systému povolení provozu do brány firewall.
1. Brána firewall provádí zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidlo brány firewall 2 (protokol RDP IIS) na něho nevztahuje. Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall 3 (RDP DNS01). Přesunout na další pravidla.
   1. Pravidlo brány firewall 4 (protokol RDP AppVM01) nevztahuje, takže provoz je povolený. Brána firewall se přesměrovává prostřednictvím překladu adres k 10.0.2.5:3386 (port RDP na AppVM01).
1. Back endové podsítě provádí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Brána firewall přesměrovány tento provoz prostřednictvím překladu adres, proto teď zdrojovou adresu brány firewall, která je v podsíti zabezpečení. Skupina NSG podsítě back-end detekovala jako místní provoz a je povolen. Přesunout na další pravidla.
   1. Výchozí pravidla NSG povolit podsítě pro podsíť provozu tak, že tento provoz je povolený. Zastavte zpracování pravidel NSG.
1. AppVM01 naslouchá provozu protokolu RDP a odpovídá.
1. Nejsou žádná odchozí pravidla NSG, výchozí pravidla se vztahují. Zpětný provoz je povolený.
1. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování.
1. Protože vrací provoz na navázanou relaci, brána firewall předá odpověď zpět do Internetu uživatele.
1. Relaci RDP je povolená.
1. AppVM01 vyzve k zadání uživatelského jména hesla.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povolena) Vyhledávání DNS webového serveru na serveru DNS

1. Webový server IIS01 vyžádá datový kanál HTTP\:\/\/www.data.gov, ale je potřeba vyřešit adresu.
1. Konfiguraci sítě pro virtuální síť seznamy DNS01 (10.0.2.4 na back endovou podsíť) jako primární server DNS. IIS01 odešle DNS01 žádosti DNS.
1. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování.
1. Žádná odchozí pravidla NSG je vázána na front-endové podsítě. Provoz je povolený.
1. Kterou firewall provede zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidlo brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidla brány firewall na 6 a 7 (pravidla pro aplikace) se nevztahují. Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall na 8 (do Internetu). Přesunout na další pravidla.
   1. Použít pravidlo brány firewall 9 (DNS). Provoz je povolený. Brána firewall předává provoz na 10.0.2.4 (DNS01).
1. Back endové podsítě provádí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Přesunout na další pravidla.
   1. Povolit výchozí pravidla NSG podsítě pro podsíť provozu. Provoz je povolený. Zastavte zpracování pravidel NSG.
1. DNS server obdrží požadavek.
1. DNS server nemá adresu do mezipaměti a požádá kořenový server DNS na Internetu.
1. Uživatelem definovaná TRASA odchozí provoz směruje do brány firewall jako další segment směrování.
1. Nejsou žádná odchozí pravidla NSG na back endové podsítě na provoz je povolený.
1. Kterou firewall provede zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidlo brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidla brány firewall na 6 a 7 (pravidla pro aplikace) se nevztahují. Přesunout na další pravidla.
   1. Použít pravidlo brány firewall na 8 (k Internetu). Provoz je povolený. Relace je přesměrovány prostřednictvím SNAT kořenovému serveru DNS na Internetu.
1. Internetového DNS server odpoví. Tuto relaci bylo zahájeno ze brána firewall, tak odpověď je přijat bránou firewall.
1. Tato relace se už navázalo tak předá odpověď na původní server DNS01 bránu firewall.
1. Back endové podsítě provádí zpracování příchozí pravidlo:
    1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Přesunout na další pravidla.
    1. Výchozí pravidla skupiny zabezpečení sítě umožní provoz podsítě pro podsíť pro tento provoz je povolený. Zastavte zpracování pravidel NSG.
1. DNS server obdrží odpověď do mezipaměti a pak odpovídá původní žádost zpět na IIS01.
1. Trasu UDR v back endové podsítě je brána firewall další segment směrování.
1. Žádná odchozí pravidla skupiny zabezpečení sítě existují na back endové podsítě, takže provoz je povolený.
1. Tato relace se už navázalo v bráně firewall tak, že brána firewall přesměrovává odpověď zpět do serveru služby IIS.
1. Front-endové podsítě provádí zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo skupiny zabezpečení sítě pro příchozí provoz z podsítě back-end k front-endové podsítě, takže pravidla NSG nepoužijí.
    1. Výchozí systémové pravidlo povoluje provoz mezi podsítěmi. Provoz je povolený.
1. IIS01 obdrží odpověď od DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Povolena) Server back-end k front-endového serveru

1. Správce přihlášený k AppVM02 přes protokol RDP požaduje soubor přímo ze serveru IIS01 prostřednictvím Průzkumníka souborů windows.
1. Trasu UDR v back endové podsítě je brána firewall další segment směrování.
1. Nejsou žádná odchozí pravidla NSG na back endové podsítě, aby odpovědi.
1. Brána firewall provádí zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidlo brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidla brány firewall na 6 a 7 (pravidla pro aplikace) se nevztahují. Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall na 8 (do Internetu). Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall 9 (DNS). Přesunout na další pravidla.
   1. Pravidlo brány firewall na 10 (Intra-podsítě) nevztahuje. Provoz je povolený. Brána firewall předává provoz 10.0.1.4 (IIS01).
1. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidlo NSG Rule 1 (Block internet)
   1. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený. Zastavte zpracování pravidel NSG.
1. Za předpokladu, že řádné ověření a autorizace, IIS01 tato služba požadavek přijme a odpovídá.
1. Trasu UDR na front-endové podsítě je brána firewall další segment směrování.
1. Nejsou žádná odchozí pravidla NSG na front-endové podsítě, aby odpovědi.
1. Tato relace už existuje v bráně firewall, a proto může tuto odpověď. Brána firewall vrací odpověď AppVM02.
1. Back endové podsítě provádí zpracování příchozí pravidlo:
    1. Neplatí pravidlo skupiny zabezpečení sítě 1 (internet bloku). Přesunout na další pravidla.
    2. Výchozí pravidla NSG povolit podsítě pro podsíť provozu, provoz je povolený. Zastavte zpracování pravidel NSG.
1. AppVM02 obdrží odpověď.

### <a name="denied-internet-direct-to-web-server"></a>(Zakázaný) Internet s přímým přístupem k webovému serveru

1. Uživateli s Internetu pokusí o přístup k IIS01 webový server prostřednictvím služby FrontEnd001.CloudApp.Net.
1. Otevřený pro provoz protokolu HTTP neexistují koncové body, aby tento provoz neprojde prostřednictvím cloudové služby. Provoz nemá přístup na server.
1. Pokud z nějakého důvodu jsou spuštěné koncové body, skupiny zabezpečení sítě (internet bloku) na front-endové podsítě blokuje provoz.
1. Nakonec front-endové podsítě uživatelem definovaná TRASA trasa odesílá veškerý odchozí provoz z IIS01 do brány firewall jako další segment směrování. Brána firewall vnímá jako asymetrický provoz a zahodí odchozí odpovědi.

>Proto existují aspoň tři nezávislá vrstev obrany mezi Internetem a IIS01. Cloudové služby brání v přístupu neoprávněným nebo nevhodný.

### <a name="denied-internet-to-back-end-server"></a>(Zakázaný) Internetu a back endového serveru

1. Uživateli s Internetu pokusí o přístup k souboru na AppVM01 přes službu BackEnd001.CloudApp.Net.
2. Žádné koncové body nejsou otevřené pro sdílení souborů, takže tento požadavek není úspěšný cloudovou službu. Provoz nemá přístup na server.
3. Pokud z nějakého důvodu jsou spuštěné koncové body, skupiny zabezpečení sítě (internet bloku) blokuje provoz.
4. Nakonec uživatelem definovaná TRASA trasa odesílá veškerý odchozí provoz z AppVM01 do brány firewall jako další segment směrování. Brána firewall vnímá jako asymetrický provoz a zahodí odchozí odpovědi.

> Proto existují aspoň tři nezávislá vrstev obrany mezi Internetem a AppVM01. Cloudové služby brání v přístupu neoprávněným nebo nevhodný.

### <a name="denied-front-end-server-to-back-end-server"></a>(Zakázaný) Front-endového serveru back endového serveru

1. IIS01 dojde k ohrožení a běží škodlivý kód pokusu skenovací servery back endové podsítě.
1. Front-endové podsítě uživatelem definovaná TRASA trasa odesílá veškerý odchozí provoz z IIS01 do brány firewall jako další segment směrování. Ohrožené virtuální počítač nelze změnit toto směrování.
1. Brána firewall zpracovává přenosy. Pokud je požadavek AppVM01 nebo serveru DNS pro vyhledávání DNS, brána firewall může potenciálně umožňovat provoz z důvodu pravidla brány Firewall 7 a 9. Veškerý ostatní provoz je blokován pravidlem brány Firewall 11 (Zamítnout vše).
1. Pokud povolíte Pokročilá detekce hrozeb v bráně firewall, může zabránit provoz, který obsahuje známé podpisy nebo vzorce, které příznak pravidlo pokročilé hrozby. Toto opatření můžou fungovat i v případě, že provoz je povolený v souladu s pravidly základní přesměrování, které jsou popsané v tomto článku. Pokročilá detekce hrozeb není zahrnuté v tomto dokumentu. V dokumentaci dodavatele pro vaše konkrétní síťové zařízení rozšířené možnosti hrozeb.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Zakázaný) Vyhledávání DNS pro Internet na serveru DNS

1. Internetu uživatel se pokusí vyhledat interní DNS záznam na DNS01 prostřednictvím BackEnd001.CloudApp.Net služby.
1. Protože je otevřeno žádné koncové body pro provoz DNS, není úspěšný tento provoz prostřednictvím cloudové služby. Nepodporuje se připojit k serveru.
1. Pokud z nějakého důvodu jsou spuštěné koncové body, pravidlo skupiny zabezpečení sítě (internet bloku) na front-endové podsítě blokuje provoz.
1. Nakonec back endové podsítě trasu UDR odesílá veškerý odchozí provoz z DNS01 do brány firewall jako další segment směrování. Brána firewall to vidí jako asymetrický provoz a zahodí odchozí odpovědi.

> Proto existují aspoň tři nezávislá vrstev obrany mezi Internetem a DNS01. Cloudové služby brání v přístupu neoprávněným nebo nevhodný.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Zakázaný) Internetu, aby přístup k SQL přes bránu firewall

1. Internet uživatele vyžaduje SQL data z SecSvc001.CloudApp.Net přístupem k Internetu cloudovou službu.
1. Otevřete SQL neexistují koncové body, aby tento provoz neprojde cloudovou službu. To nemá přístup do brány firewall.
1. Pokud z nějakého důvodu jsou spuštěné koncových bodů SQL, kterou firewall provede zpracování pravidla:
   1. Pravidlo brány firewall 1 (FW Mgmt) nevztahuje. Přesunout na další pravidla.
   1. Pravidla brány firewall 2 – 5 (pravidla protokolu RDP) se nevztahují. Přesunout na další pravidla.
   1. Pravidla brány firewall na 6 a 7 (pravidla aplikací) se nevztahují. Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall na 8 (do Internetu). Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall 9 (DNS). Přesunout na další pravidla.
   1. Neplatí pravidlo brány firewall na 10 (Intra-podsítě). Přesunout na další pravidla.
   1. Pravidlo brány firewall 11 (Zamítnout vše) nevztahuje. Provoz je blokován. Zastavte zpracování pravidla.

## <a name="references"></a>Odkazy

Tato část obsahuje následující položky:

* Úplná skript. Uložte v souboru skriptu prostředí PowerShell.
* Konfigurace sítě. Uložte ho do souboru s názvem NetworkConf2.xml.

Uživatelem definované proměnné v souborech podle potřeby upravte. Spusťte skript a pak postupujte podle pokynů nastavení pravidla brány Firewall uvedené dříve v tomto článku.

### <a name="full-script"></a>Celý skript

Po nastavení uživatelem definované proměnné, spuštěním tohoto skriptu:

1. Připojení k předplatnému Azure
1. Vytvoření nového účtu úložiště
1. Vytvořit novou virtuální síť a tři podsítě, jak jsou definovány v souboru konfigurace sítě
1. Sestavení pět virtuálních počítačů: Brána firewall a čtyři virtuální počítače Windows serveru
1. Konfigurace směrování definovaného uživatelem:
   1. Vytvořit dvě nové směrovací tabulky
   1. Přidání tras do tabulek
   1. Vytvoření vazby tabulky do příslušné podsítě
1. Povolit předávání IP na síťové virtuální zařízení
1. Nakonfigurujte skupiny zabezpečení sítě:
   1. Vytvořte skupinu zabezpečení sítě
   1. Přidání pravidla
   1. Vytvoření vazby skupiny zabezpečení sítě k příslušné podsítě

Toto prostředí PowerShell spustit skript místně na Internetu připojený počítač nebo server.

> [!IMPORTANT]
> Při spuštění tohoto skriptu upozornění a další informační zprávy může vyvolat přes pop up v prostředí PowerShell. Pouze red chybové zprávy jsou příčinou znepokojení.

```powershell
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
       - IP Forwarding from the FireWall out to the internet
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
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

      # Associate the Route Tables with the Subnets
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
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Soubor konfigurace sítě

Uložte tento soubor XML s aktualizované umístění. Změnit `$NetworkConfigFile` proměnné v úplná skript výše odkaz na soubor konfigurace sítě uložený.

```xml
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
```

## <a name="next-steps"></a>Další postup

Můžete nainstalovat ukázkovou aplikaci, abychom vám pomohli s v tomto příkladu hraniční sítě.

> [!div class="nextstepaction"]
> [Ukázkový skript aplikace](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Obousměrné DMZ pomocí síťového virtuálního zařízení, skupiny zabezpečení sítě a směrování definovaného uživatelem"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logické zobrazení pravidel brány Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Vytvoření front-endové síti objektu"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Vytvoření objektu serveru DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopii výchozí pravidlo protokolu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 Rule"
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
