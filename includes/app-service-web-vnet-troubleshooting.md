---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999423"
---
Tato funkce se snadno nastavuje, ale to neznamená, že vaše prostředí bude mít problém zdarma. Pokud narazíte na problémy s přístupem k požadovanému koncovému bodu, můžete použít k otestování připojení z konzoly aplikace některé nástroje. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola v Azure Portal. Pokud se chcete připojit ke konzole Kudu z vaší aplikace, navštivte **Nástroj nástroje**  >  **Kudu**. Ke konzole Kudo se můžete dostat i na adrese [název_webu]. SCM. azurewebsites. NET. Po načtení webu přejdete na kartu **ladit konzolu** . Pokud se chcete dostat do konzoly hostované pro Azure Portal z vaší aplikace, pokračujte v  >  **konzole** nástroje.

#### <a name="tools"></a>Nástroje
V nativních aplikacích pro Windows nebudou nástroje **příkazového testu, příkazy** **nslookup** a **tracert** fungovat prostřednictvím konzoly z důvodu omezení zabezpečení (pracují ve [vlastních kontejnerech Windows](../articles/app-service/quickstart-custom-container.md)). K vyplnění void se přidají dva samostatné nástroje. K otestování funkcí DNS jsme přidali nástroj s názvem **nameresolver.exe**. Syntaxe je:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Nameresolver můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete testovat, jestli máte nějaké nesprávně nakonfigurované služby DNS nebo možná nemáte přístup k vašemu serveru DNS. Server DNS, který vaše aplikace používá, můžete zobrazit v konzole nástroje tak, že prohlížíte proměnné prostředí WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe v současnosti nefungují v vlastních kontejnerech Windows.
>

K otestování připojení TCP k hostitelskému a kombinaci portů můžete použít další nástroj. Tento nástroj se nazývá **tcpping** a syntaxe je:

```console
tcpping.exe hostname [optional: port]
```

Nástroj **tcpping** vám oznamuje, jestli můžete kontaktovat konkrétního hostitele a port. Může zobrazit úspěch pouze v případě, že aplikace naslouchá na kombinaci hostitelů a portu a má přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Ladění přístupu k prostředkům hostovaným ve virtuální síti
Řada věcí může zabránit tomu, aby vaše aplikace dosáhla konkrétního hostitele a portu. Ve většině případů se jedná o jednu z těchto věcí:

* **Brána firewall je v cestě.** Pokud máte bránu firewall způsobem, zasáhnete časový limit TCP. V tomto případě je časový limit TCP 21 sekund. K otestování připojení použijte nástroj **tcpping** . Vypršení časových limitů TCP může být způsobeno počtem věcí mimo brány firewall, ale začněte.
* **Služba DNS není přístupná.** Časový limit DNS je 3 sekundy na server DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zjistit, jestli služba DNS funguje. Nástroj Nslookup nemůžete použít, protože nepoužívá službu DNS, pro kterou je nakonfigurovaná vaše virtuální síť. Pokud je nepřístupná, můžete mít bránu firewall nebo NSG blokující přístup k DNS nebo může být vypnutá.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se na první věci:

**Místní integrace virtuální sítě**
* Je vaše cílové místo na RFC1918 adrese a nemáte WEBSITE_VNET_ROUTE_ALL nastavené na 1?
* Existuje NSG blokující výstup z podsítě integrace?
* Pokud pracujete v rámci Azure ExpressRoute nebo VPN, je místní brána nakonfigurovaná tak, aby směrovala provoz zpět do Azure? Pokud máte přístup k koncovým bodům ve vaší virtuální síti, ale ne v místním prostředí, Projděte si své trasy.
* Máte dostatečná oprávnění k nastavení delegování v podsíti Integration? Během konfigurace integrace místní virtuální sítě je vaše podsíť integrace delegovaná na Microsoft. Web/serverových farem. Rozhraní Integration UI virtuální sítě deleguje podsíť do Microsoftu. Web/serverových farem automaticky. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení delegování, budete potřebovat někoho, kdo může nastavit atributy v podsíti Integration pro delegování podsítě. Pokud chcete integrační podsíť manuálně delegovat, vyhledejte v uživatelském rozhraní podsítě Azure Virtual Network a nastavte delegování pro Microsoft. Web/serverových farem.

