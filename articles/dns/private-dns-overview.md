---
title: Co je Privátní DNS v Azure?
description: Přehled privátní DNS, který hostuje službu v Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: aedace031eaedf2709993b5185979e8777821759
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444839"
---
# <a name="what-is-azure-private-dns"></a>Co je Privátní DNS v Azure?

> [!IMPORTANT]
> Azure Private DNS je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) název služby na jeho IP adresu.  Azure DNS je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů využitím infrastruktury Microsoft Azure. Kromě podpory domén DNS směřujících k Internetu, Azure DNS podporuje také privátní zóny DNS.

Privátní DNS Azure nabízí spolehlivou a zabezpečenou službu DNS pro správu a řešení názvů domén ve virtuální síti, aniž by bylo nutné přidat vlastního řešení DNS. S využitím privátních zón DNS, můžete použít vlastní názvy vlastních domén, místo ještě dnes dostupných názvů poskytovaných Azure. Pomocí vlastních názvů domén vám umožní přizpůsobit Architektura virtuální sítě podle potřeb vaší organizace. Nabízí řešení názvů pro virtuální počítače (VM) v rámci virtuální sítě a mezi virtuálními sítěmi. Navíc můžete konfigurovat názvy zón s rozděleným zobrazením, což umožňuje, aby privátní a veřejné zóny DNS název sdílené složky.

Chcete-li vyřešit záznamy privátní zóny DNS z vaší virtuální sítě, je třeba propojit virtuální sítě v zóně. Propojené virtuální sítě získáte plný přístup a může překládat všechny záznamy DNS, které jsou zveřejněné v privátní zónu. Kromě toho můžete také povolit Automatická registrace na propojení virtuální sítě. Pokud jsou povolená Automatická registrace na propojení virtuální sítě záznamy DNS pro virtuální počítače ve virtuální síti jsou registrovány v privátní zónu. Pokud je povolená Automatická registrace, Azure DNS také aktualizuje záznamy zóny pokaždé, když je vytvořen virtuální počítač, změny jeho "IP adresu, nebo je odstranit.

