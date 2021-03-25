---
title: Nejčastější dotazy týkající se služby Azure disk Backup
description: Získejte odpovědi na nejčastější dotazy týkající se zálohování disků Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7729bc1120fc0e2f4361739a8e05f3a82ccb4268
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107308"
---
# <a name="frequently-asked-questions-about-azure-disk-backup"></a>Nejčastější dotazy týkající se služby Azure disk Backup

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure disk Backup. Další informace o dostupnosti oblasti [Azure disk Backup](disk-backup-overview.md) , podporovaných scénářích a omezeních najdete v tématu věnovaném [maticům podpory](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Můžu zálohovat disk pomocí řešení zálohování disku Azure, pokud je stejný disk zálohovaný pomocí zálohování virtuálních počítačů Azure?

Azure Backup nabízí souběžnou podporu pro zálohování spravovaného disku pomocí zálohování disku a řešení [zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md) . To je užitečné v případě, že potřebujete jednorázově zálohovat virtuální počítače konzistentní s aplikacemi a také častější zálohování disku s operačním systémem nebo konkrétního datového disku, což je konzistentní s chybou, aniž by to mělo vliv na výkon produkčních aplikací.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Návody najít skupinu prostředků snímku, kterou používám ke konfiguraci zálohování pro disk?

Na obrazovce **instance zálohování** můžete najít pole pro skupinu prostředků snímku v části **základy** . Můžete vyhledat a vybrat svou instanci zálohování odpovídajícího disku ze služby Backup Center nebo trezoru záloh.

![Pole skupiny prostředků snímku](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Co je skupina prostředků snímku?

Azure disk Backup nabízí zálohu provozní vrstvy pro spravovaný disk. To znamená, že se snímky vytvořené během plánovaných operací zálohování a na vyžádání ukládají do skupiny prostředků v rámci vašeho předplatného. Azure Backup nabízí okamžité obnovení, protože přírůstkové snímky se ukládají v rámci předplatného. Tato skupina prostředků se označuje jako skupina prostředků snímku. Další informace najdete v tématu [Konfigurace zálohování](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Proč musí být skupina prostředků snímku ve stejném předplatném jako zálohovaný disk?

Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku. Proto vyberte skupinu prostředků v rámci stejného předplatného, jako má zálohovaný disk. Přečtěte si další informace o [přírůstkovém snímku](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravované disky.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Proč potřebuji zadat přiřazení rolí, aby bylo možné konfigurovat zálohování, provádět plánované zálohování a zálohy na vyžádání a obnovovat?

Azure disk Backup využívá k zjišťování, ochraně a obnovování spravovaných disků ve vašich předplatných přístup s minimálními oprávněními. K tomu Azure Backup k přístupu k dalším prostředkům Azure používá spravovanou identitu [trezoru záloh](backup-vault-overview.md) . Spravovaná identita přiřazená systémem je omezená na jeden prostředek a je vázaná na životní cyklus tohoto prostředku. Pomocí řízení přístupu na základě role Azure (RBAC) můžete udělit oprávnění ke spravované identitě. Spravovaná identita je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Přečtěte si další informace o [spravovaných identitách](../active-directory/managed-identities-azure-resources/overview.md). Ve výchozím nastavení nemá úložiště záloh oprávnění pro přístup k disku, který se má zálohovat, vytváření periodických snímků, odstraňování snímků po dobu uchování a obnovení disku ze zálohy. Tím, že explicitně udělíte přiřazení rolí k spravované identitě trezoru záloh, budete řídit správu oprávnění k prostředkům v předplatných.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Proč zásady zálohování omezují dobu uchování?

Azure disk Backup používá přírůstkové snímky, které jsou omezeny na 200 snímků na disk. Aby bylo možné převzít zálohy na vyžádání z naplánovaných záloh, zásady zálohování omezí celkové zálohy na 180. Přečtěte si další informace o [přírůstkových snímcích](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravované disky.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Jak funguje hodinová a denní četnost záloh v zásadách zálohování?

Azure disk Backup nabízí více záloh za den. Pokud potřebujete častější zálohování, vyberte **hodinovou** četnost zálohování. Zálohy jsou plánovány na základě zvoleného **časového** intervalu. Pokud například vyberete **každé 4 hodiny**, zálohování se povede přibližně každých 4 hodiny, aby se zálohy rovnoměrně rozdělují mezi jednotlivé dny. Pokud je k dispozici dostatečně velké zálohování za den, zvolte **denní** četnost zálohování. V denní četnosti zálohování můžete zadat dobu, po kterou budou zálohy provedeny. Je důležité si uvědomit, že čas dne označuje čas zahájení zálohování a ne čas, kdy se zálohování dokončí. Čas potřebný k dokončení operace zálohování závisí na různých faktorech, včetně četnosti změn mezi po sobě jdoucími zálohami. Zálohování disku Azure je ale zálohování bez agentů, které používá [přírůstkové snímky](../virtual-machines/disks-incremental-snapshots.md) , které nemají vliv na výkon produkčních aplikací.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Proč nastavení redundance trezoru záloh neplatí pro zálohy uložené v provozní úrovni (skupina prostředků snímku)?

Azure Backup používá [přírůstkové snímky](../virtual-machines/disks-incremental-snapshots.md#restrictions) spravovaných disků, které ukládají jenom rozdílové změny disků od posledního snímku HDD úrovně Standard úložiště, bez ohledu na typ úložiště nadřazeného disku. Pro zajištění větší spolehlivosti jsou přírůstkové snímky uloženy v zóně redundantního úložiště (ZRS) ve výchozím nastavení v oblastech, které podporují ZRS. V současné době Azure disk Backup podporuje provozní zálohy spravovaných disků, které nekopírují zálohy do úložiště trezorů záloh. Proto se nastavení redundance úložiště zálohování v trezoru služby Backup nevztahuje na body obnovení.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Můžu pomocí centra zálohování nakonfigurovat zálohování a spravovat instance záloh pro disky Azure?

Ano, služba Azure disk Backup je integrovaná do [centra záloh](backup-center-overview.md), což poskytuje **jednotné jednotné prostředí pro správu** v Azure, které podnikům umožňuje řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku. Pomocí trezoru služby Backup můžete také zálohovat, obnovovat a spravovat instance záloh chráněné v rámci trezoru.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Proč potřebuji vytvořit úložiště záloh a nepoužívat Recovery Services trezor?

Trezor záloh je entita úložiště v Azure, která poskytuje zálohovaná data pro některé novější úlohy, které Azure Backup podporuje. Trezory služby Backup můžete použít k uchovávání zálohovaných dat pro různé služby Azure, jako jsou Azure Database for PostgreSQL servery, disky Azure a novější úlohy, které Azure Backup budou podporovat. Trezory služby Backup usnadňují uspořádání zálohovaných dat a současně minimalizují nároky na správu. Další informace najdete v části [trezory služby Backup](./backup-vault-overview.md) .

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Může být disk zálohovaný a trezor služby Backup v různých předplatných?

Ano, disk spravovaný na zdroj, který se má zálohovat, a úložiště záloh může být v různých předplatných.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Může být disk zálohovaný a trezor služby Backup v různých oblastech?

Ne, momentálně je zálohovaný zdrojový disk a trezor služby Backup musí být ve stejné oblasti.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Můžu obnovit disk do jiného předplatného?

Ano, disk můžete obnovit do jiného předplatného, než na kterém je zdrojový disk spravovaný, ze kterého se zálohování provádí.

### <a name="can-i-back-up-multiple-disks-together"></a>Můžu zálohovat víc disků společně?

Ne, snímky k určitému bodu v čase s více disky připojenými k virtuálnímu počítači se nepodporují. Další informace najdete v tématu [Konfigurace zálohování](backup-managed-disks.md#configure-backup) a další informace o omezeních najdete v části [support Matrix](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Jaké mám možnosti zálohování disků napříč několika předplatnými?

V současné době se pomocí Azure Portal ke konfiguraci zálohování disků omezí na maximálně 20 disků ze stejného předplatného.

### <a name="what-is-a-target-resource-group"></a>Co je cílová skupina prostředků?

Během operace obnovení můžete zvolit předplatné a skupinu prostředků, do které chcete disk obnovit. Azure Backup vytvoří nové disky z bodu obnovení ve vybrané skupině prostředků. Tento postup se označuje jako cílová skupina prostředků. Všimněte si, že spravovaná identita trezoru záloh vyžaduje přiřazení role v cílové skupině prostředků, aby bylo možné úspěšně provést operaci obnovení. Další informace najdete v [dokumentaci k obnovení](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Jaká jsou oprávnění, která Azure Backup používá během operace zálohování a obnovení?

Níže jsou uvedené akce, které se používají v roli **čtečky zálohování disku** přiřazené k zazálohovanému **disku** :

"Microsoft. COMPUTE/disky/číst"

"Microsoft. COMPUTE/disks/beginGetAccess/Action"

Níže jsou uvedené akce, které se používají v roli **Přispěvatel snímku disku** přiřazenou ke **skupině prostředků snímku**:

Microsoft. COMPUTE/snímky/odstranit

Microsoft. COMPUTE/snímky/zápis

Microsoft. COMPUTE/snímky/číst

"Microsoft. Storage/storageAccounts/Write"

"Microsoft. Storage/storageAccounts/Read"

"Microsoft. Storage/storageAccounts/Delete"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

"Microsoft. Storage/storageAccounts/klíče listkey/Action"

"Microsoft. COMPUTE/snímky/beginGetAccess/Action"

"Microsoft. COMPUTE/snímky/endGetAccess/Action"

"Microsoft. COMPUTE/disks/beginGetAccess/Action"

Níže jsou uvedené akce, které se používají v roli **operátora obnovení disku** přiřazenou v **cílové skupině prostředků**:

"Microsoft. COMPUTE/disks/Write"

"Microsoft. COMPUTE/disky/číst"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

>[!NOTE]
>Oprávnění k těmto rolím se mohou v budoucnu změnit v závislosti na funkcích, které přidávají služba Azure Backup.

## <a name="next-steps"></a>Další kroky

- [Matice podpory pro zálohování disků Azure](disk-backup-support-matrix.md)