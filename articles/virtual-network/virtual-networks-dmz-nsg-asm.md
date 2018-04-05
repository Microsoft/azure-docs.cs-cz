---
title: Příklad Azure DMZ – vytvoření jednoduché DMZ pomocí skupin Nsg | Microsoft Docs
description: Sestavení DMZ se skupinami zabezpečení sítě (NSG)
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
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin Nsg classic PowerShell
[Návrat na stránku osvědčené postupy zabezpečení hranic][HOME]

> [!div class="op_single_selector"]
> * [Šablona Resource Manageru](virtual-networks-dmz-nsg.md)
> * [Classic – prostředí PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Tento příklad vytvoří primitivní hraniční sítě se čtyřmi servery Windows a skupiny zabezpečení sítě. Tento příklad popisuje všechny relevantní příkazy prostředí PowerShell, zadejte podrobnější vysvětlení jednotlivých kroků. Je také části provoz scénář zajistit podrobné krok za krokem, jak se provoz pokračuje prostřednictvím vrstev obrany v hraniční síti. Nakonec v odkazy na části je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![Příchozí DMZ s NSG][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu obsahuje odběru v následujících zdrojích informací:

* Dva cloudové služby: "FrontEnd001" a "BackEnd001"
* Virtuální síť, "CorpNetwork" se dvěma podsítěmi; "FrontEnd" a "Back-end"
* Skupinu zabezpečení sítě, který se použije pro obě podsítě
* Windows Server, který představuje server webových aplikací ("IIS01")
* Dva windows serverů, které představují servery back-end aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

V části odkazy je skript prostředí PowerShell, který sestaví většinu prostředí popsané v tomto příkladu. Vytváření virtuálních počítačů a virtuálních sítí, i když provádějí ukázkový skript, nejsou podrobně popsané v tomto dokumentu. 

K vytvoření prostředí;

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizovat název, umístění a IP adresy, které odpovídají danému scénáři)
2. Aktualizace uživatelské proměnné ve skriptu tak, aby odpovídaly prostředí, ve kterém je skript ke spouštění (odběry, názvy služeb atd.)
3. Spusťte skript v prostředí PowerShell

>[!Note]
>Oblast označený ve skriptu PowerShell musí odpovídat oblasti označeny v souboru xml konfigurace sítě.
>
>

Jakmile se skript spustí úspěšně další volitelné kroky mohou být přijata, v části odkazy jsou dva skripty, nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací hraniční sítě.

V následujících částech najdete podrobný popis skupiny zabezpečení sítě a jak fungují v tomto příkladu pomocí klíče řádky skriptu prostředí PowerShell s návodem.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupinu NSG vytvořené a pak načtená šesti pravidla. 

> [!TIP]
> Obecně řečeno měli byste vytvořit konkrétní pravidel "Povolit" nejprve a pak více obecná pravidla "Deny" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocena první. Jakmile provoz nenajde Pokud chcete použít pro konkrétní pravidlo, jsou vyhodnotit žádná další pravidla. Pravidla NSG můžete použít buď v příchozí nebo odchozí směr (z hlediska podsítě).
> 
> 

Následující pravidla deklarativně, se budou vytvářeny pro příchozí provoz:

1. Interní DNS provoz (port 53) je povolený
2. Provoz protokolu RDP (portu 3389) z Internetu do všech virtuálních počítačů je povoleno.
3. Je povolen přenos HTTP (port 80) z Internetu webový server (IIS01)
4. Všechny přenosy (všechny porty) z IIS01 na AppVM1 je povolen.
5. Přenosy dat (všechny porty) z Internetu pro celou virtuální síť (obě podsítě) byl odepřen.
6. Přenosy dat (všechny porty) z podsítě front-endu do podsítě back-end byl odepřen.

Pomocí těchto pravidel vázána na každou podsíť, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (Odepřít) by použít, ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu jenom by použít a pravidlo 5 nebude možné uplatnit. Proto bude mít možnost požadavku HTTP k webovému serveru. Pokud tento stejný provoz se pokusil připojit k serveru DNS01, pravidlo 5 (Odepřít) bude první použití a přenos nebude možné předat serveru. Pravidlo 6 (Odepřít) blokuje podsíť Frontend z rozhovoru s back-end podsítě (s výjimkou povolené přenosy v pravidlech 1 a 4), této sady pravidel chrání síť back-end v případě ohrožení útočník webové aplikace na Frontendový, útočník by mít omezený přístup k back-end "chráněná" Síťová (pouze pro prostředky zveřejněné na serveru AppVM01).

Je výchozí odchozí pravidlo, které umožňuje přenos se k Internetu. V tomto příkladu jsme se umožňuje odchozí provoz a úprava není žádná odchozí pravidla. Zamknout provoz v obou směrech, směrování definovaného uživatele je povinná a je prozkoumali "Příklad 3" na [stránku osvědčené postupy zabezpečení hranic][HOME].

Každé pravidlo je podrobněji popsána takto (**Poznámka**: libovolnou položku v následujícím seznamu počínaje znak dolaru (například: $NSGName) je uživatelské proměnné ve skriptu v části odkaz na tohoto dokumentu):

1. Skupina zabezpečení sítě musí nejprve vytvořeny tak, aby udržení pravidla:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. První pravidlo v tomto příkladu umožňuje přenosů mezi všechny interní sítě na server DNS v podsíti back-end. Toto pravidlo má některé důležité parametry:
   
   * "Typ" označuje, že ve směru toku provozu účinné toto pravidlo. Směr je z hlediska podsíť nebo virtuálního počítače (v závislosti na tom, kde je tato skupina NSG vázán). Proto pokud je typ "Příchozí" a provoz vstupující podsíť, pravidlo vztahuje a odchozího provozu z podsítě ovlivněn tímto pravidlem.
   * "Priority" Nastaví pořadí, ve kterém je vyhodnocena tok přenosů. Nižší počet tím vyšší je priorita. Když se pravidlo vztahuje na konkrétní přenosový tok, žádná další pravidla se zpracovávají. Proto pokud pravidlo s prioritou 1 umožňuje provoz a pravidlo s prioritou 2 odmítne provozu a použít obě pravidla pro provoz pak provoz se bude moct toku (vzhledem k tomu, že pravidlo 1 měl vyšší prioritu trvalo účinek a žádná další pravidla byly použity).
   * "Action" označuje, že pokud toto pravidlo přenosů blokovat nebo povolit.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Toto pravidlo umožňuje provoz protokolu RDP, které jsou předávány z Internetu k portu RDP na libovolném serveru v vázané podsíti. Toto pravidlo používá dva speciální typy předpon adres; "VIRTUAL_NETWORK" a "INTERNET". Tyto značky jsou snadný způsob, jak vyřešit vyšší kategorie předpon adres.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Toto pravidlo umožňuje příchozí internetové přenosy narazila na webovém serveru. Toto pravidlo nedojde ke změně chování směrování. Toto pravidlo umožňuje pouze provoz určený pro IIS01 předat. Takže pokud provoz z Internetu měl webový server jako svůj cíl toto pravidlo by se povolit a zastavit zpracování další pravidla. (V pravidla s důležitostí 140 všechny ostatní příchozí internetový provoz blokováno). Pokud máte pouze zpracování přenos HTTP, může být toto pravidlo další omezena a Povolit jenom cílový Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Toto pravidlo umožňuje přenos dat ze serveru IIS01 k serveru AppVM01 novější bloky pravidel všechny front-end pro provoz back-end. Aby se zlepšil toto pravidlo, pokud je znám port, který má být přidána. Například pokud server služby IIS je stiskne pouze SQL Server na AppVM01, rozsah cílových portů by mělo být změněno z "*" (Any) 1433 (SQL port), což umožňuje menší prostor pro příchozí útok na AppVM01 by měl webové aplikace někdy dojít k ohrožení.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Toto pravidlo na všechny servery v síti odmítne přenosy z Internetu. Pravidla s důležitostí 110 a 120 účinek je umožnit pouze příchozí internetové přenosy pro brány firewall a porty protokolu RDP na serverech a bloky nic jiného. Toto pravidlo je "pohotovostního" pravidlo pro zablokování všechny neočekávané toky.
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
7. Poslední pravidlo odmítne provozu z podsítě front-endu do podsítě back-end. Vzhledem k tomu, že toto pravidlo je pouze příchozí pravidlo, zpětné provoz je povolený (z back-end na front-endu).

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
#### <a name="allowed-internet-to-web-server"></a>(*Povolené*) Internet na webový server
1. Internetu uživatel požádá o stránku HTTP z FrontEnd001.CloudApp.Net (Internet čelí cloudové služby)
2. Cloudové služby předává přenos prostřednictvím otevřených koncových bodů na portu 80 směrem IIS01 (webový server)
3. Podsíť frontend zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Použít NSG pravidla 3 (Internet k IIS01), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu serveru webového IIS01 (10.0.1.5)
5. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
6. IIS01 systému SQL Server na AppVM01 vyzve k zadání informací
7. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti front-endu, provoz je povolený
8. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Není použít, přejděte k další pravidla NSG pravidla 3 (Internet do brány Firewall)
   4. Skupina NSG pravidla 4 použít (IIS01 k AppVM01), provoz je povolený, zastavte zpracování pravidla
9. AppVM01 přijme příkaz jazyka SQL a odpovídá
10. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti back-end, je povoleno odpovědi
11. Podsíť frontend zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
    2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povolen.
12. Server služby IIS obdrží odpověď SQL a dokončení odpovědi HTTP a odešle do žadatel
13. Vzhledem k tomu, že neexistují žádná odchozí pravidla Internetu uživatel obdrží požadovanou webovou stránku a na podsíť Frontend odpovědi povoleny.

#### <a name="allowed-rdp-to-backend"></a>(*Povolené*) protokolu RDP na back-end
1. Správce serveru na Internetu požadavky protokolu RDP relace AppVM01 na BackEnd001.CloudApp.Net:xxxxx kde xxxxx je číslo náhodně přidělenému portu pro protokol RDP na AppVM01 (přidělenému portu naleznete na portálu Azure nebo pomocí prostředí PowerShell)
2. Back-end podsíť zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Použít NSG pravidlo 2 (RDP), Probíhá zpracování povolených, zastavení pravidla
3. Žádná odchozí pravidla použít výchozí pravidla a návratový provoz je povolený
4. Je povoleno relaci protokolu RDP.
5. AppVM01 vyzve k zadání uživatelského jména a hesla

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Povolené*) hledání DNS webového serveru na serveru DNS
1. Webový Server, IIS01, požadavky datového kanálu v www.data.gov, ale musí pro překlad adres.
2. Konfigurace sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v podsíti back-end) jako primární server DNS, IIS01 odešle žádost DNS do DNS01
3. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
4. Back-end podsíť zahájí zpracování příchozí pravidlo:
   * Použít NSG pravidlo 1 (DNS), Probíhá zpracování povolených, zastavení pravidla
