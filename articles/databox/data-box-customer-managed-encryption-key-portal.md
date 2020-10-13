---
title: Použití Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pro Azure Data Box
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault pro Azure Data Box. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739931"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Použití klíčů spravovaných zákazníkem v Azure Key Vault pro Azure Data Box

Azure Data Box chrání klíč pro odemčení zařízení (označovaný také jako heslo zařízení), který se používá k uzamčení zařízení prostřednictvím šifrovacího klíče. Ve výchozím nastavení je klíč pro odemčení zařízení pro Data Box pro objednávku zašifrovaný pomocí spravovaného klíče společnosti Microsoft. Pro další kontrolu nad klíčem pro odemknutí zařízení můžete také zadat klíč spravovaný zákazníkem. 

Klíče spravované zákazníkem musí být vytvořeny a uloženy v Azure Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md).

V tomto článku se dozvíte, jak používat klíče spravované zákazníky s Azure Data Box v [Azure Portal](https://portal.azure.com/). Tento článek se týká Azure Data Boxch zařízení a Azure Data Box Heavych zařízení.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Vytvořili jste pořadí Azure Data Box podle pokynů v [kurzu: Azure Data box objednávky](data-box-deploy-ordered.md).

2. Máte existující Azure Key Vault s klíčem, který můžete použít k ochraně klíče odemčení zařízení. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../key-vault/secrets/quick-create-portal.md).

    - **Obnovitelné odstranění** a **nevyprázdnit** se nastaví na stávající Trezor klíčů. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, přečtěte si část s názvem **Povolení obnovitelného odstranění** a **Povolení vyprázdnit ochranu** v jednom z následujících článků:

        - [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Jak používat obnovitelné odstranění pomocí rozhraní](../key-vault/general/soft-delete-cli.md)PŘÍKAZového řádku
    - Existující Trezor klíčů by měl mít klíč RSA o velikosti 2048 nebo více. Další informace o klíčích najdete v tématu [o Azure Key Vaultch klíčích](../key-vault/keys/about-keys.md).
    - Trezor klíčů musí být ve stejné oblasti jako účty úložiště používané pro vaše data. S vaším Azure Data Box prostředkem je možné propojit více účtů úložiště.
    - Pokud nemáte existující Trezor klíčů, můžete ho vytvořit také tak, jak je popsáno v následující části.

## <a name="enable-keys"></a>Povolit klíče

Konfigurace klíče spravovaného zákazníkem pro vaši Azure Data Box je volitelná. Ve výchozím nastavení používá Data Box k ochraně klíče BitLockeru spravovaný klíč společnosti Microsoft. Pokud chcete povolit klíč spravovaný zákazníkem v Azure Portal, postupujte podle následujících kroků:

