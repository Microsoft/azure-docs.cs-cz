---
title: Příklad hraniční síti Azure – vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě | Dokumentace Microsoftu
description: Vytvoření DMZ pomocí skupin zabezpečení sítě (NSG)
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
ms.openlocfilehash: 2f399b5084ab65736adfebb5cf0a77ccfbc972e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457285"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě pomocí šablony Azure Resource Manageru
[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

> [!div class="op_single_selector"]
> * [Šablona Resource Manageru](virtual-networks-dmz-nsg.md)
> * [Classic – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Tento příklad vytvoří primitivní hraniční sítě se čtyřmi servery Windows a skupiny zabezpečení sítě. Tento příklad popisuje každý oddíl odpovídající šablonu zajistit lepší představu o jednotlivých kroků. Je také část provozu scénář poskytnout podrobný podrobný rozbor jak pokračuje provoz přes vrstev obrany v hraniční síti. Nakonec v odkazech oddíl je kód dokončení šablony a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Příchozí DMZ pomocí skupiny zabezpečení sítě][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu předplatné obsahuje následující prostředky:

* Jedna skupina prostředků
* Virtuální síť se dvěma podsítěmi; "FrontEnd" a "Back-end"
* Skupina zabezpečení sítě, které platí pro obě podsítě
* Windows Server, který představuje aplikační server web (dále jen "IIS01")
* Dva windows serverů, které představují servery back endové aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")
* Veřejnou IP adresu přidruženou k serveru webové aplikace

V části odkazy je odkaz na šablonu Azure Resource Manageru, který vytváří prostředí popsané v tomto příkladu. Vytváření virtuálních počítačů a virtuálních sítí, i když se provádí Ukázková šablona, nejsou v podrobně popsány v tomto dokumentu. 

**K vytvoření tohoto prostředí** (podrobné pokyny najdete v oddílu odkazy tohoto dokumentu);

1. Nasazení šablony Azure Resource Manageru v: [šablony rychlý start Azure][Template]
2. Nainstalovat ukázkovou aplikaci v: [skript ukázkové aplikace][SampleApp]

>[!NOTE]
>Pro protokol RDP na jakékoli servery back-end v tomto případě se používá server služby IIS jako "jump box." První protokol RDP na serveru IIS a pak RDP Server služby IIS na back endového serveru. Můžete také veřejné IP adresy můžou být spojené s každý server síťovou kartu pro snazší protokol RDP.
> 
>

Následující části obsahují podrobný popis skupiny zabezpečení sítě a jak funguje v tomto příkladu pomocí provede klíčové řádky šablony Azure Resource Manageru.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupinu NSG vytvořené a pak načíst šest pravidlům. 

>[!TIP]
>Obecně řečeno byste měli nejprve vytvořit konkrétní pravidla "Povolit" a pak obecnější pravidla "Zakázat" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocují první. Po nalezení provozu použít na příslušné pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít buď ve směru příchozí nebo odchozí (z hlediska podsíť).
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

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Použít zásady zabezpečení na provoz v obou směrech, směrování definovaného uživatele je povinný a je zkoumat "Příklad 3" [stránku osvědčené postupy zabezpečení hranic][HOME].

Každé pravidlo je podrobněji takto:

1. Prostředek skupiny zabezpečení sítě musí být vytvořena pro uložení pravidel:

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

2. V tomto příkladu první pravidlo povoluje provoz DNS mezi všechny interní sítě na serveru DNS na podsíť back-endu. Toto pravidlo má některé důležité parametry:
  * "destinationAddressPrefix" - předpona cílové adresy nastavena na "10.0.2.4" tak, aby provoz DNS se moct připojit k serveru DNS.
  * "Směr" označuje, že ve směru toku provozu toto pravidlo projeví. Směr je z hlediska podsítí nebo virtuálních počítačů (v závislosti na tom, kde je vázána tato skupina zabezpečení sítě). Proto pokud je směr "Příchozí" a provoz vstupující podsítě, pravidlo vztahuje a nebude mít vliv přenosů z podsítě tímto pravidlem.
  * "Priority" Nastaví pořadí, ve kterém je tok přenosů vyhodnoceny. Čím nižší číslo vyšší je priorita. Když pravidlo platí pro konkrétní tok, žádná další pravidla se zpracovávají. Proto pokud pravidlo s prioritou 1 umožňuje provoz a pravidlo s prioritou 2 odepření provozu a obě pravidla se vztahují na provoz pak provoz by bylo možné flow (protože pravidlo 1 má vyšší prioritu vstoupily v platnost a byly použity žádná další pravidla).
  * "Access" označuje, že pokud je provoz, které jsou ovlivněny tímto pravidlem blokované ("Zakázat") nebo povolených ("Povolit").

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

3. Toto pravidlo umožňuje provoz protokolu RDP, které mají být předány portu RDP na libovolný server, na vázané podsítě z Internetu. 

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

4. Toto pravidlo umožňuje příchozího internetového provozu narazila na webovém serveru. Toto pravidlo nezmění chování směrování. Toto pravidlo povoluje jenom provoz určený pro IIS01 předat. Proto pokud měl provoz z Internetu webový server jako svůj cíl toto pravidlo by mohla a zastavit zpracování dalších pravidel. (V pravidlo s prioritou 140 všechny ostatní příchozí internetový provoz blokovaný). Pokud máte pouze zpracování přenosy HTTP, může toto pravidlo dále omezen a Povolit jenom cílový Port 80.

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

5. Toto pravidlo umožňuje přenos dat ze serveru IIS01 na server AppVM01 novější pravidlo blokuje všechny ostatní front-endu do back-endu provoz. K vylepšení tohoto pravidla, pokud se port, který označuje, že by měly být přidány. Například pokud server služby IIS dosahuje pouze systém SQL Server na AppVM01, rozsah cílových portů by měl být změněn z "*" (všechny) 1433 (SQL port), což umožní menší příchozí útoky na AppVM01 by měla webová aplikace stále dojít k ohrožení bezpečnosti.

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

6. Pravidla můžete použít speciální typ předpona adresy se nazývá "Výchozí značka", tyto značky jsou identifikátory poskytnuté systémem, které umožňují snadný způsob, jak adresování větší kategorie předpony adres. Toto pravidlo používá výchozí značka "Virtuální síť" pro Předpona cílové adresy místo jakoukoli adresu v rámci virtuální sítě. Ostatní popisky předpony jsou Internet a AzureLoadBalancer. Toto pravidlo blokuje provoz z Internetu na všechny servery v síti. S pravidly na priority 110 a 120 efekt je povolit pouze příchozího internetového provozu do brány firewall a portů RDP na serverech a bloky všechno ostatní. Toto pravidlo je pravidlo "odolný proti selhání" blokovat všechny neočekávané toky.

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

7. Konečné pravidlo odepření provozu z podsítě front-endu do back-endové podsítě. Toto pravidlo je pouze příchozí pravidlo, je povolen zpětný provoz (z back-end k front-endu).

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
#### <a name="allowed-internet-to-web-server"></a>(*Povolené*) Internetu, aby webový server
1. Internetu uživatel požádá o stránku protokolu HTTP z veřejné IP adresy přidružené k síťovému rozhraní IIS01 síťové karty
2. Veřejná IP adresa předává provoz do virtuální sítě směrem k IIS01 (webový server)
3. Front-endové podsítě začíná zpracování příchozí pravidlo:
  1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
  2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
  3. Vztahuje se 3 pravidlo skupiny zabezpečení sítě (Internet k IIS01), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa webového serveru IIS01 (adresa 10.0.1.5)
5. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
6. IIS01 dotazem SQL serveru na AppVM01 informace
7. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
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
13. Protože nejsou žádná odchozí pravidla ve front-endové podsíti, odpovědi je povolený a Internet uživatel obdrží požadované webové stránky.

#### <a name="allowed-rdp-to-iis-server"></a>(*Povolené*) připojení RDP k serveru služby IIS
1. Správce serveru k Internetu vyžaduje relaci RDP na IIS01 na veřejnou IP adresu síťové karty přidružená k síťové KARTĚ IIS01 (tuto veřejnou IP adresu můžete najít pomocí portálu nebo Powershellu)
2. Veřejná IP adresa předává provoz do virtuální sítě směrem k IIS01 (webový server)
3. Front-endové podsítě začíná zpracování příchozí pravidlo:
  1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
  2. Použít pravidlo NSG 2 (RDP), provoz je povolený, zastavte pravidla zpracování
4. Žádná odchozí pravidla použít výchozí pravidla a zpětný provoz je povolený.
5. Povolené relace protokolu RDP
6. IIS01 vyzve k zadání uživatelského jména a hesla

>[!NOTE]
>Pro protokol RDP na jakékoli servery back-end v tomto případě se používá server služby IIS jako "jump box." První protokol RDP na serveru IIS a pak RDP Server služby IIS na back endového serveru.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Byl odepřen*) připojení RDP k back-endu
1. Internet uživatele pokusí se protokolu RDP na server AppVM01
2. Protože nejsou žádné veřejné IP adresy přidružené k této síťové karty serverům, tento provoz by nikdy zadejte virtuální síť a nebude připojit k serveru
3. Pokud z nějakého důvodu byl povolen veřejnou IP adresu, ale by NSG rule 2 (RDP) umožňují tento přenos

>[!NOTE]
>Pro protokol RDP na jakékoli servery back-end v tomto případě se používá server služby IIS jako "jump box." První protokol RDP na serveru IIS a pak RDP Server služby IIS na back endového serveru.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Byl odepřen*) Web back-end server
1. Uživatel s Internetu pokusí o přístup k souboru na AppVM01
2. Protože nejsou žádné veřejné IP adresy přidružené k této síťové karty serverům, tento provoz by nikdy zadejte virtuální síť a nebude připojit k serveru
3. Pokud z nějakého důvodu byl povolen veřejnou IP adresu, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Byl odepřen*) vyhledávací Web DNS na serveru DNS
1. Internet uživatele se pokusí vyhledat záznam interní DNS DNS01
2. Protože nejsou žádné veřejné IP adresy přidružené k této síťové karty serverům, tento provoz by nikdy zadejte virtuální síť a nebude připojit k serveru
3. Pokud z nějakého důvodu byl povolen veřejnou IP adresu, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz (Poznámka:, že pravidlo 1 (DNS) nedají použít, protože požadavky zdrojová adresa je internet a 1 pravidlo platí jenom pro místní virtuální sítě jako zdroj)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Byl odepřen*) přístup k SQL na webovém serveru
1. Internet uživatele požádá IIS01 dat SQL
2. Protože nejsou žádné veřejné IP adresy přidružené k této síťové karty serverům, tento provoz by nikdy zadejte virtuální síť a nebude připojit k serveru
3. Pokud z nějakého důvodu byl povolen veřejnou IP adresu, podsíť Frontend začíná zpracování příchozí pravidlo:
  1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
  2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
  3. Vztahuje se 3 pravidlo skupiny zabezpečení sítě (Internet k IIS01), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa IIS01 (adresa 10.0.1.5)
