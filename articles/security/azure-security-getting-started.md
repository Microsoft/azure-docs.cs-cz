---
title: Začínáme s Microsoft Azure zabezpečení | Microsoft Docs
description: Tento článek obsahuje přehled o funkcích zabezpečení Microsoft Azure a obecné požadavky pro organizace, které provádíte migraci jejich prostředky do poskytovatele cloudových služeb.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: d0443128064332a37c95d5c39cd73b759a002cca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="getting-started-with-microsoft-azure-security"></a>Začínáme se zabezpečením Microsoft Azure

Při vytvoření nebo migraci IT prostředky do poskytovatele cloudových služeb, jsou spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky poskytují ke správě zabezpečení vaše cloudové prostředky.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure navíc poskytuje širokou škálu konfigurovatelných možností zabezpečení a umožňuje jejich nastavování, takže můžete zabezpečení přizpůsobit jedinečným požadavkům svého nasazení.

V tomto přehledovém článku o zabezpečení Azure se budeme zabývat následujícími tématy:

* Služby Azure a funkce, které můžete použít k zabezpečení služeb a dat v rámci Azure.
* Jak Microsoft zabezpečuje infrastruktury Azure k ochraně dat a aplikací.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Řízení přístupu k infrastruktuře IT, datům a aplikacím je mimořádně důležité. Microsoft Azure poskytuje tyto možnosti službami například Azure Active Directory (Azure AD), Azure Storage a podpora pro mnoho standardů a rozhraní API.

