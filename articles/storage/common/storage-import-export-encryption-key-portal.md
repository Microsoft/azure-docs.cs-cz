---
title: Použití Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pro službu import/export
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault pro službu import/export Azure. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4362b579b7f01570a2b5fd072bf53ad495797cd8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783772"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Použití klíčů spravovaných zákazníkem v Azure Key Vault pro službu import/export

Import/export v Azure chrání klíče BitLockeru používané k uzamčení jednotek prostřednictvím šifrovacího klíče. Klíče BitLockeru se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat i klíče spravované zákazníkem.

Klíče spravované zákazníkem musí být vytvořeny a uloženy v Azure Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md)

V tomto článku se dozvíte, jak používat klíče spravované zákazníky se službou Import/export v [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Vytvořili jste úlohu importu nebo exportu podle pokynů v tématu:

    - [Vytvořte úlohu importu pro objekty blob](storage-import-export-data-to-blobs.md).
    - [Vytvoří úlohu importu pro soubory](storage-import-export-data-to-files.md).
    - [Vytvoření úlohy exportu pro objekty blob](storage-import-export-data-from-blobs.md)

2. Máte existující Azure Key Vault s klíčem, který můžete použít k ochraně klíče nástroje BitLocker. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/secrets/quick-create-portal.md).

    - **Obnovitelné odstranění** a **nevyprázdnit** se nastaví na stávající Key Vault. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, přečtěte si část s názvem **Povolení obnovitelného odstranění** a **Povolení vyprázdnit ochranu** v jednom z následujících článků:

        - [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](../../key-vault/general/key-vault-recovery.md).
        - [Jak používat obnovitelné odstranění pomocí rozhraní](../../key-vault/general/key-vault-recovery.md)PŘÍKAZového řádku
    - Existující Trezor klíčů by měl mít klíč RSA o velikosti 2048 nebo více. Další informace o klíčích najdete v tématu [o klíčích](../../key-vault/keys/about-keys.md).
    - Trezor klíčů musí být ve stejné oblasti jako účet úložiště pro vaše data.  
    - Pokud nemáte existující Azure Key Vault, můžete ho také vytvořit, jak je popsáno v následující části.

## <a name="enable-keys"></a>Povolit klíče