![Přehled systému DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Jako osvědčený postup, nepoužívejte *.local* domény pro privátní zóny DNS. Všechny operační systémy podporují.

## <a name="benefits"></a>Výhody

Azure Private DNS poskytuje následující výhody:

* **Eliminuje nutnost vlastního řešení DNS**. Mnoho zákazníků vytvořili vlastní DNS řešení ke správě zón DNS v jejich virtuální sítě. Teď můžete spravovat zóny DNS pomocí nativní infrastrukturu Azure, který odstraní starosti související s vytvořením a správou vlastního řešení DNS.

* **Použít všechny běžné typy záznamů DNS**. Azure DNS podporuje záznamy A, AAAA, CNAME, MX, PTR, SOA, SRV a TXT.

* **Správa záznamů automatické hostname**. Spolu s hostitelem vašich vlastních záznamů DNS, Azure automaticky uchovávat záznamy názvů hostitelů pro virtuální počítače v zadané virtuální sítě. V tomto scénáři můžete optimalizovat názvy domén, které používáte, bez nutnosti vytvoření vlastního řešení DNS nebo úprava aplikací.

* **Překlad názvů hostitelů mezi virtuálními sítěmi**. Na rozdíl od názvů hostitelů poskytuje Azure mohou být sdíleny privátních zón DNS virtuální sítě. Tato možnost zjednodušuje scénáře různé sítě a zjišťování služby, třeba partnerský vztah virtuální sítě.

* **Známé nástroje a činnost koncových uživatelů**. Aby se snížilo učit se, tato služba využívá zavedené nástroje Azure DNS (portál Azure portal, prostředí Azure PowerShell, rozhraní příkazového řádku Azure, šablon Azure Resource Manageru a rozhraní REST API).

* **Podpora DNS zobrazením Split-horizon**. S Azure DNS můžete vytvořit zóny se stejným názvem, které odkazují na různé odpovědi z v rámci virtuální sítě a z veřejného Internetu. Typický scénář pro službu DNS zobrazením split-horizon je poskytnout vyhrazený verze služby pro použití ve vaší virtuální síti.

* **K dispozici ve všech oblastech Azure**. Funkce privátních zón Azure DNS je k dispozici ve všech oblastech Azure ve veřejném cloudu Azure.

## <a name="capabilities"></a>Možnosti

Azure DNS poskytuje následující možnosti:

* **Automatická registrace virtuálních počítačů z virtuální sítě, který je propojený s privátní zóny s povolená Automatická registrace**. Virtuální počítače jsou registrované (přidal) do privátní zóny jako záznamy odkazující na jejich privátní IP adresy. Při odstranění virtuálního počítače ve virtuální síti odkaz s povolená Automatická registrace Azure DNS také automaticky odebere odpovídající záznam DNS z propojené privátní zónu.

* **Přední rozlišení serveru DNS je podporována mezi virtuálními sítěmi, které jsou propojeny s privátní zóny**. Pro různé virtuální síť překlad názvů DNS neexistuje žádná explicitní závislost tak, aby mezi sebou vytvoření partnerského vztahu virtuálních sítí. Však můžete chtít vytvořit partnerský vztah virtuálních sítí pro další scénáře (například přenosy HTTP).

* **Zpětné vyhledávání DNS je podporována v rámci virtuální sítě oboru**. Zpětné vyhledávání DNS pro privátní IP adresu v rámci virtuální sítě, které jsou přiřazeny k privátní zóně vrátí plně kvalifikovaný název domény, který obsahuje název hostitele/záznamu a zóny název jako přípona.

## <a name="known-issues"></a>Známé problémy
Následující položky jsou známé chyby a problémy ve verzi preview:
* Pokud odstraníte virtuální sítě propojené s privátní zóny DNS, nedojde k odstranění odkazů na privátní zóny DNS. Odkaz se nezdaří, pokud znovu vytvořte virtuální síť se stejným názvem a skupinu prostředků a zkuste jej znovu připojit k libovolné privátní zóny DNS. Chcete-li tento problém obejít, vytvořte virtuální síť v jiné skupině prostředků nebo s jiným názvem ve stejné skupině prostředků.
* Pokud přesunete virtuální sítě do jiné skupiny prostředků nebo předplatného, že neaktualizuje odkazy pro privátní zóny DNS. Překlad názvů pro přesunutý virtuální sítě i nadále fungovat, ale uvidíte původní ID ARM ve virtuální síti, když zobrazujete propojení virtuální sítě privátní zóny DNS.
* V současné době může selhat propojené virtuální sítě, které jsou hostované v Spojené arabské emiráty – sever, střed Spojené arabské emiráty, Jihoafrická republika – Západ, Jihoafrická republika – sever, Kanada – východ, Francie – jih a může se zobrazit přerušované problémy překlad DNS. 


## <a name="other-considerations"></a>Další důležité informace

Azure DNS má následující omezení:

* Konkrétnímu virtuálnímu síťovému lze propojit pouze jednu privátní zónu, jako kdyby je povolená Automatická registrace záznamů DNS virtuálního počítače. Můžete však propojit s několika virtuálními sítěmi jednu zónu DNS.
* Reverzní DNS funguje pouze pro privátní adresní prostor IP v propojené virtuální sítě
* Reverzních záznamů DNS pro privátní IP adresu pro virtuální síť propojenou vrátí "internal.cloudapp.net" jako výchozí přípona pro virtuální počítač. Pro virtuální sítě, které jsou propojeny s privátní zónou s povolená Automatická registrace, reverzní DNS pro privátní IP adresa vrátí 2 plně kvalifikované názvy domén, s výchozí příponou *internal.cloudapp.net* a druhou s příponou privátní zónu.
* Podmíněné předávání se v tuto chvíli nepodporuje nativně. Aby bylo možné rozlišení mezi Azure a místní sítí. Zobrazit [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu [ceny služby Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit privátní zónu v Azure DNS s využitím [prostředí Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku Azure](./private-dns-getstarted-cli.md).

* Přečtěte si informace o některé běžné [scénáře pro privátní zóny](./private-dns-scenarios.md) , která se dají realizovat s privátními zónami v Azure DNS.

* Pro běžné otázky a odpovědi k privátní zóny v Azure DNS, včetně konkrétní chování můžete očekávat pro některé typy operací, najdete v článku [nejčastější dotazy k DNS privátní](./dns-faq-private.md).

* Další informace o záznamy a zóny DNS najdete [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
