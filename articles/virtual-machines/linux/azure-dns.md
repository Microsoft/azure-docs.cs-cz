---
title: Možnosti překladu názvů DNS pro virtuální počítače se systémem Linux
description: Scénáře překladu názvů pro virtuální počítače se systémem Linux ve službě Azure IaaS, včetně poskytovaných služeb DNS, hybridního externího DNS a uvedení vlastního serveru DNS.
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556787"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Možnosti překladu názvů DNS pro virtuální počítače se systémem Linux v Azure
Azure poskytuje překlad názvů DNS standardně pro všechny virtuální počítače, které jsou v jedné virtuální síti. Můžete implementovat vlastní řešení překladu názvů DNS tak, že na virtuálních počítačích, které hostují Azure, nakonfigurujete vlastní služby DNS. Následující scénáře vám pomůžou vybrat ten, který bude fungovat pro vaši situaci.

* [Překlad názvů, který poskytuje Azure](#name-resolution-that-azure-provides)
* [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server)

Typ překladu názvů, který použijete, závisí na tom, jak vaše virtuální počítače a instance rolí potřebují vzájemně komunikovat.

Následující tabulka ilustruje scénáře a odpovídající řešení pro překlad názvů:

| **Scénář** | **Řešení** | **Auditování** |
| --- | --- | --- |
| Překlad názvů mezi instancemi rolí nebo virtuálními počítači ve stejné virtuální síti |Překlad názvů, který poskytuje Azure |název hostitele nebo plně kvalifikovaný název domény (FQDN) |
| Překlad názvů mezi instancemi rolí nebo virtuálními počítači v různých virtuálních sítích |Servery DNS spravované zákazníkem, které předávají dotazy mezi virtuálními sítěmi pro překlad prostřednictvím Azure (DNS proxy). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení místních počítačů a názvů služeb z instancí rolí nebo virtuálních počítačů v Azure |Servery DNS spravované zákazníkem (například místní řadič domény, místní řadič domény jen pro čtení nebo sekundární služba DNS, synchronizovaný pomocí zónových přenosů). Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad názvů hostitelů Azure z místních počítačů |Přepošle dotazy na proxy server DNS spravované zákazníkem v odpovídající virtuální síti. Proxy server předává dotazy do Azure pro řešení. Přečtěte si téma [Překlad adres IP pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Reverzní DNS pro interní IP adresy |[Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server) |Není k dispozici |

## <a name="name-resolution-that-azure-provides"></a>Překlad názvů, který poskytuje Azure
Spolu s překladem veřejných názvů DNS poskytuje Azure interní překlad adres IP pro virtuální počítače a instance rolí, které se nacházejí ve stejné virtuální síti. Ve virtuálních sítích, které jsou založené na Azure Resource Manager, je přípona DNS v rámci virtuální sítě konzistentní; plně kvalifikovaný název domény není potřeba. Názvy DNS je možné přiřadit jak ke kartám síťových rozhraní, tak i k virtuálním počítačům. I když překlad IP adres, který Azure poskytuje, nevyžaduje žádnou konfiguraci, není to vhodná volba pro všechny scénáře nasazení, jak je vidět v předchozí tabulce.

### <a name="features-and-considerations"></a>Funkce a požadavky
**Funkce**

* Pro použití překladu IP adres, kterou poskytuje Azure, není nutná žádná konfigurace.
* Služba překladu názvů, kterou poskytuje Azure, je vysoce dostupná. Nemusíte vytvářet a spravovat clustery pro vlastní servery DNS.
* Služba překladu IP adres, kterou poskytuje Azure, se dá použít společně s vlastními servery DNS k překladu místních i hostitelských názvů Azure.
* Překlad názvů je k dispozici mezi virtuálními počítači ve virtuálních sítích bez nutnosti plně kvalifikovaného názvu domény.
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení, a ne pracovat s automaticky generovanými názvy.

**Odůvodněn**

* Příponu DNS, kterou Azure vytvoří, nejde změnit.
* Nemůžete ručně registrovat vlastní záznamy.
* Služba WINS a rozhraní NetBIOS nejsou podporovány.
* Názvy hostitelů musí být kompatibilní se službou DNS.
    Názvy musí používat jenom 0-9, a-z a-a nesmí začínat ani končit znakem-. Viz dokument RFC 3696 část 2.
* Přenos dotazů DNS je pro každý virtuální počítač omezený. Omezení by nemělo mít vliv na většinu aplikací.  Je-li zjištěno omezení požadavků, zajistěte, aby bylo povoleno ukládání do mezipaměti na straně klienta.  Další informace najdete v tématu [získání maximum z překladu adres IP, které poskytuje Azure](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Získání maximum z překladu IP adres, které poskytuje Azure
**Ukládání do mezipaměti na straně klienta:**

Některé dotazy DNS nejsou přenášeny přes síť. Ukládání do mezipaměti na straně klienta pomáhá snižovat latenci a zlepšit odolnost proti nekonzistencím sítí tím, že překládá opakované dotazy DNS z místní mezipaměti. Záznamy DNS obsahují hodnotu TTL (Time to Live), která umožňuje ukládat záznamy co nejkratší, pokud by to mělo dopad na záznam aktuálnosti. V důsledku toho je ukládání do mezipaměti na straně klienta vhodné ve většině situací.

Některé distribuce systému Linux nezahrnují ve výchozím nastavení ukládání do mezipaměti. Po kontrole, že již není místní mezipaměť, doporučujeme přidat do každého virtuálního počítače se systémem Linux mezipaměť.

K dispozici je několik různých balíčků pro ukládání do mezipaměti DNS, například Dnsmasq. Tady je postup instalace Dnsmasq do nejběžnějších distribucí:

**Ubuntu (používá Resolvconf)**
  * Nainstalujte balíček Dnsmasq ("sudo apt-get install Dnsmasq").

**SUSE (používá NETCONF)**:
1. Nainstalujte balíček Dnsmasq (sudo zypperu Install Dnsmasq).
2. Povolte službu Dnsmasq (systemctl Enable Dnsmasq. Service).
3. Spusťte službu Dnsmasq (systemctl Start Dnsmasq. Service).
4. Upravte "/etc/sysconfig/Network/config" a změňte NETCONFIG_DNS_FORWARDER = "" na "Dnsmasq".
5. Aktualizujte soubor resolv. conf ("netconfig Update") a nastavte mezipaměť jako místní Překladač DNS.

**CentOS pomocí neautorizovaných vln softwaru (dříve OpenLogic; používá NetworkManager)**
1. Nainstalujte balíček Dnsmasq (sudo Yumu Install Dnsmasq).
2. Povolte službu Dnsmasq (systemctl Enable Dnsmasq. Service).
3. Spusťte službu Dnsmasq (systemctl Start Dnsmasq. Service).
4. Přidejte "předřadí název domény-servery 127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Restartujte síťovou službu ("restartování sítě služby") a nastavte mezipaměť jako místní Překladač DNS.

> [!NOTE]
> : Balíček ' Dnsmasq ' je pouze jednou z mnoha mezipamětí služby DNS, které jsou k dispozici pro Linux. Než ho použijete, ověřte, jestli vyhovuje vašim potřebám a jestli není nainstalovaná žádná jiná mezipaměť.
>
>

**Opakované pokusy na straně klienta**

DNS je primárně protokol UDP. Vzhledem k tomu, že protokol UDP nezaručí doručování zpráv, samotný protokol DNS zpracovává logiku opakování. Každý klient DNS (operační systém) může v závislosti na předvolbách autora vykazovat různou logiku opakování:

* Operační systémy Windows se zopakují po jedné sekundě a potom znovu po dalších dvou, čtyřech a dalších čtyřech sekundách.
* Výchozí nastavení pro Linux se zopakuje po pěti sekundách.  Tuto změnu byste měli změnit na opakování v sekundách za sekundu.  

Chcete-li zkontrolovat aktuální nastavení na virtuálním počítači se systémem Linux, ' Cat/etc/resolv.conf ' a podívejte se na řádek ' Options ', například:

```config-conf
options timeout:1 attempts:5
```

Soubor soubor resolv. conf se vygeneruje automaticky a neměl by se upravovat. Konkrétní kroky, které přidávají řádek ' Options ', se liší podle distribuce:

**Ubuntu** (používá Resolvconf)
1. Přidejte řádek Options do '/etc/Resolvconf/resolv.conf.d/head '.
2. Spusťte příkaz ' Resolvconf-u ', který chcete aktualizovat.

**SUSE** (používá NETCONF)
1. Přidání timeout: 1 pokusů: 5 do parametru NETCONFIG_DNS_RESOLVER_OPTIONS = "v"/etc/sysconfig/Network/config ".
2. Spusťte aktualizaci spuštěním příkazu ' netconfig Update '.

**CentOS pomocí neautorizovaných vln softwaru (dříve OpenLogic)** (používá NetworkManager)
1. Přidání RES_OPTIONS = časový limit: 1 pokusy: 5 "" na "/etc/sysconfig/Network".
2. Spusťte restart síťové služby, aby se aktualizoval.

## <a name="name-resolution-using-your-own-dns-server"></a>Překlad názvů pomocí vlastního serveru DNS
Vaše potřeby překladu IP adres můžou překračovat rámec funkcí, které poskytuje Azure. Například můžete vyžadovat překlad DNS mezi virtuálními sítěmi. Pro pokrytí tohoto scénáře můžete používat vlastní servery DNS.  

Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů, které jsou ve stejné virtuální síti. Například server DNS, který běží v Azure, může reagovat na dotazy DNS na vlastní soubory zóny DNS a předávat všechny ostatní dotazy do Azure. Tato funkce umožňuje virtuálním počítačům zobrazit jak vaše položky v souborech zóny, tak i názvy hostitelů, které poskytuje Azure (přes službu pro zápis). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

Předávání DNS taky umožňuje překlad DNS mezi virtuálními sítěmi a umožňuje překlad názvů hostitelů, které poskytuje Azure, v místních počítačích. Aby bylo možné přeložit název hostitele virtuálního počítače, musí se virtuální počítač serveru DNS nacházet ve stejné virtuální síti a musí být nakonfigurovaný tak, aby do Azure předal dotazy na název hostitele. Vzhledem k tomu, že se přípona DNS v každé virtuální síti liší, můžete použít pravidla podmíněného předávání k odeslání dotazů DNS do správné virtuální sítě pro rozlišení. Následující obrázek ukazuje dvě virtuální sítě a místní síť, která provádí překlad DNS mezi virtuálními sítěmi pomocí této metody:

![Překlad názvů DNS mezi virtuálními sítěmi](./media/azure-dns/inter-vnet-dns.png)

Když použijete překlad IP adres, který poskytuje Azure, interní přípona DNS se poskytne každému virtuálnímu počítači pomocí DHCP. Pokud používáte vlastní řešení překladu IP adres, tato přípona není k dispozici virtuálním počítačům, protože přípona koliduje s ostatními architekturami DNS. Pokud chcete odkazovat na počítače podle plně kvalifikovaného názvu domény nebo nakonfigurovat příponu na virtuálních počítačích, můžete k určení přípony použít PowerShell nebo rozhraní API:

* Pro virtuální sítě, které jsou spravovány nástrojem Azure Resource Manager, je přípona k dispozici prostřednictvím prostředku [síťového rozhraní](/rest/api/virtualnetwork/networkinterfaces) . Můžete také spustit `azure network public-ip show <resource group> <pip name>` příkaz, který zobrazí podrobnosti o veřejné IP adrese, včetně plně kvalifikovaného názvu domény síťového rozhraní.

Pokud předávání dotazů do Azure nevyhovuje vašim potřebám, je třeba zadat vlastní řešení DNS.  Vaše řešení DNS potřebuje:

* Poskytněte vhodné rozlišení názvu hostitele, například přes [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Pokud používáte DDNS, možná budete muset zakázat úklid záznamů DNS. Zapůjčení DHCP v Azure je velmi dlouhé a úklid může předčasně odebírat záznamy DNS.
* Poskytněte vhodné rekurzivní řešení, aby bylo možné řešit názvy externích domén.
* Musí být přístupná (TCP a UDP na portu 53) od klientů, které obsluhuje a který bude mít přístup k Internetu.
* Zabezpečený přístup z Internetu, aby se zmírnily hrozby, které představují externí agenti.

> [!NOTE]
> Nejlepšího výkonu dosáhnete, když při použití virtuálních počítačů na serverech Azure DNS zakážete protokol IPv6 a přiřadíte [veřejnou IP adresu na úrovni instance](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) ke každému virtuálnímu počítači serveru DNS.  
>
>
