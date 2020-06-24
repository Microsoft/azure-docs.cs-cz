---
title: Použití Azure Portal ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Azure Storage
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault pro Azure Storage šifrování.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 30ef840e254355e7db34c626f482d86246e6a738
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805407"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí [Azure Portal](https://portal.azure.com/). Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale můžete je povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů.

Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení funkce vyprázdnit ochranu** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Jak používat obnovitelné odstranění pomocí rozhraní](../../key-vault/general/soft-delete-cli.md)PŘÍKAZového řádku

Azure Storage šifrování podporují jenom 2048 klíčů RSA a RSA-HSM. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte na svůj účet úložiště.
1. V okně **Nastavení** pro účet úložiště klikněte na **šifrování**. Vyberte možnost **spravované klíče zákazníka** , jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky portálu ukazující možnost šifrování](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Zadat klíč

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k účtu úložiště.

### <a name="specify-a-key-as-a-uri"></a>Zadat klíč jako identifikátor URI

Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče a zobrazte nastavení této verze.
1. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. V nastavení **šifrování** pro účet úložiště klikněte na možnost **zadat identifikátor URI klíče** .
1. Vložte identifikátor URI, který jste zkopírovali do pole **identifikátor URI klíče** .

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Zadejte předplatné, které obsahuje Trezor klíčů.
1. Uložte provedené změny.

### <a name="specify-a-key-from-a-key-vault"></a>Zadat klíč z trezoru klíčů

Pokud chcete zadat klíč z trezoru klíčů, nejdřív se ujistěte, že máte Trezor klíčů, který obsahuje klíč. Pokud chcete zadat klíč z trezoru klíčů, použijte následující postup:

1. Zvolte možnost **vybrat z Key Vault** .
1. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Uložte provedené změny.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, aktualizujte účet úložiště, aby používal novou verzi. Postupujte následovně:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro Azure Storage šifrování, postupujte takto:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI nového klíče. Alternativně můžete vybrat Trezor klíčů a zvolit nový klíč.
1. Uložte provedené změny.

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš účet úložiště se znovu zašifruje pomocí klíčů spravovaných Microsoftem. K zakázání klíčů spravovaných zákazníkem použijte tento postup:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zrušte zaškrtnutí políčka u nastavení **použít vlastní klíč** .

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
