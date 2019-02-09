---
title: DMZ příklad – vytvoření DMZ k ochraně aplikací s bránou Firewall a skupin zabezpečení sítě | Dokumentace Microsoftu
description: Vytvoření DMZ pomocí brány Firewall a skupin zabezpečení sítě (NSG)
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
ms.openlocfilehash: 31d945f64cccd0c811d4dc45163583224102fb8a
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965235"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Příklad 2 – Vytvoření DMZ k ochraně aplikací s bránou Firewall a skupin zabezpečení sítě
[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

V tomto příkladu bude vytvoření DMZ pomocí brány firewall, čtyři windows serverů a skupin zabezpečení sítě. To vás také provede všechny relevantní příkazy zajistit lepší představu o jednotlivých kroků. Je také část provozu scénář k poskytování podrobné krok za krokem, jak se provoz pokračuje přes vrstev obrany v hraniční síti. Nakonec v odkazech oddíl je kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a experimentovat s různými scénáři. 

![Příchozí DMZ pomocí síťového virtuálního zařízení a skupiny zabezpečení sítě][1]

## <a name="environment-description"></a>Popis prostředí
V tomto příkladu je předplatné, které obsahuje následující:

* Dva cloudových služeb: "FrontEnd001" a "BackEnd001"
* Virtuální síť "CorpNetwork" se dvěma podsítěmi: "FrontEnd" a "Back-end"
* Jednu skupinu zabezpečení sítě, které platí pro obě podsítě
* Síťové virtuální zařízení, v tomto příkladu Barracuda NextGen Firewall, připojený k podsíti Frontend
* Windows Server, který představuje aplikační server web (dále jen "IIS01")
* Dva windows serverů, které představují zpět aplikaci ukončit servery ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

> [!NOTE]
> Přestože tento příklad používá Barracuda NextGen Firewall, řadu různých síťových virtuálních zařízení lze použít v tomto příkladu.
> 
> 

V části odkazy níže je skript prostředí PowerShell, který sestaví většinu prostředí, je popsáno výše. Vytváření virtuálních počítačů a virtuálních sítí, i když se provádí ukázkový skript, nejsou v podrobně popsány v tomto dokumentu.

Sestavení prostředí:

1. Uložte soubor xml konfigurace sítě v oddíle odkazy (aktualizováno s názvy, umístění a IP adresy, které odpovídají situaci)
2. Aktualizace uživatelské proměnné ve skriptu, tak, aby odpovídaly prostředí, ve kterém je skript běžet před (předplatné, názvy služeb atd.)
3. Spusťte skript prostředí PowerShell

**Poznámka:** Oblast označeny Powershellového skriptu musí odpovídat oblasti označeny v souboru xml konfigurace sítě.

Po úspěšném spuštění skriptu bude může provést následující kroky pozálohovací skript:

1. Nastavení pravidel brány firewall, tento proces je popsán v následující části s názvem: Pravidla brány firewall.
2. Volitelně můžete v části odkazy jsou dva skripty pro nastavení webového serveru a aplikačního serveru s jednoduchou webovou aplikaci umožňující testování s touto konfigurací DMZ.

V další části vysvětluje většinu příkazů skriptů vzhledem k skupiny zabezpečení sítě.

## <a name="network-security-groups-nsg"></a>Skupiny zabezpečení sítě (NSG)
V tomto příkladu je skupina NSG vytvořené a pak načíst šest pravidlům. 

> [!TIP]
> Obecně řečeno byste měli nejprve vytvořit konkrétní pravidla "Povolit" a pak obecnější pravidla "Zakázat" poslední. Přiřazené priority určují, které jsou pravidla vyhodnocují první. Po nalezení provozu použít na příslušné pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít buď ve směru příchozí nebo odchozí (z hlediska podsíť).
> 
> 

Deklarativní byly sestaveny pro příchozí provoz následující pravidla:

1. Interní DNS provoz (port 53) je povolený.
2. Je povolený provoz protokolu RDP (portu 3389) z Internetu do všech virtuálních počítačů
3. Je povolený provoz protokolu HTTP (port 80) z Internetu do síťových virtuálních zařízení (Brána firewall)
4. Je povolen veškerý provoz (všechny porty) z IIS01 k AppVM1
5. Veškerý provoz (všechny porty) z Internetu do celé virtuální síť (obě podsítě) byl odepřen.
6. Veškerý provoz (všechny porty) z podsítě front-endu do back-endové podsítě je odepřen.

Pomocí těchto pravidel vázán ke každé podsíti, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (zabránění) se vztahují, ale protože pouze by použít a pravidlo 5 by souvisejí, má vyšší prioritu pravidla 3. Požadavek HTTP se proto by bylo možné bránu firewall. Pokud tento stejný provoz se snažil to spojit se serverem DNS01, pravidlo 5 (odmítnout) by se použít jako první a provoz nebude možné předat serveru. Pravidlo 6 (odmítnout) blokuje front-endové podsítě z komunikaci na podsíť back-endu (s výjimkou povolený provoz v pravidlech 1 a 4), tím se zajistí ochrana síť back-end v případě, že ohrožení útočník k webové aplikaci na front-endu, útočník bude omezený přístup back-end "protected" sítě (jenom k prostředkům zveřejněné na AppVM01 server).

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Zamezit provoz v obou směrech, směrování definovaného uživatele se vyžaduje, to je prozkoumali v jiném příkladu, který lze nalézt v [hlavní zabezpečení hranic dokumentu][HOME].

Popsané výše uvedených pravidel skupiny zabezpečení sítě jsou velmi podobné pravidla skupiny zabezpečení sítě v [– Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě][Example1]. Přečtěte si prosím popis skupiny zabezpečení sítě v tomto dokumentu podrobný pohled na každé pravidlo skupiny zabezpečení sítě a jeho atributy.

## <a name="firewall-rules"></a>Pravidla brány firewall
Klienta správy bude potřeba nainstalovat na počítači pro správu brány firewall a vytváření konfigurací potřeba. O správě zařízení najdete v článku dodavatele dokumentace ke službě z vaší brány firewall (nebo jiné síťové virtuální zařízení). Zbytek tohoto oddílu popisuje konfigurace, brány firewall prostřednictvím dodavatele správy klienta (tzn. ne Azure portal nebo Powershellu).

Pokyny pro stažení klienta a připojení k Barracuda použitý v tomto příkladu najdete tady: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

V bráně firewall pravidla předávání bude nutné vytvořit. Protože v tomto příkladu pouze směruje přenosy z Internetu ve vázané na bránu firewall a potom na webový server, je potřeba jenom jeden předávání pravidlo překladu adres. Pravidlo v bráně Firewall Barracuda NextGen použitý v tomto příkladu bude pravidlo NAT cíl ("letního času NAT"), abyste tento provoz.

Vytvořit následující pravidlo (nebo ověřit existující výchozí pravidla), od řídicího panelu Barracuda NG správce klienta, přejděte na kartu Konfigurace, v provozní konfigurační oddíl, klikněte na sady pravidel. Mřížka volat, "Hlavní pravidla" se zobrazí existující pravidla aktivní a deaktivované v bráně firewall. V pravém horním rohu tuto mřížku je malá zelená "+" tlačítko, klepnutím na toto tlačítko Vytvořit nové pravidlo (Poznámka: vaše brána firewall může být "uzamčen" změny, pokud se zobrazí tlačítko označený "Lock" a nejde vytvořit nebo upravit pravidla, klikněte na toto tlačítko "odemknout" Sada pravidel a Povolte úpravy). Pokud chcete upravit stávající pravidlo, vyberte toto pravidlo, klikněte pravým tlačítkem a vyberte Upravit pravidlo.

Vytvořit nové pravidlo a zadejte název, jako je například "WebTraffic". 

Ikona pravidel NAT cílové vypadá takto: ![Určení ikony NAT][2]

Pravidlo samotné by vypadat přibližně takto:

![Pravidlo brány firewall][3]

Zde žádné příchozí adresu, kterou narazí na bránu Firewall při pokusu o přístup protokolu HTTP (port 80 nebo 443 pro protokol HTTPS) budou odeslány mimo brány Firewall na "Místní IP Adrese počítače DHCP1" rozhraní a přesměruje na webový Server s adresou IP adresa 10.0.1.5. Jelikož je příchozí na portu 80 a přechod na webový server na portu 80 provoz portu byla nutná žádná. Ale cílový seznam mohlo být 10.0.1.5:8080 Pokud naslouchali náš webový Server na portu 8080, tedy překladu příchozí port 80 v bráně firewall na portu 8080 pro příchozí spojení na webovém serveru.

Způsob připojení by měl také být označeny, pro cílové pravidlo z Internetu, že je nejvhodnější "Dynamické SNAT". 

I když byl vytvořen pouze jedno pravidlo je důležité, jestli je správně nastavené prioritu. V mřížce všechna pravidla v bráně firewall toto nové pravidlo je v dolní části (nižší než pravidla "BLOCKALL") do hry se nikdy přijde. Zajistěte, aby nově vytvořeného pravidla pro webový provoz je vyšší než BLOCKALL pravidlo.

Jakmile se vytvoří pravidlo, musí být vloženo do brány firewall a pak se aktivuje, pokud tato podmínka není splněna pravidla změna projeví. Nabízená a aktivační proces je popsaný v další části.

## <a name="rule-activation"></a>Pravidlo aktivace
Pomocí pravidel upravit a přidat toto pravidlo musí být nahráli do brány firewall a aktivovat sady pravidel.

![Aktivace pravidla brány firewall][4]

V pravém horním rohu okna správy klienta se cluster tlačítek. Klikněte na tlačítko "Odeslat změny" a odeslat upravenou pravidla brány firewall a potom klikněte na tlačítko "Aktivovat".

Díky aktivaci sady pravidel brány firewall prostředí sestavení tohoto příkladu je dokončena. Volitelně lze spustit skripty sestavení příspěvek v části odkazy pro přidání aplikace do tohoto prostředí pro testování následující scénáře provoz.

> [!IMPORTANT]
> Je důležité si uvědomit, že nebude dosáhnete webový server přímo. Když prohlížeč požádá FrontEnd001.CloudApp.Net stránku protokolu HTTP, koncový bod protokolu HTTP (port 80) předá tento přenos není webový server do brány firewall. Bránu firewall pak podle pravidlo vytvořené výše NAT, které požadují k webovému serveru.
> 
> 

## <a name="traffic-scenarios"></a>Provoz scénáře
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Povolena) Web pro webový Server přes bránu Firewall
1. Internet uživatelské požadavky HTTP stránky FrontEnd001.CloudApp.Net (Internet směřující cloudové služby)
2. Cloudové služby předává provoz přes otevřených koncových bodů na portu 80 na místní rozhraní brány firewall na 10.0.1.4:80
3. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Vztahuje se 3 pravidlo skupiny zabezpečení sítě (Internet do brány Firewall), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa brány firewall (10.0.1.4)
5. To je provoz na portu 80, přesměruje na webový server IIS01 naleznete v tématu pravidlo brány firewall pro předávání
6. IIS01 naslouchá pro webový provoz, získá tento požadavek a spustí zpracování požadavku
7. IIS01 dotazem SQL serveru na AppVM01 informace
8. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
9. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. 3 pravidlo skupiny zabezpečení sítě (Internet do brány Firewall) nebude použít, přejděte k další pravidla
   4. 4 pravidlo skupiny zabezpečení sítě (IIS01 k AppVM01) použít, je povolený provoz, zastavit zpracování pravidla
