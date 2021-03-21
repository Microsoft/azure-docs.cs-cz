---
title: Matice podpory pro zálohování disků Azure
description: Poskytuje souhrn nastavení podpory a omezení zálohování disku Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 922bd532275cdd6b09df83b0e9d36fdec1da0b47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707213"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Matice podpory Azure disk Backup (ve verzi Preview)

>[!IMPORTANT]
>Služba Azure disk Backup je ve verzi Preview bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Vyplňte tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) a zaregistrujte se do verze Preview.

K ochraně disků Azure můžete použít [Azure Backup](./backup-overview.md) . Tento článek shrnuje dostupnost oblasti, podporované scénáře a omezení.

## <a name="supported-regions"></a>Podporované oblasti

Azure disk Backup je k dispozici ve verzi Preview v následujících oblastech: Západní USA, Středozápadní USA, východní USA 2, Kanada Central, Velká Británie – západ, Švýcarsko – sever, Švýcarsko – západ, Austrálie – střed, Austrálie – střed 2, Korea – jih, USA – západ, Japonsko, Východní Asie, Spojené arabské emiráty sever, Brazílie – jih, Střed Indie. 

Další oblasti budou oznámeny, jakmile budou k dispozici.

## <a name="limitations"></a>Omezení

- Pro Azure Managed Disks se podporuje zálohování disku Azure, včetně sdílených disků (Shared Premium SSD). Nespravované disky se nepodporují. V současné době toto řešení nepodporuje extrémně disky, včetně sdílených extrémně disků, z důvodu nedostatku možnosti snímku.

- Zálohování disku Azure podporuje zálohování Akcelerátor zápisu disku. Během obnovování se ale disk obnoví jako běžný disk. Po připojení k virtuálnímu počítači můžete na disku povolit urychlení zápisu.

- Azure Backup poskytuje provozní (snímkovou) úroveň zálohy Azure Managed disks s podporou pro více záloh za den. Zálohy se nekopírují do trezoru služby Backup.

- V současné době se možnost obnovení Original-Location (OLR) obnoví tak, že nahradíte stávající zdrojové disky, ze kterých se zálohy provedly, a nepodporují se. Můžete obnovit z bodu obnovení a vytvořit nový disk ve stejné skupině prostředků jako zdrojový disk, ze kterého byly zálohy pořízeny nebo v jakékoli jiné skupině prostředků. To se říká Alternate-Location Recovery (ALR).

- Azure Backup pro Managed Disks používá přírůstkové snímky, které jsou omezeny na 200 snímků na disk. Aby bylo možné převzít zálohy na vyžádání z naplánovaných záloh, zásady zálohování omezí celkové zálohy na 180. Přečtěte si další informace o [přírůstkovém snímku](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravované disky.

- [Limity předplatného a služeb](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) Azure se vztahují na celkový počet snímků disku na oblast a předplatné.

- Snímky na více discích připojených k virtuálnímu počítači se nepodporují na základě bodu v čase.

- Úložiště záloh a disky, které se mají zálohovat, můžou být ve stejném nebo jiném předplatném. Úložiště záloh i zálohovaný disk musí být ale ve stejné oblasti.

- Disky, které mají být zálohovány, a skupinu prostředků snímku, kde jsou snímky místně uloženy, musí být ve stejném předplatném.

- Je podporováno obnovení disku ze zálohy do stejného nebo jiného předplatného. Obnovený disk se ale vytvoří ve stejné oblasti jako snímek.

- Šifrované disky ADE nelze chránit.

- Podporují se disky šifrované pomocí klíčů spravovaných platformou nebo klíčů spravovaných zákazníkem. Obnovení však selže pro body obnovení disku, který je zašifrovaný pomocí klíčů spravovaných zákazníkem (CMK), pokud je klíč trezoru klíčů pro nastavení šifrování disku zakázaný nebo odstraněný.

- Zásady zálohování se v současné době nedají upravit a skupina prostředků snímku, která je přiřazená k instanci zálohy, když nakonfigurujete zálohování disku, nejde změnit.

- V současné době se Azure Portal prostředí pro konfiguraci zálohování disků omezí na maximálně 20 disků od stejného předplatného.

- V současné době (ve verzi Preview) není podporováno použití PowerShellu a Azure CLI ke konfiguraci zálohování a obnovení disků.

- Při konfiguraci zálohování musí být vybraný disk, který se má zálohovat, a skupinu prostředků snímku, kde mají být snímky uložené, a to v rámci stejného předplatného. Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku. Přečtěte si další informace o [přírůstkových snímcích](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravovaný disk. Další informace o tom, jak zvolit skupinu prostředků snímku, najdete v tématu  [Konfigurace zálohování](backup-managed-disks.md#configure-backup).

- Pro úspěšné operace zálohování a obnovení vyžadují přiřazení role spravovanou identitu trezoru záloh. Používejte jenom definice rolí, které jsou uvedené v dokumentaci. Použití jiných rolí, jako je vlastník, přispěvatel atd., se nepodporuje. Pokud po přiřazení rolí začnete konfigurovat operace zálohování nebo obnovení, můžete se setkat s problémy s oprávněními. Důvodem je to, že platnost přiřazení rolí trvá několik minut.

- Spravované disky umožňují měnit úroveň výkonu při nasazení nebo pak beze změny velikosti disku. Řešení zálohování disku Azure podporuje změny úrovně výkonu na zdrojovém disku, který se zálohuje. Během obnovení bude úroveň výkonu obnoveného disku stejná jako u zdrojového disku v době zálohování. Pokud chcete po operaci obnovení změnit úroveň výkonu [disku, postupujte](../virtual-machines/disks-performance-tiers-portal.md) podle pokynů v dokumentaci.

- Podpora [privátních odkazů](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) u spravovaných disků umožňuje omezit export a import spravovaných disků tak, aby se staly jenom v rámci vaší virtuální sítě Azure. Azure disk Backup podporuje zálohování disků, které mají povolené soukromé koncové body. Nezahrnuje data záloh nebo snímky, které budou přístupné prostřednictvím privátního koncového bodu.

- Během období Preview nemůžete zakázat zálohování, takže možnost **Zastavit zálohování a zachovat data zálohy** se nepodporuje. Můžete odstranit instanci zálohování, která neukončí pouze zálohování, ale také odstraní všechna data zálohy.

## <a name="next-steps"></a>Další kroky

- [Zálohování Azure Managed Disks](backup-managed-disks.md)