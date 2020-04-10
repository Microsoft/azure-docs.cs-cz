---
title: Překlad názvů pro prostředky ve virtuálních sítích Azure
titlesuffix: Azure Virtual Network
description: Scénáře překladu názvů pro Azure IaaS, hybridní řešení, mezi různými cloudovými službami, službou Active Directory a používáním vlastního serveru DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: d7d0699718642a7eb9f85b2e8a86623092c34365
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010558"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Překlad názvů pro prostředky ve virtuálních sítích Azure

V závislosti na tom, jak používáte Azure k hostování iaas, PaaS a hybridních řešení, možná budete muset povolit virtuální počítače (VM) a další prostředky nasazené ve virtuální síti ke vzájemné komunikaci. I když můžete povolit komunikaci pomocí IP adres, je mnohem jednodušší používat názvy, které lze snadno zapamatovat, a nemění. 

Když prostředky nasazené ve virtuálních sítích potřebují přeložit názvy domén na interní IP adresy, můžou použít jednu ze tří metod:

* [Privátní zóny Azure DNS](../dns/private-dns-overview.md)
* [Překlad názvů zadaný Azure](#azure-provided-name-resolution)
* [Překlad názvů, který používá vlastní server DNS](#name-resolution-that-uses-your-own-dns-server) (který může předávat dotazy na servery DNS poskytované službou Azure)

To, který typ překladu názvů použijete, závisí na tom, jak spolu vaše prostředky potřebují vzájemně komunikovat. Následující tabulka ilustruje scénáře a odpovídající řešení překladu názvů:

> [!NOTE]
> Privátní zóny Azure DNS jsou upřednostňovaným řešením a poskytují flexibilitu při správě zón a záznamů DNS. Další informace najdete v tématu o [použití Azure DNS pro privátní domény](../dns/private-dns-overview.md).

> [!NOTE]
> Pokud používáte Azure Provided DNS, bude na vaše virtuální počítače automaticky použita příslušná přípona DNS. Pro všechny ostatní možnosti je nutné použít plně kvalifikované názvy domén (FQDN) nebo ručně použít příslušnou příponu DNS pro vaše virtuální počítače.

| **Scénář** | **Řešení** | **Přípona DNS** |
| --- | --- | --- |
| Překlad názvů mezi virtuálními počítači umístěnými ve stejné virtuální síti nebo instancemi rolí Cloudových služeb Azure ve stejné cloudové službě. | [Privátní zóny Azure DNS](../dns/private-dns-overview.md) nebo [překlad názvů zadaný Azure](#azure-provided-name-resolution) |Název hostitele nebo vícejmenný název |
| Překlad názvů mezi virtuálními počítači v různých virtuálních sítích nebo instance role v různých cloudových službách. |[Privátní zóny Azure DNS](../dns/private-dns-overview.md) nebo servery DNS spravované zákazníkem, které předávají dotazy mezi virtuálními sítěmi pro rozlišení pomocí Azure (proxy DNS). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Překlad názvů ze služby Azure App Service (Web App, Function, nebo Bot) pomocí integrace virtuální sítě k rolím instance nebo virtuální počítače ve stejné virtuální síti. |Servery DNS spravované zákazníkem předávají dotazy mezi virtuálními sítěmi pro rozlišení pomocí Azure (proxy DNS). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Překlad názvů z webových aplikací App Service na virtuální počítače ve stejné virtuální síti. |Servery DNS spravované zákazníkem předávají dotazy mezi virtuálními sítěmi pro rozlišení pomocí Azure (proxy DNS). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Překlad názvů z webových aplikací app service v jedné virtuální síti na virtuální počítače v jiné virtuální síti. |Servery DNS spravované zákazníkem předávají dotazy mezi virtuálními sítěmi pro rozlišení pomocí Azure (proxy DNS). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Řešení místních názvů počítačů a služeb z virtuálních počítačů nebo instancí rolí v Azure. |Servery DNS spravované zákazníkem (například místní řadič domény, místní řadič domény jen pro čtení nebo sekundární synchronizovaný pomocí zónových přenosů). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Řešení názvů hostitelů Azure z místních počítačů. |Předávat dotazy na zákazníkem spravovaný server DNS proxy v odpovídající virtuální síti, proxy server předá dotazy do Azure pro řešení. Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-that-uses-your-own-dns-server). |Pouze fQDN |
| Reverzní DNS pro interní IP adresy. |[Privátní zóny Azure DNS](../dns/private-dns-overview.md) nebo [překlad názvů zadaný Azure](#azure-provided-name-resolution) nebo Překlad názvů pomocí vlastního serveru [DNS](#name-resolution-that-uses-your-own-dns-server). |Neuvedeno |
| Překlad názvů mezi virtuálními počítači nebo instancemi rolí umístěnými v různých cloudových službách, nikoli ve virtuální síti. |Neužívá se. Připojení mezi virtuálními počítači a instancemi rolí v různých cloudových službách není podporováno mimo virtuální síť. |Neuvedeno|

## <a name="azure-provided-name-resolution"></a>Překlad názvů zadaný Azure

Azure za předpokladu, překlad názvů poskytuje pouze základní autoritativní funkce DNS. Pokud použijete tuto možnost, názvy a záznamy zón DNS budou automaticky spravovány službou Azure a nebudete moci řídit názvy zón DNS ani životní cyklus záznamů DNS. Pokud potřebujete plně vybavené řešení DNS pro vaše virtuální sítě, musíte použít [privátní zóny Azure DNS](../dns/private-dns-overview.md) nebo [servery DNS spravované zákazníkem](#name-resolution-that-uses-your-own-dns-server).

Spolu s překladem veřejných názvů DNS poskytuje Azure interní překlad názvů pro virtuální počítače a instance rolí, které se nacházejí v rámci stejné virtuální sítě nebo cloudové služby. Virtuální servery a instance v cloudové službě sdílejí stejnou příponu DNS, takže samotný název hostitele je dostačující. Ale ve virtuálních sítích nasazených pomocí klasického modelu nasazení mají různé cloudové služby různé přípony DNS. V takovém případě budete potřebovat celý název doménové adresy k překladu názvů mezi různými cloudovými službami. Ve virtuálních sítích nasazených pomocí modelu nasazení Azure Resource Manageru je přípona DNS konzistentní ve všech virtuálních počítačích v rámci virtuální sítě, takže není potřeba celý název domény. Názvy DNS lze přiřadit k virtuálním počítačům i síťovým rozhraním. Přestože překlad názvů zadaný Azure nevyžaduje žádnou konfiguraci, není vhodnou volbou pro všechny scénáře nasazení, jak je podrobně popsáno v předchozí tabulce.

> [!NOTE]
> Při použití webových a pracovních rolí cloudových služeb můžete také přistupovat k interním IP adresám instancí rolí pomocí rozhraní REST API pro správu služeb Azure. Další informace naleznete v [odkazu rest api služby pro správu služeb](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adresa je založena na názvu role a čísle instance. 
>

### <a name="features"></a>Funkce

Překlad názvů poskytovaný azure zahrnuje následující funkce:
* Snadné použití. Není nutná žádná konfigurace.
* Vysoká dostupnost Není nutné vytvářet a spravovat clustery vlastních serverů DNS.
* Službu můžete použít ve spojení s vlastními servery DNS k vyřešení názvů místních hostitelů i hostitelů Azure.
* Můžete použít překlad názvů mezi virtuálními aplikacemi a instancemi rolí v rámci stejné cloudové služby, aniž byste potřebovali hlavní název souboru.
* Překlad názvů mezi virtuálními počítači můžete použít ve virtuálních sítích, které používají model nasazení Azure Resource Manageru, bez nutnosti velkého názvu na webu. Virtuální sítě v modelu klasického nasazení vyžadují hlavní název doménového jména při překladu názvů v různých cloudových službách. 
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení, spíše než pracovat s automaticky generovanými názvy.

### <a name="considerations"></a>Požadavky

Body, které je třeba vzít v úvahu při použití překladu názvů zapředpokladu Azure:
* Příponu DNS vytvořenou Azure nelze změnit.
* Vyhledávání DNS je vymezeno na virtuální síť. Názvy DNS vytvořené pro jednu virtuální sítě nelze přeložit z jiných virtuálních sítí.
* Vlastní záznamy nelze zaregistrovat ručně.
* Služby WINS a NetBIOS nejsou podporovány. Virtuální počítače se v Průzkumníkovi Windows nezobrazují.
* Názvy hostitelů musí být kompatibilní se službou DNS. Názvy musí používat pouze 0-9, a-z a '-', a nesmí začínat ani končit '-'.
* Provoz dotazů DNS je omezen pro každý virtuální server. Omezení by nemělo mít vliv na většinu aplikací. Pokud je pozorováno omezení požadavků, ujistěte se, že je povoleno ukládání do mezipaměti na straně klienta. Další informace naleznete v [tématu konfigurace klienta DNS](#dns-client-configuration).
* Pouze virtuální počítače v prvních 180 cloudových služeb jsou registrované pro každou virtuální síť v modelu klasické nasazení. Toto omezení se nevztahuje na virtuální sítě ve Správci prostředků Azure.
* Ip adresa Azure DNS je 168.63.129.16. Jedná se o statickou adresu IP, která se nezmění.

### <a name="reverse-dns-considerations"></a>Obrácené aspekty DNS
Reverzní DNS je podporován ve všech virtuálních sítích založených na ARM. Můžete vydávat reverzní dotazy DNS (ptr dotazy) mapovat IP adresy virtuálních počítačů na fqdn virtuálních počítačů.
* Všechny dotazy PTR pro IP adresy virtuálních počítačů vrátí fQDN formuláře \[vmname\].internal.cloudapp.net
* Dopředné vyhledávání na názvových \[názevch\]ve formuláři vmname .internal.cloudapp.net se přeloží na ADRESU IP přiřazenou virtuálnímu počítači.
* Pokud je virtuální síť propojená s [privátními zónami Azure DNS](../dns/private-dns-overview.md) jako registrační virtuální síť, vrátí se zpětné dotazy DNS dva záznamy. Jeden záznam bude formuláře \[vmname\]. [priatednszonename] a další by \[měly\]být ve tvaru vmname .internal.cloudapp.net
* Zpětné vyhledávání DNS je vymezeno na danou virtuální síť, i když je partnerský vztah k jiným virtuálním sítím. Reverzní dotazy DNS (dotazy PTR) pro IP adresy virtuálních počítačů umístěných v partnerských virtuálních sítích vrátí NXDOMAIN.

> [!NOTE]
> Pokud chcete, aby se zpětné vyhledávání DNS rozšířilo napříč virtuální sítí, můžete vytvořit zónu zpětného vyhledávání (in-addr.arpa) [privátní zóny Azure DNS](../dns/private-dns-overview.md) a propojit ji s více virtuálními sítěmi. Budete však muset ručně spravovat reverzní záznamy DNS pro virtuální počítače.
>


## <a name="dns-client-configuration"></a>Konfigurace klienta DNS

Tato část se zabývá ukládáním do mezipaměti na straně klienta a opakováním na straně klienta.

### <a name="client-side-caching"></a>Ukládání do mezipaměti na straně klienta

Ne každý dotaz DNS musí být odeslán v síti. Ukládání do mezipaměti na straně klienta pomáhá snížit latenci a zlepšit odolnost vůči síťovým výkyvům tím, že řeší opakované dotazy DNS z místní mezipaměti. Záznamy DNS obsahují mechanismus time-to-live (TTL), který umožňuje mezipaměti ukládat záznam tak dlouho, jak je to možné bez dopadu na čerstvost záznamu. Ukládání do mezipaměti na straně klienta je tedy vhodné pro většinu situací.

Výchozí klient SLUŽBY DNS systému Windows má vestavěnou mezipaměť DNS. Některé distribuce Linuxu ve výchozím nastavení neobsahují ukládání do mezipaměti. Pokud zjistíte, že místní mezipaměti ještě neexistuje, přidejte do každého virtuálního počítače SIP mezipaměť DNS.

K dispozici je řada různých balíčků ukládání do mezipaměti DNS (například dnsmasq). Zde je návod, jak nainstalovat dnsmasq na nejběžnější distribuce:

* **Ubuntu (používá resolvconf)**:
  * Nainstalujte balíček dnsmasq s programem `sudo apt-get install dnsmasq`.
* **SUSE (používá netconf)**:
  * Nainstalujte balíček dnsmasq s programem `sudo zypper install dnsmasq`.
  * Povolte službu dnsmasq pomocí služby `systemctl enable dnsmasq.service`. 
  * Spusťte službu dnsmasq pomocí služby `systemctl start dnsmasq.service`. 
  * Upravit **/etc/sysconfig/network/config**, a změnit *NETCONFIG_DNS_FORWARDER=""* na *dnsmasq*.
  * Aktualizujte soubor resolv.conf s `netconfig update`, chcete-li nastavit mezipaměť jako místní překladač DNS.
* **CentOS (používá NetworkManager)**:
  * Nainstalujte balíček dnsmasq s programem `sudo yum install dnsmasq`.
  * Povolte službu dnsmasq pomocí služby `systemctl enable dnsmasq.service`.
  * Spusťte službu dnsmasq pomocí služby `systemctl start dnsmasq.service`.
  * Přidat *předřadní servery název domény 127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Restartujte síťovou `service network restart`službu pomocí aplikace , chcete-li nastavit mezipaměť jako místní překladač DNS.

> [!NOTE]
> Balíček dnsmasq je pouze jedním z mnoha DNS cache k dispozici pro Linux. Před použitím zkontrolujte jeho vhodnost pro vaše konkrétní potřeby a zkontrolujte, zda není nainstalována žádná jiná mezipaměť.

    
### <a name="client-side-retries"></a>Opakování na straně klienta

DNS je především protokol UDP. Vzhledem k tomu, že protokol UDP nezaručuje doručení zprávy, logika opakování je zpracována v samotném protokolu DNS. Každý klient DNS (operační systém) může vykazovat jinou logiku opakování v závislosti na předvolbách autora:

* Operační systémy Windows opakujte po jedné sekundě a pak znovu po dalších dvou sekundách, čtyřech sekundách a dalších čtyřech sekundách. 
* Výchozí nastavení Linuxu se opakuje po pěti sekundách. Doporučujeme změnit specifikace opakování na pětkrát, v intervalech jedné sekundy.

Zkontrolujte aktuální nastavení na virtuálním počítači s Linuxem s `cat /etc/resolv.conf`. Podívejte se na řádek *možností,* například:

```bash
options timeout:1 attempts:5
```

Soubor resolv.conf je obvykle automaticky generován a neměl by být upravován. Konkrétní kroky pro přidání řádku *možností* se liší podle rozdělení:

* **Ubuntu** (používá resolvconf):
  1. Přidejte řádek *možností* do **/etc/resolvconf/resolv.conf.d/tail**.
  2. Spusťte `resolvconf -u` aktualizaci.
* **SUSE** (používá netconf):
  1. Přidejte *timeout:1 pokusy:5* do parametru **NETCONFIG_DNS_RESOLVER_OPTIONS=""** v **parametru /etc/sysconfig/network/config**.
  2. Spusťte `netconfig update` aktualizaci.
* **CentOS** (používá NetworkManager):
  1. Přidejte *echo "options timeout:1 attempts:5"* to **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Aktualizovat `service network restart`pomocí .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Překlad názvů, který používá vlastní server DNS

Tato část popisuje virtuální chody, instance rolí a webové aplikace.

### <a name="vms-and-role-instances"></a>Virtuální virtuální uživatele a instance rolí

Vaše potřeby překladu názvů mohou jít nad rámec funkcí poskytovaných Azure. Může být například nutné použít domény služby Microsoft Windows Server Active Directory, překládání názvů DNS mezi virtuálními sítěmi. Azure vám k pokrytí těchto scénářů umožňuje používat vlastní servery DNS.

Servery DNS ve virtuální síti můžou předávat dotazy DNS rekurzivním překladačům v Azure. To umožňuje přeložit názvy hostitelů v rámci této virtuální sítě. Například řadič domény (DC) spuštěný v Azure může reagovat na dotazy DNS pro své domény a předat všechny ostatní dotazy do Azure. Předávání dotazů umožňuje virtuálním počítačům zobrazit vaše místní prostředky (prostřednictvím řadiče domény) i názvy hostitelů poskytovaných Azure (prostřednictvím serveru pro předávání). Přístup k rekurzivním překladačům v Azure je k dispozici prostřednictvím virtuální IP 168.63.129.16.

Předávání DNS také umožňuje rozlišení DNS mezi virtuálními sítěmi a umožňuje místním počítačům vyřešit názvy hostitelů s azure. Aby bylo možné přeložit název hostitele virtuálního počítače, musí být virtuální počítač DNS umístěn ve stejné virtuální síti a musí být nakonfigurován tak, aby přesměroval dotazy na názvy hostitelů do Azure. Vzhledem k tomu, že se přípona DNS v jednotlivých virtuálních sítích liší, můžete použít pravidla podmíněného předávání k odeslání dotazů DNS do správné virtuální sítě k vyřešení. Následující obrázek ukazuje dvě virtuální sítě a místní sítě, které provádí překlad DNS mezi virtuálními sítěmi pomocí této metody. Příklad serveru pro předávání DNS je k dispozici v [galerii Šablon rychlého startu Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) a [githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Instance role může provádět překlad názvů virtuálních počítačů v rámci stejné virtuální sítě. Činí tak pomocí hlavního názvu domény, který se skládá z názvu hostitele virtuálního uživatele a **internal.cloudapp.net** přípony DNS. V tomto případě je však překlad názvů úspěšný pouze v případě, že instance role má název virtuálního počítače definovaný ve [schématu rolí (soubor.cscfg).](https://msdn.microsoft.com/library/azure/jj156212.aspx)
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Instance rolí, které je potřeba provést překlad názvů virtuálních počítačů v jiné virtuální síti (FQDN pomocí **internal.cloudapp.net** přípony) musí tak učinit pomocí metody popsané v této části (vlastní dns servery předávání mezi dvěma virtuálními sítěmi).
>

![Diagram DNS mezi virtuálními sítěmi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Pokud používáte překlad názvů poskytnutý azure, Azure Dynamic Host Configuration Protocol (DHCP) poskytuje interní příponu DNS (**.internal.cloudapp.net**) pro každý virtuální počítač. Tato přípona umožňuje překlad názvů hostitele, protože záznamy názvů hostitelů jsou v **zóně internal.cloudapp.net.** Pokud používáte vlastní řešení překladu názvů, tato přípona se nedodává virtuálním počítačům, protože narušuje jiné architektury DNS (jako scénáře spojené s doménou). Místo toho Azure poskytuje nefunkční zástupný symbol (*reddog.microsoft.com*).

V případě potřeby můžete určit interní příponu DNS pomocí prostředí PowerShell nebo rozhraní API:

* Pro virtuální sítě v modelech nasazení Azure Resource Manager je přípona dostupná prostřednictvím [síťového rozhraní REST API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), rutiny Prostředí PowerShell [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) a příkaza Azure CLI [sítě AZ nic.](/cli/azure/network/nic#az-network-nic-show)
* V klasických modelech nasazení je přípona k dispozici prostřednictvím volání [rozhraní API pro získání nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) nebo rutiny [Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure/get-azurevm)

Pokud předávání dotazů do Azure nevyhovuje vašim potřebám, měli byste poskytnout vlastní řešení DNS. Vaše řešení DNS musí:

* Zadejte odpovídající překlad názvu hostitele, například prostřednictvím [DDNS](virtual-networks-name-resolution-ddns.md). Pokud používáte DDNS, možná budete muset zakázat úklid záznamů DNS. Zapůjčení azure DHCP jsou dlouhé a úklid může předčasně odebrat záznamy DNS. 
* Poskytněte vhodné rekurzivní rozlišení umožňující rozlišení externích názvů domén.
* Být přístupné (TCP a UDP na portu 53) od klientů, které slouží, a mít přístup k internetu.
* Být zabezpečeny proti přístupu z internetu, aby se zmírnily hrozby, které představují externí agenti.

> [!NOTE]
> Pro dosažení nejlepšího výkonu, pokud používáte virtuální počítače Azure jako servery DNS, iPv6 by měl být zakázán.

### <a name="web-apps"></a>Webové aplikace
Předpokládejme, že potřebujete provést překlad názvů z webové aplikace vytvořené pomocí služby App Service, propojené s virtuální sítí, na virtuální počítače ve stejné virtuální síti. Kromě nastavení vlastního serveru DNS, který má server pro předávání DNS, který předává dotazy do Azure (virtuální IP 168.63.129.16), proveďte následující kroky:
1. Povolte integraci virtuální sítě pro vaši webovou aplikaci, pokud to ještě není hotové, jak je popsáno v [integrujte aplikaci s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Na webu Azure Portal vyberte pro plán služby App Service, který webovou aplikaci hostuje, **možnost Synchronizovat síť** v části **Síť**, Integrace **virtuální sítě**.

    ![Snímek obrazovky s překladem názvů virtuální sítě](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Pokud potřebujete provést překlad názvů z webové aplikace vytvořené pomocí služby App Service, propojené s virtuálními počítači, na virtuální počítače v jiné virtuální síti, musíte použít vlastní servery DNS v obou virtuálních sítích, a to následovně:

* Nastavte DNS server v cílové virtuální síti na virtuálním počítači, který může také předávat dotazy rekurzivnímu překládání v Azure (virtuální IP 168.63.129.16). Příklad serveru pro předávání DNS je k dispozici v [galerii Šablon rychlého startu Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) a [githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Nastavte server pro předávání DNS ve zdrojové virtuální síti na virtuálním počítači. Nakonfigurujte tuto službu předávání DNS tak, aby přesměrovávala dotazy na server DNS v cílové virtuální síti.
* Nakonfigurujte zdrojový server DNS v nastavení zdrojové virtuální sítě.
* Povolte integraci virtuální sítě pro webovou aplikaci pro propojení se zdrojovou virtuální sítí podle pokynů v [části Integrace aplikace s virtuální sítí](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Na webu Azure Portal vyberte pro plán služby App Service, který webovou aplikaci hostuje, **možnost Synchronizovat síť** v části **Síť**, Integrace **virtuální sítě**.

## <a name="specify-dns-servers"></a>Určení serverů DNS
Pokud používáte vlastní servery DNS, Azure poskytuje možnost zadat více serverů DNS na virtuální síť. Můžete také zadat více serverů DNS pro každé síťové rozhraní (pro Azure Resource Manager) nebo pro každou cloudovou službu (pro klasický model nasazení). Servery DNS určené pro síťové rozhraní nebo cloudovou službu mají přednost před servery DNS určenými pro virtuální síť.

> [!NOTE]
> Vlastnosti síťového připojení, jako jsou IP adresy serveru DNS, by neměly být upravovány přímo v rámci virtuálních počítače. Důvodem je, že mohou získat vymazány během služby léčit při nahrazení adaptéru virtuální sítě. To platí pro virtuální počítače s Windows i Linux.

Pokud používáte model nasazení Azure Resource Manager, můžete zadat servery DNS pro virtuální síť a síťové rozhraní. Podrobnosti naleznete [v tématech Správa virtuální sítě](manage-virtual-network.md) a Správa [síťového rozhraní](virtual-network-network-interface.md).

> [!NOTE]
> Pokud se rozhodnete pro vlastní server DNS pro vaši virtuální síť, musíte zadat alespoň jednu adresu IP serveru DNS. v opačném případě virtuální síť bude ignorovat konfiguraci a místo toho použije DNS poskytovované Azure.

Pokud používáte klasický model nasazení, můžete zadat servery DNS pro virtuální síť na portálu Azure nebo v [souboru Konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100). U cloudových služeb můžete určit servery DNS prostřednictvím [souboru Konfigurace služby](https://msdn.microsoft.com/library/azure/ee758710) nebo pomocí prostředí PowerShell pomocí [nového AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Pokud změníte nastavení DNS pro virtuální síť nebo virtuální počítač, který je již nasazen, pro nové nastavení DNS se projeví, musíte provést obnovení zapůjčení DHCP na všech ovlivněných virtuálních počítačích ve virtuální síti. Pro virtuální servery se systémem Windows OS to `ipconfig /renew` můžete provést zadáním přímo do virtuálního provozu. Kroky se liší v závislosti na os. Podívejte se na příslušnou dokumentaci pro váš typ operačního systému.

## <a name="next-steps"></a>Další kroky

Model nasazení Azure Resource Manageru:

* [Správa virtuální sítě](manage-virtual-network.md)
* [Správa síťového rozhraní](virtual-network-network-interface.md)

Klasický model nasazení:

* [Schéma konfigurace služby Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schéma konfigurace virtuální sítě](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurace virtuální sítě pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md)
