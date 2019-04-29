---
title: Příklad hraniční sítě – sestavení do hraniční sítě k ochraně aplikací s bránou firewall a skupin zabezpečení sítě | Dokumentace Microsoftu
description: Vytvářejte hraniční síti s bránou firewall a skupin zabezpečení sítě (Nsg)
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
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868317"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Příklad 2: Vytvořit hraniční síť k ochraně aplikací s bránou firewall a skupin zabezpečení sítě
[Vraťte se do Microsoft cloud services a stránka zabezpečení sítě][HOME]

Tento příklad ukazuje, jak vytvořit hraniční síti (označované také jako *DMZ*, *demilitarizovaná zóna nebo*, a *monitorována podsíť*) s bránou firewall, čtyř počítačů Windows serveru a skupiny zabezpečení sítě (Nsg). Obsahuje podrobnosti o každém z příslušných příkazy zajistit lepší představu o jednotlivých kroků. V části "Provoz scénáře" poskytuje podrobné vysvětlení, jak se provoz pokračuje přes vrstev obrany v hraniční síti. Nakonec v části "Odkazy na" obsahuje kompletní kód a pokyny k vytvoření tohoto prostředí pro testování a vyzkoušet různé scénáře.

![Příchozí hraniční sítě pomocí síťového virtuálního zařízení a skupin zabezpečení sítě][1]

## <a name="environment"></a>Prostředí 
V tomto příkladu je založené na scénářích s předplatným Azure, která obsahuje tyto položky:

* Dva cloudových služeb: FrontEnd001 a BackEnd001.
* Virtuální síť s názvem CorpNetwork, která má dvě podsítě: Front-endových a back-endu.
* Jedné skupině NSG, která se uplatňuje na obě podsítě.
* Síťové virtuální zařízení: Barracuda NextGen Firewall připojené k podsíti FrontEnd.
* Počítač s Windows serverem, který představuje server webových aplikací: IIS01.
* Dva počítače Windows serveru, které představují aplikace back-end serverů: AppVM01 a AppVM02.
* Počítač s Windows serverem, který představuje DNS server: DNS01.

> [!NOTE]
> Přestože tento příklad používá Barracuda NextGen Firewall, můžete použít mnoho síťových virtuálních zařízení.
> 
> 

Skript Powershellu v části "Odkazy" v tomto článku vytváří většinu prostředí, je zde popsáno. Virtuální počítače a virtuální sítě jsou vytvořeny skriptem, ale tyto procesy nejsou podrobně popsané v tomto dokumentu.

Sestavení prostředí:

1. Uložte soubor XML konfigurace sítě zahrnuté v části "Odkazy" (aktualizováno s názvy, umístění a IP adresy, které odpovídají vašemu scénáři).
2. Aktualizujte uživatelské proměnné ve skriptu prostředí PowerShell tak, aby odpovídaly prostředí, ve kterém skript poběží na (předplatných, názvy služeb a tak dále).
3. Spusťte skript prostředí PowerShell.

> [!NOTE]
> Oblast zadali ve skriptu prostředí PowerShell musí odpovídat oblasti zadaný v souboru XML konfigurace sítě.
>
>

Po úspěšném spuštění skriptu můžete provést tyto kroky:

1. Nastavení pravidel brány firewall. Najdete v části "Pravidla brány Firewall" v tomto článku.
2. Pokud chcete, můžete nastavit webový server a server aplikace s jednoduchou webovou aplikaci umožňující testování s konfigurací hraniční sítě. Můžete použít skripty dvě aplikace uvedené v části "Odkazy".

V další části vysvětluje většinu příkazů skriptu, které se týkají skupin zabezpečení sítě.

## <a name="nsgs"></a>Skupiny NSG
V tomto příkladu je skupinu NSG vytvořené a pak načíst šest pravidlům.

> [!TIP]
> Obecně platí nejdřív byste měli vytvořit konkrétní pravidla "Povolit" a naposledy obecnější pravidla "Zakázat". Přiřazené priority ovládací prvky, které jsou pravidla se vyhodnocují první. Po provoz nenajde, která platí pro konkrétní pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít buď příchozí nebo odchozí směr (z hlediska podsíť).
> 
> 

Deklarativní tato pravidla jsou vytvořeny pro příchozí provoz:

1. Interní DNS provoz (port 53) je povolený.
2. Provoz protokolu RDP (portu 3389) z Internetu do všech virtuálních počítačů je povolené.
3. Je povolený provoz protokolu HTTP (port 80) z Internetu do síťových virtuálních zařízení (Brána firewall).
4. Je povolen veškerý provoz (všechny porty) z IIS01 do AppVM01.
5. Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (obě podsítě) byl odepřen.
6. Veškerý provoz (všechny porty) z podsítě front-endu do back-endové podsítě je odepřen.

