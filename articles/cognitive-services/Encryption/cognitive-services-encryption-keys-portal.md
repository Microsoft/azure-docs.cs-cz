---
title: Konfigurace klíčů spravovaných zákazníky pomocí portálu Azure Portal
titleSuffix: Cognitive Services
description: Přečtěte si, jak pomocí portálu Azure nakonfigurovat klíče spravované zákazníky pomocí azure key vaultu. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat ovládací prvky přístupu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053606"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníky pomocí služby Azure Key Vault pomocí portálu Azure

K ukládání klíčů spravovaných zákazníky je nutné použít azure key vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Prostředek služeb Cognitive Services a trezor klíčů musí být ve stejné oblasti a ve stejném tenantovi Služby Azure Active Directory (Azure AD), ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Tento článek ukazuje, jak nakonfigurovat Azure Key Vault s klíči spravovanými zákazníky pomocí [portálu Azure](https://portal.azure.com/). Informace o tom, jak vytvořit trezor klíčů pomocí webu Azure Portal, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem vyžaduje, aby byly v trezoru klíčů nastaveny dvě **vlastnosti, funkce Obnovitelné odstranění** a **Neodstranit**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale lze povolit pomocí powershellu nebo rozhraní příkazového příkazu Azure v novém nebo existujícím trezoru klíčů.

> [!IMPORTANT]
> Pokud nemáte obnovitelné **vlastnosti Obnovitelné odstranění** a **Nevyčistit** a odstraníte klíč, nebudete moci obnovit data v prostředku služby Cognitive Service.

Informace o povolení těchto vlastností v existujícím trezoru klíčů naleznete v částech s názvem **Povolení obnovitelného odstranění** a **povolení ochrany proti vymazání** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění s Prostředím PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Jak používat soft-delete s CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Šifrováním úložiště Azure jsou podporované jenom klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

Pokud chcete povolit klíče spravované zákazníky na webu Azure Portal, postupujte takto:

1. Přejděte na prostředek služeb Cognitive Services.
1. V okně **Nastavení** zdroje služeb Cognitive Services klepněte na tlačítko **Šifrování**. Vyberte možnost **Spravované klíče zákazníka,** jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky s výběrem klíčů spravovaných zákazníky](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Určení klíče

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k prostředku služeb Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Určení klíče jako identifikátoru URI

Chcete-li určit klíč jako identifikátor URI, postupujte takto:

1. Pokud chcete najít identifikátor URI klíče na webu Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **Klíče.** Vyberte požadovanou klávesu a klepnutím na klávesu zobrazte jeho verze. Vyberte verzi klíče, chcete-li zobrazit nastavení pro tuto verzi.
1. Zkopírujte hodnotu pole **Identifikátor klíče,** které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče klíče klíče klíče](../media/cognitive-services-encryption/key-uri-portal.png)

1. V nastavení **šifrování** pro váš účet úložiště zvolte možnost **Zadat klíč URI.**
1. Vložte identifikátor URI, který jste zkopírovali, do pole **Identifikátor URI klíče.**

   ![Snímek obrazovky znázorňující zadání identifikátoru URI klíče](../media/cognitive-services-encryption/ssecmk2.png)

1. Zadejte odběr, který obsahuje trezor klíčů.
1. Uložte provedené změny.

### <a name="specify-a-key-from-a-key-vault"></a>Určení klíče z trezoru klíčů

Chcete-li zadat klíč z trezoru klíčů, nejprve se ujistěte, že máte trezor klíčů, který obsahuje klíč. Chcete-li určit klíč z trezoru klíčů, postupujte takto:

1. Zvolte **volbu Vybrat z trezoru klíčů.**
1. Vyberte trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](../media/cognitive-services-encryption/ssecmk3.png)

1. Uložte provedené změny.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Při vytváření nové verze klíče aktualizujte prostředek služeb Cognitive Services tak, aby používal novou verzi. Postupujte následovně:

1. Přejděte na prostředek služeb Cognitive Services a zobrazte nastavení **šifrování.**
1. Zadejte identifikátor URI pro novou verzi klíče. Případně můžete znovu vybrat trezor klíčů a klíč pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="use-a-different-key"></a>Použití jiného klíče

Chcete-li změnit klíč použitý pro šifrování, postupujte takto:

1. Přejděte na prostředek služeb Cognitive Services a zobrazte nastavení **šifrování.**
1. Zadejte identifikátor URI pro nový klíč. Případně můžete vybrat trezor klíčů a zvolit nový klíč.
1. Uložte provedené změny.

## <a name="disable-customer-managed-keys"></a>Zakázání klíčů spravovaných zákazníkem

Když zakážete klíče spravované zákazníky, váš prostředek služeb Cognitive Services se pak zašifruje pomocí klíčů spravovaných společností Microsoft. Chcete-li zakázat klíče spravované zákazníkem, postupujte takto:

1. Přejděte na prostředek služeb Cognitive Services a zobrazte nastavení **šifrování.**
1. Zrušte zaškrtnutí políčka vedle nastavení **Použít vlastní klíč.**

## <a name="next-steps"></a>Další kroky

* [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulář žádosti o klíč spravované zákazníkem cognitive services](https://aka.ms/cogsvc-cmk)
* [Face Services šifrování dat v klidovém stavu](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker šifrování dat v klidovém stavu](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Šifrování dat v klidovém stavu služby Language Understanding](../LUIS/luis-encryption-of-data-at-rest.md)
* [Šifrování dat moderátorem obsahu v klidovém stavu](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Překladač šifrování dat v klidovém stavu](../translator/translator-encryption-of-data-at-rest.md)
* [Personalizace dat v klidovém stavu](../personalizer/personalizer-encryption-of-data-at-rest.md)
