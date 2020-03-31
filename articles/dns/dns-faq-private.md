---
title: Nejčastější dotazy k Azure Private DNS
description: V tomto článku najdete nejčastější dotazy týkající se Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939429"
---
# <a name="azure-private-dns-faq"></a>Nejčastější dotazy k Azure Private DNS

Níže jsou nejčastější dotazy týkající se privátní DNS Azure.

## <a name="does-azure-dns-support-private-domains"></a>Podporuje Azure DNS privátní domény?

Privátní domény jsou podporované pomocí funkce Zóny Azure Privátní DNS. Privátní zóny DNS lze řešit pouze v rámci určených virtuálních sítí. Další informace naleznete v [přehledu](private-dns-overview.md).

Informace o dalších interních možnostech DNS v Azure najdete v [tématu Překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Budou privátní zóny DNS Azure fungovat napříč oblastmi Azure?

Ano. Privátní zóny jsou podporované pro rozlišení DNS mezi virtuálními sítěmi napříč oblastmi Azure. Privátní zóny funguje i bez explicitního partnerského vztahu virtuálních sítí. Všechny virtuální sítě musí být propojeny s privátní zónou DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Je připojení k Internetu z virtuálních sítí vyžadováno pro privátní zóny?

Ne. Soukromé zóny fungují společně s virtuálními sítěmi. Slouží ke správě domén pro virtuální počítače nebo jiné prostředky v rámci virtuálních sítí i mezi nimi. Připojení k Internetu není nutné pro překlad názvů.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Lze stejnou privátní zónu použít pro několik virtuálních sítí pro rozlišení?

Ano. Soukromou zónu DNS můžete propojit s tisíci virtuálních sítí. Další informace najdete v [tématu Azure DNS Limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Může být virtuální síť, která patří k jinému předplatnému, propojena se soukromou zónou?

Ano. Musíte mít oprávnění k operaci zápisu ve virtuálních sítích a privátní zóně DNS. Oprávnění k zápisu lze udělit několika rolím RBAC. Například role RBAC klasického síťového přispěvatele má oprávnění k zápisu do virtuálních sítí a role přispěvatele privátních zón DNS má oprávnění k zápisu do privátních zón DNS. Další informace o rolích RBAC naleznete v [tématu Řízení přístupu na základě rolí](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Budou automaticky registrované záznamy DNS virtuálního počítače v privátní zóně automaticky odstraněny, když virtuální počítač odstraníte?

Ano. Pokud odstraníte virtuální počítač v rámci propojené virtuální sítě s povolenou automatickou registrací, registrované záznamy se automaticky odstraní.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Může být automaticky registrovaný záznam virtuálního počítače v privátní zóně z propojené virtuální sítě odstraněn ručně?

Ano. Automaticky registrované záznamy DNS můžete přepsat ručně vytvořeným záznamem DNS v zóně. Následující otázka a odpověď se zabývají tímto tématem.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co se stane, když se pokusím ručně vytvořit nový záznam DNS do soukromé zóny, která má stejný název hostitele jako automaticky registrovaný existující virtuální počítač v propojené virtuální síti?

Pokoušíte se ručně vytvořit nový záznam DNS do privátní zóny, která má stejný název hostitele jako existující, automaticky registrovaný virtuální počítač v propojené virtuální síti. Když to uděláte, nový záznam DNS přepíše automaticky registrovaný záznam virtuálního počítače. Pokud se pokusíte odstranit tento ručně vytvořený záznam DNS ze zóny znovu, odstranění proběhne úspěšně. Automatická registrace se stane znovu, pokud virtuální počítač stále existuje a má k němu připojenou privátní IP adresu. Záznam DNS je automaticky znovu vytvořen v zóně.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co se stane, když odpojíme propojenou virtuální síť od privátní zóny? Budou automaticky registrované záznamy virtuálních strojů z virtuální sítě odebrány ze zóny také?

Ano. Chcete-li odpojit propojenou virtuální síť ze soukromé zóny, aktualizujte zónu DNS a odeberte přidružené propojení virtuální sítě. V tomto procesu jsou ze zóny odebrány záznamy virtuálních strojů, které byly automaticky zaregistrovány.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co se stane, když odstraníme propojenou virtuální síť propojenou se soukromou zónou? Musíme ručně aktualizovat privátní zónu, abychom odpojili virtuální síť jako propojenou virtuální síť od zóny?

Ne. Pokud odstraníte propojenou virtuální síť, aniž byste ji nejprve odpojili od privátní zóny, operace odstranění proběhne úspěšně a propojení se zónou DNS se automaticky vymaže.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Bude rozlišení DNS pomocí výchozího hlavního názvového práva (internal.cloudapp.net) stále fungovat i v případě, že je soukromá zóna (například private.contoso.com) propojena s virtuální sítí?

Ano. Privátní zóny nenahrazují výchozí internal.cloudapp.net zónu poskytovanou Azure. Ať už spoléháte na internal.cloudapp.net poskytované Azure nebo na vlastní privátní zónu, použijte hlavní název souboru se kvoferátoru zóny, proti které chcete vyřešit.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Změní se přípona DNS na virtuálních počítačích v rámci propojené virtuální sítě na příponu privátní zóny?

Ne. Přípona DNS na virtuálních počítačích ve vaší propojené virtuální síti zůstane jako výchozí přípona poskytovaná Azure ("*.internal.cloudapp.net"). Tuto příponu DNS na virtuálních počítačích můžete ručně změnit na příponu privátní zóny.
Pokyny ke změně této přípony naleznete [v seznamu Použití dynamickéslužby DNS k registraci názvů hostitelů na vlastním serveru DNS.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Jaké jsou limity využití privátních zón Azure DNS?

Podrobnosti o limitech využití privátních zón Azure DNS najdete v [limitech](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) Azure DNS.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Proč se moje stávající privátní zóny DNS nezobrazují v novém prostředí portálu?

Pokud byla vaše stávající privátní zóna DNS vytvořena pomocí rozhraní PREVIEW API, je nutné tyto zóny migrovat do nového modelu prostředků. Privátní zóny DNS vytvořené pomocí rozhraní PREVIEW API se v novém prostředí portálu nezobrazí. Pokyny k migraci do nového modelu prostředků naleznete níže.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Jak mohu migrovat své stávající privátní zóny DNS do nového modelu?

Důrazně doporučujeme migrovat na nový model prostředků co nejdříve. Starší model prostředků bude podporován, ale další funkce nebudou vyvinuty nad tento model. V budoucnu máme v úmyslu zamezit ji ve prospěch nového modelu zdrojů. Pokyny k migraci existujících privátních zón DNS do nového modelu prostředků najdete[v průvodci migrací privátních zón Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Další kroky

- [Další informace o Privátním DNS Azure](private-dns-overview.md)
