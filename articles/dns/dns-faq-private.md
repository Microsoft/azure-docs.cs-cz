---
title: Nejčastější dotazy k Azure Private DNS
description: Nejčastější dotazy k Azure Privátní DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155709"
---
# <a name="azure-private-dns-faq"></a>Nejčastější dotazy k Azure Private DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Podporuje Azure DNS privátní domény?

Podpora privátních domén je podporovaná pomocí funkce zóny Azure Privátní DNS. Privátní DNS zóny se spravují pomocí stejných nástrojů, jako internetové Azure DNS zóny. Dají se přeložit jenom v zadaných virtuálních sítích. Další informace najdete v tématu [Přehled](private-dns-overview.md).

Informace o dalších interních možnostech DNS v Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Bude Azure DNS Private Zones fungovat napříč oblastmi Azure?

Ano. Privátní zóny se podporují pro překlad DNS mezi virtuálními sítěmi napříč oblastmi Azure. Soukromé zóny fungují i bez explicitního vytvoření partnerského vztahu virtuálních sítí. Všechny virtuální sítě musí být zadány jako virtuální sítě pro překlad pro privátní zónu. Možná budete potřebovat, aby virtuální sítě byly partnerského vztahu pro provoz TCP/HTTP, aby bylo možné tok z jedné oblasti do druhé.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Je připojení k Internetu z virtuálních sítí nezbytných pro privátní zóny?

Ne. Soukromé zóny fungují společně s virtuálními sítěmi. Můžete je použít ke správě domén pro virtuální počítače nebo jiné prostředky v rámci virtuálních sítí a mezi nimi. Pro překlad IP adres není nutné připojení k Internetu.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Může být stejná privátní zóna použita pro více virtuálních sítí pro řešení?

Ano. K jedné privátní zóně můžete přidružit až 1000 virtuálních sítí.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Může být virtuální síť, která patří do jiného předplatného, přidaná jako propojená virtuální síť k privátní zóně?

