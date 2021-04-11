---
title: Matice podpory pro zálohování disků Azure
description: Poskytuje souhrn nastavení podpory a omezení zálohování disku Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612853"
---
# <a name="azure-disk-backup-support-matrix"></a>Matice podpory pro zálohování disků Azure

K ochraně disků Azure můžete použít [Azure Backup](./backup-overview.md) . Tento článek shrnuje dostupnost oblasti, podporované scénáře a omezení.

## <a name="supported-regions"></a>Podporované oblasti

Zálohování disku Azure je dostupné v těchto oblastech: Západní USA, Západní USA 2, Středozápadní USA, Východní USA, východní USA 2, Střed USA, Střed USA – jih, Střed USA – sever, Kanada – střed, Brazílie – jih, USA – sever, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Severní Evropa, Švýcarsko – sever, Švýcarsko – západ, Německo – středozápad, Francie – střed, Norsko – východ, Spojené arabské emiráty sever, Austrálie – střed, Austrálie – střed 2 , Austrálie – východ, Jižní Korea, Korea – jih, Japonsko – východ, Japonsko – západ, Východní Asie, jihovýchodní Asie, Střed Indie. 

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

- Azure disk Backup podporuje PowerShell. Rozhraní příkazového řádku Azure se v současné době nepodporuje.

- Při konfiguraci zálohování musí být vybraný disk, který se má zálohovat, a skupinu prostředků snímku, kde mají být snímky uložené, a to v rámci stejného předplatného. Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku. Přečtěte si další informace o [přírůstkových snímcích](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravovaný disk. Další informace o tom, jak zvolit skupinu prostředků snímku, najdete v tématu  [Konfigurace zálohování](backup-managed-disks.md#configure-backup).

- Pro úspěšné operace zálohování a obnovení vyžadují přiřazení role spravovanou identitu trezoru záloh. Používejte jenom definice rolí, které jsou uvedené v dokumentaci. Použití jiných rolí, jako je vlastník, přispěvatel atd., se nepodporuje. Pokud po přiřazení rolí začnete konfigurovat operace zálohování nebo obnovení, můžete se setkat s problémy s oprávněními. Důvodem je to, že platnost přiřazení rolí trvá několik minut.

- Spravované disky umožňují měnit úroveň výkonu při nasazení nebo pak beze změny velikosti disku. Řešení zálohování disku Azure podporuje změny úrovně výkonu na zdrojovém disku, který se zálohuje. Během obnovení bude úroveň výkonu obnoveného disku stejná jako u zdrojového disku v době zálohování. Pokud chcete po operaci obnovení změnit úroveň výkonu [disku, postupujte](../virtual-machines/disks-performance-tiers-portal.md) podle pokynů v dokumentaci.

- Podpora [privátních odkazů](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) u spravovaných disků umožňuje omezit export a import spravovaných disků tak, aby se staly jenom v rámci vaší virtuální sítě Azure. Azure disk Backup podporuje zálohování disků, které mají povolené soukromé koncové body. Nezahrnuje data záloh nebo snímky, které budou přístupné prostřednictvím privátního koncového bodu.

- Můžete odstranit instanci zálohování, která ukončí zálohování, a také odstraní všechna data zálohy. V současné době nejde zálohování zakázat, protože možnost **Zastavit zálohování a zachovat data zálohy** se nepodporuje.

## <a name="next-steps"></a>Další kroky

- [Zálohování Azure Managed Disks](backup-managed-disks.md)