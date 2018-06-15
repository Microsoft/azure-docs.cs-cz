---
title: Příklad DMZ – sestavení DMZ k ochraně aplikací s bránou Firewall a skupin Nsg | Microsoft Docs
description: Sestavení DMZ s bránou Firewall a skupiny zabezpečení sítě (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885123"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Příklad 2 – Vytvoření DMZ k ochraně aplikací s bránou Firewall a skupiny Nsg
[Návrat na stránku osvědčené postupy zabezpečení hranic][HOME]

Tento příklad vytvoří DMZ s bránou firewall, čtyři windows serverů a skupin zabezpečení sítě. Je také provede každou z relevantních příkazů, které poskytují podrobnější vysvětlení jednotlivých kroků. Je také části provoz scénář zajistit podrobné krok za krokem, jak se provoz pokračuje prostřednictvím vrstev obrany v hraniční síti. Nakonec v odkazy na části je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![Příchozí DMZ s hodnocení chyb zabezpečení a NSG][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu je odběr, který obsahuje následující:

* Dva cloudové služby: "FrontEnd001" a "BackEnd001"
* Virtuální síť "CorpNetwork" se dvěma podsítěmi: "FrontEnd" a "Back-end"
* Jednu skupinu zabezpečení sítě, která se použije pro obě podsítě
* Virtuální zařízení sítě, v tomto příkladu Barracuda NextGen Firewall, připojený k podsíti front-endu
* Windows Server, který představuje server webových aplikací ("IIS01")
* Dva windows servery, které představují aplikace zpět ukončení servery ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

> [!NOTE]
> I když tento příklad používá Barracuda NextGen Firewall, řadu různých síťových virtuálních zařízení by mohly být použity v tomto příkladu.
> 
> 

V části odkazy níže je skript prostředí PowerShell, který bude vytvořit většinu prostředí popsané výše. Vytváření virtuálních počítačů a virtuálních sítí, i když provádějí ukázkový skript, nejsou podrobně popsané v tomto dokumentu.

K vytvoření prostředí:

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizovat název, umístění a IP adresy, které odpovídají danému scénáři)
2. Aktualizace uživatelské proměnné ve skriptu tak, aby odpovídaly prostředí, ve kterém je skript ke spouštění (odběry, názvy služeb atd.)
3. Spusťte skript v prostředí PowerShell

**Poznámka:**: oblasti označený ve skriptu PowerShell musí odpovídat oblasti označeny v souboru xml konfigurace sítě.

Po úspěšném spuštění skriptu mohou být přijata po skriptu takto:

1. Nastavit pravidla brány firewall, najdete v následující části s názvem: pravidla brány Firewall.
2. Volitelně můžete v části odkazy jsou dva skripty k nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ.

V další části vysvětluje většinu příkazů skripty, relativně k skupin zabezpečení sítě.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupina NSG vytvořené a pak načtená šesti pravidla. 

> [!TIP]
> Obecně řečeno měli byste vytvořit konkrétní pravidel "Povolit" nejprve a pak více obecná pravidla "Deny" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocena první. Jakmile provoz nenajde Pokud chcete použít pro konkrétní pravidlo, jsou vyhodnotit žádná další pravidla. Pravidla NSG můžete použít buď v příchozí nebo odchozí směr (z hlediska podsítě).
> 
> 

Následující pravidla deklarativně, se budou vytvářeny pro příchozí provoz:

1. Interní DNS provoz (port 53) je povolený
2. Provoz protokolu RDP (portu 3389) z Internetu do všech virtuálních počítačů je povoleno.
3. Je povolen přenos HTTP (port 80) z Internetu do hodnocení chyb zabezpečení sítě (brány firewall)
4. Všechny přenosy (všechny porty) z IIS01 na AppVM1 je povolen.
5. Přenosy dat (všechny porty) z Internetu pro celou virtuální síť (obě podsítě) byl odepřen.
6. Přenosy dat (všechny porty) z podsítě front-endu do podsítě back-end byl odepřen.

Pomocí těchto pravidel vázána na každou podsíť, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (Odepřít) by použít, ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu jenom by použít a pravidlo 5 nebude možné uplatnit. Proto bude mít možnost požadavek HTTP do brány firewall. Pokud tento stejný provoz se pokusil připojit k serveru DNS01, pravidlo 5 (Odepřít) bude první použití a přenos nebude možné předat serveru. Pravidlo 6 (Odepřít) blokuje podsíť Frontend z rozhovoru s back-end podsítě (s výjimkou povolené přenosy v pravidlech 1 a 4), to chrání síť back-end v případě ohrožení útočník webové aplikace na Frontendový, útočník by mít omezený přístup k back-end "chráněná" Síťová (pouze pro prostředky zveřejněné na serveru AppVM01).

Je výchozí odchozí pravidlo, které umožňuje přenos se k Internetu. V tomto příkladu jsme se umožňuje odchozí provoz a úprava není žádná odchozí pravidla. Zamknout provoz v jak pokynů, směrování definovaného uživatele je vyžadován, to je v jiné příkladu, který lze nalézt v prozkoumali [hlavní zabezpečení hranic dokumentu][HOME].

Výše uvedené pravidla NSG jsou velmi podobné pravidla NSG v [Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin Nsg][Example1]. Přečtěte si popis NSG v tomto dokumentu pro podrobný pohled na každé pravidlo NSG a jeho atributy.

## <a name="firewall-rules"></a>Pravidla brány firewall
Klient správy bude potřeba nainstalovat na počítač pro správu brány firewall a vytvoření konfigurací potřeby. O tom, jak spravovat zařízení, najdete v článku dodavatele dokumentace z brány firewall (nebo jiné hodnocení chyb zabezpečení). Zbytek této části se popisují konfiguraci brány firewall, samostatně, prostřednictvím dodavatele správy klienta (tzn. ne portál Azure nebo PowerShell).

Pokyny pro stažení klienta a připojení k Barracuda použité v tomto příkladu naleznete zde: [Barracuda NG správce](https://techlib.barracuda.com/NG61/NGAdmin)

V bráně firewall předávání pravidla bude nutné vytvořit. Vzhledem k tomu, že v tomto příkladu pouze trasy internetové přenosy v vázané do brány firewall a poté na webový server, je potřeba jenom jeden předávání pravidlo NAT. V bráně Barracuda NextGen Firewall použité v tomto příkladu bude pravidlo NAT cílové pravidlo ("Dst NAT"), abyste předat tento provoz.

Vytvořit následující pravidlo (nebo ověřit existující výchozí pravidla), od řídicím panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v provozní konfiguraci oddíl, klikněte na Ruleset. Mřížka názvem, zobrazí "Hlavní pravidla" existující pravidla aktivní a deaktivované v bráně firewall. V pravém horním rohu mřížce je malý, zelená "+" tlačítko, klepněte sem a vytvořit nové pravidlo (Poznámka: Brána firewall může "zamknout" změny, pokud se zobrazí tlačítko označeno "Zamknout" a nelze vytvořit nebo upravit pravidla, klikněte na toto tlačítko a "odemknout" Sada pravidel pro povolení úprav). Pokud chcete upravit existující pravidlo, vyberte toto pravidlo, klikněte pravým tlačítkem a vyberte Upravit pravidlo.

Vytvořit nové pravidlo a zadejte název, jako je například "WebTraffic". 

Ikona pravidlo NAT cílové vypadá takto: ![ikonu cílové NAT][2]

Pravidlo samotné by vypadat přibližně takto:

![Pravidlo brány firewall][3]

Zde žádné příchozí adresu, která dotkne bránu Firewall při pokusu o přístup protokolu HTTP (port 80 nebo 443 pro protokol HTTPS) budou odeslány mimo bránu Firewall "Místní IP adresy DHCP1" rozhraní a přesměruje na webový server s IP adresou 10.0.1.5. Vzhledem k tomu, že je provoz brzo na portu 80 a přejdete na webový server na portu 80 bylo potřeba žádné změny portu. Ale cílového seznamu by byly 10.0.1.5:8080 Pokud naslouchali naše Webový Server na portu 8080, proto překladu příchozí port 80 v bráně firewall pro příchozí port 8080 na webovém serveru.

Metodu připojení musí také být označeny, cílový pravidla z Internetu, "Dynamické překládat pomocí SNAT" je nejvhodnější. 

I když existuje pouze jedno pravidlo je důležité, aby jeho priorita je nastavena správně. Pokud v mřížce všechna pravidla v bráně firewall toto nové pravidlo je v dolní (pod pravidlo "BLOCKALL") se nikdy přijít do play. Zajistěte, aby nově vytvořeného pravidla pro webový provoz je větší než BLOCKALL pravidlo.

Po vytvoření pravidla, musí být nabídnutých do brány firewall a pak se aktivuje, pokud to neuděláte pravidlo změny se neprojeví. Proces nabízené a aktivace je popsaná v další části.

## <a name="rule-activation"></a>Aktivace pravidla
S ruleset upravit tak, aby přidat toto pravidlo musí být nahrán do brány firewall a aktivovat ruleset.

![Aktivace pravidla brány firewall][4]

V pravém horním rohu správy klienta jsou součástí clusteru tlačítka. Kliknutím na tlačítko "Změny odeslat" poslat upravené pravidla brány firewall a potom klikněte na tlačítko "Aktivovat".

S aktivace sada pravidel brány firewall pro tento příklad prostředí sestavení je dokončena. Volitelně lze spustit skripty sestavení post v části odkazy a přidání aplikace do tohoto prostředí pro testování níže provoz scénáře.

> [!IMPORTANT]
> Je důležité si uvědomit, že nedosáhnete stropu webového serveru přímo. Pokud prohlížeč požaduje stránku HTTP z FrontEnd001.CloudApp.Net, koncový bod HTTP (port 80) předá tento provoz není webový server do brány firewall. Brána firewall pak podle pravidla vytvořili výše, zařízení NAT, které požadují k webovému serveru.
> 
> 

## <a name="traffic-scenarios"></a>Provoz scénáře
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Povoleno) Web pro webový Server přes bránu Firewall
1. Internet uživatelské požadavky HTTP stránky z FrontEnd001.CloudApp.Net (Internet čelí cloudové služby)
2. Cloudové služby předává přenos prostřednictvím otevřených koncových bodů na portu 80 k rozhraní místní brány firewall na 10.0.1.4:80
3. Podsíť frontend zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Použít NSG pravidla 3 (Internet do brány Firewall), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu brány firewall (10.0.1.4)
5. To je provoz na portu 80, přesměruje na webový server IIS01 najdete v části předávání pravidlo brány firewall
6. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
7. IIS01 systému SQL Server na AppVM01 vyzve k zadání informací
8. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
9. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Není použít, přejděte k další pravidla NSG pravidla 3 (Internet do brány Firewall)
   4. Skupina NSG pravidla 4 použít (IIS01 k AppVM01), provoz je povolený, zastavte zpracování pravidla
10. AppVM01 přijme příkaz jazyka SQL a odpovídá
11. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti back-end je povoleno odpovědi
12. Podsíť frontend zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
    2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povolen.
13. Server služby IIS obdrží odpověď SQL a dokončení odpovědi HTTP a odešle do žadatel
14. Vzhledem k tomu, že je relace NAT z brány firewall, cílový odpovědi (původně) je pro bránu Firewall
15. Bránu firewall, obdrží odpověď z webového serveru a předá zpět do Internetu uživatele
16. Vzhledem k tomu, že neexistují žádná odchozí pravidla na podsíť Frontend odpověď je povoleno a Internet uživatel obdrží požadované webové stránky.

#### <a name="allowed-rdp-to-backend"></a>(Povoleno) RDP na back-end
1. Správce serveru na Internetu požadavky protokolu RDP relace AppVM01 na BackEnd001.CloudApp.Net:xxxxx kde xxxxx je číslo náhodně přidělenému portu pro protokol RDP na AppVM01 (přidělenému portu naleznete na portálu Azure nebo pomocí prostředí PowerShell)
2. Vzhledem k tomu, že brána Firewall naslouchá jenom na adresu FrontEnd001.CloudApp.Net, není zapojená tento tok provozu
3. Back-end podsíť zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Použít NSG pravidlo 2 (RDP), Probíhá zpracování povolených, zastavení pravidla
4. Žádná odchozí pravidla použít výchozí pravidla a návratový provoz je povolený
5. Je povoleno relaci protokolu RDP.
6. AppVM01 vyzve k zadání názvu heslo uživatele

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povoleno) Webový Server DNS vyhledávání na serveru DNS
1. Webový Server, IIS01, požadavky datového kanálu v www.data.gov, ale musí pro překlad adres.
2. Konfigurace sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v podsíti back-end) jako primární server DNS, IIS01 odešle žádost DNS do DNS01
3. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
4. Back-end podsíť zahájí zpracování příchozí pravidlo:
   1. Použít NSG pravidlo 1 (DNS), Probíhá zpracování povolených, zastavení pravidla
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Povoleno) Přístup k souboru webového serveru na AppVM01
1. IIS01 požádá o soubor na AppVM01
2. Žádná odchozí pravidla na podsíť Frontend provoz je povolený.
3. Podsíť back-end zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Není použít, přejděte k další pravidla NSG pravidla 3 (Internet do brány Firewall)
   4. Skupina NSG pravidla 4 použít (IIS01 k AppVM01), provoz je povolený, zastavte zpracování pravidla
