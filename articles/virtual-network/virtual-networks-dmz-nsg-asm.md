---
title: Příklad hraniční síti Azure – vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě | Dokumentace Microsoftu
description: Vytvoření DMZ pomocí skupin zabezpečení sítě (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 115a459c6a9e4ea96931c89272a49396f0656258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362203"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě pomocí prostředí PowerShell classic
[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

> [!div class="op_single_selector"]
> * [Šablona Resource Manageru](virtual-networks-dmz-nsg.md)
> * [Classic – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Tento příklad vytvoří primitivní hraniční sítě se čtyřmi servery Windows a skupiny zabezpečení sítě. Tento příklad popisuje všechny relevantní Powershellové příkazy, které poskytují lepší představu o jednotlivých kroků. Je také část provozu scénář k poskytování podrobné krok za krokem, jak se provoz pokračuje přes vrstev obrany v hraniční síti. Nakonec v odkazech oddíl je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![Příchozí DMZ pomocí skupiny zabezpečení sítě][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu předplatné obsahuje následující prostředky:

* Dva cloudových služeb: "FrontEnd001" a "BackEnd001"
* Virtuální síť "CorpNetwork" se dvěma podsítěmi; "FrontEnd" a "Back-end"
* Skupina zabezpečení sítě, které platí pro obě podsítě
* Windows Server, který představuje aplikační server web (dále jen "IIS01")
* Dva windows serverů, které představují servery back endové aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

V části odkazy se Powershellový skript, který vytváří většinu prostředí popsané v tomto příkladu. Vytváření virtuálních počítačů a virtuálních sítí, i když se provádí ukázkový skript, nejsou v podrobně popsány v tomto dokumentu. 

K vytvoření prostředí;

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizováno s názvy, umístění a IP adresy, které odpovídají situaci)
2. Aktualizace uživatelské proměnné ve skriptu, tak, aby odpovídaly prostředí, ve kterém je skript běžet před (předplatné, názvy služeb atd.)
3. Spusťte skript prostředí PowerShell

>[!Note]
>Oblast označeny Powershellového skriptu musí odpovídat oblasti označeny v souboru xml konfigurace sítě.
>
>

Jakmile skript spustí úspěšně další volitelné kroky může provést, v části odkazy jsou dva skripty pro nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ.

Následující části obsahují podrobný popis skupiny zabezpečení sítě a jak fungují v tomto příkladu pomocí provede klíčové řádky skriptu prostředí PowerShell.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupinu NSG vytvořené a pak načíst šest pravidlům. 

> [!TIP]
> Obecně řečeno byste měli nejprve vytvořit konkrétní pravidla "Povolit" a pak obecnější pravidla "Zakázat" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocují první. Po nalezení provozu použít na příslušné pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít buď ve směru příchozí nebo odchozí (z hlediska podsíť).
> 
> 

Deklarativní byly sestaveny pro příchozí provoz následující pravidla:

1. Interní DNS provoz (port 53) je povolený.
2. Je povolený provoz protokolu RDP (portu 3389) z Internetu do všech virtuálních počítačů
3. Je povolený provoz HTTP (port 80) z Internetu webový server (IIS01)
4. Je povolen veškerý provoz (všechny porty) z IIS01 k AppVM1
5. Veškerý provoz (všechny porty) z Internetu do celé virtuální síť (obě podsítě) byl odepřen.
6. Veškerý provoz (všechny porty) z podsítě front-endu do back-endové podsítě je odepřen.

Pomocí těchto pravidel vázán ke každé podsíti, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (zabránění) se vztahují, ale protože pouze by použít a pravidlo 5 by souvisejí, má vyšší prioritu pravidla 3. Požadavek HTTP se proto by bylo možné webový server. Pokud tento stejný provoz se snažil to spojit se serverem DNS01, pravidlo 5 (odmítnout) by se použít jako první a provoz nebude možné předat serveru. Pravidlo 6 (odmítnout) blokuje front-endové podsítě z komunikaci na podsíť back-endu (s výjimkou povolený provoz v pravidlech 1 a 4), tato sada pravidel chrání síť back-end, v případě, že ohrožení útočník by mají omezenou webovou aplikaci na front-endu, útočník přístup k back-end "protected" sítě (jenom k prostředkům zveřejněné na AppVM01 server).

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Zamezit provoz v obou směrech, směrování definovaného uživatele je povinný a je zkoumat "Příklad 3" [stránku osvědčené postupy zabezpečení hranic][HOME].

Každé pravidlo je podrobněji takto (**Poznámka**: libovolnou položku v následující seznamu začíná znakem dolaru (Příklad: $NSGName) je uživatelem definované proměnné ze skriptu v referenční části tohoto dokumentu):

1. Skupina zabezpečení sítě nejprve musí být sestaveny pro uložení pravidel:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. V tomto příkladu první pravidlo povoluje provoz DNS mezi všechny interní sítě na serveru DNS na podsíť back-endu. Toto pravidlo má některé důležité parametry:
   
   * "Typ", znamená to, v jakém směru toku provozu se projeví toto pravidlo. Směr je z hlediska podsítí nebo virtuálních počítačů (v závislosti na tom, kde je vázána tato skupina zabezpečení sítě). Proto pokud je typ "Příchozí" a provoz vstupující podsítě, pravidlo vztahuje a nebude mít vliv přenosů z podsítě tímto pravidlem.
   * "Priority" Nastaví pořadí, ve kterém je tok přenosů vyhodnoceny. Čím nižší číslo vyšší je priorita. Když pravidlo platí pro konkrétní tok, žádná další pravidla se zpracovávají. Proto pokud pravidlo s prioritou 1 umožňuje provoz a pravidlo s prioritou 2 odepření provozu a obě pravidla se vztahují na provoz pak provoz by bylo možné flow (protože pravidlo 1 má vyšší prioritu vstoupily v platnost a byly použity žádná další pravidla).
   * "Action" označuje, že pokud je provoz, které jsou ovlivněny tímto pravidlem blokované nebo povolené.

     ```PowerShell    
     Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
     ```

3. Toto pravidlo umožňuje provoz protokolu RDP, které mají být předány portu RDP na libovolný server, na vázané podsítě z Internetu. Toto pravidlo používá dva speciální druhy předpony adres; "VIRTUAL_NETWORK" a "INTERNET". Tyto značky jsou snadný způsob, jak adresování větší kategorie předpony adres.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Toto pravidlo umožňuje příchozího internetového provozu narazila na webovém serveru. Toto pravidlo nezmění chování směrování. Toto pravidlo povoluje jenom provoz určený pro IIS01 předat. Proto pokud měl provoz z Internetu webový server jako svůj cíl toto pravidlo by mohla a zastavit zpracování dalších pravidel. (V pravidlo s prioritou 140 všechny ostatní příchozí internetový provoz blokovaný). Pokud máte pouze zpracování přenosy HTTP, může toto pravidlo dále omezen a Povolit jenom cílový Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Toto pravidlo umožňuje přenos dat ze serveru IIS01 na server AppVM01 novější pravidlo blokuje všechny ostatní front-endu do back-endu provoz. K vylepšení tohoto pravidla, pokud se port, který označuje, že by měly být přidány. Například pokud server služby IIS dosahuje pouze systém SQL Server na AppVM01, rozsah cílových portů by měl být změněn z "*" (všechny) 1433 (SQL port), což umožní menší příchozí útoky na AppVM01 by měla webová aplikace stále dojít k ohrožení bezpečnosti.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Toto pravidlo blokuje provoz z Internetu na všechny servery v síti. S pravidly na priority 110 a 120 efekt je povolit pouze příchozího internetového provozu do brány firewall a portů RDP na serverech a bloky všechno ostatní. Toto pravidlo je pravidlo "odolný proti selhání" blokovat všechny neočekávané toky.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. Konečné pravidlo odepření provozu z podsítě front-endu do back-endové podsítě. Toto pravidlo je pouze příchozí pravidlo, je povolen zpětný provoz (z back-end k front-endu).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Provoz scénáře
#### <a name="allowed-internet-to-web-server"></a>(*Povolené*) Internetu, aby webový server
1. Internetu uživatel požádá o stránku protokolu HTTP z FrontEnd001.CloudApp.Net (Internet směřující cloudové služby)
2. Cloudové služby předává provoz přes otevřených koncových bodů na portu 80 na IIS01 (webový server)
3. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Vztahuje se 3 pravidlo skupiny zabezpečení sítě (Internet k IIS01), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa webového serveru IIS01 (adresa 10.0.1.5)
5. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
6. IIS01 dotazem SQL serveru na AppVM01 informace
7. Protože nejsou žádná odchozí pravidla ve front-endové podsíti, je povolený provoz
8. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. 3 pravidlo skupiny zabezpečení sítě (Internet do brány Firewall) nebude použít, přejděte k další pravidla
   4. 4 pravidlo skupiny zabezpečení sítě (IIS01 k AppVM01) použít, je povolený provoz, zastavit zpracování pravidla
9. AppVM01 obdrží dotaz SQL a odpovídá
10. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povoleno odpovědi
11. Front-endové podsítě začíná zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz.
12. Server služby IIS obdrží odpověď SQL a dokončí odpovědi HTTP a pošle žadateli
13. Protože nejsou žádná odchozí pravidla na front-endové podsítě je povolena odpovědi a Internetu uživateli se zobrazí požadované webové stránky.

#### <a name="allowed-rdp-to-backend"></a>(*Povolené*) připojení RDP k back-endu
1. Správce serveru k Internetu vyžaduje relaci RDP na AppVM01 na BackEnd001.CloudApp.Net:xxxxx kde xxxxx je počet náhodně přidělenému portu pro připojení RDP k AppVM01 (přidělenému portu najdete na webu Azure portal nebo prostřednictvím prostředí PowerShell)
2. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Použít pravidlo NSG 2 (RDP), provoz je povolený, zastavte pravidla zpracování
3. Žádná odchozí pravidla použít výchozí pravidla a zpětný provoz je povolený.
4. Povolené relace protokolu RDP
5. AppVM01 vyzve k zadání uživatelského jména a hesla

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Povolené*) vyhledávání DNS webového serveru na serveru DNS
1. Webový Server, IIS01, potřebám datového kanálu na www.data.gov, ale potřebám pro překlad adres.
2. Konfiguraci sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v back-endové podsíti) jako primární server DNS, IIS01 odešle žádosti DNS DNS01
3. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
4. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   * Vztahuje se 1 pravidlo skupiny zabezpečení sítě (DNS), provoz je povolený, zastavte pravidla zpracování
5. DNS server obdrží požadavek
6. DNS server nemá adresu do mezipaměti a požádá kořenový server DNS na Internetu
7. Žádná odchozí pravidla na back-endové podsítě je povolený provoz
8. Server DNS pro Internet odpoví, protože tato relace byla zahájena interně, odpověď může
9. DNS server, odpověď do mezipaměti a reaguje na původní žádost zpět na IIS01
10. Žádná odchozí pravidla na back-endové podsítě je povolený provoz
11. Front-endové podsítě začíná zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz
12. IIS01 obdrží odpověď od DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Povolené*) přístup k souboru webového serveru na AppVM01
1. IIS01 vyzve k zadání souboru na AppVM01
2. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
3. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. 3 pravidlo skupiny zabezpečení sítě (Internet k IIS01) nebude použít, přejděte k další pravidla
   4. 4 pravidlo skupiny zabezpečení sítě (IIS01 k AppVM01) použít, je povolený provoz, zastavit zpracování pravidla
