---
title: Šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: Microsoft vám umožňuje spravovat Cognitive Services předplatná s vlastními klíči nazývanými Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro překladatele a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce7ff6ae134835de23a0d2670e8b4f44783654f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079196"
---
# <a name="translator-encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

Překladatel automaticky šifruje vaše data, která nahrajete k sestavení vlastních modelů překladu, když jsou zachovaná v cloudu a pomáhají splnit cíle zabezpečení vaší organizace a požadavky na dodržování předpisů.

## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services

Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou zabezpečená ve výchozím nastavení, a abyste mohli využívat šifrování, nemusíte upravovat kód ani aplikace.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. Pokud používáte cenovou úroveň, která podporuje klíče spravované zákazníkem, můžete zobrazit nastavení šifrování prostředku v části **šifrování** [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/encryptionblade.png)

U předplatných, která podporují jenom šifrovací klíče spravované Microsoftem, nebudete mít **šifrovací** oddíl.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s využitím Azure Key Vaultu

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. K dispozici je také možnost spravovat vaše předplatné s vlastními klíči nazvanými klíče spravované zákazníkem (CMK). CMK nabízí větší flexibilitu při vytváření, rotaci, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat. Pokud je pro vaše předplatné nakonfigurované CMK, je k dispozici dvojité šifrování, které nabízí druhou vrstvu ochrany, a přitom vám umožní řídit šifrovací klíč pomocí Azure Key Vault.

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou k dispozici pro všechny cenové úrovně služby Translator. Pokud chcete požádat o možnost použití klíčů spravovaných zákazníkem, vydejte a odešlete [překladateli Customer-Managed klíč žádosti o klíč](https://aka.ms/cogsvc-cmk) , který bude trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou Translator budete muset vytvořit nový prostředek překladatele. Po vytvoření prostředku překladatele můžete k nastavení spravované identity použít Azure Key Vault.

Pomocí těchto kroků povolíte klíčům spravovaným zákazníkem pro překladatele:

1. Vytvořte svůj nový místní Překladatel nebo místní Cognitive Services prostředek. Tato akce nebude fungovat s globálním prostředkem.
2. V Azure Portal povolenou spravovanou identitu a přidejte informace o klíčích spravované zákazníkem.
3. Vytvořte nový pracovní prostor ve vlastním překladateli a přidružte informace k tomuto předplatnému.

### <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

K ukládání klíčů spravovaných zákazníkem musíte použít službu Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek Cognitive Services a trezor klíčů musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nový prostředek Cognitive Services je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. Klíče spravované zákazníkem není možné povolit v době, kdy se prostředek vytvořil. Klíče spravované zákazníkem jsou uloženy v Azure Key Vault a trezor klíčů musí být zřízen pomocí zásad přístupu, které udělují klíčová oprávnění ke spravované identitě, která je přidružena k prostředku Cognitive Services. Spravovaná identita je k dispozici hned po vytvoření prostředku.

Informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Cognitive Services najdete v těchto tématech:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Cognitive Services šifrování z Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Povolením zákaznických klíčů taky povolíte spravovanou identitu přiřazenou systémem, což je funkce Azure AD. Po povolení spravované identity přiřazené systémem se tento prostředek zaregistruje ve službě Azure Active Directory. Po registraci bude spravované identitě udělen přístup k Key Vault vybranému během nastavení spravovaného klíče zákazníka. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)najdete v.

> [!IMPORTANT]
> Pokud zakážete spravované identity přiřazené systémem, odeberou se přístup k trezoru klíčů a veškerá data zašifrovaná pomocí klíčů zákazníka už nebudou dostupná. Jakékoli funkce závislé na těchto datech přestanou fungovat. Dojde také k nasazení všech modelů, které jste nasadili. Všechna nahraná data se odstraní z vlastního překladatele. Pokud se spravované identity znovu povolí, nebudeme model automaticky znovu nasazovat.

> [!IMPORTANT]
> Spravované identity v současné době nepodporují scénáře pro více adresářů. Při konfiguraci klíčů spravovaných zákazníkem v Azure Portal se spravovaná identita automaticky přiřadí v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře služby Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenáší do nového tenanta, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Cognitive Services šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Pokud se odstraní celý Trezor klíčů, vaše data se už nebudou zobrazovat a všechny vaše modely se nebudou nasazovat. Všechna nahraná data se odstraní z vlastního překladatele. 

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo rozhraní příkazového [řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v prostředku Cognitive Services a vaše modely nebudou nasazené, protože Cognitive Services šifrovací klíč není přístupný pomocí. Všechna nahraná data budou také odstraněna z vlastního překladatele.


## <a name="next-steps"></a>Další kroky

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