**Brána – požadovaná integrace virtuální sítě**
* Je rozsah adres Point-to-site v rozsahu RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Zobrazuje se brána na portálu? Pokud vaše brána nefunguje, přeneste ji do záložního prostředí.
* Zobrazují se certifikáty jako synchronizované nebo se domníváte, že se změnila konfigurace sítě?  Pokud vaše certifikáty nejsou synchronizované nebo máte podezření, že došlo ke změně konfigurace vaší virtuální sítě, která nebyla synchronizovaná s vaší ASP, vyberte **synchronizovat síť**.
* Pokud pracujete v rámci sítě VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud máte přístup k koncovým bodům ve vaší virtuální síti, ale ne v místním prostředí, Projděte si své trasy.
* Pokoušíte se použít bránu koexistence, která podporuje obě body na lokalitu a ExpressRoute? Pro integraci virtuální sítě se nepodporují brány koexistence.

Ladění problémů se sítí je problém, protože nemůžete zjistit, co blokuje přístup k určitému hostiteli: kombinace portů. Mezi některé příčiny patří:

* Máte bránu firewall na hostiteli, která brání přístupu k portu aplikace z rozsahu IP adres typu Point-to-site. Překročení podsítí často vyžaduje veřejný přístup.
* Cílový hostitel nefunguje.
* Vaše aplikace je mimo provoz.
* Měli jste nesprávnou IP adresu nebo název hostitele.
* Vaše aplikace naslouchá na jiném portu, než jste očekávali. ID procesu můžete porovnat s portem naslouchání pomocí příkazu netstat-aon na hostiteli koncového bodu.
* Vaše skupiny zabezpečení sítě jsou nakonfigurovány tak, aby bránily přístupu k hostiteli a portu vaší aplikace z rozsahu IP adres typu Point-to-site.

Nevíte, jakou adresu vaše aplikace skutečně používá. Může to být jakákoli adresa v podsíti integrace nebo rozsah adres Point-to-site, takže je potřeba umožnit přístup z celého rozsahu adres.

Mezi další kroky ladění patří:

* Připojte se k virtuálnímu počítači ve virtuální síti a pokuste se připojit k hostiteli prostředků: port. K otestování přístupu TCP použijte příkaz PowerShellu **test-NetConnection**. Syntaxe je:

```powershell
test-netconnection hostname [optional: -Port]
```

* Zaveďte aplikaci na virtuální počítač a otestujte přístup k tomuto hostiteli a portu z konzoly z vaší aplikace pomocí **tcpping**.

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud se vaše aplikace nemůže připojit k místnímu prostředku, ověřte, jestli se můžete spojit s prostředkem z vaší virtuální sítě. K ověření přístupu TCP použijte příkaz prostředí PowerShell **test-NetConnection** . Pokud se váš virtuální počítač nemůže připojit k místnímu prostředku, připojení VPN nebo ExpressRoute možná není správně nakonfigurované.

Pokud se virtuální počítač hostovaný virtuální sítí může dostat k vašemu místnímu systému, ale vaše aplikace nemůže, příčinou může být jeden z následujících důvodů:

* Vaše trasy nejsou nakonfigurované s vaší podsítí nebo rozsahy adres Point-to-site v místní bráně.
* Vaše skupiny zabezpečení sítě blokují přístup k rozsahu IP adres Point-to-site.
* Místní brány firewall blokují provoz z rozsahu IP adres Point-to-site.
* Snažíte se spojit s adresou, která není RFC 1918, pomocí funkce regionální integrace virtuální sítě.
