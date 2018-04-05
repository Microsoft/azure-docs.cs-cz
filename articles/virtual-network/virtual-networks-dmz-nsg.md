---
title: Příklad Azure DMZ – vytvoření jednoduché DMZ pomocí skupin Nsg | Microsoft Docs
description: Sestavení DMZ se skupinami zabezpečení sítě (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin Nsg pomocí šablony Azure Resource Manager
[Návrat na stránku osvědčené postupy zabezpečení hranic][HOME]

> [!div class="op_single_selector"]
> * [Šablona Resource Manageru](virtual-networks-dmz-nsg.md)
> * [Classic – prostředí PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Tento příklad vytvoří primitivní hraniční sítě se čtyřmi servery Windows a skupiny zabezpečení sítě. Tento příklad popisuje jednotlivých částech odpovídající šablonu zajistit podrobnější vysvětlení jednotlivých kroků. Je také části provoz scénář poskytnout podrobný podrobný rozbor toho, jak se provoz pokračuje prostřednictvím vrstev obrany v hraniční síti. Nakonec v odkazy na části je kód dokončení šablony a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Příchozí DMZ s NSG][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu obsahuje odběru v následujících zdrojích informací:

* Jedna skupina prostředků
* Virtuální síť se dvěma podsítěmi; "FrontEnd" a "Back-end"
* Skupinu zabezpečení sítě, který se použije pro obě podsítě
* Windows Server, který představuje server webových aplikací ("IIS01")
* Dva windows serverů, které představují servery back-end aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")
* Veřejné IP adresy přidružené k serveru webové aplikace

V části odkazy je odkaz na šablonu Azure Resource Manager, který sestaví prostředí popsané v tomto příkladu. Vytváření virtuálních počítačů a virtuálních sítí, i když provádí šabloně příklad nejsou podrobně popsány v tomto dokumentu. 

**K vytvoření tohoto prostředí** (podrobné pokyny naleznete v části odkazy tohoto dokumentu);

1. Nasazení šablony Azure Resource Manager v: [šablony Azure rychlý start][Template]
2. Nainstalovat ukázkovou aplikaci v: [ukázkový skript aplikace][SampleApp]

>[!NOTE]
>Pro připojení RDP k žádnému back-end serverů v této instanci serveru IIS slouží jako "jump pole." První RDP na server služby IIS a pak z RDP Server služby IIS na back-end serverů. Případně může být veřejnou IP adresu ke každému serveru síťový adaptér pro snazší RDP přidružena.
> 
>

Následující části obsahují podrobný popis skupinu zabezpečení sítě a jak funguje v tomto příkladu pomocí s návodem klíče řádky šablony Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupinu NSG vytvořené a pak načtená šesti pravidla. 

>[!TIP]
>Obecně řečeno měli byste vytvořit konkrétní pravidel "Povolit" nejprve a pak více obecná pravidla "Deny" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocena první. Jakmile provoz nenajde Pokud chcete použít pro konkrétní pravidlo, jsou vyhodnotit žádná další pravidla. Pravidla NSG můžete použít buď v příchozí nebo odchozí směr (z hlediska podsítě).
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

Je výchozí odchozí pravidlo, které umožňuje přenos se k Internetu. V tomto příkladu jsme se umožňuje odchozí provoz a úprava není žádná odchozí pravidla. Použít zásady zabezpečení na provoz v obou směrech, směrování definovaného uživatele je povinná a je prozkoumali "Příklad 3" na [stránku osvědčené postupy zabezpečení hranic][HOME].

Každé pravidlo je podrobněji popsána následujícím způsobem:

1. Prostředek skupinu zabezpečení sítě musí být vytvořena instance pro uložení pravidla:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. První pravidlo v tomto příkladu umožňuje přenosů mezi všechny interní sítě na server DNS v podsíti back-end. Toto pravidlo má některé důležité parametry:
  * "destinationAddressPrefix" - pravidla můžete použít zvláštní typ předpona adresy názvem "Výchozí značka", tyto značky jsou identifikátory poskytované systémem, které umožňují snadný způsob, jak vyřešit vyšší kategorie předpon adres. Toto pravidlo používá výchozí značky "Internet" k označují každou adresu, mimo síť VNet. Ostatní předponu značky jsou virtuální síť a AzureLoadBalancer.
  * "Směr" označuje, že ve směru toku provozu účinné toto pravidlo. Směr je z hlediska podsíť nebo virtuálního počítače (v závislosti na tom, kde je tato skupina NSG vázán). Proto pokud je směr "Příchozí" a provoz vstupující podsíť, pravidlo vztahuje a odchozího provozu z podsítě ovlivněn tímto pravidlem.
  * "Priority" Nastaví pořadí, ve kterém je vyhodnocena tok přenosů. Nižší počet tím vyšší je priorita. Když se pravidlo vztahuje na konkrétní přenosový tok, žádná další pravidla se zpracovávají. Proto pokud pravidlo s prioritou 1 umožňuje provoz a pravidlo s prioritou 2 odmítne provozu a použít obě pravidla pro provoz pak provoz se bude moct toku (vzhledem k tomu, že pravidlo 1 měl vyšší prioritu trvalo účinek a žádná další pravidla byly použity).
  * "Přístup" označuje, že toto pravidlo je-li blokované ("Deny") nebo povolených ("Povolit").

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Toto pravidlo umožňuje provoz protokolu RDP, které jsou předávány z Internetu k portu RDP na libovolném serveru v vázané podsíti. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Toto pravidlo umožňuje příchozí internetové přenosy narazila na webovém serveru. Toto pravidlo nedojde ke změně chování směrování. Toto pravidlo umožňuje pouze provoz určený pro IIS01 předat. Takže pokud provoz z Internetu měl webový server jako svůj cíl toto pravidlo by se povolit a zastavit zpracování další pravidla. (V pravidla s důležitostí 140 všechny ostatní příchozí internetový provoz blokováno). Pokud máte pouze zpracování přenos HTTP, může být toto pravidlo další omezena a Povolit jenom cílový Port 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Toto pravidlo umožňuje přenos dat ze serveru IIS01 k serveru AppVM01 novější bloky pravidel všechny front-end pro provoz back-end. Aby se zlepšil toto pravidlo, pokud je znám port, který má být přidána. Například pokud server služby IIS je stiskne pouze SQL Server na AppVM01, rozsah cílových portů by mělo být změněno z "*" (Any) 1433 (SQL port), což umožňuje menší prostor pro příchozí útok na AppVM01 by měl webové aplikace někdy dojít k ohrožení.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Toto pravidlo na všechny servery v síti odmítne přenosy z Internetu. Pravidla s důležitostí 110 a 120 účinek je umožnit pouze příchozí internetové přenosy pro brány firewall a porty protokolu RDP na serverech a bloky nic jiného. Toto pravidlo je "pohotovostního" pravidlo pro zablokování všechny neočekávané toky.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. Poslední pravidlo odmítne provozu z podsítě front-endu do podsítě back-end. Vzhledem k tomu, že toto pravidlo je pouze příchozí pravidlo, zpětné provoz je povolený (z back-end na front-endu).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Provoz scénáře
#### <a name="allowed-internet-to-web-server"></a>(*Povolené*) Internet na webový server
1. Internetu uživatel požádá o stránku HTTP z veřejné IP adresy přidružené k seskupování IIS01 síťového adaptéru
2. Veřejná IP adresa předá provoz do virtuální sítě směrem IIS01 (webový server)
3. Podsíť frontend zahájí zpracování příchozí pravidlo:
  1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
  2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
  3. Použít NSG pravidla 3 (Internet k IIS01), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu serveru webového IIS01 (10.0.1.5)
5. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
6. IIS01 systému SQL Server na AppVM01 vyzve k zadání informací
7. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
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
13. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti front-endu, odpověď je povoleno a Internetu uživatel obdrží požadované webové stránky.

#### <a name="allowed-rdp-to-iis-server"></a>(*Povolené*) protokolu RDP na server služby IIS
1. Správce serveru na Internetu požadavky relaci protokolu RDP pro IIS01 na veřejnou IP adresu na síťový adaptér přidružený IIS01 síťový adaptér (Tato veřejná IP adresa naleznete prostřednictvím portálu nebo prostředí PowerShell)
2. Veřejná IP adresa předá provoz do virtuální sítě směrem IIS01 (webový server)
3. Podsíť frontend zahájí zpracování příchozí pravidlo:
  1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
  2. Použít NSG pravidlo 2 (RDP), Probíhá zpracování povolených, zastavení pravidla
4. Žádná odchozí pravidla použít výchozí pravidla a návratový provoz je povolený
5. Je povoleno relaci protokolu RDP.
6. IIS01 vyzve k zadání uživatelského jména a hesla

>[!NOTE]
>Pro připojení RDP k žádnému back-end serverů v této instanci serveru IIS slouží jako "jump pole." První RDP na server služby IIS a pak z RDP Server služby IIS na back-end serverů.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Byl odepřen*) protokolu RDP na back-end
1. Uživatelé Internetu pokusí protokolu RDP na server AppVM01
2. Vzhledem k tomu, že nejsou žádné veřejné IP adresy přidružené k této servery síťový adaptér, tato komunikace by nikdy zadejte síť VNet a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byla povolená veřejnou IP adresu, ale by pravidla NSG 2 (RDP) povolit tento provoz

>[!NOTE]
>Pro připojení RDP k žádnému back-end serverů v této instanci serveru IIS slouží jako "jump pole." První RDP na server služby IIS a pak z RDP Server služby IIS na back-end serverů.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Byl odepřen*) webové back-end server
1. Uživatel s Internetu pokusí o přístup k souboru na AppVM01
2. Vzhledem k tomu, že nejsou žádné veřejné IP adresy přidružené k této servery síťový adaptér, tato komunikace by nikdy zadejte síť VNet a nebude moci připojit k serveru
3. Pokud z nějakého důvodu bylo povolené veřejnou IP adresu, by tento provoz blokovat pravidla NSG 5 (Internet do virtuální sítě)

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Byl odepřen*) hledání DNS pro Web na serveru DNS
1. Uživatelé Internetu pokusí vyhledat interní záznam DNS na DNS01
2. Vzhledem k tomu, že nejsou žádné veřejné IP adresy přidružené k této servery síťový adaptér, tato komunikace by nikdy zadejte síť VNet a nebude moci připojit k serveru
3. Pokud z nějakého důvodu bylo povolené veřejnou IP adresu, pravidla NSG 5 (Internet do virtuální sítě) by blokovat tento provoz (Poznámka: aby pravidlo 1 (DNS) nebude použít, protože žádosti zdrojová adresa je Internetu a pravidla 1 se vztahuje pouze na místní virtuální síť jako zdroj)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Byl odepřen*) přístup SQL na webovém serveru
1. Uživatel s Internetu vyžaduje SQL data z IIS01
2. Vzhledem k tomu, že nejsou žádné veřejné IP adresy přidružené k této servery síťový adaptér, tato komunikace by nikdy zadejte síť VNet a nebude moci připojit k serveru
3. Pokud z nějakého důvodu bylo povolené veřejnou IP adresu, podsíť Frontend zahájí zpracování příchozí pravidlo:
  1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
  2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
  3. Použít NSG pravidla 3 (Internet k IIS01), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu IIS01 (10.0.1.5)
