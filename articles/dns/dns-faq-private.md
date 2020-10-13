---
title: Nejčastější dotazy k Azure Private DNS
description: V tomto článku se seznámíte s nejčastějšími dotazy k Azure Privátní DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 9d183f2da7b916b1547fa1f81aa877b1b5488b41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308431"
---
# <a name="azure-private-dns-faq"></a>Nejčastější dotazy k Azure Private DNS

Následují Nejčastější dotazy týkající se privátního DNS Azure.

## <a name="does-azure-dns-support-private-domains"></a>Podporuje Azure DNS privátní domény?

Privátní domény se podporují pomocí funkce zóny Azure Privátní DNS. Zóny Privátní DNS lze přeložit pouze v rámci zadaných virtuálních sítí. Další informace najdete v tématu [Přehled](private-dns-overview.md).

Informace o dalších interních možnostech DNS v Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Budou zóny Azure Privátní DNS fungovat napříč oblastmi Azure?

Ano. Privátní zóny se podporují pro překlad DNS mezi virtuálními sítěmi napříč oblastmi Azure. Soukromé zóny fungují i bez explicitního vytvoření partnerského vztahu virtuálních sítí. Všechny virtuální sítě musí být propojeny s privátní zónou DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Je připojení k Internetu z virtuálních sítí nezbytných pro privátní zóny?

Ne. Soukromé zóny fungují společně s virtuálními sítěmi. Můžete je použít ke správě domén pro virtuální počítače nebo jiné prostředky v rámci virtuálních sítí a mezi nimi. Pro překlad IP adres není nutné připojení k Internetu.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Může být stejná privátní zóna použita pro více virtuálních sítí pro řešení?

Ano. Můžete propojit privátní zónu DNS s tisíci virtuálních sítí. Další informace najdete v tématu [omezení Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) .

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Může být virtuální síť, která patří do jiného předplatného, propojená s privátní zónou?

Ano. U virtuálních sítí a privátní zóny DNS musíte mít oprávnění k operaci zápisu. Oprávnění k zápisu je možné udělit několika rolím Azure. Například role Azure Přispěvatel klasických sítí má oprávnění k zápisu do virtuálních sítí a role přispěvatele Privátní DNSch zón má oprávnění k zápisu do privátních zón DNS. Další informace o rolích Azure najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Budou automaticky při odstranění virtuálního počítače automaticky odstraněny záznamy DNS virtuálního počítače v privátní zóně?

Ano. Pokud odstraníte virtuální počítač v rámci propojené virtuální sítě s povoleným automatickým registrací, zaregistrované záznamy se automaticky odstraní.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Překonfigurovali jsme operační systém ve virtuálním počítači tak, aby měl nový název hostitele nebo statickou IP adresu. Proč se nezobrazují změny, které se projeví v privátní zóně?

Záznamy privátní zóny vyplní služba DHCP Azure. zprávy o registraci klienta jsou ignorovány. Pokud jste na virtuálním počítači zakázali podporu klientů DHCP tak, že nakonfigurujete statickou IP adresu, změny názvu hostitele nebo statické IP adresy se v tomto virtuálním počítači neprojeví v zóně.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Nakonfiguroval (a) jsem upřednostňovanou příponu DNS na svém virtuálním počítači s Windows. Proč se moje záznamy pořád zaregistrovaly v zóně propojené s virtuální sítí?

Služba DHCP Azure při registraci privátní zóny DNS ignoruje všechny přípony DNS. Pokud je například váš virtuální počítač nakonfigurovaný `contoso.com` jako primární přípona DNS, ale virtuální síť je propojená s `fabrikam.com` privátní zónou DNS, registrace virtuálního počítače se zobrazí v `fabrikam.com` privátní zóně DNS.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Je možné automaticky zaregistrovaný záznam virtuálního počítače v privátní zóně z propojené virtuální sítě odstranit ručně?

