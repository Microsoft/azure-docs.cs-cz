---
title: Možnosti překladu názvů DNS pro virtuální počítače s Linuxem v Azure
description: Název služby DNS scénáře pro virtuální počítače s Linuxem v Azure IaaS, včetně poskytuje řešení, hybridní externí DNS a přineste si vlastní DNS server.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: d899e037a144892d6d2c843fec08d63a9e3e514a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Možnosti překlad názvů DNS pro virtuální počítače s Linuxem v Azure
Azure poskytuje překlad názvu DNS ve výchozím nastavení pro všechny virtuální počítače, které se nacházejí v jedné virtuální sítě. Vlastní řešení rozlišení názvu DNS můžete implementovat podle konfigurace služby DNS pro vaše virtuální počítače Azure který je hostitelem. Následující scénáře by vám pomohou zvolit ten, který se dá použít pro vaši situaci.

* [Rozlišení názvů, které poskytuje Azure](#azure-provided-name-resolution)
* [Překlad názvů pomocí serveru DNS](#name-resolution-using-your-own-dns-server)

Typ překladu, který používáte závisí na tom, jak vaše virtuální počítače a instance rolí musí vzájemně komunikovat.

Následující tabulka uvádí scénáře a odpovídající název řešení řešení:

| **Scénář** | **Řešení** | **Přípona** |
| --- | --- | --- |
| Překlad mezi instancí role nebo virtuální počítače ve stejné virtuální síti |[Rozlišení názvů, které poskytuje Azure](#azure-provided-name-resolution) |název hostitele nebo plně kvalifikovaný název domény (FQDN) |
| Překlad mezi instance rolí virtuálních počítačů v různých virtuálních sítích |Spravované zákazníkem serverů DNS, které předávání dotazů mezi virtuálními sítěmi pro řešení Azure (DNS proxy). V tématu [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení místní počítačů a názvy služby z role instance nebo virtuálních počítačů v Azure |Spravované zákazníkem servery DNS (například místní řadič domény, řadiče místní domény jen pro čtení nebo sekundární DNS synchronizovat pomocí přenosy zóny). V tématu [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení Azure názvy hostitelů z místního počítače |Předávání dotazů na zákazníkem spravovaný proxy server DNS v odpovídající virtuální síť. Proxy server předává dotazy k Azure pro překlad. V tématu [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Reverse DNS pro interní IP adresy |[Překlad názvů pomocí serveru DNS](#name-resolution-using-your-own-dns-server) |neuvedeno |

## <a name="name-resolution-that-azure-provides"></a>Rozlišení názvů, které poskytuje Azure
Společně s překladu názvů DNS veřejné Azure poskytuje interní překlad adres pro virtuální počítače a instance rolí, které jsou ve stejné virtuální síti. Ve virtuálních sítích, které jsou založeny na Azure Resource Manager přípona DNS je konzistentní napříč virtuální sítě; plně kvalifikovaný název domény není potřeba. Názvy DNS lze přiřadit do karty síťového rozhraní (NIC) a virtuálních počítačů. I když překlad názvů, které Azure poskytuje nevyžaduje žádnou konfiguraci, není příslušnou volbu pro všechny scénáře nasazení, jak je vidět v předchozí tabulce.

### <a name="features-and-considerations"></a>Funkce a důležité informace
**Funkce:**

* Použít název řešení, které Azure poskytuje není nutná žádná konfigurace.
* Překládání adres poskytující Azure je vysoce dostupný. Nemusíte vytvářet a spravovat clustery serverů DNS.
* Překládání adres poskytující Azure můžete použít vlastní servery DNS přeložit na místě a Azure názvy hostitelů.
* Název řešení je k dispozici mezi virtuálními počítači ve virtuálních sítích bez nutnosti plně kvalifikovaný název domény.
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení místo práce s automaticky generovaných názvů.

**Aspekty:**

* Přípona DNS, která vytvoří Azure nelze upravit.
* Nelze ručně zaregistrovat vlastní záznamy.
* Služba WINS a NetBIOS nejsou podporovány.
* Názvy hostitelů musí být kompatibilní s DNS.
    Názvy musí používat pouze 0 – 9,-z, a '-', a nesmí začínat ani končit '-'. Najdete v dokumentu RFC 3696 části 2.
* Pro každý virtuální počítač je omezen provoz dotazu DNS. Omezení by nemělo mít vliv na většinu aplikací.  Pokud je dodržena omezení požadavků, ujistěte se, zda je povoleno ukládání do mezipaměti na straně klienta.  Další informace najdete v tématu [maximální využití služby překladu názvů, které Azure poskytuje](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Maximální využití služby překladu tohoto Azure poskytuje
**Ukládání do mezipaměti klienta:**

Některé dotazy DNS se neodesílají v síti. Ukládání do mezipaměti na straně klienta pomáhá snížit latenci a zlepšit odolnost proti sítě nekonzistence vyřešte opakující se dotazy DNS z místní mezipaměti. Záznamy DNS obsahovat Time To Live (TTL), který povolí výstupní mezipaměť pro uložení záznamu pro stejně dlouho bez dopadu na záznamů aktuálnosti. V důsledku toho ukládání do mezipaměti na straně klienta je vhodná pro většinu situace.

Některé Linuxových distribucích nezahrnují ukládání do mezipaměti ve výchozím nastavení. Doporučujeme přidat do mezipaměti pro každý virtuální počítač Linux po je zkontrolovat, že není k dispozici místní mezipaměti již.

Několik různých DNS ukládání do mezipaměti balíčků, jako je například dnsmasq, jsou k dispozici. Tady jsou kroky instalace dnsmasq na nejběžnější distribuce:

**Ubuntu (používá resolvconf)**
  * Nainstalujte balíček dnsmasq ("sudo instalace výstižný get dnsmasq").

**SUSE (používá netconf)**:
1. Nainstalujte balíček dnsmasq ("sudo zypper instalace dnsmasq").
2. Povolte službu dnsmasq ("Povolit dnsmasq.service systemctl").
3. Spusťte službu dnsmasq ("systemctl počáteční dnsmasq.service").
4. Upravit "/ atd/sysconfig/síť/config" a změňte NETCONFIG_DNS_FORWARDER = "" na "dnsmasq".
5. Aktualizujte resolv.conf ("netconfig update") k nastavení mezipaměti jako místní překladač služby DNS.

**CentOS softwarem podvodný Wave (dříve OpenLogic; používá NetworkManager)**
1. Nainstalujte balíček dnsmasq ("sudo yum instalace dnsmasq").
2. Povolte službu dnsmasq ("Povolit dnsmasq.service systemctl").
3. Spusťte službu dnsmasq ("systemctl počáteční dnsmasq.service").
4. Přidání "předřazení domény názvové servery 127.0.0.1;" na "/etc/dhclient-eth0.conf".
5. Restartujte síťovou službu ("sítě restartování služby") nastavit jako překladač DNS místní mezipaměti

> [!NOTE]
> : Balíček 'dnsmasq' je pouze jeden z mnoha mezipaměti DNS, které jsou k dispozici pro Linux. Dříve, než ho použijete, zkontrolujte jeho vhodnosti pro vaše potřeby a žádné jiné mezipaměti nainstalovaného.
>
>

**Opakování na straně klienta**

DNS je primárně protokol UDP. Protože protokol UDP nezaručuje doručení zpráv, zpracovává samotný protokol DNS logika opakovaných pokusů. Každý klient DNS (operačního systému) může vykazovat různé opakování logiku v závislosti na nástroj creator předvoleb:

* Operační systémy Windows opakujte po jeden druhý a pak znovu po další dva, čtyři a jiné čtyři sekund.
* Výchozí opakování instalace Linux po pět sekund.  Měli byste změnit to opakovat pětkrát v intervalech jednu sekundu.  

Chcete-li zkontrolovat aktuální nastavení na virtuální počítač Linux, 'cat /etc/resolv.conf' a podívejte se na ose položku Možnosti' například:

    options timeout:1 attempts:5

Soubor resolv.conf se generuje automaticky a by neměla být upravována. Konkrétní kroky, které přidejte položku Možnosti' řádek se liší podle distribuční:

**Ubuntu** (používá resolvconf)
1. Přidání možnosti řádek pro "/ etc/resolveconf/resolv.conf.d/head'.
2. Spustit "resolvconf -u" k aktualizaci.

**SUSE** (používá netconf)
1. Přidejte 'timeout:1 pokusů: 5' do NETCONFIG_DNS_RESOLVER_OPTIONS = "" parametr v/atd/sysconfig nebo sítě nebo konfigurace.
2. Spustit 'netconfig aktualizace, aktualizace.

**CentOS softwarem Wave podvodný (dříve OpenLogic)** (používá NetworkManager)
1. Přidejte ' RES_OPTIONS = "timeout:1 pokusů: 5" ' do '/ etc/sysconfig/síťová'.
2. Spustit 'služby sítě restartování' Chcete-li aktualizovat.

## <a name="name-resolution-using-your-own-dns-server"></a>Překlad názvů pomocí serveru DNS
Potřeb název řešení může přesahovat funkce, které poskytuje Azure. Například může vyžadovat překlad DNS mezi virtuálními sítěmi. Pro tento scénář, můžete použít vlastní servery DNS.  

Servery DNS v rámci virtuální sítě může předat dotazy DNS rekurzivní překladače Azure přeložit názvy hostitelů, které jsou ve stejné virtuální síti. Server DNS, který běží v Azure můžete například odpovídat na dotazy DNS pro vlastní soubory zón DNS a předávat všechny další dotazy k Azure. Tato funkce umožňuje virtuálním počítačům v tématu vaše záznamy v zóně soubory a názvy hostitelů, které Azure poskytuje (prostřednictvím předávání). Přístup k rekurzivní překladače Azure se poskytuje prostřednictvím virtuální IP adresa 168.63.129.16.

Předávání DNS také umožňuje překlad DNS mezi virtuálními sítěmi a umožňuje vaše místní počítače přeložit názvy hostitelů, které poskytuje Azure. Virtuální počítač serveru DNS přeložit název hostitele virtuálního počítače, se musí nacházet ve stejné virtuální síti a nakonfigurovat tak, aby dotazy dopředného název hostitele Azure. Protože přípona DNS se liší v každé virtuální sítě, můžete použít pravidla pro podmíněné předávání aby odesílalo dotazy DNS na správnou virtuální síť pro překlad. Následující obrázek ukazuje dvě virtuální sítě a k místní síti provádění překlad DNS mezi virtuálními sítěmi pomocí této metody:

![Překlad DNS mezi virtuálními sítěmi](./media/azure-dns/inter-vnet-dns.png)

Při použití rozlišení názvů, které Azure poskytuje interní přípona DNS je určen k každý virtuální počítač pomocí protokolu DHCP. Pokud používáte vlastní název řešení řešení, tato přípona se nedodává k virtuálním počítačům, protože přípona naruší jiné DNS architektury. K odkazování na počítače ve plně kvalifikovaný název domény nebo konfigurace přípony na virtuálních počítačích můžete PowerShell nebo rozhraní API k určení příponu:

* Pro virtuální sítě, které jsou spravovány nástrojem Azure Resource Manager, je k dispozici prostřednictvím přípona [karty síťového rozhraní](https://msdn.microsoft.com/library/azure/mt163668.aspx) prostředků. Můžete taky spustit `azure network public-ip show <resource group> <pip name>` příkaz k zobrazení podrobností o vaší veřejné IP adresy, který obsahuje plně kvalifikovaný název domény na síťový adaptér.

Pokud předávání dotazů do Azure, není vašim potřebám, potřebujete poskytovat řešení DNS.  Musí vaše řešení DNS:

* Zadejte odpovídající název hostitele řešení, například prostřednictvím [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Pokud používáte DDNS, může je nutné zakázat proces úklidu záznamů DNS. Zapůjčení DHCP ve službě Azure jsou velmi dlouhé a proces úklidu může odebrat záznamy DNS předčasně.
* Zadejte odpovídající rekurzivní překlad názvů povolit překlad názvů externí domény.
* Být přístupné (TCP a UDP na port 53) z klientů, které slouží a mít přístup k Internetu.
* Nutné zabezpečit před přístupem z Internetu, a zmírnit hrozby představované externí agenty.

> [!NOTE]
> Pro nejlepší výkon, pokud používáte virtuální počítače v Azure DNS servery, zakažte IPv6 a přiřadit [veřejná IP adresa na úrovni Instance](../../virtual-network/virtual-networks-instance-level-public-ip.md) pro každý virtuální počítač na serveru DNS.  
>
>