5. DNS server obdrží požadavek
6. DNS server nemá adresu do mezipaměti a požádá kořenový server DNS na Internetu
7. Žádná odchozí pravidla na back-end podsítě provoz je povolený.
8. Server DNS pro Internet odpoví, vzhledem k tomu, že tuto relaci bylo zahájeno interně, je povoleno odpovědi
9. DNS server odpověď do mezipaměti a reaguje na počáteční požadavek zpět na IIS01
10. Žádná odchozí pravidla na back-end podsítě provoz je povolený.
11. Podsíť frontend zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
    2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povoleno
12. IIS01 obdrží odpověď od DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Povolené*) přístup k souboru webového serveru na AppVM01
1. IIS01 požádá o soubor na AppVM01
2. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
3. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Není použít, přejděte k další pravidla NSG pravidla 3 (Internet k IIS01)
   4. Skupina NSG pravidla 4 použít (IIS01 k AppVM01), provoz je povolený, zastavte zpracování pravidla
4. AppVM01 obdrží požadavek a odpoví souboru (za předpokladu, že je autorizovaný přístup)
5. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti back-end, je povoleno odpovědi
6. Podsíť frontend zahájí zpracování příchozí pravidlo:
   1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
   2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povolen.
7. Server služby IIS obdrží soubor

