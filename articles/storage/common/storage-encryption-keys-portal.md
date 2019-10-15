---
title: Konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure Portal
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pro Azure Storage šifrování. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 74ca6f15baeeb9fe8adad4bda80e313a4b4cf03a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376227"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí [Azure Portal](https://portal.azure.com/). Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/quick-create-portal.md). 

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, použijte PowerShell nebo rozhraní příkazového řádku Azure CLI.
> Podporují se jenom klíče RSA a velikost klíče 2048.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte na svůj účet úložiště.
1. V okně **Nastavení** pro účet úložiště klikněte na **šifrování**. Vyberte možnost **použít vlastní klíč** , jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky portálu ukazující možnost šifrování](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Zadat klíč

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k účtu úložiště.

### <a name="specify-a-key-as-a-uri"></a>Zadat klíč jako identifikátor URI

Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho nastavení. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](media/storage-encryption-keys-portal/key-uri-portal.png)

1. V nastavení **šifrování** pro účet úložiště klikněte na možnost **zadat identifikátor URI klíče** .
1. Do pole **identifikátor URI klíče** zadejte identifikátor URI.

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Zadat klíč z trezoru klíčů

Pokud chcete zadat klíč z trezoru klíčů, nejdřív se ujistěte, že máte Trezor klíčů, který obsahuje klíč. Pokud chcete zadat klíč z trezoru klíčů, použijte následující postup:

1. Zvolte možnost **vybrat z Key Vault** .
2. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
3. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, budete muset aktualizovat účet úložiště, aby používal novou verzi. Postupujte následovně:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.

## <a name="next-steps"></a>Další kroky

- [Azure Storage šifrování dat v klidovém umístění](storage-service-encryption.md)
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