Ano. U virtuálních sítí a privátní zóny DNS musíte mít oprávnění k operaci zápisu. Oprávnění k zápisu lze udělit několika rolím RBAC. Například role RBAC přispěvatele klasické sítě má oprávnění k zápisu do virtuálních sítí. Další informace o rolích RBAC najdete v tématu [řízení přístupu na základě role](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Budou automaticky při odstranění virtuálního počítače automaticky odstraněny záznamy DNS virtuálního počítače v privátní zóně?

Ano. Pokud odstraníte virtuální počítač v rámci propojené virtuální sítě s povoleným automatickým registrací, zaregistrované záznamy se automaticky odstraní.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Je možné automaticky zaregistrovaný záznam virtuálního počítače v privátní zóně z propojené virtuální sítě odstranit ručně?

Ano. Můžete přepsat automaticky zaregistrované záznamy DNS pomocí ručně vytvořeného záznamu DNS v zóně. Následující otázku a odpověď řeší toto téma.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co se stane, když se pokusím ručně vytvořit nový záznam DNS do privátní zóny, která má stejný název hostitele jako automaticky registrovaný existující virtuální počítač v propojené virtuální síti?

Pokusíte se ručně vytvořit nový záznam DNS do privátní zóny, která má stejný název hostitele jako stávající, automaticky registrovaný virtuální počítač v propojené virtuální síti. Když to uděláte, nový záznam DNS přepíše záznam automaticky registrovaných virtuálních počítačů. Pokud se pokusíte odstranit ručně vytvořený záznam DNS ze zóny, bude odstranění úspěšné. Automatická registrace proběhne znovu, dokud virtuální počítač stále existuje a má k němu připojenou privátní IP adresu. Záznam DNS se znovu vytvoří automaticky v zóně.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co se stane, když odpojíme propojenou virtuální síť od privátní zóny? Budou automaticky registrované záznamy virtuálních počítačů z této virtuální sítě odebrány z této zóny.

Ano. Pokud chcete odpojit propojenou virtuální síť od privátní zóny, aktualizujte zónu DNS tak, aby se odebral odkaz na přidruženou virtuální síť. V tomto procesu se záznamy virtuálních počítačů, které byly automaticky zaregistrované, odeberou ze zóny.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co se stane, když odstraníme propojenou virtuální síť, která je propojená s privátní zónou? Musíme privátní zónu ručně aktualizovat, aby se virtuální síť odpojí jako propojená virtuální síť z zóny?

Ano. Při odstranění propojené virtuální sítě bez předchozího odpojení od privátní zóny bude operace odstranění úspěšná. Virtuální síť ale není automaticky odpojování od privátní zóny, pokud existuje. Je nutné ručně zrušit propojení virtuální sítě z privátní zóny. Z tohoto důvodu odpojte svoji virtuální síť od privátní zóny, než ji odstraníte.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Bude překlad DNS pomocí výchozího plně kvalifikovaného názvu domény (internal.cloudapp.net) stále fungovat i v případě, že privátní zóna (například private.contoso.com) je propojena s virtuální sítí?

Ano. Privátní zóny nenahrazují výchozí rozlišení DNS pomocí zóny internal.cloudapp.net poskytované službou Azure. Nabízí se jako další funkce nebo vylepšení. Bez ohledu na to, jestli jste spoléhat na internal.cloudapp.net poskytované Azure nebo ve vaší vlastní privátní zóně, použijte plně kvalifikovaný název domény zóny, se kterou chcete řešení vyřešit.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Bude přípona DNS na virtuálních počítačích v propojené virtuální síti změněna na síť privátní zóny?

Ne. Přípona DNS na virtuálních počítačích v propojené virtuální síti zůstane jako výchozí přípona poskytnutá pro Azure (*. internal.cloudapp.net). Tuto příponu DNS můžete na virtuálních počítačích ručně změnit na adresu privátní zóny.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Jaká jsou omezení využití pro Azure Privátní DNS?

Následující výchozí omezení platí při použití Azure Privátní DNS.

| Resource | Výchozí limit |
| --- | --- |
|Privátní DNS zóny na předplatné|1000|
|Sady záznamů na zónu Privátní DNS|25,000|
|Počet záznamů na sadu záznamů|20|
|Virtual Network odkazy na privátní zónu DNS|1000|
|Odkazy na virtuální sítě podle privátních zón DNS s povolenou automatickou registrací|100|
|Počet privátních zón DNS, na které může virtuální síť připojit s povolenou automatickou registrací|1|
|Počet privátních zón DNS, které může virtuální síť připojit|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Je na portálu podporovaná podpora privátních zón?

Ano a privátní zóny, které už jsou vytvořené prostřednictvím rozhraní API, PowerShellu, rozhraní příkazového řádku a sad SDK, jsou na Azure Portal viditelné.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Proč se moje existující privátní zóny DNS zobrazují v novém prostředí portálu?

V rámci aktualizace verze Preview jsme dodali nový model prostředků pro privátní zóny DNS. Vaše stávající privátní zóny DNS se budou muset migrovat do nového modelu prostředků, aby se mohly zobrazit v novém prostředí portálu. Pokyny k migraci na nový model prostředků najdete níže.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Návody migrovat existující privátní zóny DNS do nového modelu?
Důrazně doporučujeme migrovat na nový model prostředků co nejdříve. Starší verze modelu prostředků bude ale podporována, ale další funkce nebudou vyvinuty nad tímto modelem. V budoucnu máme v úmyslu tuto společnost zařadit jako místo nového modelu prostředků. Pokyny k migraci stávajících privátních zón DNS do nového modelu prostředků najdete v[Průvodci migrací pro Azure DNS privátní zóny](private-dns-migration-guide.md).

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure Privátní DNS](private-dns-overview.md)