4. AppVM01 obdrží požadavek a odpovídá zprávou souboru (za předpokladu, že přístup je autorizovaný)
5. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povoleno odpovědi
6. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
   2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz.
7. Server služby IIS obdrží soubor

#### <a name="denied-web-to-backend-server"></a>(*Byl odepřen*) Web back-end server
1. Uživatel s Internetu pokusí o přístup k souboru na AppVM01 přes službu BackEnd001.CloudApp.Net
2. Protože je otevřeno žádné koncové body pro sdílenou složku, tento provoz by předat Cloudovou službu a nebude připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncových bodů, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Byl odepřen*) vyhledávací Web DNS na serveru DNS
1. Internet uživatele se pokusí vyhledat interní DNS záznam na DNS01 přes službu BackEnd001.CloudApp.Net
2. Protože jsou otevřené pro službu DNS žádné koncové body, tento provoz by předat Cloudovou službu a nebude připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncových bodů, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz (Poznámka: Tento pravidlo 1 (DNS) se nedají použít pro dva důvody, nejprve zdrojové adresy je Internetu, toto pravidlo platí pouze pro místní virtuální sítě jako zdroj také toto pravidlo je pravidlo povolení, takže by nikdy zakazují provoz)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Byl odepřen*) Web pro přístup k SQL přes bránu firewall
1. Uživatel k Internetu vyžaduje SQL data z FrontEnd001.CloudApp.Net (Internet směřující cloudové služby)
2. Protože je otevřeno žádné koncové body pro server SQL, tento provoz by předat Cloudovou službu a by kontaktovat bránu firewall
3. Pokud z nějakého důvodu otevřelo koncové body, front-endové podsítě začíná zpracování příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Vztahuje se 3 pravidlo skupiny zabezpečení sítě (Internet k IIS01), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa IIS01 (adresa 10.0.1.5)
5. IIS01 nenaslouchá na portu 1433, takže žádná odezva na žádosti