1. Přejít na okno **Přehled** pro vaši data box objednávku.

    ![Okno s přehledem Data Boxho pořadí](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Přejít na **nastavení > šifrování**. V části **typ šifrování**můžete zvolit, jak chcete chránit klíč odemknutí zařízení. Ve výchozím nastavení se k ochraně vašeho hesla pro odemknutí zařízení používá spravovaný klíč společnosti Microsoft. 

    ![Zvolit možnost šifrování](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Jako **spravovaný klíč zákazníka**vyberte typ šifrování. Po výběru klíče spravovaného zákazníkem **Vyberte Trezor klíčů a klíč**.

    ![Vybrat klíč spravovaný zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. V okně **Vybrat klíč z Azure Key Vault** se předplatné automaticky vyplní. V případě **trezoru klíčů**můžete v rozevíracím seznamu vybrat existující Trezor klíčů.

    ![Vytvořit nový Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Můžete také vybrat **vytvořit nové** a vytvořit nový trezor klíčů. V okně **vytvořit Trezor klíčů**zadejte skupinu prostředků a název trezoru klíčů. Ujistěte se, že je povolená ochrana proti **odstranění** a **vyprázdnění** . Přijměte všechny ostatní výchozí hodnoty. Vyberte **zkontrolovat + vytvořit**.

    ![Vytvořit nový Azure Key Vault 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Zkontrolujte informace spojené s vaším trezorem klíčů a vyberte **vytvořit**. Počkejte pár minut, než se vytváření trezoru klíčů dokončí.

    ![Vytvořit Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. V poli **Vybrat klíč z Azure Key Vault**můžete vybrat klíč v existující trezoru klíčů.

    ![Vytvořit nový klíč v Azure Key Vault 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Pokud chcete vytvořit nový klíč, vyberte **vytvořit nový** a vytvořte klíč. Velikost klíče RSA může být 2048 nebo vyšší.

    ![Vytvořit nový klíč v Azure Key Vault 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Zadejte název pro svůj klíč, Přijměte ostatní výchozí hodnoty a vyberte **vytvořit**. 

    ![Vytvořit nový klíč](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Zobrazí se oznámení o vytvoření klíče v trezoru klíčů. Vyberte **verzi** a pak zvolte **Vybrat**.

    ![Nový klíč vytvořený v trezoru klíčů](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. V podokně **typ šifrování** uvidíte Trezor klíčů a klíč vybraný pro klíč spravovaný zákazníkem.

    ![Klíč a trezor klíčů pro klíč spravovaný zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Uložte klíč. 

    ![Uložit klíč spravovaný zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Adresa URL klíče se zobrazí v části **typ šifrování**.

    ![Adresa URL klíče spravovaného zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Můžete zakázat spravovaný klíč společnosti Microsoft a přejít na klíč spravovaný zákazníkem v jakékoli fázi Data Boxho pořadí. Po vytvoření klíče spravovaného zákazníkem ale nebudete moct přejít zpátky na klíč spravovaný společností Microsoft.

## <a name="troubleshoot-errors"></a>Řešení chyb

Pokud obdržíte nějaké chyby týkající se vašeho klíče spravovaného zákazníkem, použijte následující tabulku k řešení potíží.

| Kód chyby| Podrobnosti o chybě| Obnovitelné?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nepovedlo se načíst klíč, protože je zakázaný spravovaný klíč zákazníka.| Ano, povolením verze klíče.|
| SsemUserErrorEncryptionKeyExpired| Nepovedlo se načíst klíč, protože vypršela platnost klíče spravovaného zákazníkem.| Ano, povolením verze klíče.|
| SsemUserErrorKeyDetailsNotFound| Klíč nešlo načíst, protože se nepovedlo najít spravovaný klíč zákazníka.| Pokud jste odstranili Trezor klíčů, nemůžete obnovit klíč spravovaný zákazníkem.  Pokud jste tento trezor klíčů migrovali do jiného tenanta, přečtěte si téma [Změna ID tenanta trezoru klíčů po přesunu předplatného](../key-vault/general/move-subscription.md). Pokud jste odstranili Trezor klíčů:<ol><li>Ano, pokud je v době trvání ochrany vyprázdnění, postupujte podle kroků v části [obnovení trezoru klíčů](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Ne, pokud je mimo dobu trvání ochrany vyprázdnění.</li></ol><br>V opačném případě, pokud Trezor klíčů prošl při migraci tenanta, můžete ho obnovit pomocí jednoho z následujících kroků: <ol><li>Obnovte Trezor klíčů zpátky na starého tenanta.</li><li>Nastavte `Identity = None` a pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se po vytvoření nové identity odstraní a znovu vytvoří identita. Povolte `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Klíč nebylo možné načíst, protože přístup ke spravovanému klíči zákazníka je odvolán.| Ano, zjistit, zda: <ol><li>Trezor klíčů má stále instalační soubor MSI v zásadách přístupu.</li><li>Zásady přístupu poskytují oprávnění k získání, zabalení a rozbalení.</li><li>Pokud je Trezor klíčů ve virtuální síti za bránou firewall, ověřte, jestli je povolená možnost **Povolit důvěryhodné služby společnosti Microsoft** .</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Nepovedlo se načíst klíč, protože se nepovedlo najít přidružený Trezor klíčů pro spravovaný klíč zákazníka. | Pokud jste odstranili Trezor klíčů, nemůžete obnovit klíč spravovaný zákazníkem.  Pokud jste tento trezor klíčů migrovali do jiného tenanta, přečtěte si téma [Změna ID tenanta trezoru klíčů po přesunu předplatného](../key-vault/general/move-subscription.md). Pokud jste odstranili Trezor klíčů:<ol><li>Ano, pokud je v době trvání ochrany vyprázdnění, postupujte podle kroků v části [obnovení trezoru klíčů](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Ne, pokud je mimo dobu trvání ochrany vyprázdnění.</li></ol><br>V opačném případě, pokud Trezor klíčů prošl při migraci tenanta, můžete ho obnovit pomocí jednoho z následujících kroků: <ol><li>Obnovte Trezor klíčů zpátky na starého tenanta.</li><li>Nastavte `Identity = None` a pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se po vytvoření nové identity odstraní a znovu vytvoří identita. Povolte `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Klíč nešlo načíst, protože se nepovedlo najít spravovaný klíč zákazníka.| Ano, zjistit, zda: <ol><li>Trezor klíčů má stále instalační soubor MSI v zásadách přístupu.</li><li>Identita je typu přiřazeno systémem.</li><li>Povolte oprávnění získat, zabalit a zrušit zalomení identity v zásadách přístupu trezoru klíčů.</li></ol>|
| Obecná chyba  | Nepovedlo se načíst klíč.| Toto je obecná chyba. Chcete-li vyřešit chybu a určit další kroky, obraťte se na podpora Microsoftu.|


## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault](../key-vault/general/overview.md)?