Pomocí těchto pravidel vázána pro každou podsíť, kdyby příchozí z Internetu webový server, požadavek HTTP i pravidla 3 (Povolit) a pravidla 5 (zabránění) by se zdá, že chcete použít, ale protože pravidlo 3 má vyšší prioritu, pouze se použije. Pravidlo 5 nebude souvisejí. Požadavek protokolu HTTP bude tak možné do brány firewall.

Pokud tento stejný provoz se snažil to spojit se serverem DNS01, pravidlo 5 (zabránění) by první použití, takže provoz nebude možné předat serveru. Pravidla 6 (zabránění) blokuje front-endové podsítě v komunikaci s back-endové podsítě (s výjimkou provoz povolený v pravidlech 1 a 4). Tím se zajistí ochrana síť back-end v případě, že útočník zneužije webovou aplikaci na front-endu. V takovém případě útočník by mít omezený přístup k back-end "chráněné" sítě. (Útočník bude mít přístup pouze na prostředky, které jsou zveřejněné na AppVM01 server.)

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Zamezit provoz v obou směrech, budete potřebovat, směrování definovaného uživatelem. Informace o této techniky v jiném příkladu v [hlavní zabezpečení hranic dokumentu][HOME].

Pravidla skupiny zabezpečení sítě je zde popsáno, jsou podobné pravidlům NSG v [– Příklad 1 – Vytvoření jednoduché DMZ pomocí skupin zabezpečení sítě][Example1]. Zkontrolujte popis skupiny zabezpečení sítě v tomto článku podrobný pohled na každé pravidlo skupiny zabezpečení sítě a jeho atributy.

## <a name="firewall-rules"></a>Pravidla brány firewall
Je potřeba nainstalovat klienta správy na počítač pro správu brány firewall a vytvořit konfigurace potřebné. Viz dokumentace ke službě z vaší brány firewall (nebo jiné síťové virtuální zařízení) dodavatele o tom, jak spravovat zařízení. Zbytek tohoto oddílu popisuje konfigurace, brány firewall prostřednictvím klienta pro správu od dodavatele (není webu Azure portal nebo Powershellu).