10. AppVM01 obdrží dotaz SQL a odpovídá
11. Protože nejsou žádná odchozí pravidla na back-endové podsítě je povolena odpovědi
12. Front-endové podsítě začíná zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz.
13. Server služby IIS obdrží odpověď SQL a dokončí odpovědi HTTP a pošle žadateli
14. Protože NAT relace ze brána firewall, cíl odpovědi (zpočátku) je pro bránu Firewall
15. Brána firewall obdrží odpověď od serveru a předá zpět do Internetu uživatele
16. Vzhledem k tomu, že jsou povolená žádná odchozí pravidla ve front-endové podsíti odpovědi a Internet uživatel obdrží požadované webové stránky.

#### <a name="allowed-rdp-to-backend"></a>(Povolena) Připojení RDP k back-endu
1. Správce serveru k Internetu vyžaduje relaci RDP na AppVM01 na BackEnd001.CloudApp.Net:xxxxx kde xxxxx je počet náhodně přidělenému portu pro připojení RDP k AppVM01 (přidělenému portu najdete na webu Azure Portal nebo prostřednictvím prostředí PowerShell)
2. Vzhledem k tomu, že brána Firewall naslouchá jenom na adrese FrontEnd001.CloudApp.Net, není zapojená tento tok provozu
3. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Použít pravidlo NSG 2 (RDP), provoz je povolený, zastavte pravidla zpracování
4. Žádná odchozí pravidla použít výchozí pravidla a zpětný provoz je povolený.
5. Povolené relace protokolu RDP
6. AppVM01 vyzve k zadání uživatelského jména hesla

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povolena) Webové vyhledávání serveru DNS na serveru DNS
1. Webový Server, IIS01, potřebám datového kanálu na www.data.gov, ale potřebám pro překlad adres.
2. Konfiguraci sítě pro virtuální síť seznamy DNS01 (10.0.2.4 v back-endové podsíti) jako primární server DNS, IIS01 odešle žádosti DNS DNS01
3. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
4. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. Vztahuje se 1 pravidlo skupiny zabezpečení sítě (DNS), provoz je povolený, zastavte pravidla zpracování
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Povolena) Přístup k souboru webového serveru na AppVM01
1. IIS01 vyzve k zadání souboru na AppVM01
2. Žádná odchozí pravidla na front-endové podsítě je povolený provoz
3. Podsíť back-endu se začne zpracovávat příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. 3 pravidlo skupiny zabezpečení sítě (Internet do brány Firewall) nebude použít, přejděte k další pravidla
   4. 4 pravidlo skupiny zabezpečení sítě (IIS01 k AppVM01) použít, je povolený provoz, zastavit zpracování pravidla
