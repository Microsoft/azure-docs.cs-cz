---
title: Obnovit Managed Disks Azure
description: Přečtěte si, jak obnovit Azure Managed Disks z Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 995217cd17d1e2a16cd7a5f963ee88aa7116d4a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703745"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Obnovit Azure Managed Disks (ve verzi Preview)

>[!IMPORTANT]
>Služba Azure disk Backup je ve verzi Preview bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Informace o dostupnosti oblastí najdete v tématu [matice podpory](disk-backup-support-matrix.md).
>
>[Vyplňte tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) a zaregistrujte se do verze Preview.

Tento článek vysvětluje, jak obnovit [Azure Managed disks](../virtual-machines/managed-disks-overview.md) z bodu obnovení vytvořeného pomocí Azure Backup.

V současné době se možnost obnovení Original-Location (OLR) obnovuje nahrazením stávajícího zdrojového disku, ze kterého se zálohy provedly, ale nepodporují. Můžete obnovit z bodu obnovení a vytvořit nový disk ve stejné skupině prostředků jako zdrojový disk, ze kterého byly zálohy pořízeny nebo v jakékoli jiné skupině prostředků. Tento postup se označuje Alternate-Location jako ALR Recovery () a pomáhá udržet zdrojový disk i obnovený (nový) disk.

V tomto článku se naučíte:

- Obnovit pro vytvoření nového disku

- Sledovat stav operace obnovení

## <a name="restore-to-create-a-new-disk"></a>Obnovit pro vytvoření nového disku

Úložiště záloh používá spravovanou identitu pro přístup k dalším prostředkům Azure. Pro obnovení ze zálohy vyžaduje spravovaná identita trezoru záloh sadu oprávnění pro skupinu prostředků, do které se má disk obnovit.

Úložiště záloh používá spravovanou identitu přiřazenou systémem, která je omezená na jeden prostředek a je vázaná na životní cyklus tohoto prostředku. Pomocí řízení přístupu na základě role Azure (RBAC) můžete udělit oprávnění ke spravované identitě. Spravovaná identita je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Přečtěte si další informace o [spravovaných identitách](../active-directory/managed-identities-azure-resources/overview.md).

K provedení operace obnovení se vyžadují následující požadavky:

