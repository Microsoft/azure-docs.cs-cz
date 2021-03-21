---
title: Zálohování Azure Managed Disks
description: Naučte se, jak zálohovat Azure Managed Disks z Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: ca86550c4dec4b51c60d9ecdef124e38783a3764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738148"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Zálohování Azure Managed Disks (ve verzi Preview)

>[!IMPORTANT]
>Služba Azure disk Backup je ve verzi Preview bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Informace o dostupnosti oblastí najdete v tématu [matice podpory](disk-backup-support-matrix.md).
>
>[Vyplňte tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) a zaregistrujte se do verze Preview.

Tento článek vysvětluje, jak zálohovat službu [Azure Managed disk](../virtual-machines/managed-disks-overview.md) z Azure Portal.

V tomto článku se naučíte:

- Vytvoření trezoru záloh

- Vytvoření zásad zálohování

- Konfigurace zálohy disku Azure

- Spuštění úlohy zálohování na vyžádání

Informace o dostupnosti oblasti Azure disk Backup, podporovaných scénářích a omezeních najdete v tématu věnovaném [maticům podpory](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Vytvoření trezoru záloh

Trezor záloh je entita úložiště v Azure, která uchovává data pro různé novější úlohy, které Azure Backup podporuje, například servery Azure Database for PostgreSQL a disky Azure. Trezory služby Backup usnadňují uspořádání zálohovaných dat a současně minimalizují nároky na správu. Trezory služby Backup jsou založené na modelu Azure Resource Manager Azure, který poskytuje vylepšené možnosti, které usnadňují zabezpečení zálohovaných dat.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
1. Do vyhledávacího pole zadejte **Centrum zálohování** .
1. V části **služby** vyberte **Centrum zálohování**.
1. Na stránce **zálohovat centrum** vyberte **trezor**.

   ![Vybrat trezor v centru zálohování](./media/backup-managed-disks/backup-center.png)

1. Na obrazovce **zahájit: vytvořit trezor** vyberte **úložiště záloh** a **pokračujte**.

   ![Iniciace: vytvořit trezor](./media/backup-managed-disks/initiate-create-vault.png)

1. Na kartě **základy** zadejte předplatné, skupinu prostředků, název trezoru záloh, název oblasti a redundanci záložního úložiště. Pokračujte výběrem možnosti **zkontrolovat + vytvořit**. Přečtěte si další informace o [Vytvoření trezoru záloh](./backup-vault-overview.md#create-a-backup-vault).

   ![Kontrola a vytvoření trezoru](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Vytvořit zásady zálohování

1. V **úložišti záloh** *DemoVault* , který jste vytvořili v předchozím kroku, přejdete na **zásady zálohování** a vyberte **Přidat**.

   ![Přidat zásady zálohování](./media/backup-managed-disks/backup-policies.png)

1. Na kartě **základy** zadejte název zásady, jako **disk Azure** vyberte **typ DataSource** . Trezor je již předem vyplněný a jsou uvedeny vlastnosti vybraného trezoru.

   >[!NOTE]
   > I když vybraný trezor může mít nastavení globální redundance, služba Azure disk Backup aktuálně podporuje pouze úložiště snímků. Všechny zálohy se ukládají do skupiny prostředků ve vašem předplatném a nekopírují se do úložiště trezoru služby Backup.

   ![Vybrat typ zdroje dat](./media/backup-managed-disks/datasource-type.png)

1. Na kartě **zásady zálohování** vyberte četnost plánu zálohování.

   ![Vybrat četnost plánu zálohování](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure disk Backup nabízí více záloh za den. Pokud potřebujete častější zálohování, vyberte **hodinovou** četnost zálohování s možností provádět zálohování s intervaly každých 4, 6, 8 nebo 12 hodin. Zálohy jsou plánovány na základě zvoleného **časového** intervalu. Pokud například vyberete **každou 4 hodiny**, zálohování se povede přibližně v intervalu každých 4 hodin, aby se zálohy rovnoměrně rozdělují mezi jednotlivé dny. Pokud je zálohování jednou denně dostatečné, zvolte **denní** četnost zálohování. V denní četnosti zálohování můžete zadat čas, kdy se mají zálohy vymezit. Je důležité si uvědomit, že čas dne označuje čas zahájení zálohování a ne čas, kdy se zálohování dokončí. Čas potřebný k dokončení operace zálohování závisí na různých faktorech, včetně velikosti disku, a rychlosti přenosů mezi po sobě jdoucími zálohami. Zálohování disku Azure je ale zálohování bez agentů, které používá [přírůstkové snímky](../virtual-machines/disks-incremental-snapshots.md), což nemá vliv na výkon produkčních aplikací.

1. Na kartě **zásady zálohování** vyberte nastavení uchovávání, která splňují požadavky bodu obnovení (RPO).

   Výchozí pravidlo uchovávání se použije, pokud není zadané žádné jiné pravidlo uchovávání. Výchozí pravidlo uchovávání lze změnit, aby se změnila doba uchovávání, ale nelze ji odstranit. Nové pravidlo uchovávání můžete přidat tak, že vyberete **Přidat pravidlo uchovávání**.

   ![Přidat pravidlo uchovávání informací](./media/backup-managed-disks/add-retention-rule.png)

   Můžete vybrat **první úspěšnou zálohu** pořízenou každý den nebo každý týden a zadat dobu uchování, kterou budou konkrétní zálohy uchovány, než se odstraní. Tato možnost je užitečná pro uchování určitých záloh dne nebo týdne po delší dobu. Všechny ostatní časté zálohy je možné uchovat po kratší dobu.

   ![Nastavení uchovávání](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup pro Managed Disks používá přírůstkové snímky, které jsou omezeny na 200 snímků na disk. Aby bylo možné převzít zálohy na vyžádání z naplánovaných záloh, zásady zálohování omezí celkové zálohy na 180. Přečtěte si další informace o [přírůstkových snímcích](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravovaný disk.

1. Dokončete vytvoření zásady zálohování výběrem možnosti **zkontrolovat + vytvořit**.

## <a name="configure-backup"></a>Konfigurace zálohování

Úložiště záloh používá spravovanou identitu pro přístup k dalším prostředkům Azure. Pokud chcete nakonfigurovat zálohování spravovaných disků, spravovaná identita trezoru záloh vyžaduje sadu oprávnění na zdrojových discích a skupinách prostředků, ve kterých se vytvářejí a spravují snímky.

Spravovaná identita přiřazená systémem je omezená na jeden prostředek a je vázaná na životní cyklus tohoto prostředku. Pomocí řízení přístupu na základě role Azure (RBAC) můžete udělit oprávnění ke spravované identitě. Spravovaná identita je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Přečtěte si další informace o [spravovaných identitách](../active-directory/managed-identities-azure-resources/overview.md).

Pro konfiguraci zálohování spravovaných disků se vyžadují tyto požadavky:

1. Na zdrojovém disku, který se má zálohovat, přiřaďte roli **čtečky zálohování disku** k spravované identitě trezoru záloh.

   1. Přejít na disk, který je třeba zálohovat.

   1. Přejděte na **řízení přístupu (IAM)** a vyberte **Přidat přiřazení rolí** .

   1. V pravém kontextovém podokně vyberte v rozevíracím seznamu **role** možnost **čtecí modul zálohování disku** . Vyberte spravovanou identitu trezoru záloh a **uložte** ji.

   >[!TIP]
   >Zadejte název trezoru záloh a vyberte spravovanou identitu trezoru.

   ![Přidat roli čtečky zálohování disku](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Přiřaďte roli **Přispěvatel snímků disku** ke spravované identitě trezoru záloh ve skupině prostředků, ve které se vytváří a spravují zálohy službou Azure Backup. Snímky disků se ukládají do skupiny prostředků v rámci vašeho předplatného. Aby služba Azure Backup mohla vytvářet, ukládat a spravovat snímky, musíte poskytnout oprávnění k trezoru záloh.

   **Výběr skupiny prostředků pro ukládání a správu snímků:**

   - Nevybírejte stejnou skupinu prostředků jako zdrojový disk.

   - Jako vodítko doporučujeme vytvořit vyhrazenou skupinu prostředků jako úložiště dat snímku, které bude používat služba Azure Backup. Pokud máte vyhrazenou skupinu prostředků, můžete omezit přístupová oprávnění ke skupině prostředků, což poskytuje zabezpečení a snadné správy zálohovaných dat.

   - Tuto skupinu prostředků můžete použít k ukládání snímků na více disků, které jsou (nebo jsou plánovány) zálohování.  

   - Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku. Proto vyberte skupinu prostředků v rámci stejného předplatného, jako má zálohovaný disk. Přečtěte si další informace o [přírůstkovém snímku](../virtual-machines/disks-incremental-snapshots.md#restrictions) pro spravované disky.

   K přiřazení role použijte následující postup:

   1. Přejít do skupiny prostředků. Skupina prostředků je například *SnapshotRG*, která je ve stejném předplatném jako disk, který se má zálohovat.

   1. Přejděte na **řízení přístupu (IAM)** a vyberte **Přidat přiřazení rolí**.

   1. V pravém kontextovém podokně vyberte v rozevíracím seznamu **role** možnost **Přispěvatel snímku disku** . Vyberte spravovanou identitu trezoru záloh a **uložte** ji.

   >[!TIP]
   >Zadejte název trezoru záloh a vyberte spravovanou identitu trezoru.

   ![Přidat roli Přispěvatel snímku disku](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Ověřte, jestli má spravovaná identita trezoru záloh správnou sadu přiřazení rolí na zdrojovém disku a skupině prostředků, která slouží jako úložiště dat snímku.

   1. Přejít do **trezoru záloh – > identity** a vybrat **přiřazení rolí Azure**.

      ![Vybrat přiřazení rolí Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Ověřte, že se správně odrážejí role, název prostředku a typ prostředku.

      ![Ověření role, názvu prostředku a typu prostředku](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >I když se přiřazení rolí na portálu správně odrážejí, může trvat přibližně 15 minut, než se oprávnění použije ve spravované identitě trezoru záloh.

1. Po splnění požadovaných požadavků přejdete do **trezoru služby Backup – > přehled** a výběrem možnosti **zálohování** spustíte konfiguraci zálohování disků.

   ![Vybrat zálohu](./media/backup-managed-disks/select-backup.png)

1. Na kartě **základy** vyberte jako typ zdroje dat možnost **disk Azure** .

   ![Vybrat disk Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup používá [přírůstkové snímky](../virtual-machines/disks-incremental-snapshots.md#restrictions) spravovaných disků, které ukládají jenom rozdílové změny disku od posledního snímku HDD úrovně Standard úložiště, bez ohledu na typ úložiště nadřazeného disku. Pro zvýšení spolehlivosti jsou přírůstkové snímky uloženy v zóně redundantního úložiště (ZRS) ve výchozím nastavení v oblastech, které podporují ZRS. V současné době Azure disk Backup podporuje provozní zálohování spravovaných disků, které nekopírují zálohy do úložiště trezorů záloh. Proto se nastavení redundance úložiště zálohování v trezoru služby Backup nevztahuje na body obnovení.

1. Na kartě **zásady zálohování** vyberte zásadu zálohování.

   ![Zvolit zásady zálohování](./media/backup-managed-disks/choose-backup-policy.png)

1. Na kartě **prostředky** vyberte **vybrat disk Azure**. V pravém kontextovém podokně vyberte disky, které chcete zálohovat.

   ![Vybrat disky k zálohování](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >I když portál umožňuje vybrat více disků a konfigurovat zálohování, každý disk je individuální instance zálohování. V současné době Azure disk Backup podporuje jenom zálohování jednotlivých disků. Zálohování několika disků připojených k virtuálnímu disku v čase se nepodporuje.
   >
   >Když používáte portál, budete omezeni na výběr disků v rámci stejného předplatného. Pokud máte několik disků, které se mají zálohovat, nebo pokud jsou disky rozdělené do jiného předplatného, můžete k automatizaci použít skripty.
   >
   >Další informace o dostupnosti oblasti Azure disk Backup, podporovaných scénářích a omezeních najdete v tématu věnovaném [maticům podpory](disk-backup-support-matrix.md).

1. Vyberte **skupinu prostředků snímku** a vyberte **ověřit**. Jedná se o skupinu prostředků, ve které služba Azure Backup vytvořila a spravuje přírůstkové snímky úložiště záloh. Spravované identitě je přiřazena požadovaná oprávnění role v rámci požadavků.

   ![Vybrat skupinu prostředků snímku](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Dokončení ověření může trvat několik minut, než budete moct dokončit konfiguraci pracovního postupu zálohování. Ověření může selhat, pokud:
   >
   > - disk není podporován. Nepodporované scénáře najdete v [matrici podpory](disk-backup-support-matrix.md) .
   > - spravovaná identita úložiště záloh nemá platná přiřazení rolí na **disku** , který se má zálohovat, nebo ve **skupině prostředků snímku** , kde jsou uložené přírůstkové snímky.

1. Po úspěšném ověření vyberte **zkontrolovat a nakonfigurovat** a nakonfigurujte zálohování vybraných disků.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

1. V **úložišti záloh** *DemoVault* vytvořeném v předchozím kroku přejdete na **instance záloh** a vyberete instanci zálohování.

   ![Vybrat instanci zálohování](./media/backup-managed-disks/select-backup-instance.png)

1. Na obrazovce **instance zálohování** najdete tyto informace:

   - **základní** informace, včetně názvu zdrojového disku, snímku skupiny prostředků, kde jsou uložené přírůstkové snímky, Trezor záloh a zásady zálohování.
   - **Stav úlohy** znázorňující souhrn operací zálohování a obnovení a jejich stav za posledních sedm dní.
   - Seznam **bodů obnovení** pro vybrané časové období.

1. Vyberte **zálohování** pro zahájení zálohování na vyžádání.

   ![Vyberte zálohovat hned](./media/backup-managed-disks/backup-now.png)

1. Vyberte jedno z pravidel uchování přidružených k zásadám zálohování. Toto pravidlo uchovávání informací Určuje dobu uchování této zálohy na vyžádání. Pokud chcete spustit zálohování, vyberte **Zálohovat nyní** .

   ![Spustit zálohování](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Sledování operace zálohování

Služba Azure Backup vytvoří úlohu pro naplánovaná zálohování nebo v případě, že jste pro sledování aktivovali operaci zálohování na vyžádání. Zobrazení stavu úlohy zálohování:

1. Přejít na obrazovku **instance zálohování** Zobrazuje řídicí panel úlohy s operací a stav za posledních 7 dnů.

   ![Řídicí panel úloh](./media/backup-managed-disks/jobs-dashboard.png)

1. Chcete-li zobrazit stav operace zálohování, vyberte **Zobrazit vše** , pokud chcete zobrazit probíhající a minulé úlohy této instance zálohování.

   ![Vybrat Zobrazit vše](./media/backup-managed-disks/view-all.png)

1. Projděte si seznam úloh zálohování a obnovení a jejich stav. Vyberte úlohu ze seznamu úloh pro zobrazení podrobností o úloze.

   ![Výběr úlohy pro zobrazení podrobností](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Další kroky

- [Obnovit Managed Disks Azure](restore-managed-disks.md)