#### <a name="denied-web-to-backend-server"></a>(*Byl odepřen*) webové back-end server
1. Uživatel s Internetu pokusí o přístup k souboru na AppVM01 prostřednictvím služby BackEnd001.CloudApp.Net
2. Vzhledem k tomu, že nejsou otevřené pro sdílené složky koncové body, tato komunikace by předat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, by tento provoz blokovat pravidla NSG 5 (Internet do virtuální sítě)

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Byl odepřen*) hledání DNS pro Web na serveru DNS
1. Uživatelé Internetu pokusí vyhledat interní DNS záznam na DNS01 prostřednictvím služby BackEnd001.CloudApp.Net
2. Vzhledem k tomu, že nejsou otevřené pro DNS koncové body, tato komunikace by předat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, pravidla NSG 5 (Internet do virtuální sítě) by blokovat tento provoz (Poznámka: Tento pravidlo 1 (DNS) nebude použít dvou důvodů, nejprve zdrojové adresy je Internetu, toto pravidlo se vztahuje pouze na místní virtuální síť jako zdroj, také toto pravidlo je pravidlo povolení, takže by nikdy zakážou provoz)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Byl odepřen*) webový přístup SQL pomocí brány firewall
1. Uživatel s Internetu vyžaduje SQL data z FrontEnd001.CloudApp.Net (Internet čelí cloudové služby)
2. Vzhledem k tomu, že jsou pro SQL otevřené žádné koncové body, tento provoz by předat cloudové služby a nebude kontaktovat bránu firewall
3. Pokud z nějakého důvodu byly otevřené koncových bodů, podsíť Frontend zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Použít NSG pravidla 3 (Internet k IIS01), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu IIS01 (10.0.1.5)
5. IIS01 nenaslouchá na portu 1433, takže žádná odpověď na žádost

