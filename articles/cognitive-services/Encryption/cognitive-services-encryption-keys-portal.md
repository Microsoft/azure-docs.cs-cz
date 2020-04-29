---
title: Použití Azure Portal ke konfiguraci klíčů spravovaných zákazníkem
titleSuffix: Cognitive Services
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455253"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pomocí Azure Portal

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek Cognitive Services a trezor klíčů musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

V tomto článku se dozvíte, jak nakonfigurovat Azure Key Vault s použitím klíčů spravovaných zákazníkem pomocí [Azure Portal](https://portal.azure.com/). Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale můžete je povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů.

> [!IMPORTANT]
> Pokud nemáte k dispozici možnost **obnovitelné odstranění** a **Nemazat** vlastnosti a odstraníte klíč, nebudete moci obnovit data v prostředku služby vnímání.

Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení funkce vyprázdnit ochranu** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Jak používat obnovitelné odstranění pomocí rozhraní](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)PŘÍKAZového řádku

Azure Storage šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte k prostředku Cognitive Services.
1. V okně **Nastavení** pro prostředek Cognitive Services klikněte na **šifrování**. Vyberte možnost **spravované klíče zákazníka** , jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky ukazující, jak vybrat spravované klíče zákazníka](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Zadat klíč

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k prostředku Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Zadat klíč jako identifikátor URI

Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče a zobrazte nastavení této verze.
1. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](../media/cognitive-services-encryption/key-uri-portal.png)

1. V nastavení **šifrování** pro účet úložiště klikněte na možnost **zadat identifikátor URI klíče** .
1. Vložte identifikátor URI, který jste zkopírovali do pole **identifikátor URI klíče** .

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](../media/cognitive-services-encryption/ssecmk2.png)

1. Zadejte předplatné, které obsahuje Trezor klíčů.
1. Uložte provedené změny.

### <a name="specify-a-key-from-a-key-vault"></a>Zadat klíč z trezoru klíčů

Pokud chcete zadat klíč z trezoru klíčů, nejdřív se ujistěte, že máte Trezor klíčů, který obsahuje klíč. Pokud chcete zadat klíč z trezoru klíčů, použijte následující postup:

1. Zvolte možnost **vybrat z Key Vault** .
1. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](../media/cognitive-services-encryption/ssecmk3.png)

1. Uložte provedené změny.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, aktualizujte prostředek Cognitive Services tak, aby používal novou verzi. Postupujte následovně:

1. Přejděte k prostředku Cognitive Services a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro šifrování, postupujte podle těchto kroků:

1. Přejděte k prostředku Cognitive Services a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI nového klíče. Alternativně můžete vybrat Trezor klíčů a zvolit nový klíč.
1. Uložte provedené změny.

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš prostředek Cognitive Services je pak zašifrovaný pomocí klíčů spravovaných Microsoftem. K zakázání klíčů spravovaných zákazníkem použijte tento postup:

1. Přejděte k prostředku Cognitive Services a zobrazte nastavení **šifrování** .
1. Zrušte zaškrtnutí políčka u nastavení **použít vlastní klíč** .

## <a name="next-steps"></a>Další kroky

* [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulář žádosti o klíč spravovaný zákazníkem Cognitive Services](https://aka.ms/cogsvc-cmk)
* [Šifrování neaktivních dat ve službě faceation Services](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker šifrování dat v klidovém umístění](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Šifrování neaktivních dat Language Understanding služby](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator šifrování dat v klidovém umístění](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Šifrování dat v klidovém umístění](../translator/translator-encryption-of-data-at-rest.md)
* [Individuální Šifrování neaktivních dat](../personalizer/personalizer-encryption-of-data-at-rest.md)
