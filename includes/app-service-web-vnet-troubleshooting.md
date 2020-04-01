---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419557"
---
Tato funkce se snadno nastavuje, ale to neznamená, že vaše zkušenosti budou bezproblémové. Pokud narazíte na problémy s přístupem k požadovanému koncovému bodu, existují některé nástroje, které můžete použít k testování připojení z konzoly aplikace. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola na webu Azure Portal. Chcete-li se z aplikace dostat ke konzoli Kudu, přejděte na **Tools** > **Kudu**. Ke konzoli Kudo se můžete také dostat na adrese [sitename].scm.azurewebsites.net. Po načtení webu přejděte na kartu **Ladicí konzola.** Pokud se chcete z aplikace dostat ke konzoli hostované na portálu Azure, přejděte na **Nástrojovou** > **konzolu**.

#### <a name="tools"></a>nástroje
Nástroje **ping**, **nslookup**a **tracert** nebude fungovat prostřednictvím konzoly z důvodu omezení zabezpečení. Chcete-li vyplnit prázdnotu, jsou přidány dva samostatné nástroje. Chcete-li otestovat funkčnost SLUŽBY DNS, přidali jsme nástroj **nameresolver.exe**. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

Překladač názvů můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete otestovat, zda máte něco nesprávně nakonfigurovaného se službou DNS nebo nemáte přístup k serveru DNS. Server DNS, který vaše aplikace používá v konzole, se zobrazí v WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER proměnných prostředí.

Pomocí dalšího nástroje můžete otestovat připojení tcp k kombinaci hostitele a portu. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj **pro připínání** vám řekne, zda se můžete dostat ke konkrétnímu hostiteli a portu. Může zobrazovat úspěch pouze v případě, že na kombinaci hostitele a portu naslouchá aplikace a z vaší aplikace je přístup k síti k určenému hostiteli a portu.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Ladění přístupu k prostředkům hostovaným virtuální sítí
Řada věcí může vaší aplikaci zabránit v tom, aby se dostala na konkrétního hostitele a port. Většinou je to jedna z těchto věcí:

* **Brána firewall stojí v cestě.** Pokud máte bránu firewall v cestě, narazí na časový čas TCP. Časový rozsah protokolu TCP je v tomto případě 21 sekund. Pomocí nástroje **pro tcpping** otestujte připojení. Časové výčasové opony protokolu TCP mohou být způsobeny mnoha věcmi mimo brány firewall, ale začněte tam.
* **DNS není přístupný.** Časový rozsah DNS je 3 sekundy na server DNS. Pokud máte dva servery DNS, časový rozsah je 6 sekund. Pomocí překladače názvů zjistěte, zda služba DNS funguje. Nslookup nelze použít, protože nepoužívá DNS, se kterým je virtuální síť nakonfigurována. Pokud je nepřístupný, můžete mít firewall nebo NSG blokuje přístup k DNS, nebo to může být dolů.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se nejprve na věci, jako je:

**Regionální integrace virtuální sítě**
* Je váš cíl non-RFC1918 adresu a nemáte WEBSITE_VNET_ROUTE_ALL nastavena na 1?
* Je k dispozici skupina nsg blokování odchozí z podsítě integrace?
* Pokud přecchcete přes Azure ExpressRoute nebo VPN, je vaše místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud můžete dosáhnout koncových bodů ve virtuální síti, ale ne místně, zkontrolujte trasy.
* Máte dostatečná oprávnění k nastavení delegování v podsíti integrace? Během konfigurace regionální integrace virtuální sítě je podsíť integrace delegována na Microsoft.Web. Uživatelské rozhraní integrace virtuální sítě automaticky deleguje podsíť na Microsoft.Web. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení delegování, budete k delegování podsítě potřebovat někoho, kdo může nastavit atributy v podsíti integrace. Pokud chcete integrační podsíť delegovat ručně, přejděte na uživatelské rozhraní podsítě Virtuální sítě Azure a nastavte delegování pro Microsoft.Web.

**Integrace virtuální sítě požadovaná v bráně**
* Je rozsah adres bodu k místu v rozsahu RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Zobrazuje se brána jako na portálu? Pokud je vaše brána vypnutá, pak ji přiveďte zpět nahoru.
* Zobrazují se certifikáty jako synchronizované nebo máte podezření, že byla změněna konfigurace sítě?  Pokud jsou certifikáty nesynchronizované nebo máte podezření, že byla provedena změna konfigurace virtuální sítě, která nebyla synchronizována s vašimi přístupovými zařízeními, vyberte **možnost Synchronizovat síť**.
* Pokud procházíte vpn, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud můžete dosáhnout koncových bodů ve virtuální síti, ale ne místně, zkontrolujte trasy.
* Pokoušíte se použít bránu koexistence, která podporuje bod na web i ExpressRoute? Brány koexistence nejsou podporované s integrací virtuální sítě.

Ladění problémů se sítí je výzva, protože nevidíte, co blokuje přístup k určité kombinaci host:port. Některé příčiny zahrnují:

* Na hostiteli máte bránu firewall, která brání přístupu k portu aplikace z rozsahu IP bodů na pracoviště. Křížení podsítí často vyžaduje veřejný přístup.
* Váš cílový hostitel je dole.
* Vaše přihláška neplatí.
* Měli jste špatnou IP nebo název hostitele.
* Aplikace naslouchá na jiném portu, než jste očekávali. ID procesu můžete porovnat s naslouchacím portem pomocí "netstat -aon" na hostiteli koncového bodu.
* Skupiny zabezpečení sítě jsou konfigurovány tak, aby bránily přístupu k hostiteli aplikace a portu z rozsahu IP bodů na lokalitu.

Nevíte, jakou adresu vaše aplikace vlastně používá. Může se jedná o libovolnou adresu v podsíti integrace nebo rozsahu adres point-to-site, takže je třeba povolit přístup z celého rozsahu adres.

Mezi další kroky ladění patří:

* Připojte se k virtuálnímu počítači ve virtuální síti a pokuste se odtud dosáhnout vašeho hostitele prostředků: port. Chcete-li otestovat přístup tcp, použijte příkaz PowerShell **test-netconnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Vyvolat aplikaci na virtuálním počítači a otestovat přístup k tomuto hostiteli a port z konzole z vaší aplikace pomocí **tcpping**.

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud vaše aplikace nemůže dosáhnout prostředek místně, zkontrolujte, jestli můžete dosáhnout prostředek z vaší virtuální sítě. Pomocí příkazu **Test-netconnection** PowerShell zkontrolujte přístup TCP. Pokud váš virtuální počítač nemůže dosáhnout vašeho místního prostředku, nemusí být vaše připojení VPN nebo ExpressRoute správně nakonfigurované.

Pokud váš virtuální počítač hostovaný ve virtuální síti může dosáhnout vašeho místního systému, ale vaše aplikace nemůže, příčina je pravděpodobně jedním z následujících důvodů:

* Vaše trasy nejsou nakonfigurovány s rozsahy adres podsítě nebo bodu na pracoviště v místní bráně.
* Skupiny zabezpečení sítě blokují přístup pro rozsah IP adres point-to-site.
* Místní brány firewall blokují provoz z rozsahu IP adres bodu na web.
* Pokoušíte se dosáhnout adresu bez RFC 1918 pomocí funkce integrace místní virtuální sítě.