Ano. Můžete přepsat automaticky zaregistrované záznamy DNS pomocí ručně vytvořeného záznamu DNS v zóně. Následující otázku a odpověď řeší toto téma.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co se stane, když se pokusím ručně vytvořit nový záznam DNS do privátní zóny, která má stejný název hostitele jako automaticky registrovaný existující virtuální počítač v propojené virtuální síti?

Pokusíte se ručně vytvořit nový záznam DNS do privátní zóny, která má stejný název hostitele jako stávající, automaticky registrovaný virtuální počítač v propojené virtuální síti. Když to uděláte, nový záznam DNS přepíše záznam automaticky registrovaných virtuálních počítačů. Pokud se pokusíte odstranit ručně vytvořený záznam DNS ze zóny, bude odstranění úspěšné. Automatická registrace proběhne znovu, dokud virtuální počítač stále existuje a má k němu připojenou privátní IP adresu. Záznam DNS se znovu vytvoří automaticky v zóně.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co se stane, když odpojíme propojenou virtuální síť od privátní zóny? Budou automaticky registrované záznamy virtuálních počítačů z této virtuální sítě odebrány z této zóny.

Ano. Pokud chcete odpojit propojenou virtuální síť od privátní zóny, aktualizujte zónu DNS tak, aby se odebral odkaz na přidruženou virtuální síť. V tomto procesu se záznamy virtuálních počítačů, které byly automaticky zaregistrované, odeberou ze zóny.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co se stane, když odstraníme propojenou virtuální síť, která je propojená s privátní zónou? Musíme privátní zónu ručně aktualizovat, aby se virtuální síť odpojí jako propojená virtuální síť z zóny?

Ne. Když odstraníte propojenou virtuální síť bez předchozího odpojení od privátní zóny, operace odstranění proběhne úspěšně a odkazy na zónu DNS se automaticky vymažou.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Bude překlad DNS pomocí výchozího plně kvalifikovaného názvu domény (internal.cloudapp.net) stále fungovat i v případě, že privátní zóna (například private.contoso.com) je propojena s virtuální sítí?

Ano. Soukromé zóny nenahrazují výchozí zónu internal.cloudapp.net poskytovanou službou Azure. Bez ohledu na to, jestli jste spoléhat na internal.cloudapp.net poskytované Azure nebo ve vaší vlastní privátní zóně, použijte plně kvalifikovaný název domény zóny, se kterou chcete řešení vyřešit.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Bude přípona DNS na virtuálních počítačích v propojené virtuální síti změněna na síť privátní zóny?

Ne. Přípona DNS na virtuálních počítačích v propojené virtuální síti zůstane jako výchozí přípona poskytnutá pro Azure (*. internal.cloudapp.net). Tuto příponu DNS můžete na virtuálních počítačích ručně změnit na adresu privátní zóny.
Pokyny ke změně této přípony najdete [v tématu Použití dynamického DNS k registraci názvů hostitelů ve vlastním serveru DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients) .

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Jaká jsou omezení využití Azure DNS privátních zón?

Podrobnosti o limitech použití Azure DNSch privátních zón najdete v [Azure DNS omezeních](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) .

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Proč se moje existující privátní zóny DNS zobrazují v novém prostředí portálu?

Pokud se vaše stávající privátní zóna DNS vytvořila pomocí rozhraní API pro verzi Preview, musíte tyto zóny migrovat do nového modelu prostředků. Privátní DNS zóny vytvořené pomocí rozhraní API verze Preview se v novém prostředí portálu nezobrazí. Pokyny k migraci na nový model prostředků najdete níže.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Návody migrovat existující privátní zóny DNS do nového modelu?

Důrazně doporučujeme migrovat na nový model prostředků co nejdříve. Starší verze modelu prostředků bude ale podporována, ale další funkce nebudou vyvinuty nad tímto modelem. V budoucnu máme v úmyslu zařadit IT se stejným modelem jako nový model prostředků. Pokyny k migraci stávajících privátních zón DNS do nového modelu prostředků najdete v[Průvodci migrací pro Azure DNS privátní zóny](private-dns-migration-guide.md).

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure Privátní DNS](private-dns-overview.md)
