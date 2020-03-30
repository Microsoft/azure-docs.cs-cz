---
title: Konfigurace klíčů spravovaných zákazníkem pro službu Import/Export pomocí portálu Azure Portal
description: Zjistěte, jak pomocí portálu Azure nakonfigurovat klíče spravované zákazníky pomocí služby Azure Key Vault for Azure Import a Export. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat ovládací prvky přístupu.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ca1327a547e8550e47ff37e4ba100fcbd2b7a79f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282456"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Použití klíčů spravovaných zákazníkem ve službě Azure Key Vault for Import/Export

Azure Import/Export chrání klíče nástroje BitLocker používané k uzamčení jednotek pomocí šifrovacího klíče. Ve výchozím nastavení jsou klíče nástroje BitLocker šifrovány pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacíklíče, můžete také poskytnout klientspravované klíče.

Klíče spravované zákazníkem musí být vytvořeny a uloženy v trezoru klíčů Azure. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)

Tento článek ukazuje, jak používat klíče spravované zákazníkem se službou Import/Export na [webu Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Vytvořili jste úlohu importu nebo exportu podle pokynů v části:

    - [Vytvořte úlohu importu pro objekty BLOB](storage-import-export-data-to-blobs.md).
    - [Vytvořte úlohu importu pro soubory](storage-import-export-data-to-files.md).
    - [Vytvoření úlohy exportu pro objekty BLOB](storage-import-export-data-from-blobs.md)

2. Máte existující Azure Key Vault s klíčem v něm, který můžete použít k ochraně klíče Nástroje BitLocker. Informace o tom, jak vytvořit trezor klíčů pomocí webu Azure Portal, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../../key-vault/quick-create-portal.md).

    - V existujícím trezoru klíčů jsou nastaveny **funkce Neodstranit** a **Neodstraňovat.** Tyto vlastnosti nejsou ve výchozím nastavení povoleny. Chcete-li tyto vlastnosti povolit, přečtěte si části s názvem **Povolení odstranění pomocí protokolu soft a** povolení ochrany proti **vymazání** v jednom z následujících článků:

        - [Jak používat obnovitelné odstranění s Prostředím PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
        - [Jak používat soft-delete s CLI](../../key-vault/key-vault-soft-delete-cli.md).
    - Existující trezor klíčů by měl mít klíč RSA o velikosti 2048 nebo více. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Trezor klíčů musí být ve stejné oblasti jako účet úložiště pro vaše data.  
    - Pokud nemáte existující trezor klíčů Azure, můžete jej také vytvořit vřádkem, jak je popsáno v následující části.

## <a name="enable-keys"></a>Povolení klíčů

Konfigurace klíče spravovaného zákazníkem pro službu Import/Export je volitelná. Ve výchozím nastavení používá služba Import/Export klíč spravovaný společností Microsoft k ochraně klíče nástroje BitLocker. Pokud chcete povolit klíče spravované zákazníky na webu Azure Portal, postupujte takto:

1. Přejděte do okna **Přehled** pro úlohu importu.
2. V pravém podokně vyberte Zvolit způsob **šifrování klíčů nástroje BitLocker**.

    ![Zvolit možnost šifrování](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. V okně **Šifrování** můžete zobrazit a zkopírovat klíč nástroje BitLocker zařízení. V části **Typ šifrování**můžete zvolit, jak chcete chránit klíč nástroje BitLocker. Ve výchozím nastavení se používá klíč spravovaný společností Microsoft.

    ![Zobrazit klíč nástroje BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Máte možnost zadat klíč spravovaný zákazníkem. Po výběru klíče spravovaného zákazníkem **vyberte trezor klíčů a klíč**.

    ![Vybrat klíč spravovaný zákazníkem](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. V příkazu Select key z úložiště **klíčů Azure je** automaticky vyplněno. V **seznamu Trezor klíčů**můžete vybrat existující trezor klíčů z rozevíracího seznamu.

    ![Výběr nebo vytvoření trezoru klíčů Azure](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Můžete také vybrat **Vytvořit nový** a vytvořit nový trezor klíčů. V **okně Vytvořit trezor klíčů**zadejte skupinu prostředků a název trezoru klíčů. Přijměte všechny ostatní výchozí hodnoty. Vyberte **zkontrolovat + vytvořit**.

    ![Vytvoření nového trezoru klíčů Azure](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Zkontrolujte informace přidružené k trezoru klíčů a vyberte **Vytvořit**. Počkejte několik minut na dokončení vytvoření trezoru klíčů.

    ![Vytvoření trezoru klíčů Azure](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. V **klíči Select z trezoru klíčů Azure**můžete vybrat klíč v existujícím trezoru klíčů.

9. Pokud jste vytvořili nový trezor klíčů, vyberte **Vytvořit nový** a vytvořte klíč. Velikost klíče RSA může být 2048 nebo vyšší.

    ![Vytvoření nového klíče v trezoru klíčů Azure](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Pokud při vytváření trezoru klíčů není povolena ochrana proti odstranění a vymazání, bude trezor klíčů aktualizován tak, aby byla povolena ochrana proti odstranění a vymazání.

10. Zadejte název klíče, přijměte další výchozí hodnoty a vyberte **Vytvořit**.

    ![Vytvořit nový klíč](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Vyberte **verzi** a pak zvolte **Vybrat**. Budete upozorněni, že klíč je vytvořen v trezoru klíčů.

    ![V trezoru klíčů byl vytvořen nový klíč](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

V okně **Šifrování** uvidíte trezor klíčů a klíč vybraný pro klíč spravovaný zákazníkem.

## <a name="disable-keys"></a>Zakázat klíče

Můžete zakázat pouze klíče spravované společností Microsoft a přesunout se na klíče spravované zákazníkem v libovolné fázi úlohy importu nebo exportu. Po vytvoření však nelze zakázat klíč spravovaný zákazníkem.

## <a name="troubleshoot-customer-managed-key-errors"></a>Poradce při potížích s chybami klíče spravovaného zákazníkem

Pokud se zobrazí chyby související s klíčem spravovaným zákazníkem, použijte k řešení potíží následující tabulku:

| Kód chyby     |Podrobnosti     | Obnovitelné?    |
|----------------|------------|-----------------|
| CmkErrorAccessOdvolán | Použitý klíč spravovaný zákazníkem, ale přístup ke klíči je nyní odvolán. Další informace naleznete v [tématu Povolení přístupu ke klíči](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Ano, zkontrolujte, zda: <ol><li>Trezor klíčů má stále MSI v zásadách přístupu.</li><li>Zásady přístupu poskytuje oprávnění k získání, obtékání, rozbalení.</li><li>Pokud je trezor klíčů ve virtuální síti za bránou firewall, zkontrolujte, jestli je **povolená možnost Povolit důvěryhodnou službu Microsoft.**</li></ol>                                                                                            |
| CmkErrorZakázáno      | Použitý klíč spravovaný zákazníkem, ale klíč je zakázán. Další informace naleznete v [tématu Povolení klíče](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Ano, povolením klíčové verze     |
| CmkErrorNotFound      | Použil klíč spravovaný zákazníkem, ale nemůže ho najít. <br>Pokud je klíč odstraněn a vymazán po období uchování, nelze klíč obnovit. Pokud jste klíč zálohovali, můžete klíč obnovit a tento problém vyřešit. | Ne, klíč byl odstraněn a také byl vymazán po období uchovávání. <br>Ano, pouze v případě, že zákazník má zálohovaný klíč a obnoví jej.  |
| CmkErrorVaultnebyl nalezen | Použil klíč spravovaný zákazníkem, ale nemůže najít trezor klíčů přidružený ke klíči.<br>Pokud jste trezor klíčů odstranili, nemůžete klíč spravovaný zákazníkem obnovit.  Pokud jste migrovali trezor klíčů do jiného tenanta, přečtěte si informace [o změně ID klienta trezoru klíčů po přesunutí předplatného](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Ne, pokud zákazník odstranil trezor klíčů.<br> Ano, pokud trezor klíčů prošel migrací klienta, proveďte jednu z těchto částí: <ol><li>přesunout zpět trezor klíčů ke starému tenantovi.</li><li>set Identity = None a potom zpět na Identity = SystemAssigned, tato dehtí a znovu vytvoří identitu</li></ol><br>Poznámka: Případ migrace klienta je založen na omezeném porozumění, je třeba otestovat a potvrdit skutečné chování, může být upraven později. |

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
