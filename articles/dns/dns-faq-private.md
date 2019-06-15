---
title: Nejčastější dotazy k DNS Azure privátní
description: Nejčastější dotazy ohledně privátní DNS Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082843"
---
# <a name="azure-private-dns-faq"></a>Nejčastější dotazy k DNS Azure privátní

> [!IMPORTANT]
> Azure Private DNS je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS podporuje privátní domény?

Podpora pro privátní domény je podporována, pomocí funkce Azure privátní zóny DNS. Privátní zóny DNS se spravují pomocí stejných nástrojů jako Azure DNS zón směřujících k Internetu. Jsou Přeložitelné pouze z v rámci zadaného virtuálních sítích. Další informace najdete v tématu [přehled](private-dns-overview.md).

Informace o dalších interní možnosti služby DNS v Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Bude Azure DNS Private Zones fungovat v různých oblastech Azure?

Ano. Privátní zóny se podporuje pro překlad názvů DNS mezi virtuálními sítěmi v různých oblastech Azure. Privátní zóny funguje i bez explicitně partnerský vztah virtuálních sítí. Všechny virtuální sítě musí být zadán jako virtuální sítě pro překlad pro privátní zóny. Zákazníci můžou musí virtuální sítě vytvořit partnerský vztah pro provoz TCP/HTTP mají být předány z jedné oblasti.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Je připojení k Internetu z virtuální sítě požadované pro privátní zóny?

Ne. Privátní zóny fungovat společně s virtuálními sítěmi. Zákazníci používají ke správě domén pro virtuální počítače nebo jiné prostředky ve virtuálních sítích i mezi. Připojení k Internetu není vyžadováno pro překlad názvů.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Je možné stejnou privátní zóny pro několik virtuálních sítí pro překlad?

Ano. Můžete přidružit až 1000 virtuálních sítí jednu privátní zónu.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Můžete virtuální síť, která patří do jiného předplatného přidají jako propojené virtuální sítě pro privátní zóny?

Ano. Musíte mít oprávnění k zápisu operace na virtuální sítě a privátní zóny DNS. Některé role RBAC můžete udělit oprávnění k zápisu. Například role RBAC Přispěvatel klasické sítě má oprávnění k zápisu do virtuální sítě. Další informace o rolích RBAC najdete v tématu [řízení přístupu na základě rolí](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Záznamy DNS automaticky registrované virtuální počítač v privátní zóny se automaticky odstraní při odstranění virtuálního počítače?

Ano. Pokud odstraníte virtuální počítač v rámci virtuální sítě propojené s povolená Automatická registrace, odstraní se automaticky registrované záznamy.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Může být záznam automaticky registrované virtuální počítač v privátní zóny z propojené virtuální sítě odstranit ručně?

Ano. Automaticky registrované záznamy DNS můžete přepsat ručně vytvořený záznam DNS v zóně. Následující otázky a odpovědi adresy v tomto tématu.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co se stane, když se snažím ručně vytvořit nový záznam DNS do privátní zóny, který má stejný název hostitele jako automaticky registrované existujícího virtuálního počítače v propojené virtuální sítě?

Pokusíte ručně vytvořit nový záznam DNS do privátní zóny, který má stejný název hostitele jako virtuální počítač existující, automaticky registrované ve službě virtual network propojené. Pokud ano, přepíše nový záznam DNS záznam automaticky registrované virtuálního počítače. Pokud se pokusíte znovu odstranit tento ručně vytvořený záznam DNS v zóně, proběhne úspěšně odstranit. Automatická registrace dojde znovu jako virtuální počítač stále existuje a má k němu připojená privátní IP adresa. Záznam DNS nebude znovu vytvořena automaticky v zóně.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co se stane, když jsme odpojit virtuální síť propojenou z privátní zóny? Záznamy automaticky registrované virtuálního počítače z virtuální sítě se odebere ze zóny příliš?

Ano. Zrušení propojení propojené virtuální sítě z privátní zónu, aktualizujete zóny DNS se odebrat odkaz přidružených virtuálních sítí. V tomto procesu virtuální počítač záznamy, které jste zaregistrovali automaticky odeberou ze zóny.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co se stane, když jsme odstranit propojená virtuální síť, která je propojené s privátní zóny? Budeme muset ručně aktualizovat privátní zóny se zrušit propojení virtuální sítě jako propojené virtuální sítě v zóně?

Ano. Když odstraníte virtuální síť propojenou bez odpojení od privátní zónu, vaší operace odstranění úspěšná. Ale virtuální sítě není automaticky odpojit z vaší privátní zónu, pokud existuje. Musíte ručně zrušit propojení virtuální sítě z privátní zónu. Z tohoto důvodu odpojit před odstraněním virtuální sítě z vaší privátní zónu.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Budou překlad názvů DNS s využitím výchozí plně kvalifikovaný název domény (internal.cloudapp.net) i nadále fungovat i v případě, že privátní zóny (například private.contoso.com) je propojený s virtuální sítí?

Ano. Privátní zóny nenahrazuje rozlišení výchozí DNS pomocí Azure internal.cloudapp.net zóny. Je k dispozici další funkce nebo vylepšení. Ať už se spoléháte na internal.cloudapp.net poskytuje Azure nebo na vlastní privátní zónu, použijte plně kvalifikovaný název domény, které chcete, aby se přeložil zóny.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Příponu DNS na virtuální počítače v rámci virtuální sítě propojené se změní na tento privátní zóny?

Ne. Příponu DNS na virtuální počítače ve vaší virtuální sítě propojené zůstane jako výchozí příponou poskytuje Azure ("*. internal.cloudapp.net"). Tato přípona DNS můžete ručně změnit na virtuálních počítačích na, privátní zónu.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Jaká jsou omezení využití pro privátní DNS Azure?

Následující výchozí omezení platí při použití služby privátní DNS Azure.

| Resource | Výchozí omezení |
| --- | --- |
|Privátní zóny DNS na předplatné|1000|
|Sady záznamů za zóny privátního DNS|25,000|
|Záznamy na sadu záznamů|20|
|Propojení virtuální sítě na jeden privátní zóny DNS|1000|
|Povolené virtuální propojení sítí na jeden privátní zóny DNS pomocí automatické registrace|100|
|Počet privátních zón DNS virtuální sítě můžete získat spojena se pomocí automatické registrace serveru povolen|1|
|Počet privátních zón DNS, které lze získat propojit virtuální sítě|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Je k dispozici portálu podpora pro privátní zóny?

Ano a privátních zón, které budou vytvořeny již prostřednictvím rozhraní API, Powershellu, rozhraní příkazového řádku a sady SDK jsou viditelné na webu Azure portal.

## <a name="next-steps"></a>Další postup

- [Další informace o privátní DNS Azure](private-dns-overview.md)