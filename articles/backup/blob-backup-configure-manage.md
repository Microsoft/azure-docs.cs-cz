---
title: Konfigurace operačního zálohování pro objekty blob Azure
description: Přečtěte si, jak nakonfigurovat a spravovat provozní zálohu pro objekty blob Azure (ve verzi Preview).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7fe302de0e93575e7bb62f7c5cdefd7acc0720c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744908"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Konfigurace operačního zálohování pro objekty blob Azure (ve verzi Preview)

Azure Backup vám umožní snadno nakonfigurovat provozní zálohu pro ochranu objektů blob bloku v účtech úložiště. Tento článek vysvětluje, jak nakonfigurovat provozní zálohu na jednom nebo více účtech úložiště pomocí Azure Portal. Tento článek popisuje následující:

- Věci, které byste měli znát před zahájením
- Vytvoření trezoru záloh
- Udělování oprávnění k trezoru záloh na účtech úložiště, které se mají chránit
- Vytvoření zásady zálohování
- Konfigurace provozní zálohy na jednom nebo více účtech úložiště
- Důsledky zálohování účtů úložiště

## <a name="before-you-start"></a>Než začnete

- Provozní záloha objektů BLOB je místní řešení zálohování, které uchovává data po zadanou dobu v samotném zdrojovém účtu úložiště. Toto řešení neuchovává v trezoru další kopii dat.
- Toto řešení vám umožní uchovávat data po dobu až 360 dnů. Dlouhé doby uchovávání může nicméně trvat déle, než se operace obnovení povede.
- Řešení se dá použít k obnovení jenom na zdrojový účet úložiště a může způsobit přepisování dat.
- Pokud odstraníte kontejner z účtu úložiště voláním operace odstranění kontejneru, nelze tento kontejner obnovit pomocí operace obnovení. Místo odstranění celého kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít později obnovit. Kromě toho společnost Microsoft doporučuje povolit obnovitelné odstranění kontejnerů, a to i v provozním zálohování a chránit před náhodným odstraněním kontejnerů.
- Další informace o podporovaných scénářích, omezeních a dostupnosti najdete v [matrici podpory](blob-backup-support-matrix.md) .

## <a name="create-a-backup-vault"></a>Vytvoření trezoru záloh

[Trezor záloh](backup-vault-overview.md) je entita správy, která ukládá body obnovení vytvořené v průběhu času a poskytuje rozhraní pro provádění operací souvisejících se zálohováním. Mezi ně patří zálohování na vyžádání, provádění obnovení a vytváření zásad zálohování. I když je provozní záloha objektů BLOB místní zálohou a neukládá data do trezoru, vyžaduje se pro různé operace správy trezor.

>[!NOTE]
>Trezor záloh je nový prostředek, který se používá k zálohování nových podporovaných úloh a liší se od již existujícího trezoru Recovery Services.

