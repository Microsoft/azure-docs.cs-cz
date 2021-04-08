---
title: Překlad názvů pro prostředky ve virtuálních sítích Azure
titlesuffix: Azure Virtual Network
description: Scénáře překladu názvů pro Azure IaaS, hybridní řešení, mezi různými Cloud Services, Active Directory a používání vlastního serveru DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222849"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Překlad názvů pro prostředky ve virtuálních sítích Azure

V závislosti na tom, jak Azure používáte k hostování IaaS, PaaS a hybridních řešení, možná budete muset virtuálním počítačům a dalším prostředkům nasazeným ve virtuální síti nasazovat, aby vzájemně komunikovaly. I když můžete povolit komunikaci pomocí IP adres, je mnohem jednodušší použít názvy, které se dají snadno pamatovat a neměnit. 

Když prostředky nasazené ve virtuálních sítích potřebují překládat názvy domén na interní IP adresy, můžou použít jednu ze tří metod:

* [Azure DNS privátní zóny](../dns/private-dns-overview.md)
* [Překlad názvů poskytovaných službou Azure](#azure-provided-name-resolution)
* [Překlad názvů, který používá vlastní server DNS](#name-resolution-that-uses-your-own-dns-server) (který může předávat dotazy na servery DNS poskytované Azure)

To, který typ překladu názvů použijete, závisí na tom, jak spolu vaše prostředky potřebují vzájemně komunikovat. Následující tabulka ilustruje scénáře a odpovídající řešení pro překlad názvů:

> [!NOTE]
> Azure DNS privátní zóny jsou preferované řešení a nabízí flexibilitu při správě zón a záznamů DNS. Další informace najdete v tématu o [použití Azure DNS pro privátní domény](../dns/private-dns-overview.md).

> [!NOTE]
> Pokud používáte službu DNS poskytovanou systémem Azure, pak se pro vaše virtuální počítače automaticky použije odpovídající přípona DNS. Pro všechny ostatní možnosti musíte buď použít plně kvalifikované názvy domény (FQDN), nebo ručně použít odpovídající příponu DNS na virtuální počítače.

| **Scénář** | **Řešení** | **Přípona DNS** |
| --- | --- | --- |
| Překlad názvů mezi virtuálními počítači umístěnými ve stejné virtuální síti nebo instancemi rolí Azure Cloud Services ve stejné cloudové službě. | [Azure DNS privátní zóny](../dns/private-dns-overview.md) nebo [Překlad adres poskytovaných službou Azure](#azure-provided-name-resolution) |Název hostitele nebo plně kvalifikovaný název domény |
| Překlad názvů mezi virtuálními počítači v různých virtuálních sítích nebo instancích rolí v různých cloudových službách. |[Azure DNS privátní zóny](../dns/private-dns-overview.md) nebo servery DNS spravované zákazníkem, které předávají dotazy mezi virtuálními sítěmi pro účely překladu Azure (DNS proxy). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad názvů z Azure App Service (webová aplikace, funkce nebo robot) pomocí integrace virtuální sítě do instancí rolí nebo virtuálních počítačů ve stejné virtuální síti. |Servery DNS spravované zákazníky, které předávají dotazy mezi virtuálními sítěmi pro překlad prostřednictvím Azure (DNS proxy). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad názvů z App Service Web Apps na virtuální počítače ve stejné virtuální síti. |Servery DNS spravované zákazníky, které předávají dotazy mezi virtuálními sítěmi pro překlad prostřednictvím Azure (DNS proxy). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad názvů z App Service Web Apps v jedné virtuální síti na virtuální počítače v jiné virtuální síti |Servery DNS spravované zákazníky, které předávají dotazy mezi virtuálními sítěmi pro překlad prostřednictvím Azure (DNS proxy). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Rozlišení místních počítačů a názvů služeb z virtuálních počítačů nebo instancí rolí v Azure. |Servery DNS spravované zákazníky (místní řadič domény, místní řadič domény jen pro čtení nebo sekundární služba DNS, která se synchronizuje pomocí zónových přenosů, například). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad názvů hostitelů Azure z místních počítačů. |Předává dotazy do služby DNS spravované zákazníkem proxy server v odpovídající virtuální síti, proxy server předává dotazy do Azure pro řešení. Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Reverzní DNS pro interní IP adresy |[Azure DNS privátní zóny](../dns/private-dns-overview.md) nebo [překlad IP adres poskytované Azure](#azure-provided-name-resolution) [pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Neuvedeno |
| Překlad názvů mezi virtuálními počítači nebo instancemi rolí umístěných v různých cloudových službách, nikoli ve virtuální síti. |Neužívá se. Připojení mezi virtuálními počítači a instancemi rolí v různých cloudových službách není podporováno mimo virtuální síť. |Neuvedeno|

## <a name="azure-provided-name-resolution"></a>Překlad názvů poskytovaných službou Azure

Překlad názvů poskytovaných Azure poskytuje jenom základní autoritativní funkce DNS. Použijete-li tuto možnost, budou názvy a záznamy zóny DNS automaticky spravovány službou Azure a nebudete moci řídit názvy zón DNS ani životní cyklus záznamů DNS. Pokud pro vaše virtuální sítě potřebujete plně doporučené řešení DNS, musíte použít [Azure DNS privátní zóny](../dns/private-dns-overview.md) nebo [servery DNS spravované zákazníky](#name-resolution-that-uses-your-own-dns-server).

Spolu s překladem veřejných názvů DNS poskytuje Azure interní překlad adres IP pro virtuální počítače a instance rolí, které se nacházejí ve stejné virtuální síti nebo cloudové službě. Virtuální počítače a instance v cloudové službě sdílejí stejnou příponu DNS, takže název samotného hostitele je dostačující. Ale ve virtuálních sítích nasazených pomocí modelu nasazení Classic mají různé cloudové služby různé přípony DNS. V takové situaci potřebujete plně kvalifikovaný název domény pro překlad názvů mezi různými Cloud Services. Ve virtuálních sítích nasazených pomocí modelu nasazení Azure Resource Manager je přípona DNS konzistentní napříč všemi virtuálními počítači ve virtuální síti, takže plně kvalifikovaný název domény není potřeba. Názvy DNS je možné přiřadit k virtuálním počítačům i síťovým rozhraním. I když překlad názvů poskytovaný službou Azure nevyžaduje žádnou konfiguraci, není vhodná volba pro všechny scénáře nasazení, jak je popsáno v předchozí tabulce.

> [!NOTE]
> Při použití webové role a rolí pracovních procesů Cloud Services můžete k interním IP adresám instancí rolí přistupovat také pomocí REST API správy služeb Azure. Další informace najdete v referenčních informacích k [REST API správy služeb](/previous-versions/azure/ee460799(v=azure.100)). Adresa je založena na názvu role a čísle instance. 
>

### <a name="features"></a>Funkce

Překlad názvů poskytovaných Azure zahrnuje tyto funkce:
* Snadné použití. Není nutná žádná konfigurace.
* Vysoká dostupnost Nemusíte vytvářet a spravovat clustery pro vlastní servery DNS.
* Službu můžete používat ve spojení s vašimi vlastními servery DNS, abyste vyřešili názvy místních i hostitelských hostitelů Azure.
* Můžete použít překlad IP adres mezi virtuálními počítači a instancemi rolí v rámci stejné cloudové služby, a to bez plně kvalifikovaného názvu domény.
* Můžete použít překlad IP adres mezi virtuálními počítači ve virtuálních sítích, které používají model nasazení Azure Resource Manager, aniž byste museli plně kvalifikovaný název domény. Virtuální sítě v modelu nasazení Classic vyžadují při překladu názvů v různých cloudových službách plně kvalifikovaný název domény. 
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení, a ne pracovat s automaticky generovanými názvy.

### <a name="considerations"></a>Požadavky

Body, které je potřeba vzít v úvahu při použití překladu IP adres poskytované Azure:
* Příponu DNS vytvořenou v Azure nejde upravit.
* Vyhledávání DNS je vymezené na virtuální síť. Názvy DNS vytvořené pro jednu virtuální síť nejde přeložit z jiných virtuálních sítí.
* Nemůžete ručně registrovat vlastní záznamy.
* Služba WINS a rozhraní NetBIOS nejsou podporovány. Vaše virtuální počítače nemůžete zobrazit v Průzkumníkovi Windows.
* Názvy hostitelů musí být kompatibilní se službou DNS. Názvy musí používat jenom 0-9, a-z a-a nesmí začínat ani končit znakem-.
* Přenos dotazů DNS je pro každý virtuální počítač omezený. Omezení by nemělo mít vliv na většinu aplikací. Je-li zjištěno omezení požadavků, zajistěte, aby bylo povoleno ukládání do mezipaměti na straně klienta. Další informace najdete v tématu [Konfigurace klienta DNS](#dns-client-configuration).
* Pro každou virtuální síť v modelu nasazení Classic jsou registrovány pouze virtuální počítače v prvních 180 cloudových službách. Toto omezení se nevztahuje na virtuální sítě v Azure Resource Manager.
* Azure DNS IP adresa je 168.63.129.16. Jedná se o statickou IP adresu, která se nemění.

### <a name="reverse-dns-considerations"></a>Přesměrovat požadavky DNS
Reverzní DNS se podporuje ve všech virtuálních sítích založených na ARM. Můžete vystavit reverzní dotazy DNS (dotazy PTR) k mapování IP adres virtuálních počítačů na plně kvalifikované názvy domén virtuálních počítačů.
* Všechny dotazy PTR pro IP adresy virtuálních počítačů budou vracet plně kvalifikované názvy domén ve formátu \[ VMName \] . Internal.cloudapp.NET.
* Dopředné vyhledávání v plně kvalifikovaném názvu domény ve formátu \[ VMName \] . Internal.cloudapp.NET se přeloží na IP adresu přiřazenou k virtuálnímu počítači.
* Pokud je virtuální síť propojená s [Azure DNS privátní zóny](../dns/private-dns-overview.md) jako registrační virtuální síť, vrátí reverzní dotazy DNS dva záznamy. Jeden záznam bude mít formu \[ VMName \] . [ privatednszonename] a druhý bude mít formu \[ VMName \] . Internal.cloudapp.NET
* Zpětné vyhledávání DNS je vymezené na zadanou virtuální síť i v případě, že je partnerským vztahem k ostatním virtuálním sítím. Reverzní dotazy DNS (dotazy PTR) pro IP adresy virtuálních počítačů umístěných v partnerských virtuálních sítích budou vracet NXDOMAIN.
* Pokud chcete vypnout funkci reverzních DNS ve virtuální síti, můžete to udělat tak, že vytvoříte zónu zpětného vyhledávání pomocí [Azure DNS privátních zón](../dns/private-dns-overview.md) a propojíte ji s vaší virtuální sítí. Například pokud je adresní prostor IP adres vaší virtuální sítě 10.20.0.0/16, můžete vytvořit prázdnou privátní zónu DNS 20.10.in-addr. arpa a propojit ji s virtuální sítí. Při propojování zóny s vaší virtuální sítí byste měli zakázat automatickou registraci na tomto odkazu. Tato zóna přepíše výchozí zóny zpětného vyhledávání pro virtuální síť a protože tato zóna je prázdná, budete mít NXDOMAIN k reverzním dotazům DNS. Podrobnosti o tom, jak vytvořit privátní zónu DNS a propojit ji s virtuální sítí, najdete v našem [Průvodci rychlým startem](../dns/private-dns-getstarted-portal.md) .

> [!NOTE]
> Pokud chcete reverzní vyhledávání DNS provést v rámci virtuální sítě, můžete vytvořit zónu zpětného vyhledávání (in-addr. arpa) [Azure DNS privátní zóny](../dns/private-dns-overview.md) a propojíte ji s několika virtuálními sítěmi. Budete ale muset ručně spravovat reverzní záznamy DNS pro virtuální počítače.
>


## <a name="dns-client-configuration"></a>Konfigurace klienta DNS

Tato část pokrývá ukládání do mezipaměti na straně klienta a opakované pokusy na straně klienta.

### <a name="client-side-caching"></a>Ukládání do mezipaměti na straně klienta

Ne každý dotaz DNS musí být odeslán přes síť. Ukládání do mezipaměti na straně klienta pomáhá snižovat latenci a zlepšit odolnost proti síťovému výkyvůu tím, že překládá opakované dotazy DNS z místní mezipaměti. Záznamy DNS obsahují mechanismus TTL (Time-to-Live), který umožňuje ukládání záznamů co nejkratší, pokud by to ovlivnilo aktuálnost záznamu. To znamená, že ukládání do mezipaměti na straně klienta je vhodné ve většině situací.

Výchozí klient DNS systému Windows má vestavěnou mezipaměť DNS. Některé distribuce systému Linux nezahrnují ve výchozím nastavení ukládání do mezipaměti. Pokud zjistíte, že ještě není místní mezipaměť, přidejte do každého virtuálního počítače se systémem Linux mezipaměť DNS.

K dispozici je řada různých balíčků pro ukládání do mezipaměti DNS (například Dnsmasq). Zde je postup, jak nainstalovat Dnsmasq v nejběžnějších distribucích:

* **Ubuntu (používá Resolvconf)**:
  * Nainstalujte balíček Dnsmasq pomocí nástroje `sudo apt-get install dnsmasq` .
* **SUSE (používá NETCONF)**:
  * Nainstalujte balíček Dnsmasq pomocí nástroje `sudo zypper install dnsmasq` .
  * Povolte službu Dnsmasq s nástrojem `systemctl enable dnsmasq.service` . 
  * Spusťte službu Dnsmasq s nástrojem `systemctl start dnsmasq.service` . 
  * Upravte **/etc/sysconfig/Network/config** a změňte *NETCONFIG_DNS_FORWARDER = ""* na *Dnsmasq*.
  * Pokud `netconfig update` chcete nastavit mezipaměť jako místní PŘEKLADAČ DNS, aktualizujte soubor resolv. conf.
* **CentOS (používá NetworkManager)**:
  * Nainstalujte balíček Dnsmasq pomocí nástroje `sudo yum install dnsmasq` .
  * Povolte službu Dnsmasq s nástrojem `systemctl enable dnsmasq.service` .
  * Spusťte službu Dnsmasq s nástrojem `systemctl start dnsmasq.service` .
  * Přidejte předřadící *název domény-servery 127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Restartujte síťovou službu pomocí nástroje `service network restart` a nastavte mezipaměť jako místní PŘEKLADAČ DNS.

> [!NOTE]
> Balíček Dnsmasq je jenom jedna z mnoha mezipamětí DNS dostupných pro Linux. Než ho použijete, ověřte jeho vhodnost pro vaše konkrétní potřeby a ověřte, že není nainstalovaná žádná jiná mezipaměť.

    
### <a name="client-side-retries"></a>Opakované pokusy na straně klienta

DNS je primárně protokol UDP. Vzhledem k tomu, že protokol UDP nezaručí doručování zpráv, je logika opakování zpracována v samotném protokolu DNS. Každý klient DNS (operační systém) může v závislosti na předvolbách autora vykazovat různou logiku opakování:

* Operační systémy Windows se zopakují po jedné sekundě a potom znovu po dalších dvou sekundách, čtyři sekundy a další čtyři sekundy. 
* Výchozí nastavení pro Linux se zopakuje po pěti sekundách. Doporučujeme změnit specifikace opakování na pětkrát v intervalu s jednou sekundou.

Ověřte aktuální nastavení virtuálního počítače se systémem Linux pomocí nástroje `cat /etc/resolv.conf` . Podívejte se na řádek *Možnosti* , například:

```bash
options timeout:1 attempts:5
```

Soubor soubor resolv. conf se obvykle generuje automaticky a neměl by být upravován. Konkrétní kroky pro přidání řádku *Možnosti* se liší podle distribuce:

* **Ubuntu** (používá Resolvconf):
  1. Přidejte řádek *Options (možnosti* ) do **/etc/Resolvconf/resolv.conf.d/Tail**.
  2. Spusťte `resolvconf -u` aktualizaci.
* **SUSE** (používá NETCONF):
  1. Přidat *časový limit: 1 počet pokusů: 5* k parametru **NETCONFIG_DNS_RESOLVER_OPTIONS = "** v **/etc/sysconfig/Network/config**.
  2. Spusťte `netconfig update` aktualizaci.
* **CentOS** (používá NetworkManager):
  1. Přidání *ozvěny – časový limit možností: 1 pokusů: 5 "* do **/etc/NetworkManager/Dispatcher.d/11-dhclient**.
  2. Aktualizujte pomocí `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Překlad názvů, který používá vlastní server DNS

Tato část se zabývá virtuálními počítači, instancemi rolí a webovými aplikacemi.

### <a name="vms-and-role-instances"></a>Virtuální počítače a instance rolí

Vaše potřeby překladu názvů můžou přesáhnout funkce poskytované Azure. Například může být nutné použít domény služby Active Directory systému Microsoft Windows Server, přeložit názvy DNS mezi virtuálními sítěmi. Za účelem pokrytí těchto scénářů vám Azure umožní používat vlastní servery DNS.

Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS na rekurzivní překladače v Azure. To umožňuje překládat názvy hostitelů v rámci této virtuální sítě. Řadič domény, který běží v Azure, může například reagovat na dotazy DNS pro příslušné domény a předávat všechny ostatní dotazy do Azure. Předávání dotazů umožňuje virtuálním počítačům zobrazit vaše místní prostředky (přes řadič domény) a názvy hostitelů poskytované Azure (prostřednictvím služby předávání). Přístup k rekurzivním překladačům v Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

Předávání DNS taky umožňuje překlad DNS mezi virtuálními sítěmi a umožňuje vašim místním počítačům překládat názvy hostitelů poskytované Azure. Aby bylo možné přeložit název hostitele virtuálního počítače, musí se virtuální počítač serveru DNS nacházet ve stejné virtuální síti a musí být nakonfigurovaný tak, aby předávat dotazy na název hostitele do Azure. Vzhledem k tomu, že se přípona DNS v každé virtuální síti liší, můžete použít pravidla podmíněného předávání k odeslání dotazů DNS do správné virtuální sítě pro rozlišení. Následující obrázek ukazuje dvě virtuální sítě a místní síť, které provádí překlad DNS mezi virtuálními sítěmi pomocí této metody. Příklad služby DNS pro přeposílání je k dispozici v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) a na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Instance role může provádět překlad názvů virtuálních počítačů v rámci stejné virtuální sítě. K tomu připadá pomocí plně kvalifikovaného názvu domény, který se skládá z názvu hostitele virtuálního počítače a přípony DNS **internal.cloudapp.NET** . V tomto případě je překlad názvů úspěšný jenom v případě, že instance role má název virtuálního počítače definovaný ve [schématu role (soubor. cscfg)](/previous-versions/azure/reference/jj156212(v=azure.100)).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Instance rolí, které potřebují překládat IP adresy virtuálních počítačů v jiné virtuální síti (FQDN pomocí přípony **internal.cloudapp.NET** ), musí probíhat pomocí metody popsané v této části (vlastní servery DNS, které předávají mezi oběma virtuálními sítěmi).
>

![Diagram DNS mezi virtuálními sítěmi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Pokud používáte překlad adres poskytovaný službou Azure, poskytuje Azure Dynamic Host Configuration Protocol (DHCP) interní příponu DNS (**. Internal.cloudapp.NET**) pro každý virtuální počítač. Tato přípona umožňuje rozlišení názvu hostitele, protože záznamy názvů hostitelů jsou v zóně **internal.cloudapp.NET** . Pokud používáte vlastní řešení překladu IP adres, tato přípona se nedodává virtuálním počítačům, protože je v konfliktu s jinými architekturami DNS (například scénáři připojenými k doméně). Místo toho poskytuje Azure nefunkční zástupný symbol (*reddog.Microsoft.com*).

V případě potřeby můžete určit interní příponu DNS pomocí PowerShellu nebo rozhraní API:

* Pro virtuální sítě v Azure Resource Manager modely nasazení je přípona k dispozici prostřednictvím [síťového rozhraní REST API](/rest/api/virtualnetwork/networkinterfaces), rutiny [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) prostředí PowerShell a příkazu [AZ Network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI.
* V klasických modelech nasazení je přípona k dispozici prostřednictvím volání metody [Get rozhraní API](/previous-versions/azure/reference/ee460804(v=azure.100)) nebo rutiny [Get-AzureVM-Debug](/powershell/module/servicemanagement/azure.service/get-azurevm) .

Pokud předávání dotazů do Azure nevyhovuje vašim potřebám, měli byste poskytnout vlastní řešení DNS. Vaše řešení DNS potřebuje:

* Poskytněte vhodné rozlišení názvu hostitele prostřednictvím [DDNS](virtual-networks-name-resolution-ddns.md), například. Pokud používáte DDNS, možná budete muset zakázat úklid záznamů DNS. Zapůjčení Azure DHCP je dlouhé a úklid může předčasně odebrat záznamy DNS. 
* Poskytněte vhodné rekurzivní řešení, aby bylo možné řešit názvy externích domén.
* Musí být přístupná (TCP a UDP na portu 53) od klientů, které obsluhuje, a mít přístup k Internetu.
* Zabezpečte přístup z Internetu a zmírnit tak hrozby, které představují externí agenti.

> [!NOTE]
> Pro nejlepší výkon při použití virtuálních počítačů Azure jako serverů DNS by měl být protokol IPv6 zakázaný.

### <a name="web-apps"></a>Webové aplikace
Předpokládejme, že je třeba provést překlad IP adres z vaší webové aplikace vytvořené pomocí App Service propojených s virtuální sítí na virtuální počítače ve stejné virtuální síti. Kromě nastavení vlastního serveru DNS s předaným serverem DNS, který předává dotazy do Azure (virtuální IP 168.63.129.16), proveďte následující kroky:
1. Povolte integraci virtuální sítě pro vaši webovou aplikaci, pokud ještě není hotová, jak je popsáno v tématu [integrace aplikace s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. V Azure Portal pro App Service plán hostování webové aplikace vyberte možnost **synchronizovat síť** v části **síť**, **Virtual Network integrace**.

    ![Snímek obrazovky s rozlišením názvu virtuální sítě](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Pokud potřebujete provést překlad IP adres z vaší webové aplikace vytvořené pomocí App Service, která je propojená s virtuální sítí, na virtuální počítače v jiné virtuální síti, musíte použít vlastní servery DNS v obou virtuálních sítích, a to následujícím způsobem:

* Nastavte server DNS v cílové virtuální síti na virtuálním počítači, který může také předávat dotazy do rekurzivního překladače v Azure (Virtual IP 168.63.129.16). Příklad služby DNS pro přeposílání je k dispozici v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) a na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Nastavte službu DNS pro přeposílání ve zdrojové virtuální síti na virtuálním počítači. Nakonfigurujte tuto službu DNS pro přeposílání na dotazy na server DNS v cílové virtuální síti.
* Nakonfigurujte zdrojový server DNS v nastavení zdrojové virtuální sítě.
* Povolte integraci virtuální sítě pro vaši webovou aplikaci, aby se mohla propojit se zdrojovou virtuální sítí, podle pokynů v tématu [integrace aplikace s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* V Azure Portal pro App Service plán hostování webové aplikace vyberte možnost **synchronizovat síť** v části **síť**, **Virtual Network integrace**.

## <a name="specify-dns-servers"></a>Zadat servery DNS
Pokud používáte vlastní servery DNS, poskytuje Azure možnost zadat několik serverů DNS na jednu virtuální síť. Můžete také zadat více serverů DNS pro každé síťové rozhraní (pro Azure Resource Manager) nebo pro každou cloudovou službu (pro klasický model nasazení). Servery DNS zadané pro síťové rozhraní nebo cloudovou službu mají přednost před servery DNS zadanými pro virtuální síť.

> [!NOTE]
> Vlastnosti síťového připojení, například IP adresy serveru DNS, by se neměly upravovat přímo v rámci virtuálních počítačů. Důvodem je to, že při zaretušování služby se můžou vymazat, když se virtuální síťový adaptér nahradí. To platí pro virtuální počítače se systémem Windows i Linux.

Pokud používáte model nasazení Azure Resource Manager, můžete zadat servery DNS pro virtuální síť a síťové rozhraní. Podrobnosti najdete v tématu [Správa virtuální sítě](manage-virtual-network.md) a [Správa síťového rozhraní](virtual-network-network-interface.md).

> [!NOTE]
> Pokud se rozhodnete pro vlastní server DNS pro virtuální síť, musíte zadat aspoň jednu IP adresu serveru DNS. v opačném případě bude virtuální síť ignorovat konfiguraci a místo toho použít službu DNS poskytovanou službou Azure.

Pokud používáte model nasazení Classic, můžete zadat servery DNS pro virtuální síť v Azure Portal nebo v [souboru konfigurace sítě](/previous-versions/azure/reference/jj157100(v=azure.100)). Pro Cloud Services můžete zadat servery DNS prostřednictvím [konfiguračního souboru služby](/previous-versions/azure/reference/ee758710(v=azure.100)) nebo pomocí prostředí PowerShell s rutinou [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Pokud změníte nastavení DNS pro virtuální síť nebo virtuální počítač, který je již nasazený, bude nutné provést obnovení zapůjčení služby DHCP u všech ovlivněných virtuálních počítačů ve virtuální síti. Pro virtuální počítače s operačním systémem Windows to můžete provést tak, že na `ipconfig /renew` virtuálním počítači zadáte přímo. Postup se liší v závislosti na operačním systému. Podívejte se na příslušnou dokumentaci pro typ operačního systému.

## <a name="next-steps"></a>Další kroky

Model nasazení Azure Resource Manager:

* [Správa virtuální sítě](manage-virtual-network.md)
* [Správa síťového rozhraní](virtual-network-network-interface.md)

Model nasazení Classic:

* [Schéma konfigurace služby Azure](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Schéma konfigurace Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Konfigurace Virtual Network pomocí konfiguračního souboru sítě](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)