5. IIS01 nenaslouchá na portu 1433, takže žádná odpověď na žádost

## <a name="conclusion"></a>Závěr
V tomto příkladu je relativně jednoduché a splněny následující způsob izolace back-end podsíť z příchozí přenosy.

Další příklady a přehled hranice zabezpečení sítě najdete [sem][HOME].

## <a name="references"></a>Odkazy
### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Tento příklad používá šablonu Azure Resource Manager předdefinované v úložišti GitHub spravován společností Microsoft a otevřené celé komunitě. Tuto šablonu je možné nasazovat přímo z Githubu, nebo stáhnout a upravit tak, aby vyhovovaly vašim potřebám. 

Hlavní šablona je v souboru s názvem "azuredeploy.json." Tato šablona jde odeslat prostřednictvím prostředí PowerShell nebo rozhraní příkazového řádku (souborem přidružené "azuredeploy.parameters.json") k nasazení této šablony. Najít Nejjednodušším způsobem je použít tlačítko "Nasadit do Azure" na stránce README.md v Githubu.

Pokud chcete nasadit šablonu, která vytvoří tento příklad z Githubu a portálu Azure, postupujte takto:

1. V prohlížeči přejděte na [šablony][Template]
2. Klikněte na tlačítko "Nasadit do Azure" (nebo na tlačítko "Vizualizovat" v tématu grafické reprezentace této šablony)
3. Zadejte účet úložiště, uživatelské jméno a heslo v okně parametry a potom klikněte na tlačítko **OK**
5. Vytvořte skupinu prostředků pro toto nasazení (můžete použít existující šablonu, ale I doporučujeme novou nejlepších výsledků dosáhnete)
6. V případě potřeby změňte nastavení předplatném a umístění pro virtuální síť.
7. Klikněte na tlačítko **přečíst si právní podmínky**, přečtěte si podmínky a klikněte na tlačítko **nákupu** souhlasit.
8. Klikněte na tlačítko **vytvořit** zahájíte nasazení této šablony.
9. Po nasazení skončí úspěšně, přejděte do skupiny prostředků vytvořené pro toto nasazení, najdete v materiálech nakonfigurované uvnitř.

>[!NOTE]
>Tato šablona umožňuje RDP k serveru IIS01 (Najít veřejné IP adresy pro IIS01 na portálu.). Pro připojení RDP k žádnému back-end serverů v této instanci serveru IIS slouží jako "jump pole." První RDP na server služby IIS a pak z RDP Server služby IIS na back-end serverů.
>
>

Pokud chcete odebrat toto nasazení, odstraňte skupinu prostředků a všechny podřízené prostředky budou také odstraněny.

#### <a name="sample-application-scripts"></a>Ukázkové skripty aplikace
Po úspěšném spuštění šablony můžete nastavit webový server a server aplikace s jednoduchou webovou aplikaci umožňující testování s touto konfigurací hraniční sítě. Instalace ukázkové aplikace pro toto a další příklady hraniční sítě, jednu bylo zadáno na následující odkaz: [ukázkový skript aplikace][SampleApp]

## <a name="next-steps"></a>Další postup

* Tento příklad nasazení
* Vytvoření ukázkové aplikace
* Testování různé přenosové toky prostřednictvím této hraniční sítě

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Příchozí DMZ s NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md