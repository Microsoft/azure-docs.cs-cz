---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671493"
---
I když je tato funkce snadno nastavena, neznamená to, že vaše zkušenosti budou bezproblémové. Pokud narazíte na problémy s přístupem k požadovanému koncovému bodu, existují některé nástroje, které můžete použít k testování připojení z konzoly aplikace. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola na webu Azure Portal. Chcete-li se z aplikace dostat ke konzoli Kudu, přejděte na Tools -> Kudu. Ke konzoli Kudo se můžete také dostat na adrese [sitename].scm.azurewebsites.net. Jakmile se web načte, přejděte na kartu Ladicí konzole. Pokud se chcete dostat na hostovku na webu Azure Portal, pak z aplikace přejděte na Tools -> Console. 

#### <a name="tools"></a>Nástroje
Nástroje **ping**, **nslookup**a **tracert** nebudou fungovat prostřednictvím konzoly z důvodu bezpečnostních omezení. Chcete-li vyplnit prázdnotu, přidány dva samostatné nástroje. Abybylo možné otestovat funkce DNS, přidali jsme nástroj s názvem nameresolver.exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

**Překladač názvů** můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete otestovat, zda máte něco špatně nakonfigurovaného se službou DNS nebo možná nemáte přístup k serveru DNS. Server DNS, který bude vaše aplikace používat v konzole, se zobrazí WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

Další nástroj umožňuje testovat připojení TCP k kombinaci hostitele a portu. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj **pro připínání** vám řekne, zda se můžete dostat ke konkrétnímu hostiteli a portu. Úspěch může ukázat pouze v případě, že: na kombinaci hostitele a portu naslouchá aplikace a z vaší aplikace je přístup k síti k určenému hostiteli a portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístupu k hostovaným prostředkům virtuální sítě
Existuje řada věcí, které mohou vaší aplikaci zabránit v přístupu k určitému hostiteli a portu. Většinu času je to jedna ze tří věcí:

* **Brána firewall stojí v cestě.** Pokud máte bránu firewall v cestě, narazí na časový čas TCP. Časový rozsah protokolu TCP je v tomto případě 21 sekund. Pomocí nástroje **pro tcpping** otestujte připojení. TCP timeouty může být způsobeno mnoha věcmi mimo firewally, ale začít tam. 
* **DNS není přístupný.** Časový rozsah DNS je tři sekundy na server DNS. Pokud máte dva servery DNS, časový rozsah je 6 sekund. Pomocí překladače názvů zjistěte, zda služba DNS funguje. Nezapomeňte, že nemůžete použít nslookup, protože nepoužívá DNS, se kterým je virtuální síť nakonfigurovaná. Pokud je nepřístupný, můžete mít firewall nebo NSG blokuje přístup k DNS, nebo to může být dolů.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se nejprve na věci, jako je: 

**regionální integrace virtuální sítě**
* je váš cíl non-RFC1918 adresu a nemáte WEBSITE_VNET_ROUTE_ALL nastavena na 1?
* Je k dispozici nsg blokování odchozí z podsítě integrace?
* Pokud procházíte přes ExpressRoute nebo VPN, je vaše místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud můžete dosáhnout koncových bodů ve virtuální síti, ale ne místně, zkontrolujte trasy.
* Máte dostatečná oprávnění k nastavení delegování v podsíti integrace? Během konfigurace regionální integrace virtuální sítě bude vaše podsíť integrace delegována na Microsoft.Web. Uživatelské rozhraní integrace virtuální sítě bude automaticky delegovat podsíť na Microsoft.Web. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení delegování, budete k delegování podsítě potřebovat někoho, kdo může nastavit atributy v podsíti integrace. Pokud chcete integrační podsíť delegovat ručně, přejděte na uživatelské rozhraní podsítě Virtuální sítě Azure a nastavte delegování pro Microsoft.Web. 

**brána vyžaduje integraci virtuální sítě**
* je rozsah adres point-to-site v rozsahu RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Zobrazuje se brána jako na portálu? Pokud je vaše brána vypnutá, pak ji přiveďte zpět nahoru.
* Zobrazují se certifikáty jako synchronizované nebo máte podezření, že byla změněna konfigurace sítě?  Pokud jsou vaše certifikáty nesynchronizované nebo máte podezření, že došlo ke změně konfigurace virtuální sítě, která nebyla synchronizována s vašimi asp, stiskněte možnost Synchronizovat síť.
* Pokud procházíte přes VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud můžete dosáhnout koncových bodů ve virtuální síti, ale ne místně, zkontrolujte trasy.
* Pokoušíte se použít bránu koexistence, která podporuje bod na web i ExpressRoute? Brány koexistence nejsou podporované s integrací virtuální sítě.

Ladění problémů se sítí je výzvou, protože zde nevidíte, co blokuje přístup k určité kombinaci host:port. Některé z příčin patří:

* Máte firewall na vašem hostiteli brání přístupu k portu aplikace z vašeho bodu do rozsahu IP webu. Křížení podsítí často vyžaduje veřejný přístup.
* Váš cílový hostitel je dole.
* Vaše přihláška neplatí.
* Měli jste špatnou IP nebo název hostitele.
* Aplikace naslouchá na jiném portu, než jste očekávali. ID procesu můžete porovnat s naslouchacím portem pomocí "netstat -aon" na hostiteli koncového bodu. 
* Skupiny zabezpečení sítě jsou konfigurovány tak, aby bránily přístupu k hostiteli aplikace a portu z rozsahu IP bodů do lokality.

Nezapomeňte, že nevíte, jakou adresu bude vaše aplikace skutečně používat. Může se jedná o libovolnou adresu v podsíti integrace nebo rozsahu adres point-to-site, takže je třeba povolit přístup z celého rozsahu adres. 

Mezi další kroky ladění patří:

* Připojte se k virtuálnímu počítači ve virtuální síti a pokuste se odtud oslovit hostitele prostředků: port. Chcete-li otestovat přístup tcp, použijte příkaz PowerShell **test-netconnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Zobrazení aplikace na virtuálním počítači a testování přístupu k tomuto hostiteli a portu z vaší aplikace pomocí **tcppingu**

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud vaše aplikace nemůže dosáhnout prostředek místně, zkontrolujte, jestli můžete dosáhnout prostředek z vaší virtuální sítě. Pomocí příkazu **Test-netconnection** PowerShell zkontrolujte přístup TCP. Pokud váš virtuální počítač nemůže dosáhnout vašeho místního prostředku, nemusí být vaše připojení VPN nebo ExpressRoute správně nakonfigurované.

Pokud váš virtuální počítač hostovaný virtuální počítač může dosáhnout vašeho místního systému, ale vaše aplikace nemůže, pak příčina je pravděpodobně jedním z následujících důvodů:

* Trasy nejsou nakonfigurovány pomocí podsítě nebo rozsahy adres od bodu do lokality v místní bráně.
* Skupiny zabezpečení sítě blokují přístup pro rozsah IP adres point-to-site.
* Místní brány firewall blokují provoz z rozsahu IP adres point-to-site.
* Pokoušíte se dosáhnout adresy, která není rfc 1918 pomocí funkce integrace místní virtuální sítě.