4. AppVM01 obdrží požadavek a odpovídá zprávou souboru (za předpokladu, že přístup je autorizovaný)
5. Protože nejsou žádná odchozí pravidla na back-endové podsítě je povolena odpovědi
6. Front-endové podsítě začíná zpracování příchozí pravidlo:
   1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsítě, tak žádné skupiny zabezpečení sítě pravidla použít
   2. Systém výchozí pravidlo povolení provozu mezi podsítěmi by tento provoz povolit, aby provoz.
7. Server služby IIS obdrží soubor

#### <a name="denied-web-direct-to-web-server"></a>(Zakázaný) Web s přímým přístupem k webovému serveru
Vzhledem k tomu, že webový Server, IIS01 a brány Firewall jsou ve stejné cloudové službě sdílejí stejné veřejná IP adresa. Veškerý provoz HTTP by tedy přesměrováni do brány firewall. I když by se žádost úspěšně obsluhovat, nejde přejít přímo na webovém serveru, jí předán, tak, jak navrženo, přes bránu Firewall nejprve. Podívejte se první scénáře v této části pro tok přenosů.

#### <a name="denied-web-to-backend-server"></a>(Zakázaný) Web back-end server
1. Internet uživatel pokusí přistoupit k souboru na AppVM01 přes službu BackEnd001.CloudApp.Net
2. Protože je otevřeno žádné koncové body pro sdílenou složku, to nebude předejte Cloudovou službu a nebude připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncových bodů, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Zakázaný) Webové vyhledávání DNS na serveru DNS
1. Internet uživatel se pokusí vyhledat interní DNS záznam na DNS01 přes službu BackEnd001.CloudApp.Net
2. Protože je otevřeno žádné koncové body pro službu DNS, to nebude předejte Cloudovou službu a by připojit k serveru
3. Pokud z nějakého důvodu otevřelo koncových bodů, pravidlo skupiny zabezpečení sítě 5 (Internet k virtuální síti) by blokovaly tento provoz (Poznámka: Tento pravidlo 1 (DNS) se nedají použít pro dva důvody, nejprve zdrojové adresy je Internetu, toto pravidlo platí pouze pro místní virtuální sítě jako zdroj To je také pravidlo povolení, takže by nikdy odepření provozu)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Zakázaný) Web pro přístup k SQL přes bránu Firewall
1. Internet uživatel požádá o SQL data z FrontEnd001.CloudApp.Net (Internet směřující cloudové služby)
2. Protože je otevřeno žádné koncové body pro server SQL, to nebude předat Cloudovou službu a by kontaktovat bránu firewall
3. Pokud z nějakého důvodu otevřelo koncové body, front-endové podsítě začíná zpracování příchozí pravidlo:
   1. 1 pravidlo skupiny zabezpečení sítě (DNS) nelze použít, přejděte k další pravidlo
   2. Není použít, přejděte k další pravidla NSG pravidlo 2 (RDP)
   3. Použít NSG Rule 2 (Internet do brány Firewall), provoz je povolený, zastavte pravidla zpracování
