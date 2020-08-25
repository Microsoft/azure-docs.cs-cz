---
title: Použití Azure Portal ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Azure Storage
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault pro Azure Storage šifrování.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799161"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem se službou Azure Key Vault pomocí webu Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí [Azure Portal](https://portal.azure.com/). Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale můžete je povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů.

Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení funkce vyprázdnit ochranu** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Jak používat obnovitelné odstranění pomocí rozhraní](../../key-vault/general/soft-delete-cli.md)PŘÍKAZového řádku

Šifrování úložiště Azure podporuje klíče RSA a RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte na svůj účet úložiště.
1. V okně **Nastavení** pro účet úložiště klikněte na **šifrování**. Vyberte možnost **spravované klíče zákazníka** , jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky portálu ukazující možnost šifrování](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Zadat klíč

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k účtu úložiště. Můžete také určit, zda má Azure Storage automaticky aktualizovat klíč spravovaný zákazníkem na nejnovější verzi nebo zda bude verze klíče aktualizována ručně.

### <a name="specify-a-key-from-a-key-vault"></a>Zadat klíč z trezoru klíčů

Když vyberete klíč spravovaný zákazníkem z trezoru klíčů, je povolená Automatická aktualizace verze klíče. Chcete-li ručně spravovat verzi klíče, zadejte místo toho identifikátor URI klíče a zahrňte verzi klíče. Další informace najdete v tématu [určení klíče jako identifikátoru URI](#specify-a-key-as-a-uri).

Pokud chcete zadat klíč z trezoru klíčů, použijte následující postup:

1. Zvolte možnost **vybrat z Key Vault** .
1. Vyberte **možnost vyberte Trezor klíčů a klíč**.
1. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky ukazující, jak vybrat Trezor klíčů a klíč](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Uložte provedené změny.

### <a name="specify-a-key-as-a-uri"></a>Zadat klíč jako identifikátor URI

Azure Storage může automaticky aktualizovat klíč spravovaný zákazníkem, který se používá k šifrování, aby používal nejnovější verzi klíče. Při otočení klíče spravovaného zákazníkem v Azure Key Vault Azure Storage automaticky začít používat k šifrování nejnovější verzi klíče.

> [!NOTE]
> Pokud chcete klíč otočit, vytvořte v Azure Key Vault novou verzi klíče. Azure Storage nezpracovává rotaci klíče v Azure Key Vault, takže budete muset klíč otočit ručně nebo vytvořit funkci, která ho otočí podle plánu.

Když zadáte identifikátor URI klíče, vynechejte verzi klíče z identifikátoru URI, aby se povolily automatické aktualizace na nejnovější verzi klíče. Pokud zahrnete klíčovou verzi do identifikátoru URI klíče, nepovolí se automatické aktualizace a Vy musíte spravovat verzi klíče sami. Další informace o aktualizaci verze klíče najdete v tématu [Ruční aktualizace verze klíče](#manually-update-the-key-version).

Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče a zobrazte nastavení této verze.
1. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. V nastavení **šifrovacího klíče** pro váš účet úložiště klikněte na možnost **zadat identifikátor URI klíče** .
1. Vložte identifikátor URI, který jste zkopírovali do pole **identifikátor URI klíče** . Pokud chcete povolit automatickou aktualizaci verze klíče, vynechejte verzi klíče z identifikátoru URI.

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Zadejte předplatné, které obsahuje Trezor klíčů.
1. Uložte provedené změny.

Po zadání klíče Azure Portal určuje, jestli je povolená Automatická aktualizace verze klíče, a zobrazí verzi klíče, která se v tuto chvíli používá pro šifrování.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Snímek obrazovky s automatickou aktualizací aktivované verze klíče":::

## <a name="manually-update-the-key-version"></a>Ruční aktualizace verze klíče

Ve výchozím nastavení se při vytváření nové verze klíče spravovaného zákazníkem v Key Vault Azure Storage automaticky používá nová verze pro šifrování pomocí klíčů spravovaných zákazníkem, jak je popsáno v předchozích částech. Pokud se rozhodnete spravovat verzi klíče sami, musíte aktualizovat verzi klíče, která je přidružená k účtu úložiště pokaždé, když vytvoříte novou verzi klíče.

Pokud chcete aktualizovat účet úložiště, aby používal novou verzi klíče, postupujte takto:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI nové verze klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="switch-to-a-different-key"></a>Přepnout na jiný klíč

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
