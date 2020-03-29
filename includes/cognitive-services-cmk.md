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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372191"
---
### <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

K ukládání klíčů spravovaných zákazníky je nutné použít azure key vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Prostředek služeb Cognitive Services a trezor klíčů musí být ve stejné oblasti a ve stejném tenantovi Služby Azure Active Directory (Azure AD), ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nový prostředek služeb Cognitive Services je vždy šifrován pomocí klíčů spravovaných společností Microsoft. Není možné povolit klíče spravované zákazníkem v době vytvoření prostředku. Klíče spravované zákazníkem jsou uloženy v trezoru klíčů Azure a trezor klíčů musí být zřízen zásadami přístupu, které udělují oprávnění klíče spravované identitě, která je přidružena k prostředku služeb Cognitive Services. Spravovaná identita je k dispozici pouze po vytvoření prostředku pomocí cenové úrovně pro CMK.

Informace o tom, jak používat klíče spravované zákazníky pomocí šifrování Azure Key Vault for Cognitive Services, najdete v tématu:

- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro kognitivní služby šifrování z portálu Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Povolení klíčů spravovaných zákazníky také umožní systém přiřazenou spravovanou identitu, což je funkce Azure AD. Po povolení systému přiřazené spravované identity bude tento prostředek zaregistrován ve službě Azure Active Directory. Po registraci bude spravovaná identita mít přístup k trezoru klíčů vybranému během nastavení klíče spravovaného zákazníkem. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Pokud zakážete systém přiřazené spravované identity, bude přístup k trezoru klíčů odebrán a všechna data zašifrovaná pomocí klíčů zákazníků již nebudou přístupná. Všechny funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity aktuálně nepodporují scénáře mezi adresáři. Když nakonfigurujete klíče spravované zákazníky na webu Azure Portal, spravovaná identita se automaticky přiřadí pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete **v tématu Přenos předplatného mezi adresáři Azure AD** v [častých dotazech a známými problémy se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníky v trezoru klíčů Azure

Chcete-li povolit klíče spravované zákazníkem, musíte k uložení klíčů použít trezor klíčů Azure. Je nutné povolit vlastnosti **Obnovitelné odstranění** a **Nevyčistit** v trezoru klíčů.

Šifrováním služeb Cognitive Services jsou podporovány pouze klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Otočení klíčů spravovaných zákazníkem

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Při otočení klíče je nutné aktualizovat prostředek služeb Cognitive Services, aby bylo nutné použít nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče na webu Azure Portal, najdete v části **Aktualizace verze klíče** v [tématu Konfigurace klíčů spravovaných zákazníky pro služby Cognitive Services pomocí portálu Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Otočením klíče neaktivujete opětovné šifrování dat v prostředku. Neexistuje žádná další akce vyžaduje od uživatele.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, použijte PowerShell nebo Azure CLI. Další informace najdete [v tématu Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Zrušení přístupu účinně blokuje přístup ke všem datům v prostředku služeb Cognitive Services, protože šifrovací klíč není pro služby Cognitive Services přístupný.


