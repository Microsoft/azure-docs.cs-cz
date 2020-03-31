---
title: Možnosti překladu názvů DNS pro virtuální počítače s Linuxem
description: Scénáře překladu názvů pro virtuální počítače s Linuxem v Azure IaaS, včetně poskytovaných služeb DNS, hybridního externího DNS a přineste si vlastní DNS server.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 3d5ecaf67dcff182c7dace474b7bda45cdfd5c58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969318"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Možnosti překladu názvů DNS pro virtuální počítače s Linuxem v Azure
Azure poskytuje překlad názvů DNS ve výchozím nastavení pro všechny virtuální počítače, které jsou v jedné virtuální síti. Vlastní řešení překladu názvů DNS můžete implementovat konfigurací vlastních služeb DNS na virtuálních počítačích, které Azure hostuje. Následující scénáře by vám měly pomoci vybrat ten, který funguje pro vaši situaci.

* [Překlad názvů, který Azure poskytuje](#name-resolution-that-azure-provides)
* [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server)

Typ překladu názvů, který používáte, závisí na tom, jak vaše virtuální počítače a instance rolí potřebují vzájemnou komunikaci.

Následující tabulka ilustruje scénáře a odpovídající řešení překladu názvů:

| **Scénář** | **Řešení** | **Přípona** |
| --- | --- | --- |
| Překlad názvů mezi instancemi rolí nebo virtuálními počítači ve stejné virtuální síti |Překlad názvů, který Azure poskytuje |název_hostitele nebo plně kvalifikovaný název domény (Plně kvalifikovaný název domény) |
| Překlad názvů mezi instancemi rolí nebo virtuálními počítači v různých virtuálních sítích |Servery DNS spravované zákazníkem, které předávají dotazy mezi virtuálními sítěmi pro rozlišení pomocí Azure (proxy DNS). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze fQDN |
| Řešení místních počítačů a názvů služeb z instancí rolí nebo virtuálních počítačů v Azure |Servery DNS spravované zákazníkem (například místní řadič domény, místní řadič domény jen pro čtení nebo sekundární synchronizovaný dns pomocí zónových přenosů). Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze fQDN |
| Řešení názvů hostitelů Azure z místních počítačů |Přepošle dotazy na server proxy DNS spravovaný zákazníkem v odpovídající virtuální síti. Proxy server předá dotazy do Azure pro řešení. Viz [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze fQDN |
| Reverzní DNS pro interní IP adresy |[Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server) |neuvedeno |

## <a name="name-resolution-that-azure-provides"></a>Překlad názvů, který Azure poskytuje
Spolu s překladem veřejných názvů DNS poskytuje Azure interní překlad názvů pro virtuální počítače a instance rolí, které jsou ve stejné virtuální síti. Ve virtuálních sítích, které jsou založené na Azure Resource Manager, je přípona DNS konzistentní napříč virtuální sítí; není potřeba vícenežové vyžití. Názvy DNS lze přiřadit k oběma síťovým kartám (NIC) i virtuálním počítačům. Přestože překlad názvů, který Azure poskytuje nevyžaduje žádnou konfiguraci, není vhodnou volbou pro všechny scénáře nasazení, jak je vidět v předchozí tabulce.

### <a name="features-and-considerations"></a>Funkce a důležité informace
**Funkce:**

* K použití překladu názvů, který Azure poskytuje, není vyžadována žádná konfigurace.
* Služba překladu názvů, kterou Azure poskytuje, je vysoce dostupná. Není nutné vytvářet a spravovat clustery vlastních serverů DNS.
* Službu překladu názvů, kterou Azure poskytuje, lze použít společně s vlastními servery DNS k překladu názvů místních i hostitelských názvů Azure.
* Překlad názvů je k dispozici mezi virtuálními počítači ve virtuálních sítích bez nutnosti hlavního názvu sítě.
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení, spíše než pracovat s automaticky generovanými názvy.

**Úvahy:**

* Příponu DNS, kterou Azure vytvoří, nelze změnit.
* Vlastní záznamy nelze zaregistrovat ručně.
* Služby WINS a NetBIOS nejsou podporovány.
* Názvy hostitelů musí být kompatibilní se službou DNS.
    Názvy musí používat pouze 0-9, a-z a '-', a nemohou začínat ani končit '-'. Viz RFC 3696 Oddíl 2.
* Provoz dotazů DNS je omezen pro každý virtuální počítač. Omezení by nemělo mít vliv na většinu aplikací.  Pokud je pozorováno omezení požadavků, ujistěte se, že je povoleno ukládání do mezipaměti na straně klienta.  Další informace najdete [v tématu Získání co nejvíce z překladu názvů, které Azure poskytuje](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Vytěžení nejlepšího z překladu názvů, které Azure poskytuje
**Ukládání do mezipaměti na straně klienta:**

Některé dotazy DNS nejsou odesílány v síti. Ukládání do mezipaměti na straně klienta pomáhá snížit latenci a zlepšit odolnost vůči síťovým nekonzistencím tím, že řeší opakované dotazy DNS z místní mezipaměti. Záznamy DNS obsahují hodnotu TTL (Time-To-Live), která umožňuje mezipaměti ukládat záznam co nejdéle, aniž by to mělo vliv na čerstvost záznamu. V důsledku toho je ukládání do mezipaměti na straně klienta vhodné pro většinu situací.

Některé distribuce Linuxu ve výchozím nastavení neobsahují ukládání do mezipaměti. Doporučujeme přidat mezipaměť do každého virtuálního počítače Linux po kontrole, že již není místní mezipaměti.

K dispozici je několik různých balíčků ukládání do mezipaměti DNS, například dnsmasq. Zde jsou kroky k instalaci dnsmasq na nejběžnější distribuce:

**Ubuntu (používá resolvconf)**
  * Nainstalujte balíček dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (používá netconf)**:
1. Nainstalujte balíček dnsmasq ("sudo zypper install dnsmasq").
2. Povolte službu dnsmasq ("systemctl enable dnsmasq.service").
3. Spusťte službu dnsmasq ("systemctl start dnsmasq.service").
4. Upravte "/etc/sysconfig/network/config" a změňte NETCONFIG_DNS_FORWARDER="" na "dnsmasq".
5. Aktualizujte resolv.conf ("netconfig update") a nastavte mezipaměť jako místní překladač DNS.

**CentOS by Rogue Wave Software (dříve OpenLogic, používá NetworkManager)**
1. Nainstalujte balíček dnsmasq ("sudo yum install dnsmasq").
2. Povolte službu dnsmasq ("systemctl enable dnsmasq.service").
3. Spusťte službu dnsmasq ("systemctl start dnsmasq.service").
4. Přidejte "předřadní servery 127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Restartujte síťovou službu ("restartování servisní sítě") a nastavte mezipaměť jako místní překladač DNS

> [!NOTE]
> : Balíček dnsmasq je pouze jedním z mnoha DNS cache, které jsou k dispozici pro Linux. Před použitím zkontrolujte jeho vhodnost pro vaše potřeby a zda není nainstalována žádná jiná mezipaměť.
>
>

**Opakování na straně klienta**

DNS je především protokol UDP. Vzhledem k tomu, že protokol UDP nezaručuje doručení zprávy, samotný protokol DNS zpracovává logiku opakování. Každý klient DNS (operační systém) může vykazovat jinou logiku opakování v závislosti na předvolbách autora:

* Operační systémy Windows opakujte po jedné sekundě a pak znovu po dalších dvou, čtyřech a dalších čtyřech sekundách.
* Výchozí nastavení Linuxu se opakuje po pěti sekundách.  Měli byste to změnit tak, aby se v intervalech jedné sekundy pětkrát opakovat.  

Chcete-li zkontrolovat aktuální nastavení na virtuálním počítači Linux, 'cat /etc/resolv.conf', a podívejte se na 'možnosti' řádek, například:

    options timeout:1 attempts:5

Soubor resolv.conf je automaticky generován a neměl by být upravován. Konkrétní kroky, které přidávají řádek "možnosti", se liší podle rozdělení:

**Ubuntu** (používá resolvconf)
1. Přidejte řádek možností do '/etc/resolveconf/resolv.conf.d/head'.
2. Spusťte 'resolvconf -u' aktualizovat.

**SUSE** (používá netconf)
1. Přidejte 'timeout:1 attempts:5' do parametru NETCONFIG_DNS_RESOLVER_OPTIONS="" v '/etc/sysconfig/network/config'.
2. Spusťte aktualizaci netconfig.

**CentOS by Rogue Wave Software (dříve OpenLogic)** (používá NetworkManager)
1. Přidejte 'RES_OPTIONS="timeout:1 pokusy:5"' do '/etc/sysconfig/network'.
2. Spusťte aktualizaci restartu servisní sítě.

## <a name="name-resolution-using-your-own-dns-server"></a>Překlad názvů pomocí vlastního serveru DNS
Vaše potřeby překladu názvů mohou jít nad rámec funkcí, které Azure poskytuje. Můžete například vyžadovat rozlišení DNS mezi virtuálními sítěmi. Chcete-li pokrýt tento scénář, můžete použít vlastní servery DNS.  

Servery DNS v rámci virtuální sítě mohou předávat dotazy DNS rekurzivním překládání Azure, aby vyřešily názvy hostitelů, které jsou ve stejné virtuální síti. Například server DNS, který běží v Azure, může reagovat na dotazy DNS pro své vlastní soubory zóny DNS a předávat všechny ostatní dotazy do Azure. Tato funkce umožňuje virtuálním počítačům zobrazit položky v souborech zóny a názvy hostitelů, které Azure poskytuje (prostřednictvím předávání). Přístup k rekurzivním překladačům Azure je k dispozici prostřednictvím virtuální IP 168.63.129.16.

Předávání DNS také umožňuje rozlišení DNS mezi virtuálními sítěmi a umožňuje místním počítačům vyřešit názvy hostitelů, které Azure poskytuje. Chcete-li přeložit název hostitele virtuálního počítače, virtuální počítač DNS serveru musí být umístěn ve stejné virtuální síti a musí být nakonfigurován tak, aby předávat dotazy na název hostitele do Azure. Vzhledem k tomu, že se přípona DNS v jednotlivých virtuálních sítích liší, můžete použít pravidla podmíněného předávání k odeslání dotazů DNS do správné virtuální sítě k vyřešení. Následující obrázek ukazuje dvě virtuální sítě a místní sítě, které provádí překlad DNS mezi virtuálními sítěmi pomocí této metody:

![Rozlišení DNS mezi virtuálními sítěmi](./media/azure-dns/inter-vnet-dns.png)

Při použití překladu názvů, který poskytuje Azure, interní přípona DNS je k dispozici každému virtuálnímu počítači pomocí DHCP. Při použití vlastního řešení překladu názvů se tato přípona nedodává virtuálním počítačům, protože přípona narušuje jiné architektury DNS. Chcete-li odkazovat na počítače podle fqdn nebo nakonfigurovat příponu na virtuálních počítačích, můžete použít Prostředí PowerShell nebo rozhraní API k určení přípony:

* Pro virtuální sítě, které jsou spravované Správcem prostředků Azure, je přípona dostupná prostřednictvím prostředku [karty síťového rozhraní.](https://msdn.microsoft.com/library/azure/mt163668.aspx) Můžete také spustit `azure network public-ip show <resource group> <pip name>` příkaz pro zobrazení podrobností o vaší veřejné IP adrese, která zahrnuje hlavní obrazové obvody nic.

Pokud předávání dotazů do Azure nevyhovuje vašim potřebám, musíte poskytnout vlastní řešení DNS.  Vaše řešení DNS musí:

* Poskytněte odpovídající rozlišení názvu hostitele, například prostřednictvím [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Pokud používáte DDNS, možná budete muset zakázat úklid záznamů DNS. Zapůjčení služby DHCP azure jsou velmi dlouhé a úklid může předčasně odebrat záznamy DNS.
* Poskytněte vhodné rekurzivní rozlišení umožňující rozlišení externích názvů domén.
* Být přístupné (TCP a UDP na portu 53) od klientů, které slouží, a mít přístup k Internetu.
* Být zabezpečeny proti přístupu z Internetu ke zmírnění hrozeb, které představují externí agenti.

> [!NOTE]
> Pokud chcete dosáhnout nejlepšího výkonu, při použití virtuálních počítačů na serverech Azure DNS zakažte Protokol IPv6 a přiřaďte každému virtuálnímu počítači dns serveru [veřejnou IP adresu na úrovni instance.](../../virtual-network/virtual-networks-instance-level-public-ip.md)  
>
>