## <a name="conclusion"></a>Závěr
V tomto příkladu je relativně jednoduché a splněny následující způsob izolace back-end podsíť z příchozí přenosy.

Další příklady a přehled hranice zabezpečení sítě najdete [sem][HOME].

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní skript a síťové konfigurace
Uložte úplné skript v souboru skriptu prostředí PowerShell. Uložte konfiguraci sítě do souboru s názvem "NetworkConf1.xml."
Uživatelem definované proměnné podle potřeby upravte a spusťte skript.

#### <a name="full-script"></a>Celý skript
Tento skript bude na základě proměnných uživatelem definované;

1. Připojení k předplatnému Azure
2. vytvořit účet úložiště
3. Vytvořit virtuální síť a dvě podsítě, jak jsou definovány v souboru konfigurace sítě
4. Sestavení windows čtyři virtuální počítače serveru
5. Konfigurace, včetně NSG:
   * Vytvořit skupinu NSG
   * Sestavování s pravidly
   * Vytvoření vazby skupinu NSG na příslušné podsítě

Tento skript prostředí PowerShell je vhodné spustit místně na Internetu připojený počítač nebo server.

> [!IMPORTANT]
> Když tento skript se spustí, může být upozornění nebo ostatní informační zprávy, které pop v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou problém.
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
Uložte tento soubor xml s aktualizované umístění a přidat odkaz na tento soubor do proměnné $NetworkConfigFile v předchozí skript.

```XML
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

#### <a name="sample-application-scripts"></a>Ukázkové skripty aplikace
Pokud chcete nainstalovat ukázkovou aplikaci pro toto a další příklady hraniční sítě, jednu bylo zadáno na následující odkaz: [ukázkový skript aplikace][SampleApp]

## <a name="next-steps"></a>Další postup
* Aktualizace a uložte soubor XML
* Spustit skript prostředí PowerShell k vytvoření prostředí
* Instalace ukázkové aplikace
* Testování různé přenosové toky prostřednictvím této hraniční sítě

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Příchozí DMZ s NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

