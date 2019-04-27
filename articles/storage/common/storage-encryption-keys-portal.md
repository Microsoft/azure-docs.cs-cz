---
title: Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage na webu Azure Portal
description: Zjistěte, jak nakonfigurovat spravované zákazníkem klíče pro šifrování Azure Storage pomocí webu Azure portal. Klíče spravované zákazníkem umožňují vytvořit, otáčení, zakázat a odvolat přístup k ovládacím prvkům.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 47dd38d9b5fd99431c8661c4891c4ebcdf5341d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593664"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage na webu Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Tento článek ukazuje, jak konfigurovat službu key vault s použitím klíčů spravovaných zákazníkem [webu Azure portal](https://portal.azure.com/). Zjistěte, jak vytvořit trezor klíčů pomocí webu Azure portal, najdete v článku [rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure portal](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Pomocí klíčů spravovaných zákazníkem pomocí šifrování úložiště Azure vyžaduje, že trezor klíčů má dvě požadované vlastnosti nakonfigurovat, **obnovitelné odstranění** a **proveďte není vyprázdnit**. Tyto vlastnosti jsou ve výchozím nastavení povolena, když vytvoříte nový trezor klíčů na webu Azure Portal. Ale pokud je potřeba povolit tyto vlastnosti existujícího trezoru klíčů, musíte použít PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="enable-customer-managed-keys"></a>Povolení klíče spravované zákazníkem

Pokud chcete povolit klíče spravované zákazníkem na webu Azure Portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
1. Na **nastavení** okno pro účet úložiště, klikněte na tlačítko **šifrování**. Vyberte **použít vlastní klíč** možnosti, jak je znázorněno na následujícím obrázku.

    ![Možnost šifrování portálu snímek obrazovky znázorňující](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Zadejte klíč

Když povolíte klíče spravované zákazníkem, budete mít příležitost k určení klíče pro přidružení k účtu úložiště.

### <a name="specify-a-key-as-a-uri"></a>Zadejte klíč jako identifikátor URI

Zadejte klíč jako identifikátor URI, postupujte podle těchto kroků:

1. Najít identifikátor URI klíče na webu Azure Portal, přejděte do trezoru klíčů a vyberte **klíče** nastavení. Vyberte požadovaný klíč a potom klikněte na klíč a zobrazte její nastavení. Zkopírujte hodnotu **identifikátor klíče** pole, která obsahuje identifikátor URI.

    ![Snímek obrazovky znázorňující služby key vault klíč identifikátoru URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. V **šifrování** zvolte nastavení pro váš účet úložiště **zadejte identifikátor URI klíče** možnost.
1. V **URI klíče** zadejte identifikátor URI.

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Zadejte klíč ze služby key vault

K určení klíče ze služby key vault, první Ujistěte se, že máte trezor klíčů, který obsahuje klíč. K určení klíče ze služby key vault, postupujte podle těchto kroků:

1. Zvolte **vyberte ze služby Key Vault** možnost.
2. Vyberte trezor klíčů, který obsahuje klíč, který chcete použít.
3. Zvolte klíče z trezoru klíčů.

   ![Snímek obrazovky zobrazující možnost klíče spravované zákazníkem](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, musíte aktualizovat účet úložiště na použití nové verze. Postupujte následovně:

1. Přejděte do svého účtu úložiště a zobrazí **šifrování** nastavení.
1. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat trezor klíčů a klíč znovu a aktualizujte na verzi.

## <a name="next-steps"></a>Další postup

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