Konfigurace klíče spravovaného zákazníkem pro službu import/export je volitelná. Ve výchozím nastavení používá služba import/export k ochraně klíče nástroje BitLocker spravovaný klíč společnosti Microsoft. Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejít na okno **Přehled** pro vaši úlohu importu.
2. V pravém podokně vyberte možnost **zvolit způsob šifrování klíčů BitLockeru** .

    ![Zvolit možnost šifrování](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. V okně **šifrování** můžete zobrazit a zkopírovat klíč BitLockeru zařízení. V části **typ šifrování** můžete zvolit, jakým způsobem chcete chránit klíč nástroje BitLocker. Ve výchozím nastavení se používá spravovaný klíč společnosti Microsoft.

    ![Zobrazit klíč BitLockeru](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Máte možnost zadat spravovaný klíč zákazníka. Po výběru klíče spravovaného zákazníkem **Vyberte Trezor klíčů a klíč** .

    ![Vybrat spravovaný klíč zákazníka](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. V okně **Vybrat klíč z Azure Key Vault** se předplatné automaticky vyplní. V případě **trezoru klíčů** můžete v rozevíracím seznamu vybrat existující Trezor klíčů.

    ![Vyberte nebo vytvořte Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Můžete také vybrat **vytvořit nové** a vytvořit nový trezor klíčů. V okně **vytvořit Trezor klíčů** zadejte skupinu prostředků a název trezoru klíčů. Přijměte všechny ostatní výchozí hodnoty. Vyberte **zkontrolovat + vytvořit** .

    ![Vytvořit nový Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Zkontrolujte informace spojené s vaším trezorem klíčů a vyberte **vytvořit** . Počkejte pár minut, než se vytváření trezoru klíčů dokončí.

    ![Vytvořit Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. V poli **Vybrat klíč z Azure Key Vault** můžete vybrat klíč v existující trezoru klíčů.

9. Pokud jste vytvořili nový trezor klíčů, vyberte **vytvořit nový** a vytvořte klíč. Velikost klíče RSA může být 2048 nebo vyšší.

    ![Vytvořit nový klíč v Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Pokud při vytváření trezoru klíčů nejsou zapnutá ochrana před odstraněním a vyprázdněním klíče, Trezor klíčů se aktualizuje, aby byla zapnutá ochrana proti odstranění a vyprázdnění.

10. Zadejte název pro svůj klíč, Přijměte ostatní výchozí hodnoty a vyberte **vytvořit** .

    ![Vytvořit nový klíč](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Vyberte **verzi** a pak zvolte **Vybrat** . Zobrazí se oznámení o vytvoření klíče v trezoru klíčů.

    ![Nový klíč vytvořený v trezoru klíčů](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

V okně **šifrování** uvidíte Trezor klíčů a klíč vybraný pro váš zákaznický klíč.

> [!IMPORTANT]
> Ve všech fázích úlohy import/export můžete zakázat jenom spravované klíče Microsoftu a přejít na spravované klíče zákazníka. Po vytvoření se ale spravovaný klíč zákazníka nedá zakázat.

## <a name="troubleshoot-customer-managed-key-errors"></a>Řešení chyb zákaznických klíčů

Pokud obdržíte nějaké chyby týkající se vašeho spravovaného klíče zákazníka, použijte následující tabulku k řešení potíží:

| Kód chyby     |Podrobnosti     | Obnovitelné?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Přístup ke spravovanému klíči zákazníka je odvolán.                                                       | Ano, zjistit, zda: <ol><li>Trezor klíčů má stále instalační soubor MSI v zásadách přístupu.</li><li>Zásady přístupu mají povolené oprávnění získat, zalamovat a zrušit zalomení.</li><li>Pokud je Trezor klíčů ve virtuální síti za bránou firewall, ověřte, jestli je povolená možnost **Povolit důvěryhodné služby společnosti Microsoft** .</li><li>Ověřte, zda byl soubor MSI prostředku úlohy obnoven na `None` používání rozhraní API.<br>Pokud ano, pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se znovu vytvoří identita prostředku úlohy.<br>Po vytvoření nové identity, povolení `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Spravovaný klíč zákazníka je zakázán.                                         | Ano, povolením verze klíče     |
| CmkErrorKeyNotFound      | Nepovedlo se najít spravovaný klíč zákazníka. | Ano, pokud byl klíč odstraněn, ale je stále v rámci doby trvání vyprázdnění, pomocí [odebrání klíče v trezoru klíčů](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Ostatních <ol><li>Ano, pokud má zákazník klíč zálohovaný a obnoví ho.</li><li>Ne, jinak.</li></ol>
| CmkErrorVaultNotFound |Nejde najít Trezor klíčů spravovaného klíče zákazníka. |   Pokud byl Trezor klíčů odstraněn:<ol><li>Ano, pokud je v době trvání ochrany vyprázdnění, postupujte podle kroků v části [obnovení trezoru klíčů](../../key-vault/general/soft-delete-overview.md#key-vault-recovery).</li><li>Ne, pokud je mimo dobu trvání ochrany vyprázdnění.</li></ol><br>V opačném případě, pokud byl Trezor klíčů migrován do jiného tenanta, je možné ho obnovit pomocí jednoho z následujících kroků:<ol><li>Obnovte Trezor klíčů zpátky na starého tenanta.</li><li>Nastavte `Identity = None` a pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se po vytvoření nové identity odstraní a znovu vytvoří identita. Povolte `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů.</li></ol>|

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault](../../key-vault/general/overview.md)?