5. IIS01 nenaslouchá na portu 1433, takže žádná odezva na žádosti

## <a name="conclusion"></a>Závěr
V tomto příkladu je poměrně jednoduchá a rovnou dopředné způsob izolace back endové podsítě z příchozí provoz.

Další příklady a základní informace o hranicích zabezpečení sítě najdete [tady][HOME].

## <a name="references"></a>Odkazy
### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Tento příklad používá předdefinované šablony Azure Resource Manageru v úložišti GitHub udržuje Microsoft a otevřené celé komunitě. Tato šablona je možné nasadit přímo z webu GitHub, nebo stáhnout a upravit tak, aby vyhovoval vašim požadavkům. 

Hlavní šablony je v souboru s názvem "azuredeploy.json." Tato šablona jde odeslat prostřednictvím Powershellu nebo rozhraní příkazového řádku (souborem přidružené "azuredeploy.parameters.json") k nasazení této šablony. Můžu najít že nejjednodušší způsob je pomocí tlačítka "Nasazení do Azure" na stránce README.md v Githubu.

Pokud chcete nasadit šablonu, která vytvoří v tomto příkladu z Githubu a na webu Azure portal, postupujte podle těchto kroků:

1. V prohlížeči přejděte [šablony][Template]
2. Klikněte na tlačítko "Nasazení do Azure" (nebo na tlačítko "Vizualizovat" Chcete-li zobrazit grafická reprezentace této šablony)
3. Zadejte účet úložiště, uživatelské jméno a heslo v okně parametry a potom klikněte na **OK**
5. Vytvořte skupinu prostředků pro toto nasazení (můžete použít některý z existujících, ale můžu jenom doporučit nový pro dosažení co nejlepších výsledků)
6. V případě potřeby změňte nastavení předplatném a lokalitě pro vaši virtuální síť.
7. Klikněte na tlačítko **přečíst si právní podmínky**, přečtěte si podmínky a klikněte na tlačítko **nákupní** souhlas.
8. Klikněte na tlačítko **vytvořit** zahájíte nasazení této šablony.
9. Po úspěšném dokončení nasazení přejděte do skupiny prostředků vytvořené pro toto nasazení, najdete v materiálech nakonfigurována ve.

>[!NOTE]
>Tato šablona umožňuje připojení RDP k serveru IIS01 (vyhledání veřejné IP adresy pro IIS01 na portálu). Pro protokol RDP na jakékoli servery back-end v tomto případě se používá server služby IIS jako "jump box." První protokol RDP na serveru IIS a pak RDP Server služby IIS na back endového serveru.
>
>

Odebrat toto nasazení, odstraňte skupinu prostředků a všech jejích podřízených prostředků budou také odstraněny.

#### <a name="sample-application-scripts"></a>Ukázky skriptů aplikace
Po úspěšném spuštění šablony můžete nastavit webový server a server aplikace s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ. Pokud chcete nainstalovat ukázkovou aplikaci pro tuto a další příklady DMZ, jeden byl poskytnut na následující odkaz: [skript ukázkové aplikace][SampleApp]

## <a name="next-steps"></a>Další postup

* V tomto příkladu nasazení
* Vytvoření ukázkové aplikace
* Testování jiný přenos prochází tato hraniční sítě

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Příchozí DMZ pomocí skupiny zabezpečení sítě"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md
