---
title: Šifrování neaktivních dat Language Understanding služby
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro Language Understanding (LUIS) a o tom, jak CMK povolit a spravovat.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524429"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat Language Understanding služby

Služba Language Understanding automaticky šifruje vaše data při trvalém ukládání do cloudu. Šifrování služby Language Understanding chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů v organizaci.

## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services

Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou zabezpečená ve výchozím nastavení, a abyste mohli využívat šifrování, nemusíte upravovat kód ani aplikace.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. K dispozici je také možnost spravovat vaše předplatné s vlastními klíči nazvanými klíče spravované zákazníkem (CMK). CMK nabízí větší flexibilitu při vytváření, rotaci, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s využitím Azure Key Vaultu

K dispozici je také možnost Spravovat předplatné s vlastními klíči. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat.

K ukládání klíčů spravovaných zákazníkem musíte použít službu Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek Cognitive Services a trezor klíčů musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

### <a name="customer-managed-keys-for-language-understanding"></a>Klíče spravované zákazníkem pro Language Understanding

Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč Customer-Managed Luis Service](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro použití CMK s LUIS budete muset vytvořit nový prostředek Language Understanding z Azure Portal a vybrat E0 jako cenovou úroveň. Nová SKU bude fungovat stejně jako SKU F0, která je již k dispozici, s výjimkou CMK. Uživatelé nebudou moct upgradovat z F0 na novou SKLADOVOU položku E0.

![Obrázek předplatného LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>Omezení

Při použití vrstvy E0 s existujícími nebo dříve vytvořenými aplikacemi platí určitá omezení:

* Migrace do prostředku E0 se zablokuje. Uživatelé budou moct migrovat své aplikace jenom na F0 prostředky. Po dokončení migrace existujícího prostředku na F0 můžete vytvořit nový prostředek ve vrstvě E0. Další informace o [migraci najdete tady](./luis-migration-authoring.md).  
* Přesun aplikací do nebo z prostředku E0 se zablokuje. Řešením tohoto omezení je exportovat existující aplikaci a importovat ji jako prostředek E0.
* Funkce kontroly pravopisu Bingu není podporovaná.
* Protokolování provozu koncových uživatelů je zakázané, pokud je vaše aplikace E0.
* Funkce pro dopřední řeči z Azure bot Service není podporovaná pro aplikace ve vrstvě E0. Tato funkce je k dispozici prostřednictvím Azure Bot Service, která nepodporuje CMK.
* Funkce pro dopřední řeči z portálu vyžaduje Azure Blob Storage. Další informace najdete v tématu [Přineste si vlastní úložiště](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Nový prostředek Cognitive Services je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. Klíče spravované zákazníkem není možné povolit v době, kdy se prostředek vytvořil. Klíče spravované zákazníkem jsou uloženy v Azure Key Vault a trezor klíčů musí být zřízen pomocí zásad přístupu, které udělují klíčová oprávnění ke spravované identitě, která je přidružena k prostředku Cognitive Services. Spravovaná identita je k dispozici až po vytvoření prostředku pomocí cenové úrovně pro CMK.

Informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Cognitive Services najdete v těchto tématech:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Cognitive Services šifrování z Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Povolením zákaznických klíčů taky povolíte spravovanou identitu přiřazenou systémem, což je funkce Azure AD. Po povolení spravované identity přiřazené systémem se tento prostředek zaregistruje ve službě Azure Active Directory. Po registraci bude spravované identitě udělen přístup k Key Vault vybranému během nastavení spravovaného klíče zákazníka. Další informace o [spravovaných identitách](../../active-directory/managed-identities-azure-resources/overview.md)najdete v.

> [!IMPORTANT]
> Pokud zakážete spravované identity přiřazené systémem, odeberou se přístup k trezoru klíčů a veškerá data zašifrovaná pomocí klíčů zákazníka už nebudou dostupná. Jakékoli funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity v současné době nepodporují scénáře pro více adresářů. Při konfiguraci klíčů spravovaných zákazníkem v Azure Portal se spravovaná identita automaticky přiřadí v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře služby Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenáší do nového tenanta, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Cognitive Services šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/general/about-keys-secrets-certificates.md).

### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Když je klíč otočen, je nutné aktualizovat prostředek Cognitive Services, aby používal nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče v Azure Portal, najdete v části s názvem **aktualizace verze klíče** v tématu [konfigurace klíčů spravovaných zákazníkem pro Cognitive Services pomocí Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md).

Otočení klíče neaktivuje opětovné šifrování dat v prostředku. Od uživatele není vyžadována žádná další akce.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](/powershell/module/az.keyvault//) nebo rozhraní příkazového [řádku Azure Key Vault](/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v prostředku Cognitive Services, protože šifrovací klíč není přístupný Cognitive Services.

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč Customer-Managed služby LUIS](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](../../key-vault/general/overview.md)
