---
title: Překlad názvů pro prostředky ve virtuálních sítích Azure
titlesuffix: Azure Virtual Network
description: Název scénáře řešení Azure IaaS, hybridní řešení mezi různé cloudové služby, služby Active Directory a pomocí vlastního serveru DNS.
services: virtual-network
documentationcenter: na
author: subsarma
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: subsarma
ms.openlocfilehash: 4a4a4c6a37e3c52054d7bc773ef04bf057709fdd
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025092"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Překlad názvů pro prostředky ve virtuálních sítích Azure

V závislosti na tom, jak používat Azure k hostování IaaS, PaaS a hybridní řešení může být potřeba povolit virtuální počítače (VM) a další prostředky nasazené ve virtuální síti komunikovat mezi sebou. I když můžete povolit komunikaci s použitím IP adresy, je mnohem jednodušší použít názvy, které lze snadno zapamatuje a nemění. 

Pokud prostředky nasazené ve virtuálních sítích musí překládat názvy domén na interní IP adresy, používat jeden ze dvou způsobů:

* [Překlad názvů poskytovaných Azure](#azure-provided-name-resolution)
* [Překlad názvů, který používá vlastní server DNS](#name-resolution-that-uses-your-own-dns-server) (která může být předávání dotazů na servery DNS, které poskytuje Azure) 

Typ překladu adres, které používáte závisí na tom, jak vaše prostředky musejí komunikovat mezi sebou. Následující tabulka popisuje scénáře a řešení rozlišení odpovídající název:

> [!NOTE]
> V závislosti na vašem scénáři můžete chtít použít Azure DNS Private Zones funkci, která je aktuálně ve verzi Public Preview. Další informace najdete v tématu o [použití Azure DNS pro privátní domény](../dns/private-dns-overview.md).
>

| **Scénář** | **Řešení** | **Přípona** |
| --- | --- | --- |
| Překlad mezi virtuálními počítači ve stejné virtuální síti nebo v Azure Cloud Services nachází instance rolí v rámci stejné cloudové služby. | [Azure DNS Private Zones](../dns/private-dns-overview.md) nebo [překlad názvů poskytovaných Azure](#azure-provided-name-resolution) |Název hostitele nebo plně kvalifikovaný název domény |
| Překlad mezi virtuálními počítači v různých virtuálních sítích nebo instance rolí v rámci různých cloudových služeb. |[Azure DNS Private Zones](../dns/private-dns-overview.md) nebo servery DNS spravovanými zákazníka předávání dotazů mezi virtuálními sítěmi pro překlad Azure (DNS proxy). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad ze služby Azure App Service (webové aplikace, funkce nebo Bot) pomocí integrace služby virtual network a instance rolí virtuálních počítačů ve stejné virtuální síti. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro překlad Azure (DNS proxy). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Název řešení z aplikace App Service Web Apps k virtuálním počítačům ve stejné virtuální síti. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro překlad Azure (DNS proxy). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Název řešení z aplikace App Service Web Apps v jedné virtuální sítě pro virtuální počítače v jiné virtuální sítě. |Spravované zákazníkem servery DNS předávání dotazů mezi virtuálními sítěmi pro překlad Azure (DNS proxy). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server-for-web-apps). |Pouze plně kvalifikovaný název domény |
| Rozlišení názvů místní počítače a služby z virtuálních počítačů nebo instancí role v Azure. |Spravované zákazníkem serverů DNS (místní řadič domény, řadiče místní domény jen pro čtení nebo sekundární DNS synchronizované, například pomocí přenosů zóny). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení Azure názvy hostitelů z místních počítačů. |Předávaly dotazy na zákazníkem spravovaný DNS proxy serveru v příslušné virtuální síti proxy server předává dotazy do Azure pro řešení. Zobrazit [překladu IP adresy serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Zpětné vyhledávání DNS pro interní IP adresy. |[Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Neuvedeno |
| Překlad mezi virtuální počítače nebo instance rolí, které jsou umístěné v různých cloudových službách, ne ve virtuální síti. |Není k dispozici. Připojení mezi virtuální počítače a instance rolí v rámci různých cloudových služeb není podporován mimo virtuální síť. |Neuvedeno|

## <a name="azure-provided-name-resolution"></a>Překlad názvů poskytovaných Azure

Spolu s rozlišení veřejné názvy DNS Azure poskytuje interní překlad adres pro virtuální počítače a instance rolí, které se nacházejí ve stejné virtuální síti nebo cloudovou službu. Virtuální počítače a instance v cloudové službě sdílejí stejnou příponu DNS, tak samotný název hostitele je dostačující. Ale v virtuální sítě nasazené pomocí modelu nasazení classic, různé cloudové služby mají různé přípony DNS. V takovém případě budete potřebovat plně kvalifikovaný název domény k překladu názvů mezi různými cloudovými službami. Do virtuální sítě nasazené pomocí modelu nasazení Azure Resource Manageru přípona DNS je konzistentní vzhledem k aplikacím přes virtuální síť, takže není potřeba plně kvalifikovaný název domény. Názvy DNS lze přiřadit k virtuálním počítačům a síťovým rozhraním. I když Azure překlad nevyžaduje žádnou konfiguraci, není to vhodnou volbou pro všechny scénáře nasazení, jak je uvedeno v předchozí tabulce.

> [!NOTE]
> Při použití cloudové služby webové a pracovní role, lze rovněž použít interní IP adresy instance role pomocí REST API služby Azure Service Management. Další informace najdete v tématu [Reference k REST API správy služby](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adresa je založen na číslo název a název instance role. 
> 
> 

### <a name="features"></a>Funkce

Překlad názvů poskytovaných Azure zahrnuje následující funkce:
* Snadné použití. Není nutná žádná konfigurace.
* Vysoká dostupnost Není nutné vytvořit a spravovat clustery vlastní servery DNS.
* Můžete použít službu ve spojení s vlastní servery DNS přeložit v místním i názvy hostitele Azure.
* Můžete použít překlad mezi virtuální počítače a instance rolí v rámci stejné cloudové služby, bez nutnosti plně kvalifikovaný název domény.
* Můžete použít překlad mezi virtuálními počítači ve virtuálních sítích, které používají model nasazení Azure Resource Manageru, bez nutnosti plně kvalifikovaný název domény. Virtuálním sítím v modelu nasazení classic požadovat plně kvalifikovaný název domény, když se překlad názvů v rámci různých cloudových služeb. 
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení, místo práce s automaticky generované názvy.

### <a name="considerations"></a>Požadavky

Body ke zvážení při používání Azure překlad:
* Přípona DNS Azure vytvořilo se nedá upravit.
* Nelze ručně zaregistrovat vlastní záznamy.
* Služba WINS a NetBIOS se nepodporují. Nejde zobrazit vaše virtuální počítače v Průzkumníku Windows.
* Názvy hostitelů musí být kompatibilní s DNS. Názvy musí používat pouze 0-9, a – z, a "-" a nesmí začínat ani končit '-'.
* Pro každý virtuální počítač je omezen provoz dotazu DNS. Omezení využití sítě by neměla mít vliv na většinu aplikací. Pokud se vyskytuje omezování žádostí, ujistěte se, že je povoleno ukládání do mezipaměti na straně klienta. Další informace najdete v tématu [konfigurace klienta DNS](#dns-client-configuration).
* Pouze virtuální počítače v prvních 180 cloudové služby jsou registrovány pro každou virtuální síť v modelu nasazení classic. Toto omezení se nevztahuje k virtuálním sítím v Azure Resource Manageru.

## <a name="dns-client-configuration"></a>Konfigurace klienta DNS

Tato část zahrnuje ukládání do mezipaměti na straně klienta a opakované pokusy na straně klienta.

### <a name="client-side-caching"></a>Ukládání do mezipaměti na straně klienta

Každý dotaz DNS musí být posílají přes sít. Ukládání do mezipaměti na straně klienta pomáhá snižovat latenci a zlepšit odolnost sítě blips řešení opakující se dotazy DNS z místní mezipaměti. Záznamy DNS obsahují mechanismus time to live (TTL), umožňující mezipaměť pro uložení záznamu pro co nejdéle bez dopadu na záznam aktuálnosti. Ukládání do mezipaměti na straně klienta je proto vhodný pro většinu situací.

Klient Windows DNS výchozí má integrovanou mezipaměť DNS. Některých distribucích systému Linux nezahrnují ukládání do mezipaměti ve výchozím nastavení. Pokud zjistíte, že existuje místní mezipaměti ještě není, přidejte do každého virtuálního počítače s Linuxem mezipaměť DNS.

Existuje mnoho různých DNS ukládání do mezipaměti balíčků dostupné (jako je například dnsmasq). Tady je postup instalace dnsmasq na nejběžnější distribuce:

* **Ubuntu (používá resolvconf)**:
  * Nainstalovat balíček dnsmasq s `sudo apt-get install dnsmasq`.
* **SUSE (používá netconf)**:
  * Nainstalovat balíček dnsmasq s `sudo zypper install dnsmasq`.
  * Povolit službu dnsmasq s `systemctl enable dnsmasq.service`. 
  * Spustit službu dnsmasq s `systemctl start dnsmasq.service`. 
  * Upravit **/etc/sysconfig/network/config**a změnit *NETCONFIG_DNS_FORWARDER = ""* k *dnsmasq*.
  * Aktualizovat resolv.conf s `netconfig update`pak můžete nastavit jako místního překladače DNS do mezipaměti.
* **OpenLogic (používá NetworkManager)**:
  * Nainstalovat balíček dnsmasq s `sudo yum install dnsmasq`.
  * Povolit službu dnsmasq s `systemctl enable dnsmasq.service`.
  * Spustit službu dnsmasq s `systemctl start dnsmasq.service`.
  * Přidat *předřaďte doménu názvové servery 127.0.0.1;* k **/etc/dhclient-eth0.conf**.
  * Restartujte síťovou službu s `service network restart`pak můžete nastavit jako místního překladače DNS do mezipaměti.

> [!NOTE]
> Balíček dnsmasq je pouze jeden z mnoha mezipamětí DNS k dispozici pro Linux. Před použitím, zkontrolujte jejich vhodnost pro vaše konkrétní potřeby a zkontrolujte, zda je nainstalována žádná mezipaměť.
> 
> 
    
### <a name="client-side-retries"></a>Opakované pokusy na straně klienta

DNS je primárně protokol UDP. Protože protokol UDP nezaručuje doručení zpráv, se v samotný protokol DNS zpracovává logiku opakování. Každá klient DNS (operačního systému) můžete vykazovat logiku opakování jiné, v závislosti na tvůrce příslušného předvolby:

* Operační systémy Windows opakujte po jedné sekundy a pak znovu po druhé dvě sekundy, čtyři sekundy a další čtyři sekundy. 
* Výchozí Linux nastavení opakované pokusy po pěti sekundách. Doporučujeme, abyste změna specifikace opakování pětkrát, v sekundových intervalech.

Zkontrolujte aktuální nastavení virtuálního počítače s Linuxem pomocí `cat /etc/resolv.conf`. Podívejte se na *možnosti* řádek, třeba:

```bash
options timeout:1 attempts:5
```

Soubor resolv.conf se obvykle generuje automaticky a by neměla být upravována. Konkrétní kroky pro přidání *možnosti* řádku se liší podle distribuce:

* **Ubuntu** (používá resolvconf):
  1. Přidat *možnosti* řádek a **/etc/resolvconf/resolv.conf.d/tail**.
  2. Spustit `resolvconf -u` aktualizovat.
* **SUSE** (používá netconf):
  1. Přidat *timeout:1 pokusy: 5* k **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parametr **/etc/sysconfig/network/config**. 
  2. Spustit `netconfig update` aktualizovat.
* **OpenLogic** (používá NetworkManager):
  1. Přidat *echo "možnosti timeout:1 pokusy: 5"* k **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  2. Aktualizace pomocí `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Překlad názvů, který používá vlastní server DNS

Tato část zahrnuje virtuální počítače, instance rolí a webové aplikace.

### <a name="vms-and-role-instances"></a>Virtuální počítače a instance rolí

Vaše potřeby rozlišení názvu může nad rámec funkcí poskytovaných službou Azure. Například můžete potřebovat použít domény Microsoft Windows Server Active Directory, přeložit názvy DNS mezi virtuálními sítěmi. Abyste měli pokryté tyto scénáře, Azure poskytuje možnost můžete použít vlastní servery DNS.

Servery DNS v rámci virtuální sítě může předat dotazy DNS na rekurzivní překladače v Azure. To umožňuje překládat názvy hostitelů v rámci této virtuální sítě. Například řadič domény (DC) v Azure můžete odpovídat na dotazy DNS pro jeho domény a předávat všechny ostatní dotazy do Azure. Předávání dotazů umožňuje virtuálním počítačům k místním prostředkům (prostřednictvím řadič domény) a názvy hostitelů poskytuje Azure (prostřednictvím předávání). Přístup k rekurzivní překladače v Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

Předávání DNS také umožňuje překlad názvů DNS mezi virtuálními sítěmi a povoluje vašich místních počítačů k překladu názvů hostitelů poskytuje Azure. Aby bylo možné přeložit název hostitele Virtuálního počítače, server DNS virtuálního počítače se musí nacházet ve stejné virtuální síti a nakonfigurovat tak, aby dotazy na názvy dopředné hostitele do Azure. Protože se liší v obou virtuálních sítích příponu DNS, můžete použít pravidla pro podmíněné předávání aby odesílalo dotazy DNS na správnou virtuální síť pro překlad. Následující obrázek ukazuje dvě virtuální sítě a místní sítí způsobem překlad názvů DNS mezi virtuálními sítěmi s použitím této metody. Předávání DNS příklad je k dispozici v [Galerie šablon rychlý start Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) a [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Role instance může provádět překlad názvů virtuálních počítačů ve stejné virtuální síti. Dělá to pomocí plně kvalifikovaný název, který se skládá z názvu hostitele Virtuálního počítače a **internal.cloudapp.net** příponu DNS. V takovém případě překladu je však pouze úspěšné, pokud má název virtuálního počítače, které jsou definovány v instanci role [schéma rolí (soubor .cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Instance rolí, které je potřeba provést překlad názvů virtuálních počítačů v jiné virtuální síti (plně kvalifikovaný název domény s použitím **internal.cloudapp.net** přípona) muset učinit pomocí metody popsané v této části (vlastní servery DNS předávání mezi dvě virtuální sítě).
>

![Diagram DNS mezi virtuálními sítěmi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Pokud používáte překlad názvů poskytuje Azure, Azure Dynamic Host Configuration Protocol (DHCP) poskytuje interní přípony DNS (**. internal.cloudapp.net**) pro každý virtuální počítač. Tato přípona umožňuje rozlišení názvu hostitele, protože záznamy název hostitele se **internal.cloudapp.net** zóny. Pokud používáte vlastní název řešení řešení, tato přípona není zadán k virtuálním počítačům, protože to naruší to jiné architektury DNS (jako je připojený k doméně scénáře). Místo toho Azure nabízí zástupný symbol nefunkční (*reddog.microsoft.com*).

V případě potřeby můžete určit interní přípony DNS pomocí Powershellu nebo rozhraní API:

* Pro virtuální sítě v modelech nasazení Azure Resource Manageru je k dispozici prostřednictvím příponu [síťové rozhraní REST API](/rest/api/virtualnetwork/networkinterfaces/get), [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) rutiny Powershellu a [az network nic show](/cli/azure/network/nic#az-network-nic-show) rozhraní příkazového řádku Azure.
* V modelech nasazení classic, je k dispozici prostřednictvím příponu [získání rozhraní API nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) volání nebo [Get-AzureVM – ladění](/powershell/module/servicemanagement/azure/get-azurevm) rutiny.

Pokud předávání dotazů do Azure není vyhovovala vašim potřebám, byste měli poskytnout řešení DNS. Musí vaše řešení DNS:

* Poskytují vhodného hostitele překlad, prostřednictvím [DDNS](virtual-networks-name-resolution-ddns.md), např. Pokud používáte DDNS, potřebujete zakázat úklidu záznamů DNS. Azure zapůjčení DHCP jsou dlouhé a úklid může odebrat záznamy DNS předčasně ukončen. 
* Zadejte odpovídající rekurzivní překlad názvů, chcete-li povolit překlad názvů externí domény.
* Být přístupné (TCP a UDP port 53) z klientů, které slouží a přístup k Internetu.
* Zabezpečit proti přístupu z Internetu, a zmírnit hrozby vyplývající z externí agentů.

> [!NOTE]
> Pro zajištění nejlepšího výkonu při použití virtuálních počítačů Azure jako servery DNS protokolu IPv6 by mělo být zakázáno. A [veřejnou IP adresu](virtual-network-public-ip-address.md) by mělo být přiřazeno k jednotlivým serverům DNS virtuálního počítače. Další výkonnostní analýzy a optimalizace, pokud používáte Windows Server jako DNS server, najdete v tématu [název řešení výkonu rekurzivní Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Webové aplikace
Předpokládejme, že je potřeba provést překlad z webové aplikace vytvořené pomocí služby App Service, propojené s virtuální sítí k virtuálním počítačům ve stejné virtuální síti. Kromě nastavení vlastní DNS server, který má server pro předávání DNS, který předává dotazy do Azure (virtuální IP adresy 168.63.129.16), proveďte následující kroky:
1. Povolení integrace služby virtual network pro vaši webovou aplikaci, pokud dosud neučinili, jak je popsáno v [integrujte svou aplikaci s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Na webu Azure Portal, který je hostitelem webové aplikace, vyberte pro plán služby App Service **synchronizovat síť** pod **sítě**, **integrace Virtual Network**.

    ![Snímek obrazovky překladu adres virtuální sítě](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Pokud potřebujete provádět překlad adres z webové aplikace vytvořené pomocí služby App Service, propojené s virtuální sítí k virtuálním počítačům v jiné virtuální sítě, budete muset použít vlastní servery DNS v obou virtuálních sítích, následujícím způsobem: 
* Nastavení serveru DNS v cílové virtuální sítě, na virtuálním počítači, který můžete také předat dotazy rekurzivní překladač v Azure (virtuální IP adresy 168.63.129.16). Předávání DNS příklad je k dispozici v [Galerie šablon rychlý start Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) a [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Nastavení DNS pro předávání do zdrojové virtuální sítě na virtuálním počítači. Nakonfigurujte tento server DNS pro předávání k předávání dotazů na server DNS v cílovou virtuální sítí.
* Konfigurace zdrojového serveru DNS v nastavení zdrojové virtuální sítě.
* Povolení integrace služby virtual network pro vaši aplikaci propojit s zdrojová virtuální síť, postupujte podle pokynů v [integrujte svou aplikaci s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Na webu Azure Portal, který je hostitelem webové aplikace, vyberte pro plán služby App Service **synchronizovat síť** pod **sítě**, **integrace Virtual Network**. 

## <a name="specify-dns-servers"></a>Určit servery DNS
Pokud používáte vlastní servery DNS, Azure poskytuje možnost zadat víc serverů DNS na virtuální síť. Můžete také zadat víc serverů DNS na síťové rozhraní (pro Azure Resource Manageru), nebo za cloudové služby (v případě modelu nasazení classic). Servery DNS zadaný pro síťové rozhraní nebo cloudovou službu získejte prioritu přes servery DNS zadaný pro virtuální síť.

> [!NOTE]
> Vlastnosti připojení k síti, jako je například server DNS IP adresy, se nesmí upravovat přímo v rámci virtuální počítače s Windows. Důvodem je, že může získat vymaže během služby opravy, pokud je virtuální síťový adaptér získá nahradí. 
> 
> 

Pokud používáte model nasazení Azure Resource Manageru, můžete určit servery DNS pro virtuální síť a síťové rozhraní. Podrobnosti najdete v tématu [Správa virtuální sítě](manage-virtual-network.md) a [Správa síťového rozhraní](virtual-network-network-interface.md).

Pokud používáte model nasazení classic, můžete zadat servery DNS pro virtuální síť na webu Azure Portal nebo [soubor s konfigurací sítě](https://msdn.microsoft.com/library/azure/jj157100). Pro cloudové služby, můžete určit servery DNS prostřednictvím [konfigurační soubor služby](https://msdn.microsoft.com/library/azure/ee758710) nebo pomocí prostředí PowerShell, [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Pokud změníte nastavení DNS pro virtuální síť nebo virtuální počítač, který je už nasazená, budete muset restartovat každý ovlivněné virtuální počítač se změny projevily.
> 
> 

## <a name="next-steps"></a>Další postup

Model nasazení Azure Resource Manageru:

* [Správa virtuální sítě](manage-virtual-network.md)
* [Správa síťového rozhraní](virtual-network-network-interface.md)

Model nasazení Classic:

* [Schéma konfigurace služby Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schéma konfigurace virtuální sítě](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurace virtuální sítě pomocí souboru konfigurace sítě](virtual-networks-using-network-configuration-file.md)