Zobrazit [Barracuda NG správce](https://techlib.barracuda.com/NG61/NGAdmin) pokyny pro stažení klienta a připojení k bráně firewall Barracuda použitý v tomto příkladu.

Je potřeba vytvořit pravidla pro předávání v bráně firewall. Protože scénář v tomto příkladu pouze internetový provoz směruje příchozí do brány firewall a pak na webový server, stačí jeden předávání pravidlo překladu adres. Pravidlo v bráně firewall Barracuda použitý v tomto příkladu, bude pravidlo NAT cíl (Dst NAT), abyste tento provoz.

Chcete-li vytvořit následující pravidlo (nebo ověřit existující výchozí pravidla), proveďte následující kroky:
1. Na řídicím panelu Barracuda NG správce klienta, na kartě Konfigurace v **provozní konfiguraci** vyberte **Ruleset**. 

   Mřížka volá **hlavní pravidla** ukazuje, stávající aktivní a deaktivovat pravidla v bráně firewall.

2. Chcete-li vytvořit nové pravidlo, vyberte malá zelená **+** tlačítko v pravém horním rohu tuto mřížku. (Brána firewall může být uzamčen. Pokud se zobrazí tlačítko označené **Zámek** a nemůžeme vytvořit nebo upravit pravidla, vyberte tlačítko a odemknutí sada pravidel a povolit úpravy.)
  
3. Pokud chcete upravit stávající pravidlo, vyberte pravidlo, klikněte pravým tlačítkem a pak vyberte **upravit pravidlo**.

Vytvořit nové pravidlo nazývat například **WebTraffic.** 

Ikona pravidel NAT cílové vypadá takto: ![Určení ikony NAT][2]

Pravidlo samotné bude vypadat přibližně takto:

![Pravidlo brány firewall][3]

Všechny příchozí adresa narazí na bránu firewall při pokusu o přístup protokolu HTTP (port 80 nebo 443 pro protokol HTTPS) budou odeslány mimo brány firewall na počítači DHCP1 místní IP rozhraní a přesměruje na webový server s adresou IP adresa 10.0.1.5. Protože hned provoz na portu 80 a přechod na webový server na portu 80, není nutná žádná změna portu. Ale cílový seznam mohlo být 10.0.1.5:8080 Pokud naslouchali webového serveru na portu 8080, která převedla příchozí port 80 v bráně firewall na portu 8080 pro příchozí spojení na webovém serveru.

Měli byste také určit způsob připojení. Cíl pravidla z Internetu je dynamické SNAT nejvhodnější metodu.

I když jste vytvořili jenom jedno pravidlo, je potřeba správně nastavit její prioritu. V mřížce všechna pravidla v bráně firewall Pokud je toto nové pravidlo v dolní části (nižší než pravidla BLOCKALL), to se nikdy souvisejí. Zajistěte, aby že nové pravidlo pro webový provoz je vyšší než BLOCKALL pravidlo.

Po vytvoření pravidla, musíte ji odeslat do brány firewall a poté aktivovat. Pokud neprovedete tyto kroky, změně v pravidle projeví. Další část popisuje proces nabízených oznámení a aktivace.

## <a name="rule-activation"></a>Pravidlo aktivace
Teď, když se pravidlo přidá sady pravidel, budete muset nahrát sady pravidel brány firewall a pak sadu aktivujte.

![Aktivace pravidla brány firewall][4]

V pravém horním rohu správy klienta zobrazí se vám skupina tlačítek. Vyberte **odeslat změny** odeslat upravenou sady pravidel brány firewall a potom vyberte **aktivovat**.

Teď, když po dokončení aktivace sady pravidel brány firewall, je kompletní prostředí. Pokud chcete, můžete spustit skripty ukázkové aplikace v části "Odkazy na" a přidejte aplikace do prostředí. Pokud jste přidali aplikaci, je otestovat provoz scénáře popsané v další části.

> [!IMPORTANT]
> Si uvědomit, webový server nebude přímo přístupů. Když prohlížeč vyžádá stránku protokolu HTTP od FrontEnd001.CloudApp.Net, koncový bod protokolu HTTP (port 80) předává provoz na bránu firewall, nikoli na webovém serveru. Brána firewall, pravidla, které jste vytvořili dříve, použije překladu adres k mapování požadavku na webový server.
> 
> 

## <a name="traffic-scenarios"></a>Provoz scénáře
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Povolena) Web pro webový server přes bránu firewall
1. Internet uživatele požádá o stránku protokolu HTTP FrontEnd001.CloudApp.Net (přístupem k Internetu cloudové služby).
2. Cloudové služby předává provoz přes otevřených koncových bodů na portu 80 brány firewall na místní rozhraní na 10.0.1.4:80.
3. Front-endové podsítě zahájí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (DNS). Přesunout na další pravidla.
   2. Neplatí pravidlo skupiny zabezpečení sítě 2 (RDP). Přesunout na další pravidla.
   3. Pravidlo skupiny zabezpečení sítě 3 (internet do brány firewall) nevztahuje. Povolení provozu. Zastavte zpracování pravidla.
4. Provoz narazí na interní IP adresa brány firewall (10.0.1.4).
5. Brána firewall pravidlo přeposílání zjistí, že to je port 80 provoz a přesměruje na webový server IIS01.
6. IIS01 naslouchá pro webový provoz, obdrží požadavek a spustí zpracování požadavku.
7. IIS01 požádá o informace z instance systému SQL Server na AppVM01.
8. Nejsou žádná odchozí pravidla ve front-endové podsíti, takže provoz je povolený.
9. Podsíť back-endu spustí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (DNS). Přesunout na další pravidla.
   2. Neplatí pravidlo skupiny zabezpečení sítě 2 (RDP). Přesunout na další pravidla.
   3. Neplatí pravidlo skupiny zabezpečení sítě 3 (internet do brány firewall). Přesunout na další pravidla.
   4. Pravidlo skupiny zabezpečení sítě 4 (IIS01 k AppVM01) nevztahuje. Povolení provozu. Zastavte zpracování pravidla.
10. Instance systému SQL Server na AppVM01 obdrží požadavek a odpovídá.
11. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povoleno odpovědi.
12. Front-endové podsítě zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsíti, takže pravidla NSG nepoužijí.
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi umožňuje tento provoz, takže provoz je povolený.
13. IIS01 obdrží odpověď od AppVM01 dokončí odpovědi HTTP a pošle žadateli.
14. Protože je to NAT relace ze brána firewall, cíl odpovědi je zpočátku pro bránu firewall.
15. Brána firewall obdrží odpověď od serveru a předá zpět uživateli Internetu.
16. Protože nejsou žádná odchozí pravidla ve front-endové podsíti, odpovědi je povolený a internet uživatel obdrží webové stránky.