4. AppVM01 obdrží požadavek a odpoví souboru (za předpokladu, že je autorizovaný přístup)
5. Vzhledem k tomu, že neexistují žádná odchozí pravidla v podsíti back-end je povoleno odpovědi
6. Podsíť frontend zahájí zpracování příchozí pravidlo:
   1. Neexistuje žádná skupina NSG pravidlo, které platí pro příchozí provoz z back-end podsítě pro podsíť Frontend, aby žádný z NSG pravidla použít
   2. Výchozí pravidlo systému umožňuje provoz mezi podsítěmi by povolit tento provoz, provoz je povolen.
7. Server služby IIS obdrží soubor

#### <a name="denied-web-direct-to-web-server"></a>(Byl odepřen) Web přímo na webovém serveru
Vzhledem k tomu, že webový Server, IIS01 a brány Firewall jsou ve stejné cloudové službě sdílejí stejnou veřejné přístupných IP adresu. Přenosy dat protokolu HTTP by proto přesměrováni do brány firewall. Při požadavku by úspěšně zprostředkoval, nelze přejít přímo na webovém serveru, je předán, tak, jak má, přes bránu Firewall nejdřív. V tématu prvního scénáře v této části pro tok přenosů.

#### <a name="denied-web-to-backend-server"></a>(Byl odepřen) Webový server back-end
1. Internet uživatel pokusí přistoupit k souboru na AppVM01 prostřednictvím služby BackEnd001.CloudApp.Net
2. Vzhledem k tomu, že jsou pro sdílené složky otevřené žádné koncové body, se nebude předat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, by tento provoz blokovat pravidla NSG 5 (Internet do virtuální sítě)

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Byl odepřen) Vyhledávání DNS web na serveru DNS
1. Internet uživatel se pokusí vyhledat interní DNS záznam na DNS01 prostřednictvím služby BackEnd001.CloudApp.Net
2. Vzhledem k tomu, že jsou pro DNS otevřené žádné koncové body, se nebude předat cloudové služby a nebude moci připojit k serveru
3. Pokud z nějakého důvodu byly otevřené koncových bodů, pravidla NSG 5 (Internet do virtuální sítě) by blokovat tento provoz (Poznámka: Tento pravidlo 1 (DNS) nebude použít dvou důvodů, nejprve zdrojové adresy je Internetu, toto pravidlo se vztahuje pouze na místní virtuální síť jako zdroj, také to je pravidlo povolit, protože by nikdy zakážou provoz)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Byl odepřen) Webový přístup SQL pomocí brány Firewall
1. Internet uživatel požádá o dat SQL z FrontEnd001.CloudApp.Net (Internet čelí cloudové služby)
2. Vzhledem k tomu, že nejsou otevřené pro SQL koncové body, se nebude předat cloudové služby a nebude kontaktovat bránu firewall
3. Pokud z nějakého důvodu byly otevřené koncových bodů, podsíť Frontend zahájí zpracování příchozí pravidlo:
   1. Není použít, přejděte k další pravidla NSG pravidlo 1 (DNS)
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Použít NSG pravidla 2 (Internet do brány Firewall), Probíhá zpracování povolených, zastavení pravidla
4. Provoz dotkne interní IP adresu brány firewall (10.0.1.4)
5. Brány firewall pro SQL nemá žádná pravidla pro předávání a zahodí provoz

