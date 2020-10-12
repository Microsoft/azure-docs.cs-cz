---
title: Přehled trezorů služby Backup
description: Přehled trezorů služby Backup.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 773152212ed831c0083cfdf912f45ece578d079f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995566"
---
# <a name="backup-vaults-overview"></a>Přehled trezorů služby Backup

Tento článek popisuje funkce trezoru služby Backup. Trezor záloh je entita úložiště v Azure, která poskytuje zálohovaná data pro některé novější úlohy, které Azure Backup podporuje. Trezory služby Backup můžete použít k uchovávání zálohovaných dat pro různé služby Azure, jako jsou Azure Database for PostgreSQL servery a novější úlohy, které Azure Backup budou podporovat. Trezory služby Backup usnadňují uspořádání zálohovaných dat a současně minimalizují nároky na správu. Trezory služby Backup jsou založené na modelu Azure Resource Manager Azure, který poskytuje funkce, jako například:

- **Rozšířené možnosti, které vám pomůžou zabezpečit**zálohovaná data: pomocí trezorů služby Backup Azure Backup poskytuje funkce zabezpečení pro ochranu cloudových záloh. Funkce zabezpečení zajistí, že budete moci zabezpečit zálohy a bezpečně obnovit data, i když dojde k ohrožení produkčního a záložního serveru. [Další informace](backup-azure-security-feature.md)

- **Access Control na základě rolí (RBAC)**: RBAC zajišťuje v Azure jemně odstupňované řízení přístupu. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory služby Backup jsou kompatibilní s RBAC, což omezuje přístup k nástroji pro zálohování a obnovení na definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Nastavení úložiště v úložišti záloh

Trezor záloh je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Úložiště záloh obsahuje taky zásady zálohování, které jsou přidružené k chráněným virtuálním počítačům.

- Azure Backup automaticky zpracovává úložiště pro trezor. Vyberte redundanci úložiště, která odpovídá vašim potřebám vaší firmy při vytváření trezoru záloh.