#### <a name="allowed-rdp-to-backend"></a>(Povolena) Připojení RDP k back-endu
1. Správce serveru k Internetu vyžaduje relaci RDP na AppVM01 na BackEnd001.CloudApp.Net:*xxxxx*, kde *xxxxx* je číslo náhodně přidělenému portu pro připojení RDP k AppVM01. (Zjistíte přidělenému portu na portálu Azure portal nebo pomocí prostředí PowerShell.)
2. Vzhledem k tomu, že brána firewall naslouchá jenom na adrese FrontEnd001.CloudApp.Net, není zapojená tento tok provozu.
3. Podsíť back-endu spustí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (DNS). Přesunout na další pravidla.
   2. Použít pravidlo skupiny zabezpečení sítě 2 (RDP). Povolení provozu. Zastavte zpracování pravidla.
4. Vzhledem k tomu, že nejsou žádná odchozí pravidla, výchozí pravidla použít a vracet provoz je povolený.
5. Relace protokolu RDP je povolena.
6. AppVM01 vyzve k zadání uživatelského jména a hesla.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Povolena) Vyhledávání DNS webového serveru na serveru DNS
1. Požadavky serveru, IIS01, webové datového kanálu na www.data.gov ale potřebuje pro překlad adres.
2. Konfiguraci sítě pro virtuální síť ukazuje DNS01 (10.0.2.4 v back-endové podsíti) jako primární server DNS. IIS01 odešle DNS01 žádosti DNS.
3. Protože nejsou žádná odchozí pravidla ve front-endové podsíti, je povolený provoz.
4. Podsíť back-endu spustí zpracování příchozí pravidlo:
   1. Použije pravidlo skupiny zabezpečení sítě 1 (DNS). Povolení provozu. Zastavte zpracování pravidla.
5. DNS server obdrží požadavek.
6. DNS server nemá adresu do mezipaměti a dotazuje kořenový server DNS na Internetu.
7. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povolený provoz.
8. Internetového DNS server odpoví. Protože interně zahájení relace odpovědi je povolené.
9. DNS server, odpověď do mezipaměti a odpoví na požadavek od IIS01.
10. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povolený provoz.
11. Front-endové podsítě zahájí zpracování příchozí pravidlo:
    1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsíti, takže pravidla NSG nepoužijí.
    2. Systém výchozí pravidlo povolení provozu mezi podsítěmi umožňuje tento provoz, aby provoz.
12. IIS01 obdrží odpověď od DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Povolena) Přístup k souborům webového serveru na AppVM01
1. IIS01 požádá o soubor na AppVM01.
2. Protože nejsou žádná odchozí pravidla ve front-endové podsíti, je povolený provoz.
3. Podsíť back-endu spustí zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (DNS). Přesunout na další pravidla.
   2. Neplatí pravidlo skupiny zabezpečení sítě 2 (RDP). Přesunout na další pravidla.
   3. Neplatí pravidlo skupiny zabezpečení sítě 3 (internet do brány firewall). Přesunout na další pravidla.
   4. Pravidlo skupiny zabezpečení sítě 4 (IIS01 k AppVM01) nevztahuje. Povolení provozu. Zastavte zpracování pravidla.
4. AppVM01 obdrží požadavek a odpoví na soubor (za předpokladu, že přístup je autorizovaný).
5. Protože nejsou žádná odchozí pravidla v back-endové podsíti, je povoleno odpovědi.
6. Front-endové podsítě zahájí zpracování příchozí pravidlo:
   1. Neexistuje žádné pravidlo NSG, které platí pro příchozí provoz z podsítě back-end k front-endové podsíti, takže pravidla NSG nepoužijí.
   2. Systém výchozí pravidlo povolení provozu mezi podsítěmi umožňuje tento provoz, aby provoz.
7. IIS01 obdrží soubor.

#### <a name="denied-web-direct-to-web-server"></a>(Zakázaný) Web s přímým přístupem k webovému serveru
Protože IIS01 webového serveru a brány firewall jsou ve stejné cloudové službě, sdílejí stejnou veřejnou IP adresu. Proto všechny HTTP provoz se směřuje do brány firewall. Během požadavku by se úspěšně obsluhovat, nejde přejít přímo na webovém serveru. Předá, tak, jak navrženo, přes bránu firewall nejprve. Podívejte se první scénáře v této části pro tok přenosů.

