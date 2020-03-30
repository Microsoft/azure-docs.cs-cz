---
title: Co je Privátní DNS v Azure?
description: V tomto článku můžete začít s přehledem privátní služby hostingu DNS v Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939288"
---
# <a name="what-is-azure-private-dns"></a>Co je Privátní DNS v Azure?

Dns je zodpovědný za překlad (nebo překlad) názvu služby na jeho IP adresu.  Azure DNS je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Microsoft Azure. Kromě podpory domén DNS orientovaných na internet podporuje Azure DNS také privátní zóny DNS.

Azure Private DNS poskytuje spolehlivou a zabezpečenou službu DNS pro správu a překlad názvů domén ve virtuální síti bez nutnosti přidávat vlastní řešení DNS. Pomocí privátních zón DNS můžete použít vlastní názvy domén, nikoli názvy poskytované Azure, které jsou dnes k dispozici. Použití vlastních názvů domén vám pomůže přizpůsobit architekturu virtuální sítě tak, aby co nejlépe vyhovovala potřebám vaší organizace. Poskytuje překlad názvů pro virtuální počítače (VM) v rámci virtuální sítě a mezi virtuálními sítěmi. Kromě toho můžete nakonfigurovat názvy zón pomocí zobrazení s rozděleným horizontem, které umožňuje soukromé a veřejné zóně DNS sdílet název.

Chcete-li vyřešit záznamy soukromé zóny DNS z virtuální sítě, je nutné propojit virtuální síť se zónou. Propojené virtuální sítě mají úplný přístup a mohou vyřešit všechny záznamy DNS publikované v privátní zóně. Kromě toho můžete také povolit automatickou registraci na propojení virtuální sítě. Pokud povolíte automatickou registraci na propojení virtuální sítě, záznamy DNS pro virtuální počítače v této virtuální síti jsou registrovány v privátní zóně. Pokud je povolena automatická registrace, Azure DNS také aktualizuje záznamy zóny při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

![Přehled dns](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Osvědčeným postupem je nepoužívejte místní *doménu* pro privátní zónu DNS. Ne všechny operační systémy to podporují.

## <a name="benefits"></a>Výhody

Privátní SLUŽBA Azure poskytuje následující výhody:

* **Odstraňuje potřebu vlastních řešení DNS**. Dříve mnoho zákazníků vytvořilo vlastní řešení DNS pro správu zón DNS ve své virtuální síti. Zóny DNS teď můžete spravovat pomocí nativní infrastruktury Azure, která odstraňuje zátěž spojenou s vytvářením a správou vlastních řešení DNS.

* **Používejte všechny běžné typy záznamů DNS**. Azure DNS podporuje záznamy A, AAAA, CNAME, MX, PTR, SOA, SRV a TXT.

* **Automatická správa záznamů názvu hostitele**. Spolu s hostováním vlastních záznamů DNS Azure automaticky udržuje záznamy názvů hostname pro virtuální počítače v určených virtuálních sítích. V tomto scénáři můžete optimalizovat názvy domén, které používáte bez nutnosti vytvářet vlastní řešení DNS nebo upravovat aplikace.

* **Překlad názvu hostitele mezi virtuálními sítěmi**. Na rozdíl od názvů hostitelů poskytovaných Azure lze privátní zóny DNS sdílet mezi virtuálními sítěmi. Tato funkce zjednodušuje scénáře zjišťování mezi sítěmi a službami, jako je například partnerský vztah virtuální sítě.

* **Známé nástroje a uživatelské prostředí**. Aby se zkrátila křivka učení, používá tato služba zavedené nástroje Azure DNS (Azure Portal, Azure PowerShell, Azure CLI, šablony Azure Resource Manager a rozhraní REST API).

* **Podpora služby SPLIT-horizon DNS**. Pomocí Azure DNS můžete vytvářet zóny se stejným názvem, které se převažují na různé odpovědi z virtuální sítě a z veřejného internetu. Typický scénář pro split-horizon DNS je poskytnout vyhrazenou verzi služby pro použití uvnitř virtuální sítě.

* **K dispozici ve všech oblastech Azure**. Funkce Privátních zón Azure DNS je dostupná ve všech oblastech Azure ve veřejném cloudu Azure.

## <a name="capabilities"></a>Možnosti

Azure DNS poskytuje následující funkce:

* **Automatická registrace virtuálních počítačů z virtuální sítě, která je propojená s privátní zónou s povoleným automatickým registrací**. Virtuální počítače jsou registrovány (přidány) do privátní zóny jako záznamy A odkazující na jejich privátní IP adresy. Když se odstraní virtuální počítač ve virtuální síti s povoleným automatickým registrací, Azure DNS také automaticky odebere odpovídající záznam DNS z propojené privátní zóny.

* **Překlad dopředné služby DNS je podporován ve virtuálních sítích propojených s privátní zónou**. Pro překlad DNS napříč virtuálními sítěmi neexistuje žádná explicitní závislost, takže virtuální sítě jsou vzájemně propojeny. Můžete však chtít peer virtuální sítě pro jiné scénáře (například přenoshttp).

* **Zpětné vyhledávání DNS je podporováno v rámci oboru virtuální sítě**. Zpětné vyhledávání DNS pro privátní IP adresu ve virtuální síti přiřazené k privátní zóně vrátí název domény, který obsahuje název hostitele/záznamu a název zóny jako příponu.

## <a name="other-considerations"></a>Další aspekty

Azure DNS má následující omezení:

* Konkrétní virtuální síť může být propojena pouze s jednou privátní zónou, pokud je povolena automatická registrace záznamů DNS virtuálního počítače. Můžete však propojit více virtuálních sítí s jednou zónou DNS.
* Reverzní DNS funguje pouze pro privátní IP prostor v propojené virtuální síti
* Reverzní DNS pro privátní IP adresu pro propojenou virtuální síť vrátí *internal.cloudapp.net* jako výchozí příponu pro virtuální počítač. Pro virtuální sítě, které jsou propojeny s privátní zónou s povolenou automatickou registrací, vrátí reverzní DNS pro privátní IP adresu dva soubory Domény: jeden s výchozí příponou *internal.cloudapp.net* a druhou s příponou privátní zóny.
* Podmíněné předávání není aktuálně nativně podporováno. Povolení řešení mezi Azure a místními sítěmi. Viz [Překlad názvů pro virtuální uživatele a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Ceny

Informace o cenách najdete v [tématu Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit privátní zónu ve Službě Azure DNS pomocí [Azure PowerShellu](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých [běžných scénářích privátní zóny,](./private-dns-scenarios.md) které se můžou realizovat pomocí privátních zón v Azure DNS.

* Běžné otázky a odpovědi týkající se privátních zón ve službě Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v [tématu Nejčastější dotazy k privátním DNS](./dns-faq-private.md).

* Informace o zónách a záznamech DNS naleznete v přehledu [zón DNS a záznamů](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