## <a name="conclusion"></a>Závěr
V tomto příkladu je poměrně jednoduchá a rovnou dopředné způsob izolace back endové podsítě z příchozí provoz.

Další příklady a základní informace o hranicích zabezpečení sítě najdete [tady][HOME].

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní konfigurační skript a sítě
Úplná skript uložte v souboru skriptu prostředí PowerShell. Uložit konfiguraci sítě do souboru s názvem "NetworkConf1.xml."
Uživatelem definované proměnné podle potřeby upravit a spusťte skript.

#### <a name="full-script"></a>Celý skript
Tento skript se podle uživatelem definované proměnné;

1. Připojení k předplatnému Azure
2. vytvořit účet úložiště
3. Vytvoření virtuální sítě a dvě podsítě, jak jsou definovány v souboru konfigurace sítě
4. Vytváření čtyři windows server virtuálních počítačů
5. Konfigurace, včetně skupiny zabezpečení sítě:
   * Vytvořit skupinu zabezpečení sítě
   * Sestavování s pravidly
   * Vytvoření vazby skupiny zabezpečení sítě na příslušné podsítě

Tento skript Powershellu je vhodné spustit místně na, že připojení Internetu, počítač nebo server.

> [!IMPORTANT]
> Když se skript spouští, může být upozornění nebo ostatní informační zprávy, které se objeví v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou znepokojení.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

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

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
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

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
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
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Soubor konfigurace sítě
Uložte tento soubor xml s aktualizované umístění a přidat odkaz na tento soubor do proměnné $NetworkConfigFile v předchozím skriptu.

```XML
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>Ukázky skriptů aplikace
Pokud chcete nainstalovat ukázkovou aplikaci pro tuto a další příklady hraniční sítě, jednu byl poskytnut na následující odkaz: [Ukázkový skript aplikace][SampleApp]

## <a name="next-steps"></a>Další postup
* Aktualizovat a uložit soubor XML
* Spusťte skript prostředí PowerShell k vytvoření prostředí
* Nainstalovat ukázkovou aplikaci
* Testování jiný přenos prochází tato hraniční sítě

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Příchozí DMZ pomocí skupiny zabezpečení sítě"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