1. Přiřaďte roli **operátora obnovení disku** ke spravované identitě trezoru záloh ve skupině prostředků, do které bude služba Azure Backup obnovovat disk.

    >[!NOTE]
    > Můžete zvolit stejnou skupinu prostředků, jako má zdrojový disk, ze kterého se mají zálohy považovat, nebo do jakékoli jiné skupiny prostředků v rámci stejného nebo jiného předplatného.

    1. Přejít do skupiny prostředků, do které se má disk obnovit. Skupina prostředků je například *TargetRG*.

    1. Přejděte na **řízení přístupu (IAM)** a vyberte **Přidat přiřazení rolí** .

    1. V pravém kontextovém podokně v rozevíracím seznamu **role** vyberte **operátor obnovení disku** . Vyberte spravovanou identitu trezoru záloh a **uložte** ji.

        >[!TIP]
        >Zadejte název trezoru záloh a vyberte spravovanou identitu trezoru.

        ![Vybrat roli operátora obnovení disku](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Ověřte, jestli má spravovaná identita úložiště záloh správnou sadu přiřazení rolí u skupiny prostředků, do které se disk obnoví.

    1. Přejít do **trezoru záloh – > identity** a vybrat **přiřazení rolí Azure**

        ![Vybrat přiřazení rolí Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Ověřte, jestli se role, název prostředku a typ prostředku zobrazují správně.

        ![Ověřit roli, název prostředku a typ prostředku](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >I když se přiřazení rolí na portálu správně odrážejí, může trvat přibližně 15 minut, než se oprávnění použije ve spravované identitě trezoru záloh.
    >
    >Během naplánovaných záloh nebo operací zálohování na vyžádání Azure Backup ukládá přírůstkové snímky disku ve skupině prostředků snímku, která je k dispozici při konfiguraci zálohování disku. Azure Backup používá tyto přírůstkové snímky během operace obnovení. Pokud jsou snímky smazány nebo přesunuty ze skupiny prostředků snímku nebo pokud je u skupiny prostředků snímku zrušeno přiřazení role úložiště záloh, operace obnovení selže.

1. Pokud je disk, který se má obnovit, zašifrovaný pomocí [klíčů spravovaných zákazníkem (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) nebo pomocí klíčů spravovaných [platformou a klíčů spravovaných zákazníkem](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md), přiřaďte mu oprávnění role **Čtenář** k spravované identitě úložiště záloh na prostředku **sady šifrování disku** .

Po splnění požadavků proveďte operaci obnovení pomocí těchto kroků.

1. V [Azure Portal](https://portal.azure.com/)přejít do **centra zálohování**. V části **Správa** vyberte **instance zálohování** . V seznamu instancí zálohování vyberte instanci zálohy disku, pro kterou chcete operaci obnovení provést.

    ![Seznam instancí zálohování](./media/restore-managed-disks/backup-instances.png)

    Alternativně můžete tuto operaci provést z trezoru záloh, který jste použili ke konfiguraci zálohování disku.

1. Na obrazovce **instance zálohování** vyberte bod obnovení, který chcete použít k provedení operace obnovení, a vyberte **obnovit**.

    ![Vybrat bod obnovení](./media/restore-managed-disks/select-restore-point.png)

1. V pracovním postupu **obnovení** zkontrolujte **základy** a vyberte informace na kartě **bod obnovení** a vyberte **Další: obnovit parametry**.

    ![Projděte si základy a vyberte informace o bodu obnovení.](./media/restore-managed-disks/review-information.png)

1. Na kartě **obnovit parametry** vyberte **cílové předplatné** a **cílovou skupinu prostředků** , do které chcete zálohu obnovit. Zadejte název disku, který se má obnovit. Vyberte **Další: revize a obnovení**.

    ![Obnovit parametry](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Disky, které se zálohují Azure Backup pomocí řešení zálohování na disku, se dají zálohovat taky Azure Backup pomocí řešení zálohování virtuálních počítačů Azure s Recovery Servicesm trezoru. Pokud jste nakonfigurovali ochranu virtuálního počítače Azure, ke kterému je tento disk připojený, můžete taky použít operaci obnovení virtuálního počítače Azure. Můžete se rozhodnout obnovit virtuální počítač nebo disky a soubory nebo složky z bodu obnovení odpovídající instance zálohování virtuálních počítačů Azure. Další informace najdete v tématu [zálohování virtuálních počítačů Azure](./about-azure-vm-restore.md).

1. Po úspěšném ověření vyberte **obnovit** a spusťte operaci obnovení.

    ![Zahájit operaci obnovení](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Než budete moct aktivovat operaci obnovení, může ověření trvat několik minut. Ověření může selhat, pokud:
    >
    > - disk se stejným názvem, který je zadaný v **názvu obnoveného disku** , už v **cílové skupině prostředků** existuje.
    > - spravovaná identita trezoru záloh nemá platná přiřazení role v **cílové skupině prostředků** .
    > - přiřazení rolí spravované identity úložiště záloh se odvolají ve **skupině prostředků snímku** , kde jsou uložené přírůstkové snímky.
    > - Pokud jsou přírůstkové snímky smazány nebo přesunuty ze skupiny prostředků snímku

Při obnovení se vytvoří nový disk z vybraného bodu obnovení v cílové skupině prostředků, který byl poskytnut během operace obnovení. Chcete-li použít obnovený disk ve stávajícím virtuálním počítači, je nutné provést další kroky:

- Pokud je obnovený disk datovým diskem, můžete k virtuálnímu počítači připojit stávající disk. Pokud je obnovený disk s operačním systémem, můžete disk s operačním systémem na virtuálním počítači prohodit z Azure Portal v nabídce podokno **virtuálního počítače** – > **disky** v části **Nastavení** .

    ![Prohození disků s operačním systémem](./media/restore-managed-disks/swap-os-disks.png)

- V případě virtuálních počítačů s Windows je obnovený disk s datovým diskem, podle pokynů [odpojte původní datový disk](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) od virtuálního počítače. Pak [Připojte obnovený disk](../virtual-machines/windows/attach-managed-disk-portal.md) k virtuálnímu počítači. Podle pokynů [Proměňte disk s operačním systémem](../virtual-machines/windows/os-disk-swap.md) virtuálního počítače pomocí obnoveného disku.

- V případě virtuálních počítačů se systémem Linux, pokud je obnovený disk datový disk, postupujte podle pokynů k [odpojte původní datový disk](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) od virtuálního počítače. Pak [Připojte obnovený disk](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) k virtuálnímu počítači. Podle pokynů [Proměňte disk s operačním systémem](../virtual-machines/linux/os-disk-swap.md) virtuálního počítače pomocí obnoveného disku.

Po úspěšném dokončení operace obnovení se doporučuje odvolat přiřazení role **operátora obnovení disku** ze spravované identity trezoru záloh v **cílové skupině prostředků** .

## <a name="track-a-restore-operation"></a>Sledování operace obnovení

Po aktivaci operace obnovení vytvoří služba Backup úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Postup zobrazení průběhu úlohy obnovení:

1. Přejít na obrazovku **instance zálohování** Zobrazuje řídicí panel úlohy s operací a stav za posledních 7 dnů.

    ![Řídicí panel úloh](./media/restore-managed-disks/jobs-dashboard.png)

1. Chcete-li zobrazit stav operace obnovení, vyberte **Zobrazit vše** , pokud chcete zobrazit průběžné a minulé úlohy této instance zálohování.

    ![Vybrat Zobrazit vše](./media/restore-managed-disks/view-all.png)

1. Projděte si seznam úloh zálohování a obnovení a jejich stav. Vyberte úlohu ze seznamu úloh pro zobrazení podrobností o úloze.

    ![Seznam úloh](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy k Azure disk Backup](disk-backup-faq.md)