4. Provoz narazí na interní IP adresa brány firewall (10.0.1.4)
5. Brána firewall nemá žádná pravidla předávání pro SQL a zahodí přenos

## <a name="conclusion"></a>Závěr
Toto je poměrně přímo dopředné způsob, jak ochraně vaší aplikace s bránou firewall a izolaci podsíť back-end z příchozí provoz.

Další příklady a základní informace o hranicích zabezpečení sítě najdete [tady][HOME].

## <a name="references"></a>Odkazy
### <a name="main-script-and-network-config"></a>Hlavní skript a konfigurace sítě
Úplná skript uložte v souboru skriptu prostředí PowerShell. Konfigurace sítě uložte do souboru s názvem "NetworkConf2.xml".
Podle potřeby upravte proměnné definované uživatelem. Spusťte skript a potom postupujte podle pokynů nastavení pravidla brány Firewall výše.

#### <a name="full-script"></a>Úplná skript
Tento skript bude založené na proměnné definované uživatelem:

1. Připojení k předplatnému Azure
2. Vytvoření nového účtu úložiště
3. Vytvořit novou virtuální síť a dvě podsítě, jak jsou definovány v souboru konfigurace sítě
4. Vytváření 4 windows server virtuálních počítačů
5. Konfigurace, včetně skupiny zabezpečení sítě:
   * Vytvoření NSG
   * Sestavování s pravidly
   * Vytvoření vazby skupiny zabezpečení sítě na příslušné podsítě

Tento skript Powershellu je vhodné spustit místně na, že připojení Internetu, počítač nebo server.

> [!IMPORTANT]
> Když se skript spouští, může být upozornění nebo ostatní informační zprávy, které se objeví v prostředí PowerShell. Pouze chybové zprávy červeně jsou příčinou znepokojení.
> 
> 

```powershell
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
```

#### <a name="network-config-file"></a>Soubor konfigurace sítě
Uložte tento soubor xml s aktualizované umístění a přidání odkazu do tohoto souboru $NetworkConfigFile proměnné ve skriptu výše.

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

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Příchozí DMZ pomocí skupiny zabezpečení sítě"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Určení ikony NAT"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Pravidlo brány firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktivace pravidla brány firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
