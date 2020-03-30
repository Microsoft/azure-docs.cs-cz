---
title: Konfigurace klíčů spravovaných zákazníky pomocí portálu Azure Portal
titleSuffix: Azure Storage
description: Zjistěte, jak pomocí portálu Azure nakonfigurovat klíče spravované zákazníky pomocí šifrování Azure Key Vault for Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bef3b53a160f17248c1a26e97bc85a86843cb3c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061203"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníky pomocí služby Azure Key Vault pomocí portálu Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak nakonfigurovat Azure Key Vault s klíči spravovanými zákazníky pomocí [portálu Azure](https://portal.azure.com/). Informace o tom, jak vytvořit trezor klíčů pomocí webu Azure Portal, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě **vlastnosti, obnovitelné odstranění** a **neodstraňovat**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale lze povolit pomocí powershellu nebo rozhraní příkazového příkazu Azure v novém nebo existujícím trezoru klíčů.

Informace o povolení těchto vlastností v existujícím trezoru klíčů naleznete v částech s názvem **Povolení obnovitelného odstranění** a **povolení ochrany proti vymazání** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění s Prostředím PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
- [Jak používat soft-delete s CLI](../../key-vault/key-vault-soft-delete-cli.md).

Šifrováním úložiště Azure jsou podporované jenom klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

Pokud chcete povolit klíče spravované zákazníky na webu Azure Portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
1. V okně **Nastavení** pro účet úložiště klepněte na tlačítko **Šifrování**. Vyberte možnost **Spravované klíče zákazníka,** jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky portálu s možností šifrování](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Určení klíče

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k účtu úložiště.

### <a name="specify-a-key-as-a-uri"></a>Určení klíče jako identifikátoru URI

Chcete-li určit klíč jako identifikátor URI, postupujte takto:

1. Pokud chcete najít identifikátor URI klíče na webu Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **Klíče.** Vyberte požadovanou klávesu a klepnutím na klávesu zobrazte jeho verze. Vyberte verzi klíče, chcete-li zobrazit nastavení pro tuto verzi.
1. Zkopírujte hodnotu pole **Identifikátor klíče,** které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče klíče klíče klíče](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. V nastavení **šifrování** pro váš účet úložiště zvolte možnost **Zadat klíč URI.**
1. Vložte identifikátor URI, který jste zkopírovali, do pole **Identifikátor URI klíče.**

   ![Snímek obrazovky znázorňující zadání identifikátoru URI klíče](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Zadejte odběr, který obsahuje trezor klíčů.
1. Uložte provedené změny.

### <a name="specify-a-key-from-a-key-vault"></a>Určení klíče z trezoru klíčů

Chcete-li zadat klíč z trezoru klíčů, nejprve se ujistěte, že máte trezor klíčů, který obsahuje klíč. Chcete-li určit klíč z trezoru klíčů, postupujte takto:

1. Zvolte **volbu Vybrat z trezoru klíčů.**
1. Vyberte trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Uložte provedené změny.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, aktualizujte účet úložiště tak, aby používal novou verzi. Postupujte následovně:

1. Přejděte na účet úložiště a zobrazte nastavení **šifrování.**
1. Zadejte identifikátor URI pro novou verzi klíče. Případně můžete znovu vybrat trezor klíčů a klíč pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="use-a-different-key"></a>Použití jiného klíče

Pokud chcete změnit klíč používaný pro šifrování Azure Storage, postupujte takto:

1. Přejděte na účet úložiště a zobrazte nastavení **šifrování.**
1. Zadejte identifikátor URI pro nový klíč. Případně můžete vybrat trezor klíčů a zvolit nový klíč.
1. Uložte provedené změny.

## <a name="disable-customer-managed-keys"></a>Zakázání klíčů spravovaných zákazníkem

Když zakážete klíče spravované zákazníky, váš účet úložiště se opět zašifruje pomocí klíčů spravovaných společností Microsoft. Chcete-li zakázat klíče spravované zákazníkem, postupujte takto:

1. Přejděte na účet úložiště a zobrazte nastavení **šifrování.**
1. Zrušte zaškrtnutí políčka vedle nastavení **Použít vlastní klíč.**

## <a name="next-steps"></a>Další kroky

- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md)
- [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