#### <a name="denied-web-to-backend-server"></a>(Zakázaný) Web back-end server
1. Uživateli s Internetu pokusí o přístup k souboru na AppVM01 přes službu BackEnd001.CloudApp.Net.
2. Protože je otevřeno žádné koncové body pro sdílení souborů, to nebudou předávat cloudovou službu a nebude připojit k serveru.
3. Pokud z nějakého důvodu jsou spuštěné koncové body, pravidlo NSG 5 (internet k virtuální síti) blokuje provoz.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Zakázaný) Webové vyhledávání DNS na serveru DNS
1. Internetu uživatel se pokusí vyhledat interní DNS záznam na DNS01 přes službu BackEnd001.CloudApp.Net.
2. Protože je otevřeno žádné koncové body pro službu DNS, to nebudou předávat cloudovou službu a nebude připojit k serveru.
3. Pokud z nějakého důvodu jsou spuštěné koncové body, pravidlo NSG 5 (internet k virtuální síti) blokuje provoz. (Pravidlo 1 [DNS] neplatí pro dva důvody. Nejprve zdrojové adresy je internet a pouze v případě, že místní virtuální sítě je zdroj bude použito toto pravidlo. Za druhé toto pravidlo je pravidlo povolení, takže nikdy odepření provozu.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Zakázaný) Web pro přístup k SQL přes bránu firewall
1. Uživatel k Internetu vyžaduje SQL data z FrontEnd001.CloudApp.Net (přístupem k Internetu cloudové služby).
2. Protože je otevřeno žádné koncové body pro server SQL, to nebudou předávat cloudovou službu a nedostanou bránu firewall.
3. Pokud z nějakého důvodu jsou spuštěné koncové body, začne podsítě front-endové zpracování příchozí pravidlo:
   1. Neplatí pravidlo skupiny zabezpečení sítě 1 (DNS). Přesunout na další pravidla.
   2. Neplatí pravidlo skupiny zabezpečení sítě 2 (RDP). Přesunout na další pravidla.
   3. Pravidlo skupiny zabezpečení sítě 3 (internet do brány firewall) nevztahuje. Povolení provozu. Zastavte zpracování pravidla.
4. Provoz narazí na interní IP adresa brány firewall (10.0.1.4).
5. Brána firewall nemá žádná pravidla předávání pro SQL a zahodí přenos.

## <a name="conclusion"></a>Závěr
Tento příklad ukazuje poměrně jednoduchý způsob, jak Ochrana aplikací pomocí firewallu a izolovat back endové podsítě z příchozí provoz.

Můžete najít další příklady a základní informace o síti hranice zabezpečení [tady][HOME].

## <a name="references"></a>Odkazy
### <a name="full-script-and-network-config"></a>Úplná skript a síťové konfigurace
Úplná skript uložte v souboru skriptu prostředí PowerShell. Uložte skript konfigurace sítě do souboru s názvem NetworkConf2.xml.
Uživateli definované proměnných změňte podle potřeby. Spusťte skript a pak postupujte podle pokynů v části "Pravidla brány Firewall" v tomto článku.

#### <a name="full-script"></a>Celý skript
Tento skript na základě proměnných definovaný uživatelem, bude proveďte následující kroky:

1. Připojení k předplatnému Azure.
2. Vytvoření účtu úložiště
3. Vytvoření virtuální sítě a dvě podsítě, jak jsou definovány v konfiguračním souboru sítě.
4. Vytvořte čtyři virtuální počítače Windows serveru.
5. Nakonfigurujte skupiny zabezpečení sítě. Dokončení konfigurace těchto kroků:
   * Vytvoří skupinu zabezpečení sítě.
   * Naplní skupinu zabezpečení sítě s pravidly.
   * Skupiny zabezpečení sítě se váže k příslušné podsítě.

Tento skript Powershellu byste měli spustit místně na serveru nebo počítače připojeného k Internetu.

> [!IMPORTANT]
> Při spuštění tohoto skriptu, může zobrazit upozornění a ostatní informační zprávy v prostředí PowerShell. Stačí mít obavy o chybové zprávy, které se zobrazí červeně.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

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

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
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

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG rules
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

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
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
Uložte tento soubor XML s aktualizovanou umístění a pak přidat odkaz na tento soubor do proměnné $NetworkConfigFile v předchozím skriptu.

```xml
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
Pokud chcete nainstalovat ukázkovou aplikaci pro tuto a další příklady hraniční sítě, přečtěte si článek [ukázkový skript aplikace][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Příchozí DMZ pomocí skupiny zabezpečení sítě"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Určení ikony NAT"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Pravidlo brány firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktivace pravidla brány firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