Pokyny k vytvoření trezoru služby Backup najdete v [dokumentaci k úložišti záloh](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Udělení oprávnění k trezoru záloh na účtech úložiště

Provozní zálohování také chrání účet úložiště (obsahující objekty blob, které se mají chránit) před náhodným odstraněním, a to použitím zámku pro odstranění, který je ve vlastnictví. K tomu je potřeba, aby měl trezor služby Backup určitá oprávnění pro účty úložiště, které je třeba chránit. Pro usnadnění použití jsou tato oprávnění konsolidovaná v roli Přispěvatel záloh účtu úložiště. Pro účty úložiště, které je potřeba chránit, postupujte podle následujících pokynů:

1. V účtu úložiště, který se má chránit, přejděte na **kartu Access Control (IAM)** v levém navigačním podokně.
1. Chcete-li přiřadit požadovanou roli, vyberte možnost **Přidat přiřazení rolí** .

    ![Přidat přiřazení rolí](./media/blob-backup-configure-manage/add-role-assignments.png)

1. V podokně Přidat přiřazení role:

    1. V části **role** vyberte **Přispěvatel zálohování účtu úložiště**.
    1. V části **přiřadit přístup k** vyberte **uživatel, skupina nebo instanční objekt**.
    1. Zadejte **název trezoru záloh** , který chcete chránit objekty BLOB v tomto účtu úložiště, a vyberte stejný typ z výsledků hledání.
    1. Po dokončení vyberte **Uložit**.

        ![Možnosti přiřazení role](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Umožněte, aby se přiřazení role projevilo až po dobu 10 minut.

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování obvykle řídí uchovávání a plány zálohování. Vzhledem k tomu, že je operační zálohování pro objekty blob nepřetržité, nepotřebujete k provádění záloh plán. Tato zásada je v podstatě nutná k určení doby uchování. Zásady zálohování můžete použít a znovu použít ke konfiguraci zálohování pro více účtů úložiště do trezoru.

Tady je postup pro vytvoření zásady zálohování pro provozní zálohu vašich objektů BLOB:

1. V trezoru služby Backup přejděte na **zásady zálohování** a vyberte **+ Přidat** a začněte vytvářet zásady zálohování.

    ![Zásady zálohování](./media/blob-backup-configure-manage/backup-policies.png)

1. Na kartě **základy** zadejte název zásad zálohování a jako typ zdroje dat vyberte **objekty blob Azure** . Můžete si také prohlédnout podrobnosti o vybraném trezoru.

    ![Vytvořit zásady zálohování](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >I když se vám v trezoru objeví **redundance úložiště** , redundance se ve skutečnosti nepoužije na provozní zálohu objektů blob, protože zálohování je místní a v úložišti záloh se neukládají žádná data. Úložiště záloh tady je entita správy, která vám umožní spravovat ochranu objektů blob bloku v účtech úložiště.

1. Na kartě **zásady zálohování** je místo, kde zadáte podrobnosti uchovávání. Uvidíte, že již existuje pravidlo uchovávání s názvem **výchozí** s dobou uchování 30 dnů. Pokud chcete upravit dobu uchovávání, upravte pomocí ikony **Upravit pravidlo uchovávání** a zadejte dobu trvání, pro kterou chcete uchovávat data. Můžete zadat dobu uchovávání až 360 dní.

    ![Karta zásady zálohování](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Obnovení po dlouhou dobu může způsobit dokončení operací obnovení trvá déle. Navíc čas potřebný k obnovení sady dat vychází z počtu operací zápisu a odstranění provedených během období obnovení. Například účet s 1 000 000 objekty s 3 000 objekty přidaných za den a 1 000 objekty odstraněné za den budou vyžadovat přibližně dvě hodiny k obnovení do bodu 30 dnů v minulosti. Doba uchování a obnovení více než 90 dní v minulosti se pro účet s touto mírou změny nedoporučuje.

1. V podokně **Revize + vytvořit** ověřte všechny podrobnosti zásady a po dokončení vytváření zásady vyberte **vytvořit** po dokončení. Až se zásady zálohování vytvoří a budou připravené k použití, oznámení se potvrdí.

    ![Kontrola a vytváření zásad](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Konfigurace zálohování

Zálohování objektů BLOB se konfiguruje na úrovni účtu úložiště. Všechny objekty BLOB v účtu úložiště jsou proto chráněny provozním zálohováním.

Spuštění konfigurace zálohování:

1. Na panelu hledání vyhledejte **Centrum zálohování** .
1. Přejděte na **Přehled**  ->  **+ zálohování**.

    ![Přehled služby Backup Center](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Na kartě **zahájit: Konfigurace zálohování** vyberte jako typ zdroje dat **Azure Blob (Azure Storage)** .

    ![Inicializace: karta konfigurace zálohování](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Na kartě **základy** zadejte jako typ **DataSource** **(Azure Storage) objekty blob Azure** a vyberte úložiště záloh, ke kterému chcete přidružit účty úložiště. Podrobnosti o vybraném trezoru můžete zobrazit v podokně.

    ![Karta základy](./media/blob-backup-configure-manage/basics-tab.png)

1. V dalším kroku vyberte zásadu zálohování, kterou chcete použít k určení uchování. Podrobnosti o vybrané zásadě můžete zobrazit v dolní části obrazovky. Ve sloupci provozní úložiště dat se zobrazuje doba uchování definovaná v zásadách. "Provozní" znamená, že data se uchovávají místně ve zdrojovém účtu úložiště.

    ![Zvolit zásady zálohování](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Můžete také vytvořit nové zásady zálohování. Pokud to chcete provést, vyberte **vytvořit novou** a postupujte podle následujících kroků:

    1. Zadejte název zásady, kterou chcete vytvořit. Ujistěte se, že se v ostatních polích zobrazuje správný typ zdroje dat a název trezoru.
    1. Na kartě **zásady zálohování** vyberte ikonu Upravit pravidlo uchovávání, kterou chcete upravit, a zadejte dobu trvání, pro kterou chcete data uchovat. Můžete zadat dobu uchovávání až 360 dní. Obnovení po dlouhou dobu může způsobit dokončení operací obnovení trvá déle.

        ![Vytvořit nové zásady zálohování](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Jakmile budete hotovi, vyberte **zkontrolovat + vytvořit** a vytvořte zásadu zálohování.

1. V dalším kroku budete muset zvolit účty úložiště, pro které chcete nakonfigurovat ochranu objektů BLOB. Můžete zvolit více účtů úložiště najednou a zvolit **možnost vybrat**.

    Ujistěte se však, že zvolený trezor má požadovaná oprávnění ke konfiguraci zálohování účtů úložiště, jak je popsáno výše v části [udělení oprávnění k úložišti záloh v účtech úložiště](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Vyberte prostředky, které chcete zálohovat.](./media/blob-backup-configure-manage/select-resources.png)

    Zálohování kontroluje, jestli má Trezor dostatečná oprávnění, aby bylo možné nakonfigurovat zálohování na vybraných účtech úložiště.

    ![Zálohování ověří oprávnění](./media/blob-backup-configure-manage/validate-permissions.png)

    Pokud dojde k chybám při ověřování (jako u jednoho z účtů úložiště na snímku obrazovky), přejdete do vybraných účtů úložiště a přiřadíte příslušné role, jak je popsáno [zde](#grant-permissions-to-the-backup-vault-on-storage-accounts), a pak vyberte znovu **ověřit**. Platnost přiřazení nové role může trvat až 10 minut.

1. Po úspěšném ověření u všech vybraných účtů úložiště pokračujte v **kontrole a konfiguraci** zálohování. Zobrazí se oznámení informující o stavu konfigurace ochrany a jeho dokončení.

## <a name="effects-on-backed-up-storage-accounts"></a>Vliv na zálohované účty úložiště

Po konfiguraci zálohování se sledují změny provedené u objektů blob bloku v účtech úložiště a data se uchovávají v souladu se zásadami zálohování. V účtech úložiště, pro které je nakonfigurované zálohování, si všimnete následujících změn:

- V účtu úložiště jsou povolené následující funkce. Ty si můžete prohlédnout na kartě **Ochrana dat** účtu úložiště.
  - Obnovení bodu v čase pro kontejnery: s uchováním uvedeným v zásadách zálohování
  - Obnovitelné odstranění pro objekty BLOB: s uchováním uvedeným v zásadách zálohování + 5 dní
  - Správa verzí pro objekty blob
  - Informační kanál změn objektu BLOB

  Pokud účet úložiště nakonfigurovaný pro zálohování už měl  **v čase obnovení pro kontejnery** nebo **obnovitelné odstranění pro povolené objekty blob** (před konfigurací zálohování), zajistí zálohování aspoň tak, jak je definované v zásadách zálohování. Proto pro každou vlastnost:

  - Pokud je uchovávání v zásadách zálohování větší než uchování původně přítomné v účtu úložiště: uchování v účtu úložiště se upraví podle zásad zálohování.
  - Pokud je uchovávání v zásadách zálohování menší než uchování původně přítomné v účtu úložiště: uchování v účtu úložiště zůstane beze změny v původně nastaveném trvání.

  ![Karta ochrana dat](./media/blob-backup-configure-manage/data-protection.png)

- **Zámek proti odstranění** se aplikuje zálohou v účtu chráněného úložiště. Zámek je určený k ochraně proti případům nechtěného odstranění účtu úložiště. Tato možnost se dá zobrazit v části  >  **zámky** účtu úložiště.

    ![Zámky odstranění](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Spravovat provozní zálohu

Pro správu všech záloh můžete použít Centrum zálohování jako jedno podokno skla. V souvislosti s provozním zálohováním pro objekty blob Azure můžete použít službu Backup Center k provedení těchto akcí:

- Jak vidíte výše, můžete ho použít k vytvoření trezorů a zásad služby Backup. V rámci vybraných předplatných můžete také zobrazit všechny trezory a zásady.
- Centrum zálohování poskytuje snadný způsob, jak monitorovat stav ochrany chráněných účtů úložiště i účtů úložiště, pro které není aktuálně nakonfigurované zálohování.
- Zálohu můžete nakonfigurovat pro všechny účty úložiště pomocí tlačítka **+ zálohování** .
- Obnovení můžete spustit pomocí tlačítka **obnovit** a sledovat obnovení pomocí **úloh zálohování**. Další informace o provádění obnovení najdete v tématu [obnovení objektů blob Azure](blob-backup-support-matrix.md).
- Analyzujte využití zálohy pomocí sestav zálohování.

    ![Centrum zálohování](./media/blob-backup-configure-manage/backup-center.png)

Další informace najdete v tématu [Přehled služby Backup Center (Preview)](backup-center-overview.md).

## <a name="next-steps"></a>Další kroky

- [Obnovení objektů blob Azure](blob-backup-support-matrix.md)
