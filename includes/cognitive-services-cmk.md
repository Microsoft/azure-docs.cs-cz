---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372191"
---
### <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek Cognitive Services a trezor klíčů musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nový prostředek Cognitive Services je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. Klíče spravované zákazníkem není možné povolit v době, kdy se prostředek vytvořil. Klíče spravované zákazníkem jsou uloženy v Azure Key Vault a trezor klíčů musí být zřízen pomocí zásad přístupu, které udělují klíčová oprávnění ke spravované identitě, která je přidružena k prostředku Cognitive Services. Spravovaná identita je k dispozici až po vytvoření prostředku pomocí cenové úrovně pro CMK.

Informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Cognitive Services najdete v těchto tématech:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Cognitive Services šifrování z Azure Portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Povolením zákaznických klíčů taky povolíte spravovanou identitu přiřazenou systémem, což je funkce Azure AD. Po povolení spravované identity přiřazené systémem se tento prostředek zaregistruje ve službě Azure Active Directory. Po registraci bude spravované identitě udělen přístup k Key Vault vybranému během nastavení spravovaného klíče zákazníka. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)najdete v.

> [!IMPORTANT]
> Pokud zakážete spravované identity přiřazené systémem, odeberou se přístup k trezoru klíčů a veškerá data zašifrovaná pomocí klíčů zákazníka už nebudou dostupná. Jakékoli funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity v současné době nepodporují scénáře pro více adresářů. Při konfiguraci klíčů spravovaných zákazníkem v Azure Portal se spravovaná identita automaticky přiřadí v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře služby Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenáší do nového tenanta, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Cognitive Services šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Když je klíč otočen, je nutné aktualizovat prostředek Cognitive Services, aby používal nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče v Azure Portal, najdete v části s názvem **aktualizace verze klíče** v tématu [konfigurace klíčů spravovaných zákazníkem pro Cognitive Services pomocí Azure Portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Otočení klíče neaktivuje opětovné šifrování dat v prostředku. Od uživatele není vyžadována žádná další akce.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo rozhraní příkazového [řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v prostředku Cognitive Services, protože šifrovací klíč není přístupný Cognitive Services.