- Další informace o redundanci úložiště najdete v těchto článcích o [geografické](../storage/common/storage-redundancy.md#geo-redundant-storage) a [místní](../storage/common/storage-redundancy.md#locally-redundant-storage) redundanci.

## <a name="encryption-settings-in-the-backup-vault"></a>Nastavení šifrování v úložišti záloh

Tato část popisuje možnosti, které jsou k dispozici pro šifrování zálohovaných dat uložených v úložišti záloh.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných platformou

Ve výchozím nastavení se všechna vaše data šifrují pomocí klíčů spravovaných platformou. Pro povolení tohoto šifrování není nutné provádět žádnou explicitní akci z vašeho konce. Platí pro všechny úlohy, které se zálohují do trezoru záloh.

## <a name="create-a-backup-vault"></a>Vytvoření trezoru záloh

Trezor záloh je entita správy, která ukládá body obnovení vytvořené v průběhu času a poskytuje rozhraní pro provádění operací souvisejících se zálohováním. Mezi ně patří zálohování na vyžádání, provádění obnovení a vytváření zásad zálohování.

Pokud chcete vytvořit trezor služby Backup, postupujte podle těchto kroků.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Vytvoření trezoru služby Backup

1. Do vyhledávacího pole zadejte **trezory služby Backup** .
1. V části **služby**vyberte **trezory služby Backup**.
1. Na stránce **trezory záloh** vyberte **Přidat**.
1. Na **kartě základy**v části **Project Details (podrobnosti projektu**) Zkontrolujte, zda je vybráno správné předplatné, a pak zvolte možnost **vytvořit novou** skupinu prostředků. Jako název zadejte *myResourceGroup* .

  ![Vytvořit novou skupinu prostředků](./media/backup-vault-overview/new-resource-group.png)

1. V části **Podrobnosti o instanci**zadejte *myVault* pro **název trezoru záloh** a zvolte vaši oblast, v tomto případě *východní USA* pro vaši **oblast**.
1. Teď vyberte **redundanci úložiště**. Redundanci úložiště nelze změnit po ochraně položek do trezoru.
1. Pokud používáte Azure jako primární koncový bod úložiště záloh, doporučujeme, abyste používali výchozí **geograficky redundantní** nastavení.
1. Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
1. Přečtěte si další informace o [geografické](../storage/common/storage-redundancy.md#geo-redundant-storage) a [místní](../storage/common/storage-redundancy.md#locally-redundant-storage) redundanci.

  ![Zvolit redundanci úložiště](./media/backup-vault-overview/storage-redundancy.png)

1. V dolní části stránky vyberte tlačítko revize + vytvořit.

    ![Vybrat kontrolu + vytvořit](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Odstranění trezoru služby Backup

Tato část popisuje, jak odstranit trezor služby Backup. Obsahuje pokyny pro odebrání závislostí a odstranění trezoru.

### <a name="before-you-start"></a>Než začnete

Trezor služby Backup nelze odstranit pomocí kterékoli z následujících závislostí:

- Nemůžete odstranit trezor, který obsahuje chráněné zdroje dat (například servery Azure Database for PostgreSQL).
- Trezor, který obsahuje data zálohy, nelze odstranit.

Pokud se pokusíte odstranit trezor bez odebrání závislostí, dojde k následujícím chybovým zprávám:

>Trezor služby Backup nelze odstranit, protože v trezoru existují existující instance služby Backup nebo zásady zálohování. Odstraňte všechny instance služby Backup a zásady zálohování, které se nacházejí v trezoru, a pak zkuste trezor odstranit.

### <a name="proper-way-to-delete-a-vault"></a>Správný způsob odstranění trezoru

>[!WARNING]
Následující operace je destruktivní a nelze ji vrátit zpět. Všechna zálohovaná data a zálohované položky přidružené k chráněnému serveru budou trvale odstraněny. Postupujte však opatrně.

Chcete-li odstranit trezor správně, je nutné postupovat podle kroků v tomto pořadí:

- Je nutné ověřit, zda existují chráněné položky:
  - V levém navigačním panelu přejít na **instance zálohování** . Všechny položky, které jsou zde uvedeny, musí být nejprve odstraněny.

Po dokončení těchto kroků můžete trezor i nadále odstranit.

### <a name="delete-the-backup-vault"></a>Odstranění trezoru služby Backup

Pokud v trezoru nejsou žádné další položky, vyberte **Odstranit** na řídicím panelu trezoru. Zobrazí se potvrzovací text s dotazem, jestli chcete trezor odstranit.

![Odstranit trezor](./media/backup-vault-overview/delete-vault.png)

1. Vyberte **Ano** , pokud chcete ověřit, že chcete trezor odstranit. Trezor se odstraní. Portál se vrátí do nabídky **Nová** služba.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorování a správa úložiště záloh

V této části se dozvíte, jak pomocí řídicího panelu **Přehled** úložiště záloh monitorovat a spravovat vaše trezory služby Backup. Podokno přehledu obsahuje dvě dlaždice: **úlohy** a **instance**.

![Řídicí panel přehled](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Správa instancí zálohování

V dlaždici **úlohy** získáte souhrnné zobrazení všech souvisejících úloh zálohování a obnovení v úložišti záloh. Výběrem kteréhokoli z čísel v této dlaždici můžete zobrazit další informace o úlohách pro určitý typ zdroje dat, typ operace a stav.

![Instance zálohování](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Správa úloh zálohování

Na dlaždici **zálohování instancí** získáte souhrnné zobrazení všech instancí zálohování v úložišti záloh. Výběrem kteréhokoli z čísel v této dlaždici můžete zobrazit další informace o instancích zálohy pro určitý typ zdroje dat a stav ochrany.

![Úlohy zálohování](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace zálohování pro databáze Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
