---
title: Co je Privátní DNS v Azure?
description: V tomto článku se seznámíte s přehledem privátní služby hostování DNS v Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311477"
---
# <a name="what-is-azure-private-dns"></a>Co je Privátní DNS v Azure?

Název domény systému nebo DNS zodpovídá za překlad (nebo překlad) názvu služby na IP adresu.  Azure DNS je hostingová služba pro domény a poskytuje překlad názvů pomocí Microsoft Azure infrastruktury. Azure DNS nepodporují jenom internetové domény DNS, ale podporuje taky privátní zóny DNS.

Azure Privátní DNS poskytuje spolehlivou a zabezpečenou službu DNS pro vaši virtuální síť. Azure Privátní DNS spravuje a překládá názvy domén ve virtuální síti, aniž by bylo nutné konfigurovat vlastní řešení DNS. Pomocí privátních zón DNS můžete použít vlastní název domény místo názvů poskytovaných službou Azure během nasazování. Použití vlastního názvu domény vám pomůže přizpůsobit svoji architekturu virtuální sítě tak, aby vyhovovala potřebám vaší organizace. Poskytuje překlad názvů pro virtuální počítače v rámci virtuální sítě a propojených virtuálních sítí. Kromě toho můžete nakonfigurovat názvy zón pomocí zobrazení s rozděleným horizontem, které umožňuje privátní a veřejné zóně DNS sdílet název.

Pokud chcete vyřešit záznamy privátní zóny DNS z vaší virtuální sítě, musíte propojit virtuální síť se zónou. Propojené virtuální sítě mají úplný přístup a můžou vyřešit všechny záznamy DNS publikované v privátní zóně. Můžete také povolit autoregistraci na propojení virtuální sítě. Když povolíte autoregistraci na propojení virtuální sítě, záznamy DNS pro virtuální počítače v této virtuální síti se zaregistrují v privátní zóně. Když je povolená Automatická registrace, Azure DNS aktualizuje záznam zóny při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

![Přehled služby DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Jako osvědčený postup nepoužívejte pro privátní zónu DNS *místní* doménu. Ne všechny operační systémy tuto podporu podporují.

## <a name="benefits"></a>Výhody

Azure Privátní DNS přináší následující výhody:

* **Odstraňuje nutnost vlastních řešení DNS**. Dřív řada zákazníků vytvořila vlastní řešení DNS ke správě zón DNS ve své virtuální síti. Teď můžete spravovat zóny DNS pomocí nativní infrastruktury Azure, která odstraňuje zátěž pro vytváření a správu vlastních řešení DNS.

* **Použijte všechny běžné typy záznamů DNS**. Azure DNS podporuje záznamy typu AAAA, AAAA, CNAME, MX, PTR, SOA, SRV a TXT.

* **Automatická správa záznamů názvu hostitele**. Společně s hostováním vašich vlastních záznamů DNS Azure automaticky udržuje záznamy názvů hostitelů pro virtuální počítače v zadaných virtuálních sítích. V tomto scénáři můžete optimalizovat názvy domén, které použijete, bez nutnosti vytvářet vlastní řešení DNS nebo upravovat aplikace.

* **Překlad názvů hostitelů mezi virtuálními sítěmi**. Na rozdíl od názvů hostitelů poskytovaných Azure se můžou privátní zóny DNS sdílet mezi virtuálními sítěmi. Tato možnost zjednodušuje scénáře mezi sítí a zjišťováním služeb, jako je třeba partnerský vztah virtuálních sítí.

* **Známé nástroje a uživatelské prostředí**. K omezení výukové křivky používá tato služba dobře zřízené Azure DNS nástroje (Azure Portal, Azure PowerShell, Azure CLI, šablony Azure Resource Manager a REST API).

* **Podpora DNS pro dělené Časové horizonty**. Pomocí Azure DNS můžete vytvořit zóny se stejným názvem, které se překládají na různé odpovědi z virtuální sítě a z veřejného Internetu. Typickým scénářem pro DNS s odděleným horizontem je poskytnutí vyhrazené verze služby pro použití v rámci vaší virtuální sítě.

* **K dispozici ve všech oblastech Azure**. Funkce privátních zón Azure DNS je dostupná ve všech oblastech Azure ve veřejném cloudu Azure.

## <a name="capabilities"></a>Možnosti

Azure DNS poskytuje následující možnosti:

* **Automatická registrace virtuálních počítačů z virtuální sítě, která je propojená s privátní zónou s povolenou automatickou registrací**. Virtuální počítače se zaregistrují do privátní zóny jako záznamy odkazující na jejich privátní IP adresy. Když se odstraní virtuální počítač ve virtuální síti s povoleným automatickým registrací, Azure DNS taky automaticky odebere odpovídající záznam DNS z propojené privátní zóny.

* **Předávání překladu DNS je podporované napříč virtuálními sítěmi, které jsou propojené s privátní zónou**. V případě překladu názvů DNS mezi virtuálními sítěmi neexistuje žádná explicitní závislost tak, aby byly virtuální sítě navzájem navzájem partnerské. Je však možné, že budete chtít vytvořit partnerský vztah virtuálních sítí pro jiné scénáře (například přenos HTTP).

* **Zpětné vyhledávání DNS je podporované v rámci oboru virtuální sítě**. Zpětné vyhledávání DNS pro soukromou IP adresu přidruženou k privátní zóně vrátí plně kvalifikovaný název domény, který bude obsahovat název hostitele nebo záznamu a název zóny jako příponu.

## <a name="other-considerations"></a>Další důležité informace

Azure DNS má následující omezení:

* Pokud je povolená Automatická registrace záznamů DNS virtuálních počítačů, může být konkrétní virtuální síť propojená jenom s jednou privátní zónou. Můžete však propojit více virtuálních sítí s jednou zónou DNS.
* Reverzní DNS funguje jenom pro privátní IP místo v propojené virtuální síti.
* Reverzní DNS pro soukromou IP adresu v propojené virtuální síti se vrátí `internal.cloudapp.net` jako výchozí přípona pro virtuální počítač. U virtuálních sítí, které jsou propojeny se soukromou zónou s povolenou autoregistrací, vrátí reverzní DNS pro privátní IP adresu dva plně kvalifikované názvy domén (FQDN): jednu s výchozí příponou `internal.cloudapp.net` a jinou s příponou privátní zóny.
* Podmíněné předávání není aktuálně podporováno nativně. Pokud chcete povolit řešení mezi Azure a místními sítěmi, přečtěte si téma [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).

* Seznamte se s informacemi o zónách a záznamech DNS návštěvou [přehledu zón a záznamů DNS](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