## <a name="conclusion"></a>Závěr
Toto je poměrně splněny následující způsob ochraně vaší aplikace s bránou firewall a izolace back-end podsíť z příchozí přenosy.

Další příklady a přehled hranice zabezpečení sítě najdete [sem][HOME].

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní skript a konfiguraci sítě
Uložte úplné skript v souboru skriptu prostředí PowerShell. Uložte konfiguraci sítě do souboru s názvem "NetworkConf2.xml".
Podle potřeby změňte proměnné definované uživatelem. Spusťte skript a potom postupujte podle pokynů instalace pravidlo brány Firewall výše.

#### <a name="full-script"></a>Úplné skriptu
Tento skript bude na základě uživatelsky definované proměnných:

1. Připojení k předplatnému Azure
2. Vytvořit nový účet úložiště
3. Vytvořit novou virtuální síť a dvě podsítě, jak jsou definovány v souboru konfigurace sítě
4. Sestavení 4 windows server virtuálních počítačů
5. Konfigurace, včetně NSG:
   * Vytváření skupina NSG
   * Sestavování s pravidly
   * Vytvoření vazby skupinu NSG na příslušné podsítě

Tento skript prostředí PowerShell je vhodné spustit místně na Internetu připojený počítač nebo server.

> [!IMPORTANT]
> Když tento skript se spustí, může být upozornění nebo ostatní informační zprávy, které pop v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou problém.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Příchozí DMZ s NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ikona cílové NAT"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Pravidlo brány firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktivace pravidla brány firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