[Azure AD](../active-directory/active-directory-whatis.md) je úložiště identit a modul, který poskytuje ověřování, autorizace a řízení přístupu pro organizace, uživatelé, skupiny a objekty. Kromě toho Azure Active Directory nabízí vývojářům efektivní způsob integrace správy identit do aplikací. Standardní protokoly, jako [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx), a [OpenID Connect](http://openid.net/connect/) Zkontrolujte možné přihlášení na platformách, například .NET, Java, Node.js a PHP.

Rozhraní Graph API založené na protokolu REST vývojářům umožňuje čtení a zápis do adresáře z jakékoliv platformy. Prostřednictvím podpory pro [OAuth 2.0](http://oauth.net/2/), vývojáři mohou vytvářet mobilních a webových aplikací, které se integrují s společnosti Microsoft a třetích stran webovým rozhraním API a vytvářet své vlastní zabezpečenému webovému rozhraní API. Open Source klientské knihovny jsou dostupné pro .NET, Windows Store, iOS a Android. Další knihovny se vyvíjejí.

### <a name="how-azure-enables-identity-and-access-management"></a>Umožnění správy identit a přístupu v Azure

Azure AD lze použít jako samostatný cloudový adresář pro vaši organizaci nebo jako integrované řešení se stávající místní službou Active Directory. Některé funkce integrace zahrnují synchronizaci adresářů a jednotné přihlašování (SSO). Tyto rozšiřují využitelnost vaší existující místních identit do cloudu a zlepšení uživatelského rozhraní správce a uživatele.

K dalším možnostem správy identit a přístupu patří následující:

* Azure AD podporuje [jednotné přihlašování (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) k aplikacím SaaS bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace můžou také podporovat zřizování uživatelů a ukládání hesel do trezoru.
* Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet úložiště má primární klíč ([klíč účtu úložiště](https://msdn.microsoft.com/library/azure/ee460785.aspx), nebo SAK) a sekundární tajný klíč (sdílený přístupový podpis, nebo SAS).
* Azure AD poskytuje Identity jako služby pomocí federování pomocí [Active Directory Federation Services](../active-directory/fundamentals-identity.md), synchronizace a replikace s místních adresářů.
* [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) je služba služby Multi-Factor authentication, která od uživatelů vyžaduje přihlášení ověřili pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. Můžete použít s Azure AD k zabezpečené místní prostředků s Azure Multi-Factor Authentication server a také se vlastní aplikace a adresáře pomocí sady SDK.
* [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně bez nasazení řadiče domény. Může přihlásit do těchto virtuálních počítačů pomocí svoje podnikové přihlašovací údaje služby Active Directory a spravovat virtuální počítače připojené k doméně pomocí zásad skupiny k vynucení směrné plány zabezpečení na všechny vaše virtuální počítače Azure.
* [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje službu správy vysokou dostupností globální identity pro určených aplikace, která je škálovatelná pro stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Uživatele můžete přihlásit ke všem aplikacím, které prostřednictvím přizpůsobitelné pomocí svých účtů na sociálních nebo vytvořením nové přihlašovací údaje.

## <a name="data-access-control-and-encryption"></a>Řízení přístupu k datům a šifrování

Společnost Microsoft využívá zásady dělení zodpovědnosti a [nejnižších možných oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) v rámci všech operací v Azure. Přístup k datům ze strany pracovníků podporu Azure vyžaduje, abyste udělili explicitní jednorázové oprávnění, které se zaznamená v protokolu a audituje se. Po dokončení požadovaného používání se oprávnění odvolá.

Azure také poskytuje několik možností pro ochranu dat při přenosu i v klidu. To zahrnuje šifrování dat, soubory, aplikace, služby, komunikace a jednotky. Můžete šifrovat informace před jeho umístění v Azure a také ukládání klíčů ve vašem místních datových center.

![Microsoft Antimalware v Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Technologie šifrování v Azure

Podrobné informace o přístupu správce k prostředí předplatného můžete shromažďovat prostřednictvím [sestav Azure AD](../active-directory/active-directory-reporting-audit-events.md). Můžete nakonfigurovat [nástroj BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx) na virtuální pevné disky obsahující citlivé údaje v Azure.

K dalším možnostem v Azure, které vám pomohou zabezpečit data, patří následující:

* Vývojáři aplikace můžete vytvářet šifrování do aplikací se nasadit v Azure pomocí Windows [rozhraní CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) a rozhraní .NET Framework.
* Úplné řízení klíče pomocí šifrování na straně klienta pro úložiště objektů Blob v Azure. Služba úložiště se ke klíčům nedostane a nedokáže data dešifrovat.
* [Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (s [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) poskytuje souborů a dat na úrovni šifrování a úniku dat prevence přes správu na základě zásad přístupu.
* Azure podporuje [úrovni tabulky a sloupce úroveň šifrování (TDE/vy)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) v systému SQL Server virtuální počítače a třetí strany podporuje místní servery správy klíčů v datových centrech.
* Klíče účtu úložiště, sdílené přístupové podpisy, certifikáty pro správu a další klíče jsou pro každého klienta Azure jedinečné.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) hybridní storage šifruje data prostřednictvím pár 128-bit veřejného a privátního klíče před nahráním do úložiště Azure.
* Azure podporuje a používá řada mechanismů šifrování, včetně SSL/TLS, protokol IPsec a AES, v závislosti na datové typy, kontejnery a přenosy.

## <a name="virtualization"></a>Virtualizace

Platforma Azure používá virtualizované prostředí. Uživatelské instance fungovat jako samostatné virtuální počítače, které nemají přístup k fyzického hostitelského serveru a tato izolace je požadováno pomocí fyzických [úrovně oprávnění procesoru (prstenec-0 nebo prstenec-3)](https://en.wikipedia.org/wiki/Protection_ring).

Úroveň 0 má nejvyšší oprávnění, úroveň 3 nejnižší. Hostovaný operační systém běží v 1 menší privilegovaných prstenec, aplikace a spustit v nejnižšími oprávněními prstenec 3. Tato virtualizace fyzických prostředků vede k naprostému oddělení hostovaného operačního systému od hypervisoru. Důsledkem je další oddělení zabezpečení mezi těmito dvěma stranami.

Azure hypervisor funguje jako micro jádra a předává všechny požadavky na hardware přístup z hostované virtuální počítače na hostitele pro zpracování pomocí sdílené paměti rozhraní s názvem VMBus. Tento postup brání uživatelům v získání přímého přístupu k systému pro čtení, zápis a spouštění a snižuje riziko sdílení systémových prostředků.

![Microsoft Antimalware v Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Způsob implementace virtualizace v Azure

Azure používá bránu firewall hypervisoru (paket filtr), která je implementována v hypervisoru a nakonfigurované agentem kontroleru prostředků infrastruktury. Tato funkce pomáhá chránit klienty před neoprávněným přístupem. Ve výchozím nastavení, je veškerý provoz při vytvoření virtuálního počítače a pak agenta kontroleru prostředků infrastruktury nakonfiguruje filtr paketů přidat zablokované *pravidel a výjimek* umožňující autorizovaný přenos.

Programují se tady dvě kategorie pravidel:

* **Počítač pravidla konfigurace nebo infrastrukturu**: ve výchozím nastavení, všechna komunikace je zablokovaná. Existují výjimky, které umožňují virtuálního počítače do odesílat a přijímat přenosy DHCP a DNS. Virtuální počítače můžete také odesílá data na "veřejná" internet a odesílá data na ostatní virtuální počítače v clusteru a je server aktivace operačního systému. Virtuální počítače seznam povolených odchozí cíle nezahrnuje Azure směrovač podsítě, správu Azure back end a dalších vlastností společnosti Microsoft.
* **Role konfigurační soubor**: definuje příchozí seznamy řízení přístupu (ACL) na základě modelu služby klienta. Například pokud klient má front-end webový na portu 80 pro určité virtuální počítač, pak Azure otevře port TCP 80 pro všechny IP adresy Pokud konfigurujete koncový bod v [modelu nasazení Azure classic](../azure-resource-manager/resource-manager-deployment-model.md). Pokud virtuální počítač má back end a pracovní role, která běží, pak otevře role pracovního procesu pouze k virtuálnímu počítači v rámci stejné klienta.

## <a name="isolation"></a>Izolace

Požadavek na zabezpečení jinou důležitá cloudu je oddělení, aby se zabránilo neoprávněnému i neúmyslně se překrývající přenosu informací mezi nasazení ve víceklientské architektuře sdílené.

Azure implementuje [sítě řízení přístupu](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) a oddělení prostřednictvím izolace sítě VLAN, seznamy ACL, zatížení nástroje pro vyrovnávání a filtry IP adres. Omezuje externích přenosů příchozí porty a protokoly na virtuální počítače, které definujete. Azure implementuje filtrování Pokud chcete zabránit podvodná provoz a omezit příchozí a odchozí provoz na komponenty platformy důvěryhodné sítě. Zásady toku provozu jsou implementovány v zařízeních ochrany hranic, která ve výchozím nastavení odmítají provoz.

![Microsoft Antimalware v Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

Překlad síťových adres (NAT) slouží k oddělení vnitřního síťového provozu od vnějšího. Vnitřní provoz nelze směrovat vně. [Virtuální IP adresy](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx), pro které je povoleno vnější směrování, se přeloží na [interní dynamické IP adresy](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx), které je možné směrovat jen v rámci Azure.

Externí přenosy na virtuálních počítačích Azure je bránou firewall pomocí seznamů řízení přístupu na směrovačích, nástroje pro vyrovnávání zatížení, a přepínače vrstvy 3. Povoleny jsou jen konkrétní známé protokoly. Seznamy ACL jsou zavedené omezit přenosy pocházející z hostované virtuální počítače do jiné sítě VLAN používá pro správu. Kromě toho provoz filtrovat pomocí filtrů IP adres na hostiteli, který operačního systému další omezuje provoz v obou vrstvách odkaz a sítě data.

### <a name="how-azure-implements-isolation"></a>Způsob implementace izolace v Azure

Kontroleru prostředků infrastruktury Azure zodpovídá za přidělování prostředků infrastruktury pro úlohy klientů a spravuje jednosměrná komunikace z hostitele pro virtuální počítače. Azure hypervisor vynucuje paměti a proces oddělení mezi virtuálními počítači a bezpečně směruje síťový provoz klientům hostovaného operačního systému. Azure také implementuje izolaci pro klienty, úložiště a virtuální sítě.

* Každý klient Azure AD je logicky izolované pomocí hranice zabezpečení.
* Účty služby Azure storage jsou jedinečné pro každé předplatné a přístupu musí být ověřeny pomocí klíč účtu úložiště.
* Virtuální sítě jsou logicky izolované pomocí kombinace jedinečný privátní IP adresy, brány firewall a seznamy ACL IP. Nástroje pro vyrovnávání zatížení směrují provoz do příslušných klientů na základě definic koncových bodů.

## <a name="virtual-networks-and-firewalls"></a>Virtuální sítě a brány firewall

[Distribuované a virtuální sítí](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) v Azure Nápověda zajistěte, aby byl privátní síťový provoz logicky izolované od provoz na jiné virtuální sítě Azure.

![Microsoft Antimalware v Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Vaše předplatné může obsahovat více izolované privátní sítě (a bránu firewall, Vyrovnávání zatížení a překlad síťových adres).

Azure poskytuje pro logické oddělování provozu tři primární úrovně oddělení sítí v každém clusteru Azure. [Virtuální místní sítě](https://azure.microsoft.com/services/virtual-network/) (VLAN) slouží k oddělení provozu zákazníků od zbytku sítě Azure. Přístup k síti Azure zvenčí clusteru je omezen pomocí nástrojů pro vyrovnávání zatížení.

Síťový provoz do a z virtuálních počítačů musí projít přes virtuální přepínač hypervisoru. Komponenta filtru IP v kořenovém operačním systémem izoluje virtuální počítač kořenové z hostované virtuální počítače a hostované virtuální počítače od sebe navzájem. Provede filtrování provozu omezit komunikaci mezi uzly klienta a veřejného Internetu (podle konfigurace služby zákazníka), je oddělení od ostatních klientů.

Filtr IP pomáhá zabránit hostované virtuální počítače z:

* Generování podvodná provoz.
* Příjem přenosů není adresovaný.
* Směrování provozu do koncových bodů chráněných infrastruktury.
* Odesílání nebo přijímání nevhodných vysílání provoz.

Můžete umístit virtuální počítače do [virtuálních sítí Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Tyto virtuální sítě jsou podobné sítím, které konfigurujete v místních prostředích, kde jsou obvykle přidruženy k virtuálnímu přepínači. Virtuální počítače připojené ke stejné virtuální síti může komunikovat s navzájem bez další konfigurace. Můžete také nakonfigurovat různé podsítě v rámci virtuální sítě.

K zajištění bezpečnější komunikace ve vaší virtuální síti můžete použít následující technologie Azure Virtual Network:

* [**Skupin zabezpečení (Nsg) sítě**](../virtual-network/virtual-networks-nsg.md). Můžete vytvořit skupinu NSG k řízení přenosu do jednoho nebo více instancí virtuálního počítače ve virtuální síti. Skupina zabezpečení sítě obsahuje pravidla pro řízení přístupu, která povolují nebo zakazují provoz na základě směru přenosu, protokolu, zdrojové adresy a portu a cílové adresy a portu.
* [**Uživatelem definované směrování**](../virtual-network/virtual-networks-udr-overview.md). Můžete řídit směrování paketů prostřednictvím virtuálního zařízení ve vytváření trasy definované uživatelem, které určují další segment směrování paketů odesílaných do konkrétní podsítě přejít na zařízení zabezpečení virtuální sítě.
* [**Předávání IP**](../virtual-network/virtual-networks-udr-overview.md). Zařízení pro zabezpečení virtuální sítě musí být schopné přijímat příchozí provoz, který mu není adresovaný. Pokud chcete povolit virtuální počítač přijímání dat adresovaných jiným cílům, povolíte předávání IP pro virtuální počítač.
* [**Vynucené tunelování**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Vynucené tunelování umožňuje přesměrování nebo "Vynutit" veškerý provoz vázaný Internet generované virtuální počítače ve virtuální síti zpět na místní umístění přes tunelové propojení VPN typu site-to-site pro kontrolu a auditování
* [**Seznamy ACL koncových bodů**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Můžete řídit, které počítače jsou povolená příchozí připojení z Internetu do virtuálního počítače ve vaší virtuální síti definováním seznamy ACL koncových bodů.
* [**Řešení zabezpečení pro partnerské sítě**](https://azure.microsoft.com/marketplace/). Existuje řada partnerských řešení zabezpečení sítě, kterým můžete přistupovat z Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Jak Azure implementuje virtuální sítě a brány firewall

Azure implementuje filtrováním paketů brány firewall na všechny hostitele a hostů virtuální počítače ve výchozím nastavení. Bitové kopie operačního systému Windows z Azure Marketplace taky mít ve výchozím nastavení povolená brána Windows Firewall. Nástroje pro vyrovnávání zatížení v hraniční sítě Azure veřejné řízení komunikace na základě na seznamy ACL IP, které spravují správci zákazníka.

Pokud Azure přesouvá data zákazníka v rámci běžných operací nebo při havárii, činí tak přes privátní šifrované komunikační kanály. Další možnosti zaměstnaní Azure pro použití v virtuální sítě a brány firewall jsou:

* **Brány firewall hostitele nativní**: Azure Service Fabric a Azure Storage spouštět v nativním operační systém, který nemá žádné hypervisoru. Proto je brána windows firewall nakonfigurovaný s předchozí dvě sady pravidel. Kvůli optimalizaci výkonu běží úložiště nativně.
* **Brány firewall hostitele**: bránu firewall hostitele je ochrana hostitelský operační systém, který spouští hypervisor. Pravidla jsou naprogramovaný tak, aby Povolit jenom řadič Service Fabric a přejít polí, aby komunikoval s hostitelský operační systém na určitém portu. Další výjimkou je povolení odpovědi DHCP a odpovědí DNS. Azure pomocí konfiguračního souboru počítač, který má šablonu pravidla brány firewall pro hostitelský operační systém. Hostitel, sám je chráněn před útokem externí brány Windows firewall nakonfigurovat tak, aby umožňovala komunikaci pouze ze známých, ověřený zdroje.
* **Brány firewall hosta**: replikuje pravidla ve filtru paketů přepínač virtuální počítač ale naprogramovaných v různých softwaru (například na brány Windows Firewall požadovaný hostovaný operační systém). Brána firewall hosta virtuálního počítače lze nakonfigurovat omezit komunikaci z hostovaného virtuálního počítače, nebo i v případě, že komunikace je povoleno podle konfigurace v hostiteli filtr IP. Můžete například použít bránu firewall hosta virtuálního počítače a omezit tak komunikaci mezi dvěma vaší virtuální sítě, nakonfigurovaný pro připojení k sobě.
* **Úložiště brány firewall (FW)**: filtry provoz jenom na porty 80/443 a další porty potřebné nástroje v bráně firewall na front-endu úložiště. V bráně firewall na back-end úložiště omezuje komunikaci s pocházejí pouze z front-endové úložné servery.
* **Brána virtuální sítě**: [brány virtuální sítě Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) slouží jako brána mezi různými místy, připojení k místním serverům úlohy v Azure Virtual Network. Je vyžadován pro připojení k místní servery pomocí [tunelových propojení IPsec site-to-site VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), nebo pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) okruhů. U tunelového propojení protokolu IPsec/IKE VPN brány provádět ověřování metodou handshake IKE a vytvořit tunelových propojení IPsec S2S VPN mezi virtuálními sítěmi a místními servery. Brány virtuální sítě také ukončit [point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Zabezpečený vzdálený přístup

Pro data uložená v cloudu musí být aktivováno dostatečné zabezpečení, aby se zabránilo zneužití a zachovala se důvěrnost a integrita během přenosu. To zahrnuje mechanismy řízení sítě, které jsou propojeny s identitou organizace umožňující audit a založenou na zásadách a s mechanismy správy přístupu.

Vestavěné kryptografické technologie umožňují šifrovat komunikaci v rámci nasazení a mezi nimi, mezi oblastmi Azure a z Azure do místních datových center. Přístup správce k virtuálním počítačům prostřednictvím [relací vzdálené plochy](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [vzdáleného prostředí Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), a portál Azure je zašifrovaný vždycky.

Bezpečně rozšířit vaše místní datacentra do cloudu, Azure poskytuje i [site-to-site VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) a [point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md), plus vyhrazené propojení s [ExpressRoute](../expressroute/expressroute-introduction.md) (připojení k virtuálním sítím Azure prostřednictvím sítě VPN se šifrují).

### <a name="how-azure-implements-secure-remote-access"></a>Způsob implementace zabezpečeného vzdáleného přístupu v Azure

Připojení k portálu Azure musí ověřit a vyžadují SSL/TLS. Můžete nakonfigurovat certifikáty pro správu, aby bylo možné využívat zabezpečenou správu. Zabezpečení Standardní protokoly, jako [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) a [IPsec](https://en.wikipedia.org/wiki/IPsec) jsou plně podporované.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) vám umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastrukturou, která se nachází ve vašem umístění nebo v prostředí ve společném umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická internetové odkazy. V některých případech přenosu dat mezi místními umístěními a Azure pomocí připojení ExpressRoute přispět také k výraznému snížení nákladů.

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Azure poskytuje ověřený protokolování zabezpečení relevantní události, které generují záznam pro audit a je analyzována na být odolný proti manipulaci. To zahrnuje informace o systému, jako je například protokolů událostí zabezpečení v Azure infrastruktury virtuálních počítačů a Azure AD. Sledování událostí zabezpečení zahrnuje shromažďování událostí, jako jsou například změny v DHCP nebo DNS IP adresy serverů; Pokus o přístup k porty, protokoly nebo IP adresy, které jsou blokována návrhu; změny v nastavení zabezpečení zásad nebo brány firewall; Vytvoření účtu nebo skupiny; a neočekávané procesy nebo instalace ovladače.

![Microsoft Antimalware v Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Protokoly auditu se záznamy přístupu a aktivit privilegovaných uživatelů, pokusů o oprávněný a neoprávněný přístup, výjimek systému a událostí zabezpečení informací se uchovávají po nastavenou dobu. Uchovávání protokolů je na vašem uvážení, protože shromažďování a uchovávání protokolů konfigurujete podle svých požadavků.

### <a name="how-azure-implements-logging-and-monitoring"></a>Způsob implementace protokolování a monitorování v Azure

Azure nasazuje agenty pro správu (MA) a agenty monitorování zabezpečení Azure (ASM) do každého výpočetního uzlu, uzlu úložiště nebo uzlu prostředku infrastruktury v rámci správy bez ohledu na to, zda je nativní nebo virtuální. Každý agent pro správu je nakonfigurován tak, aby se ověřoval v rámci účtu úložiště týmu služeb s použitím certifikátu získaného z úložiště certifikátů Azure a aby předával předem konfigurované diagnostické údaje a údaje o událostech do účtu úložiště. Tito agenti nejsou nasazeni ve virtuálních počítačích zákazníků.

Správci Azure využívají přístup k protokolům prostřednictvím webového portálu, který zajišťuje ověřený a kontrolovaný přístup k protokolům. Správce může protokoly parsovat, filtrovat, zjišťovat jejich korelaci a analyzovat je. Účty úložiště týmu služeb Azure pro protokoly jsou chráněny před přímým přístupem správců, a tím brání manipulaci s protokoly.

Microsoft shromažďuje protokoly ze síťových zařízení pomocí protokolu Syslog a ze serverů hostitele pomocí služby Microsoft Audit Collection Services (ACS). Tyto protokoly jsou umístěny do protokolu databáze, ze kterého jsou generovány výstrahy pro podezřelé události. Správce má k těmto protokolům přístup a může je analyzovat.

[Diagnostika Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) je funkce Azure, která umožňuje shromažďovat diagnostická data z aplikace, která běží v Azure. Toto je diagnostických dat pro ladění a řešení potíží s měření výkonu, sledování využití prostředků, analýza provozu, plánování kapacity a auditování. Po shromáždění lze diagnostická data přenést do účtu úložiště Azure a zajistit jejich uchování. Přenosy lze buď naplánovat nebo na vyžádání.

## <a name="threat-mitigation"></a>Zmírnění hrozeb

Kromě izolace, šifrování a filtrování využívá Azure mnoho mechanismů snižování rizik při ohrožení a řadu procesů pro ochranu infrastruktury a služeb. Mezi ně patří interní kontrolní mechanismy a technologie, které slouží ke zjišťování a odstraňování pokročilých ohrožení, jako jsou například DDoS, eskalace oprávnění a ohrožení uvedená v žebříčku [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Kontrolní mechanismy zabezpečení a procesy řízení rizik, které Microsoft aplikuje v rámci zabezpečení své cloudové infrastruktury, snižují riziko incidentů zabezpečení. V případě, že dojde k incidentu, je připraven reagovat kdykoli týmem zabezpečení Incident správy (SIM) v rámci týmu Microsoft Online Services zabezpečení a dodržování předpisů (OSSC).

### <a name="how-azure-implements-threat-mitigation"></a>Způsob implementace snižování rizik při ohrožení v Azure

Azure má kontrolních mechanismů pro zabezpečení na místě implementovat ke zmírnění hrozeb a můžete zmírnit potenciální hrozby ve svých prostředích zákazníků. Následující seznam shrnuje možnosti zmírnění hrozeb, které nabízí Azure:

* [Azure antimalwarových](azure-security-antimalware.md) je povoleno ve výchozím nastavení ve všech serverech infrastruktury. Volitelně ji můžete povolit v rámci virtuálních počítačů.
* Microsoft provádí nepřetržité monitorování serverů, sítí a aplikací, zjišťuje ohrožení a brání zneužití. Automatizované výstrahy upozorní správce na neobvyklé chování, a tím jim umožní provést nápravné opatření v případě vnitřních i vnějších hrozeb.
* Můžete nasadit řešení třetí strany zabezpečení v rámci vašich předplatných, jako jsou brány firewall webových aplikací z [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* Přístup společnosti Microsoft k průnikům testování zahrnuje "[síťových adaptérů Red](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)," což zahrnuje Odborníci v oblasti zabezpečení Microsoft napadení (bez zákazníka) za provozu produkční systémy v Azure k testování obranu proti reálného, pokročilé, trvalé hrozby.
* Integrované nasazovací systémy zvládají distribuci a instalaci oprav zabezpečení v rámci celé platformy Azure.

## <a name="next-steps"></a>Další postup

[Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/)

[Blog týmu zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/)

[Středisko Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Blog služby Active Directory](http://blogs.technet.com/b/ad/)
