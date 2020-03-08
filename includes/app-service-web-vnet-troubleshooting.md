---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671493"
---
I když se tato funkce dá snadno nastavit, neznamená to, že vaše zkušenosti budou bez problémů. Pokud máte problémy s přístupem k požadovanému koncovému bodu, můžete použít některé nástroje, pomocí kterých můžete testovat připojení z konzoly aplikace. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola v Azure Portal. Pokud se chcete připojit ke konzole Kudu z vaší aplikace, použijte nástroje-> Kudu. Ke konzole Kudo se můžete dostat i na adrese [název_webu]. SCM. azurewebsites. NET. Po načtení webu přejdete na kartu ladit konzolu. Pokud se chcete dostat do Azure Portal hostované konzoly, pak z aplikace přejdete do konzoly nástroje->. 

#### <a name="tools"></a>Nástroje
Nástroje **příkazového testu**, **nslookup**a **tracert** nefungují prostřednictvím konzoly z důvodu omezení zabezpečení. K vyplnění void se přidaly dva samostatné nástroje. K otestování funkcí DNS jsme přidali nástroj s názvem nameresolver. exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

**Nameresolver** můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete testovat, jestli máte nějaké chybné konfigurace služby DNS, nebo možná nemáte přístup k vašemu serveru DNS. Server DNS, který bude aplikace používat v konzole, si můžete prohlédnout na základě proměnných prostředí WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

Další nástroj umožňuje testovat připojení TCP k hostiteli a kombinaci portů. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj **tcpping** vám oznamuje, jestli můžete kontaktovat konkrétního hostitele a port. Může se zobrazit jenom v případě, že existuje aplikace, která naslouchá na hostiteli a portu, a má přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístupu k prostředkům hostovaným ve virtuální síti
K dispozici je několik věcí, které můžou zabránit tomu, aby vaše aplikace dosáhla konkrétního hostitele a portu. Většina času představuje jednu ze tří věcí:

* **Brána firewall je v cestě.** Pokud máte bránu firewall způsobem, zadáte časový limit TCP. V tomto případě je časový limit TCP 21 sekund. K otestování připojení použijte nástroj **tcpping** . K vypršení časového limitu TCP může docházet z mnoha věcí za branami firewall, ale na začátku. 
* **Služba DNS není přístupná.** Časový limit DNS je tři sekundy na jeden server DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zjistit, jestli služba DNS funguje. Pamatujte, že nemůžete použít nástroj nslookup, protože nepoužívá DNS, se kterým je nakonfigurovaná vaše virtuální síť. Pokud je nepřístupná, můžete mít bránu firewall nebo NSG blokující přístup k DNS nebo může být vypnutá.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se na první věci: 

**Místní integrace virtuální sítě**
* je vaše cílové místo na RFC1918 adrese a nemáte WEBSITE_VNET_ROUTE_ALL nastavené na 1?
* Existuje NSG blokující výstup z podsítě integrace?
* Pokud budete předávat napříč ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud máte přístup ke koncovým bodům ve vaší virtuální síti, ale ne místně, Projděte si své trasy.
* máte dostatečná oprávnění k nastavení delegování v podsíti Integration? Během konfigurace integrace místní virtuální sítě bude vaše podsíť integrace delegována na Microsoft. Web. Uživatelské rozhraní integrace virtuální sítě bude podsíť delegovat na Microsoft. Web automaticky. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení delegování, budete potřebovat někoho, kdo může nastavit atributy v podsíti Integration pro delegování podsítě. Pokud chcete integrační podsíť manuálně delegovat, vyhledejte v uživatelském rozhraní podsítě Azure Virtual Network a nastavte delegování pro Microsoft. Web. 

**požadovaná brána Integration VNet**
* je rozsah adres Point-to-site v rozsahu RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Zobrazuje se brána na portálu? Pokud vaše brána nefunguje, přeneste ji do záložního prostředí.
* Zobrazují se certifikáty jako synchronizované nebo se domníváte, že se změnila konfigurace sítě?  Pokud vaše certifikáty nejsou synchronizované nebo máte podezření, že došlo ke změně konfigurace vaší virtuální sítě, která nebyla synchronizovaná s vaší ASP, přejděte na "synchronizovat síť".
* Pokud přecházíte přes síť VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud máte přístup ke koncovým bodům ve vaší virtuální síti, ale ne místně, Projděte si své trasy.
* Pokoušíte se použít bránu koexistence, která podporuje obě body na lokalitu a ExpressRoute? Pro integraci virtuální sítě se nepodporují brány koexistence.

Ladění problémů se sítí je problém, protože nemůžete zjistit, co blokuje přístup ke konkrétnímu hostiteli: kombinace portů. Mezi tyto příčiny patří:

* Máte bránu firewall na hostiteli, která brání přístupu k portu aplikace z vašeho bodu do rozsahu IP adres lokality. Překročení podsítí často vyžaduje veřejný přístup.
* Cílový hostitel nefunguje.
* Vaše aplikace je mimo provoz.
* Měli jste nesprávnou IP adresu nebo název hostitele.
* Vaše aplikace naslouchá na jiném portu, než jste očekávali. ID procesu můžete porovnat s portem naslouchání pomocí příkazu netstat-aon na hostiteli koncového bodu. 
* Vaše skupiny zabezpečení sítě jsou nakonfigurovány takovým způsobem, že brání přístupu k hostiteli aplikace a portu z vašeho bodu do rozsahu IP adres lokality.

Mějte na paměti, že nevíte, jaká adresa bude vaše aplikace skutečně používat. Může to být jakákoli adresa v podsíti integrace nebo rozsah adres Point-to-site, takže je potřeba umožnit přístup z celého rozsahu adres. 

Mezi další kroky ladění patří:

* Připojte se k VIRTUÁLNÍmu počítači ve virtuální síti a pokuste se připojit k hostiteli prostředků: port. K otestování přístupu TCP použijte příkaz PowerShellu **test-NetConnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Vyvolejte aplikaci na virtuálním počítači a otestujte přístup k tomuto hostiteli a portu z konzoly z vaší aplikace pomocí **tcpping** .

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud se vaše aplikace nemůže připojit k místnímu prostředku, zkontrolujte, jestli se můžete připojit k prostředku z vaší virtuální sítě. K ověření přístupu TCP použijte příkaz prostředí PowerShell **test-NetConnection** . Pokud se váš virtuální počítač nemůže připojit k místnímu prostředku, připojení VPN nebo ExpressRoute možná není správně nakonfigurované.

Pokud se virtuální počítač s hostovaným virtuálním počítačem může dostat k vašemu místnímu systému, ale vaše aplikace nefunguje, může to být způsobeno jedním z následujících důvodů:

* Vaše trasy nejsou u vaší podsítě nakonfigurované nebo ukazují na rozsahy adres lokality v místní bráně.
* Vaše skupiny zabezpečení sítě blokují přístup k rozsahu IP adres Point-to-site.
* Místní brány firewall blokují provoz z rozsahu IP adres Point-to-site.
* Snažíte se spojit s adresou, kterou nenajdete v dokumentu RFC 1918, pomocí funkce regionální integrace virtuální sítě.