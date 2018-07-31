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
ms.openlocfilehash: 93614d4889c9c884f25c5e05cd620e8303226323
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357762"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Možnosti překladu názvů DNS pro virtuální počítače s Linuxem v Azure
Azure poskytuje překlad názvů DNS ve výchozím nastavení pro všechny virtuální počítače, které jsou v jedné virtuální sítě. Vašeho vlastního řešení DNS název řešení můžete implementovat podle konfigurace služby DNS na virtuálních počítačích Azure, který je hostitelem. Následující scénáře byste mohli vybrat ten, který se dá použít pro vaše konkrétní potřeby.

* [Překlad názvů, který poskytuje Azure](#name-resolution-that-azure-provides)
* [Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server)

Druh překladu, který používáte závisí na jak vaše virtuální počítače a instance rolí potřebují komunikovat mezi sebou.

Následující tabulka popisuje scénáře a řešení rozlišení odpovídající název:

| **Scénář** | **Řešení** | **Přípona** |
| --- | --- | --- |
| Překlad mezi instancemi role nebo virtuálními počítači ve stejné virtuální síti |[Překlad názvů, který poskytuje Azure](#azure-provided-name-resolution) |název hostitele nebo plně kvalifikovaný název domény (FQDN) |
| Překlad mezi instancemi role nebo virtuálními počítači v různých virtuálních sítí |Spravované zákazníkem servery DNS, které dotazy mezi virtuálními sítěmi pro překlad Azure (DNS proxy). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Překlad místních počítačů a názvy služeb z instancí rolí nebo virtuálních počítačů v Azure |Spravované zákazníkem serverů DNS (například místní řadič domény, řadiče místní domény jen pro čtení nebo sekundární DNS synchronizované s použitím přenosů zóny). Zobrazit [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Řešení Azure názvy hostitelů z místních počítačů |Předávání dotazů na zákazníkem spravovaný DNS proxy serveru v příslušné virtuální sítě. Proxy server předává dotazy k Azure pro řešení. Zobrazit [překladu IP adresy serveru DNS](#name-resolution-using-your-own-dns-server). |Pouze plně kvalifikovaný název domény |
| Zpětné vyhledávání DNS pro interní IP adresy |[Překlad názvů pomocí vlastního serveru DNS](#name-resolution-using-your-own-dns-server) |neuvedeno |

## <a name="name-resolution-that-azure-provides"></a>Překlad názvů, který poskytuje Azure
Spolu s rozlišení veřejné názvy DNS Azure poskytuje interní překlad adres pro virtuální počítače a instance rolí, které jsou ve stejné virtuální síti. Ve virtuálních sítích, které jsou založeny na Azure Resource Manageru přípona DNS je konzistentní napříč virtuální sítě plně kvalifikovaný název není potřeba. Karty síťového rozhraní (NIC) a virtuální počítače lze přiřadit názvy DNS. I když překlad názvů, který poskytuje Azure nevyžaduje žádnou konfiguraci, není vhodnou volbou pro všechny scénáře nasazení, jak je znázorněno v předchozí tabulce.

### <a name="features-and-considerations"></a>Funkce a důležité informace
**Funkce:**

* Používání překladu, které poskytuje Azure není nutná žádná konfigurace.
* Překládání adres, které poskytuje Azure má vysokou dostupnost. Není nutné vytvořit a spravovat clustery vlastní servery DNS.
* Překládání adres, které poskytuje Azure je možné společně s vlastní servery DNS přeložit v místním i Azure názvy hostitelů.
* Název řešení je k dispozici mezi virtuálními počítači ve virtuálních sítích bez nutnosti plně kvalifikovaný název domény.
* Můžete použít názvy hostitelů, které nejlépe popisují vaše nasazení místo práce s automaticky generované názvy.

**Důležité informace:**

* Příponu DNS, vytvoří Azure se nedá upravit.
* Nelze ručně zaregistrovat vlastní záznamy.
* Služba WINS a NetBIOS se nepodporují.
* Názvy hostitelů musí být kompatibilní s DNS.
    Názvy musí používat pouze 0-9, a – z, a "-", a nesmí začínat ani končit '-'. V části RFC 3696 2.
* Pro každý virtuální počítač je omezen provoz dotazu DNS. Omezení využití sítě by neměla mít vliv na většinu aplikací.  Pokud se vyskytuje omezování žádostí, ujistěte se, že je povoleno ukládání do mezipaměti na straně klienta.  Další informace najdete v tématu [maximum, od překladu, které Azure poskytuje](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Na maximum na překladu adresy tohoto Azure poskytuje
**Ukládání do mezipaměti na straně klienta:**

Některé dotazy DNS se neodesílají v síti. Ukládání do mezipaměti na straně klienta pomáhá snižovat latenci a zlepšit odolnost síti nekonzistence řešení opakující se dotazy DNS z místní mezipaměti. Záznamy DNS obsahují hodnotu Time-To-Live (TTL), umožňující mezipaměť pro uložení záznamu pro co nejdéle bez dopadu na záznam aktuálnosti. Ukládání do mezipaměti na straně klienta je proto vhodný pro většinu situací.

Některých distribucích systému Linux nezahrnují ukládání do mezipaměti ve výchozím nastavení. Doporučujeme přidat do mezipaměti na každém virtuálním počítači s Linuxem po zkontrolujte, že existuje místní mezipaměti ještě není.

Ukládání do mezipaměti balíčků, jako je například dnsmasq, několik různých DNS jsou k dispozici. Tady je postup instalace dnsmasq na nejběžnější distribuce:

**Ubuntu (resolvconf používá)**
  * Nainstalujte balíček dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (používá netconf)**:
1. Nainstalujte balíček dnsmasq ("sudo zypperu instalace dnsmasq").
2. Povolte službu dnsmasq ("Povolit dnsmasq.service systemctl").
3. Spusťte službu dnsmasq ("start systemctl dnsmasq.service").
4. Upravit "/ etc/sysconfig/network/config" a změnit NETCONFIG_DNS_FORWARDER = "" k "dnsmasq".
5. Aktualizujte nastavení mezipaměti resolv.conf ("netconfig úpravy") jako místního překladače DNS.

**CentOS softwarem podvodný Wave (dříve OpenLogic; používá NetworkManager)**
1. Nainstalujte balíček dnsmasq ("sudo yum install dnsmasq").
2. Povolte službu dnsmasq ("Povolit dnsmasq.service systemctl").
3. Spusťte službu dnsmasq ("start systemctl dnsmasq.service").
4. Přidat "předřaďte doménu názvové servery 127.0.0.1;" na "/etc/dhclient-eth0.conf".
5. Restartujte síťovou službu ("sítě restart služby") nastavení mezipaměti jako místního překladače DNS

> [!NOTE]
> : Balíček "dnsmasq" je pouze jeden z mnoha mezipamětí DNS, které jsou k dispozici pro Linux. Před jejich použitím zkontrolujte jejich vhodnost pro vaše potřeby a že je nainstalována žádná mezipaměť.
>
>

**Opakované pokusy na straně klienta**

DNS je primárně protokol UDP. Protože protokol UDP nezaručuje doručení zpráv, samotný protokol DNS se stará logika opakovaných pokusů. Každá klient DNS (operačního systému) můžete vykazovat logiku opakování různé v závislosti na tvůrce příslušného předvolby:

* Operační systémy Windows opakovat po jedné sekundy a pak znovu po druhé dva, čtyři a další čtyři sekundy.
* Výchozí Linux nastavení opakované pokusy po pěti sekundách.  Měli byste změnit to chcete zkusit znovu pětkrát v sekundových intervalech.  

Chcete-li zkontrolovat aktuální nastavení na virtuálním počítači Linux, "cat adresáři /etc/resolv.conf" a podívejte se na řádku 'možnosti', například:

    options timeout:1 attempts:5

Soubor resolv.conf je automaticky vytvářen a by neměla být upravována. Konkrétní kroky, které aplikacím dodávají řádku 'možnosti' se liší podle distribuce:

**Ubuntu** (používá resolvconf)
1. Přidejte možnosti řádek, který ' / etc/resolveconf/resolv.conf.d/head ".
2. Spuštění 'resolvconf -u' aktualizovat.

**SUSE** (používá netconf)
1. Přidejte "timeout:1 pokusy: 5" NETCONFIG_DNS_RESOLVER_OPTIONS = "" parametr in/etc/sysconfig/network/config.
2. Spuštění 'netconfig aktualizace' Chcete-li aktualizovat.

**CentOS podvodný Wave softwarem (dříve OpenLogic)** (používá NetworkManager)
1. Přidat "RES_OPTIONS ="timeout:1 pokusy: 5"" na "/ etc/sysconfig/síť".
2. Spuštění 'služby sítě restartování' Chcete-li aktualizovat.

## <a name="name-resolution-using-your-own-dns-server"></a>Překlad názvů pomocí vlastního serveru DNS
Vaše potřeby rozlišení názvu může nad rámec funkcí, které poskytuje Azure. Například můžete potřebovat překlad názvů DNS mezi virtuálními sítěmi. Pro tento scénář, můžete použít vlastní servery DNS.  

Servery DNS v rámci virtuální sítě může předat dotazy DNS na rekurzivní překladače Azure přeložit názvy hostitelů, které jsou ve stejné virtuální síti. Server DNS, který běží v Azure můžete například reagovat na dotazy DNS pro vlastní soubory zón DNS a předávat všechny ostatní dotazy do Azure. Tato funkce umožňuje zobrazit vaše příspěvky v zón a názvy hostitelů, které Azure poskytuje (prostřednictvím předávání) virtuálním počítačům. Přístup k rekurzivní překladače Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

Předávání DNS také překlad názvů DNS mezi virtuálními sítěmi a umožňuje své místní počítače přeložit názvy hostitelů, které poskytuje Azure. Přeložit název hostitele virtuálního počítače, virtuální počítač server DNS se musí nacházet ve stejné virtuální síti a nakonfigurovat tak, aby dotazy dopředný název hostitele pro Azure. Protože se liší v obou virtuálních sítích příponu DNS, můžete použít pravidla pro podmíněné předávání aby odesílalo dotazy DNS na správnou virtuální síť pro překlad. Následující obrázek ukazuje dvě virtuální sítě a místní sítí způsobem překlad názvů DNS mezi virtuálními sítěmi s použitím této metody:

![Překlad DNS mezi virtuálními sítěmi](./media/azure-dns/inter-vnet-dns.png)

Při použití překladu, které Azure poskytuje interní přípony DNS poskytuje jednotlivým virtuálním počítačům pomocí protokolu DHCP. Pokud používáte vlastní název řešení řešení, tato přípona není zadán k virtuálním počítačům, protože přípona dochází ke kolizím s další architektury DNS. K odkazování na počítače podle plně kvalifikovaného názvu domény nebo konfigurovat příponu na virtuálních počítačích, můžete použít PowerShell nebo rozhraní API k určení přípona:

* Pro virtuální sítě, které se spravují pomocí Azure Resource Manageru, je k dispozici prostřednictvím příponu [síťové karty](https://msdn.microsoft.com/library/azure/mt163668.aspx) prostředků. Můžete taky spustit `azure network public-ip show <resource group> <pip name>` příkazu můžete zobrazit podrobnosti o svoji veřejnou IP adresu, která zahrnuje plně kvalifikovaný název domény síťové rozhraní

Pokud předávání dotazů do Azure není vyhovovala vašim potřebám, budete muset zadat svoje vlastní řešení DNS.  Musí vaše řešení DNS:

* Zadejte odpovídající název hostitele řešení, například prostřednictvím [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Pokud používáte DDNS, potřebujete zakázat úklidu záznamů DNS. Zapůjčení DHCP Azure jsou velmi dlouhé a úklid může odebrat záznamy DNS předčasně ukončen.
* Zadejte odpovídající rekurzivní překlad názvů, chcete-li povolit překlad názvů externí domény.
* Být přístupné (TCP a UDP port 53) z klientů, které slouží a mít přístup k Internetu.
* Zabezpečit proti přístupu z Internetu, a zmírnit hrozby vyplývající z externí agentů.

> [!NOTE]
> Pro zajištění nejlepšího výkonu při použití virtuálních počítačů v Azure DNS servery zakázat protokol IPv6 a přiřadit [veřejné IP adresy na úrovni Instance](../../virtual-network/virtual-networks-instance-level-public-ip.md) pro každý virtuální počítač se serverem DNS.  
>
>
