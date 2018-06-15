---
title: Překlad názvů pro prostředky ve virtuální sítě Azure | Microsoft Docs
description: Název řešení scénáře Azure IaaS, hybridní řešení, která mezi jiné cloudové služby, služby Active Directory a pomocí serveru DNS.
services: virtual-network
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: subsarma
ms.openlocfilehash: 32d4f72afb4cd18e6b66c52eb78b2fc7b6b75cbd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31603653"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozlišení názvů u prostředků v Azure virtuální sítě

V závislosti na tom, jak používat Azure k hostování IaaS a PaaS, hybridní řešení možná muset povolit virtuální počítače (VM) a dalším prostředkům nasazený ve virtuální síti ke komunikaci mezi sebou. I když můžete povolit komunikaci pomocí IP adresy, je mnohem jednodušší použít názvy, které můžete snadno zapamatovaných a se nezmění. 

Pokud prostředky nasazené ve virtuálních sítích, je nutné přeložit názvy domény na interní IP adresy, mohou používat jednu ze dvou metod:

* [Rozlišení názvů Azure](#azure-provided-name-resolution)
* [Název řešení, která používá vlastní server DNS](#name-resolution-that-uses-your-own-dns-server) (které může předávat dotazy na servery DNS poskytnutých platformou Azure) 

Typ překladu adres, které můžete použít závisí na tom, jak vaše prostředky musí vzájemně komunikovat. Následující tabulka uvádí scénáře a odpovídající název řešení řešení:

> [!NOTE]
> V závislosti na scénáři můžete použít funkci Azure privátní DNS zóny, která je aktuálně ve verzi Public Preview. Další informace najdete v tématu [pomocí Azure DNS pro domény privátní](../dns/private-dns-overview.md).
>

| **Scénář** | **Řešení** | **Přípona** |
| --- | --- | --- |
| Překlad mezi virtuálními počítači umístěn ve stejné virtuální síti nebo Azure Cloud Services instancí rolí ve stejné cloudové služby. | [Zóny DNS privátní Azure](../dns/private-dns-overview.md) nebo [Azure překlad](#azure-provided-name-resolution) |název hostitele nebo plně kvalifikovaný název domény |
| Překlad mezi virtuální počítače v různých virtuálních sítích nebo instancí rolí v různých cloudové služby. |[Zóny DNS privátní Azure](../dns/private-dns-overview.md) nebo servery DNS spravované zákazníkem předávání dotazů mezi virtuálními sítěmi pro řešení Azure (DNS proxy). V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad ze Azure App Service (webová aplikace, funkce nebo robota) pomocí integrace virtuální sítě do instancí role nebo virtuální počítače ve stejné virtuální síti. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro řešení Azure (DNS proxy). V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Název řešení z webové aplikace služby App Service pro virtuální počítače ve stejné virtuální síti. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro řešení Azure (DNS proxy). V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Název řešení z webové aplikace služby App Service v jedné virtuální sítě pro virtuální počítače v jinou virtuální síť. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro řešení Azure (DNS proxy). V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server-for-web-apps). |Pouze plně kvalifikovaný název domény |
| Rozlišení názvů místní počítač a služby z virtuálních počítačů nebo instancí role v Azure. |Spravované zákazníkem servery DNS (místní řadič domény, řadiče místní domény jen pro čtení nebo sekundární DNS synchronizovat, například pomocí přenosy zón,). V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení Azure názvy hostitelů z místního počítače. |Předávaly dotazy na zákazníkem spravovaný proxy server DNS v odpovídající virtuální síť proxy server předává dotazy k Azure pro překlad. V tématu [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Reverse DNS pro interní IP adresy. |[Překlad názvů pomocí serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Neuvedeno |
| Překlad mezi virtuální počítače nebo instance rolí, které jsou umístěné v různých cloudové služby, není ve virtuální síti. |Není k dispozici. Připojení mezi virtuální počítače a instance rolí v různých cloudové služby není podporované mimo virtuální síť. |Neuvedeno|

## <a name="azure-provided-name-resolution"></a>Rozlišení názvů Azure

Společně s překladu názvů DNS veřejné Azure poskytuje interní překlad adres pro virtuální počítače a instance rolí, které se nacházejí v rámci stejné virtuální síti nebo cloudové služby. Virtuální počítače a instance v cloudové službě sdílejí stejnou příponu DNS, stačí samotná název hostitele. Ale ve virtuálních sítích nasazení pomocí modelu nasazení classic, jiné cloudové služby mají různé přípony DNS. V takovém případě musíte plně kvalifikovaný název domény přeložit názvy různých cloudových služeb. Ve virtuálních sítích nasazení pomocí modelu nasazení Azure Resource Manager je konzistentní v rámci celého virtuální sítě, příponu DNS, takže není potřeba plně kvalifikovaný název domény. Názvy DNS lze přiřadit do virtuálních počítačů a síťových rozhraní. I když Azure překlad nevyžaduje žádnou konfiguraci, není příslušnou volbu pro všechny scénáře nasazení, podle popisu v předchozí tabulce.

> [!NOTE]
> Při používání cloudové služby webových a pracovních rolí, můžete taky přejít interní IP adresy instance rolí pomocí rozhraní Azure Service Management REST API. Další informace najdete v tématu [služby referenční dokumentace rozhraní API REST správy](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adresa je založena na číslo název a instanci role. 
> 
> 

### <a name="features"></a>Funkce

Zadaný Azure překlad zahrnuje následující funkce:
* Snadné použití. Není nutná žádná konfigurace.
* Vysoká dostupnost Nemusíte vytvářet a spravovat clustery serverů DNS.
* Službu můžete použít ve spojení s vlastní servery DNS přeložit na místě a názvy hostitele Azure.
* Můžete použít překlad mezi virtuální počítače a instance rolí v rámci stejné cloudové služby, bez nutnosti plně kvalifikovaný název domény.
* Můžete použít překlad mezi virtuálními počítači ve virtuálních sítích, které používají model nasazení Azure Resource Manager, bez nutnosti plně kvalifikovaný název domény. Pokud jsou řešení názvů v různých cloudové služby, virtuálních sítí v modelu nasazení classic vyžadovat plně kvalifikovaný název domény. 
* Názvy hostitelů, které nejlépe popisují vaše nasazení, můžete místo práce s automaticky generovaných názvů.

### <a name="considerations"></a>Požadavky

Body vzít v úvahu při použití Azure překlad:
* Přípona DNS vytvořit Azure nelze upravit.
* Nelze ručně zaregistrovat vlastní záznamy.
* Služba WINS a NetBIOS nejsou podporovány. Nejde zobrazit vaše virtuální počítače v Průzkumníku Windows.
* Názvy hostitelů musí být kompatibilní s DNS. Názvy musí používat pouze 0 – 9,-z, a '-' a nesmí začínat ani končit '-'.
* Pro každý virtuální počítač je omezen provoz dotazu DNS. Omezení by nemělo mít vliv na většinu aplikací. Pokud je dodržena omezení požadavků, ujistěte se, zda je povoleno ukládání do mezipaměti na straně klienta. Další informace najdete v tématu [konfigurace klienta DNS](#dns-client-configuration).
* Pouze virtuální počítače v první 180 cloudové služby jsou registrované pro každou virtuální síť v modelu nasazení classic. Toto omezení se nevztahuje na virtuální sítě ve Správci prostředků Azure.

## <a name="dns-client-configuration"></a>Konfigurace klienta DNS

Tato část obsahuje ukládání do mezipaměti na straně klienta a klientské opakování.

### <a name="client-side-caching"></a>Ukládání do mezipaměti na straně klienta

Ne každý dotaz DNS musí být odesílají přes síť. Ukládání do mezipaměti na straně klienta pomáhá snížit latenci a zlepšit odolnost proti blips sítě, vyřešte opakující se dotazy DNS z místní mezipaměti. Záznamy DNS obsahovat mechanismus time to live (TTL), což umožňuje do mezipaměti ukládat záznam pro stejně dlouho bez dopadu na záznamů aktuálnosti. Ukládání do mezipaměti na straně klienta je proto vhodné pro většinu situacích.

Klient Windows DNS výchozí má integrovanou mezipaměť DNS. Některé Linuxových distribucích nezahrnují ukládání do mezipaměti ve výchozím nastavení. Pokud zjistíte, že není k dispozici místní mezipaměti již, přidejte do každého virtuálního počítače s Linuxem mezipaměť DNS.

Existuje několik různých DNS ukládání do mezipaměti balíčků dostupné (například dnsmasq). Tady je postup instalace dnsmasq na nejběžnější distribuce:

* **Ubuntu (používá resolvconf)**:
  * Instalovat balíček dnsmasq s `sudo apt-get install dnsmasq`.
* **SUSE (používá netconf)**:
  * Instalovat balíček dnsmasq s `sudo zypper install dnsmasq`.
  * Povolit službu dnsmasq s `systemctl enable dnsmasq.service`. 
  * Spusťte službu dnsmasq s `systemctl start dnsmasq.service`. 
  * Upravit **/etc/sysconfig/network/config**a změňte *NETCONFIG_DNS_FORWARDER = ""* k *dnsmasq*.
  * Aktualizovat resolv.conf s `netconfig update`, nastavení mezipaměti jako místní překladač služby DNS.
* **OpenLogic (používá NetworkManager)**:
  * Instalovat balíček dnsmasq s `sudo yum install dnsmasq`.
  * Povolit službu dnsmasq s `systemctl enable dnsmasq.service`.
  * Spusťte službu dnsmasq s `systemctl start dnsmasq.service`.
  * Přidat *předřadit domény názvové servery 127.0.0.1;* k **/etc/dhclient-eth0.conf**.
  * Restartujte síťovou službu s `service network restart`, nastavení mezipaměti jako místní překladač služby DNS.

> [!NOTE]
> Balíček dnsmasq je pouze jeden z mnoha mezipaměti DNS k dispozici pro Linux. Před jeho použitím, zkontrolujte jeho vhodnosti pro konkrétních potřeb a zkontrolujte, jestli je nainstalovaná žádná další mezipaměti.
> 
> 
    
### <a name="client-side-retries"></a>Opakování na straně klienta

DNS je primárně protokol UDP. Protože protokol UDP nezaručuje doručení zpráv, je logika opakovaných pokusů zpracována v samotný protokol DNS. Každý klient DNS (operačního systému) může vykazovat různé opakování logiku, v závislosti na nástroj creator předvoleb:

* Operační systémy Windows znova za jeden druhý a pak znovu za druhým dvou sekund, čtyři sekund do jiné čtyři sekund. 
* Výchozí opakování instalace Linux po pět sekund. Doporučujeme, abyste změna specifikace opakování na pětkrát, v intervalech jednu sekundu.

Zkontrolujte aktuální nastavení na virtuální počítač s Linuxem pomocí `cat /etc/resolv.conf`. Podívejte se na *možnosti* řádek, například:

```bash
options timeout:1 attempts:5
```

Soubor resolv.conf je obvykle automaticky generovaný a by neměla být upravována. Konkrétní kroky pro přidání *možnosti* řádku se liší podle distribuční:

* **Ubuntu** (používá resolvconf):
  1. Přidat *možnosti* řádek na **/etc/resolveconf/resolv.conf.d/head**.
  2. Spustit `resolvconf -u` aktualizovat.
* **SUSE** (používá netconf):
  1. Přidat *timeout:1 pokusů: 5* k **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parametr v **/etc/sysconfig/network/config**. 
  2. Spustit `netconfig update` aktualizovat.
* **OpenLogic** (používá NetworkManager):
  1. Přidat *echo "možnosti timeout:1 pokusů: 5"* k **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  2. Aktualizace s `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozlišení názvů, který používá serveru DNS

Tato část obsahuje virtuální počítače a instance rolí, webové aplikace.

### <a name="vms-and-role-instances"></a>Virtuální počítače a instance rolí

Potřeb název řešení může přesahovat funkce poskytované službou Azure. Může být nutné použít domény Microsoft Windows Server Active Directory, překlad názvů DNS mezi virtuálními sítěmi. Tak, aby pokrýval scénáře, Azure poskytuje možnost musíte použít vlastní servery DNS.

Servery DNS v rámci virtuální sítě může předat dotazy DNS překladače rekurzivní v Azure. To umožňuje překládat názvy hostitelů v rámci této virtuální sítě. Například řadič domény (DC), který běží v Azure můžete odpovídat na dotazy DNS pro své domény a předávat všechny další dotazy k Azure. Předávání dotazů umožňuje virtuálních počítačů k místním prostředkům (přes řadič domény) a názvy hostitelů Azure (prostřednictvím předávání). Přístup k rekurzivní překladače v Azure se poskytuje prostřednictvím virtuální IP adresa 168.63.129.16.

Předávání DNS také umožňuje překlad DNS mezi virtuálními sítěmi a umožňuje místní počítače překladu názvu hostitele Azure. Aby bylo možné přeložit název hostitele Virtuálního počítače, serveru DNS virtuální počítač se musí nacházet ve stejné virtuální síti a nakonfigurovat tak, aby dotazy na názvy dopředného hostitele do Azure. Protože přípona DNS se liší v každé virtuální sítě, můžete použít pravidla pro podmíněné předávání aby odesílalo dotazy DNS na správnou virtuální síť pro překlad. Následující obrázek ukazuje dvě virtuální sítě a k místní síti provádění překlad DNS mezi virtuálními sítěmi pomocí této metody. Předávání DNS příklad je k dispozici v [galerii šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) a [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Role instance můžete provádět překlad virtuálních počítačů v rámci stejné virtuální síti. Dělá to tak, pomocí plně kvalifikovaný název domény, který se skládá z názvu hostitele Virtuálního počítače a **internal.cloudapp.net** příponu DNS. V takovém případě překlad je však pouze úspěchu, pokud má název virtuálního počítače, které jsou definované v instanci role [Role schématu (soubor .cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Instance rolí, které je třeba provádět překlad virtuálních počítačů v jinou virtuální sítí (plně kvalifikovaný název domény pomocí **internal.cloudapp.net** příponu) potřeba to udělat pomocí metody popsané v této části (vlastní servery DNS předávání mezi dvě virtuální sítě).
>

![Diagram DNS mezi virtuálními sítěmi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Pokud používáte překlad názvů poskytuje Azure, Azure Dynamic Host Configuration Protocol (DHCP) poskytuje interní příponu DNS (**. internal.cloudapp.net**) pro každý virtuální počítač. Tato přípona umožňuje rozlišení názvu hostitele, protože se záznamy název hostitele **internal.cloudapp.net** zóny. Při použití vlastní název řešení řešení, tato přípona se nedodává k virtuálním počítačům, protože ji naruší jiné DNS architektury (jako je připojený k doméně scénáře). Místo toho Azure poskytuje nefunkční zástupný znak (*reddog.microsoft.com*).

V případě potřeby můžete určit interní příponu DNS pomocí prostředí PowerShell nebo rozhraní API:

* Pro virtuální sítě v modelech nasazení Azure Resource Manager, je k dispozici prostřednictvím přípona [síťové rozhraní REST API](/rest/api/virtualnetwork/networkinterfaces/get), [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) rutiny prostředí PowerShell a [az sítě seskupování zobrazit](/cli/azure/network/nic#az-network-nic-show) příkazu příkazového řádku Azure CLI.
* Ve model nasazení classic, je k dispozici prostřednictvím přípona [získat rozhraní API nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) volání nebo [Get-AzureVM – ladění](/powershell/module/azure/get-azurevm) rutiny.

Pokud předávání dotazů do Azure, není vašim potřebám, by měl poskytovat řešení DNS. Musí vaše řešení DNS:

* Zadejte odpovídající hostitelský překlad IP adres prostřednictvím [DDNS](virtual-networks-name-resolution-ddns.md), např. Pokud používáte DDNS, může je nutné zakázat proces úklidu záznamů DNS. Azure zapůjčení DHCP jsou dlouhé a proces úklidu může odebrat záznamy DNS předčasně. 
* Zadejte odpovídající rekurzivní překlad názvů povolit překlad názvů externí domény.
* Být přístupné (TCP a UDP na port 53) z klientů, které slouží a mít přístup k Internetu.
* Nutné zabezpečit před přístupem z Internetu, zmírnit hrozby představované externí agenty.

> [!NOTE]
> Pro nejlepší výkon, pokud používáte virtuální počítače Azure jako servery DNS, IPv6 by mělo být zakázáno. A [veřejnou IP adresu](virtual-network-public-ip-address.md) by se měla přiřadit k jednotlivým serverům DNS virtuálního počítače. Analýza výkonu další a optimalizace, když používáte Windows Server jako DNS server, najdete v tématu [název řešení výkon rekurzivní Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Webové aplikace
Předpokládejme, že budete muset provádět překlad adres z vaší webové aplikace vytvořené pomocí služby App Service, propojené do virtuální sítě, virtuální počítače ve stejné virtuální síti. Kromě nastavení vlastní DNS server, který má server pro předávání DNS, který předává dotazy do Azure (virtuální IP adresy 168.63.129.16), proveďte následující kroky:
1. Povolit integraci virtuální sítě pro webovou aplikaci, pokud neudělali, jak je popsáno v [integraci aplikace s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Na portálu Azure hostování webové aplikace, vyberte pro plán služby App Service **synchronizace sítě** pod **sítě**, **integrace virtuální sítě**.

    ![Snímek obrazovky překlad virtuální sítě](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Pokud potřebujete provést překlad z vaší webové aplikace vytvořené pomocí služby App Service, propojené do virtuální sítě, virtuální počítače v různých virtuální sítě, budete muset použít vlastní servery DNS na obě virtuální sítě, následujícím způsobem: 
* Nastavení serveru DNS ve virtuální síti cíl, na virtuální počítač, který může také předat dotazy rekurzivní překladač v Azure (virtuální IP adresy 168.63.129.16). Předávání DNS příklad je k dispozici v [galerii šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) a [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Nastavení DNS pro předávání ve virtuální síti zdroje na virtuálním počítači. Nakonfigurujte toto předávání DNS pro předávání dotazů na server DNS ve virtuální síti cíl.
* Nakonfigurujte zdrojového serveru DNS virtuální sítě zdroje.
* Povolit integraci virtuální sítě pro vaši webovou aplikaci k propojení virtuální sítě zdroje podle pokynů v [integraci aplikace s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Na portálu Azure hostování webové aplikace, vyberte pro plán služby App Service **synchronizace sítě** pod **sítě**, **integrace virtuální sítě**. 

## <a name="specify-dns-servers"></a>Zadejte servery DNS
Pokud používáte vlastní servery DNS, Azure poskytuje možnost určit více serverů DNS na jednu virtuální síť. Můžete také zadat více serverů DNS pro síťové rozhraní (pro Azure Resource Manager), nebo v cloudové služby (pro model nasazení classic). Servery DNS zadané pro síťové rozhraní nebo cloudové služby získat přednost přes servery DNS zadané pro virtuální síť.

> [!NOTE]
> Vlastnosti síťového připojení, jako je například server DNS IP adresy, by neměla být upravována přímo v rámci virtuálních počítačů Windows. Důvodem je, že se může získat vymazat během služby retušovat při získá nahradit virtuální síťový adaptér. 
> 
> 

Při použití modelu nasazení Azure Resource Manager, můžete zadat servery DNS pro virtuální sítě a síťové rozhraní. Podrobnosti najdete v tématu [spravovat virtuální sítě](manage-virtual-network.md) a [spravovat síťové rozhraní](virtual-network-network-interface.md).

Když používáte model nasazení classic, můžete určit servery DNS pro virtuální síť na portálu Azure nebo [soubor s konfigurací sítě](https://msdn.microsoft.com/library/azure/jj157100). Pro cloudové služby, můžete zadat servery DNS prostřednictvím [služby konfigurační soubor](https://msdn.microsoft.com/library/azure/ee758710) nebo pomocí prostředí PowerShell, [New-AzureVM](/powershell/module/azure/new-azurevm).

> [!NOTE]
> Pokud změníte nastavení DNS pro virtuální sítě nebo virtuálního počítače, který je už nasazená, budete muset restartovat jednotlivé ovlivněné virtuální počítače změny se projeví.
> 
> 

## <a name="next-steps"></a>Další postup

Model nasazení Azure Resource Manager:

* [Spravovat virtuální sítě](manage-virtual-network.md)
* [Správa síťového rozhraní](virtual-network-network-interface.md)

Model nasazení Classic:

* [Schéma konfigurace služby Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schéma konfigurace virtuální sítě](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurace virtuální sítě pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md)
