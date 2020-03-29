---
title: Překladač šifrování dat v klidovém stavu
titleSuffix: Azure Cognitive Services
description: Překladač šifrování dat v klidovém stavu.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372235"
---
# <a name="translator-encryption-of-data-at-rest"></a>Překladač šifrování dat v klidovém stavu

Překladač automaticky šifruje vaše data, která nahrajete za účelem vytvoření vlastních modelů překladů, když jsou v cloudu trvalá, což pomáhá splnit cíle organizace v oblasti zabezpečení a dodržování předpisů.

## <a name="about-cognitive-services-encryption"></a>Šifrování služeb Cognitive Services

Data jsou šifrována a dešifrována pomocí [256bitového](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) šifrování AES kompatibilního se [standardem FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Šifrování a dešifrování jsou transparentní, což znamená, že šifrování a přístup jsou spravovány za vás. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód nebo aplikace, abyste využili výhod šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení vaše předplatné používá šifrovací klíče spravované společností Microsoft. Pokud používáte cenovou úroveň, která podporuje klíče spravované zákazníkem, uvidíte nastavení šifrování pro váš prostředek v části **Šifrování** [na webu Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/encryptionblade.png)

U předplatných, která podporují pouze šifrovací klíče spravované společností Microsoft, nebudete mít oddíl **Šifrování.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s trezorem klíčů Azure

K dispozici je také možnost spravovat předplatné pomocí vlastních klíčů. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou k dispozici pro všechny cenové úrovně pro službu Překladač. Chcete-li požádat o možnost používat klíče spravované zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem](https://aka.ms/cogsvc-cmk) Bude trvat přibližně 3-5 pracovních dnů, než se dozvíte o stavu vaší žádosti. V závislosti na poptávce můžete být umístěni do fronty a schváleni, jakmile bude k dispozici místo. Po schválení pro použití CMK se službou Překladač budete muset vytvořit nový prostředek Překladač. Po vytvoření prostředku Překladač můžete použít Azure Key Vault nastavit spravovanou identitu.

Chcete-li povolit klíče spravované zákazníkem pro překladač, postupujte takto:

1. Vytvořte nový regionální překladač text nebo regionální kognitivní služby zdroje. To nebude fungovat s globálním zdrojem.
2. Povolená spravovaná identita na webu Azure portal a přidejte informace o klíči spravovanézákazníkem.
3. Vytvořte nový pracovní prostor v překladači vlastní a přidružte tyto informace o předplatném.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

K ukládání klíčů spravovaných zákazníky je nutné použít azure key vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Prostředek služeb Cognitive Services a trezor klíčů musí být ve stejné oblasti a ve stejném tenantovi Služby Azure Active Directory (Azure AD), ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nový prostředek služeb Cognitive Services je vždy šifrován pomocí klíčů spravovaných společností Microsoft. Není možné povolit klíče spravované zákazníkem v době vytvoření prostředku. Klíče spravované zákazníkem jsou uloženy v trezoru klíčů Azure a trezor klíčů musí být zřízen zásadami přístupu, které udělují oprávnění klíče spravované identitě, která je přidružena k prostředku služeb Cognitive Services. Spravovaná identita je k dispozici ihned po vytvoření prostředku.

Informace o tom, jak používat klíče spravované zákazníky pomocí šifrování Azure Key Vault for Cognitive Services, najdete v tématu:

- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro kognitivní služby šifrování z portálu Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Povolení klíčů spravovaných zákazníky také umožní systém přiřazenou spravovanou identitu, což je funkce Azure AD. Po povolení systému přiřazené spravované identity bude tento prostředek zaregistrován ve službě Azure Active Directory. Po registraci bude spravovaná identita mít přístup k trezoru klíčů vybranému během nastavení klíče spravovaného zákazníkem. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Pokud zakážete systém přiřazené spravované identity, bude přístup k trezoru klíčů odebrán a všechna data zašifrovaná pomocí klíčů zákazníků již nebudou přístupná. Všechny funkce závislé na těchto datech přestanou fungovat. Všechny modely, které jste nasadili bude také undeployed. Všechna nahraná data budou z vlastního překladače smazána. Pokud jsou spravované identity znovu povoleny, model za vás automaticky znovu nenasadíme.

> [!IMPORTANT]
> Spravované identity aktuálně nepodporují scénáře mezi adresáři. Když nakonfigurujete klíče spravované zákazníky na webu Azure Portal, spravovaná identita se automaticky přiřadí pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete **v tématu Přenos předplatného mezi adresáři Azure AD** v [častých dotazech a známými problémy se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníky v trezoru klíčů Azure

Chcete-li povolit klíče spravované zákazníkem, musíte k uložení klíčů použít trezor klíčů Azure. Je nutné povolit vlastnosti **Obnovitelné odstranění** a **Nevyčistit** v trezoru klíčů.

Šifrováním služeb Cognitive Services jsou podporovány pouze klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Pokud je odstraněn celý trezor klíčů, data se již nebudou zobrazovat a všechny modely nebudou nenasazené. Všechna nahraná data budou z vlastního překladače smazána. 

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, použijte PowerShell nebo Azure CLI. Další informace najdete [v tématu Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Zrušení přístupu účinně blokuje přístup ke všem datům v prostředku služeb Cognitive Services a vaše modely budou nenasazené, protože šifrovací klíč je nepřístupný službou Cognitive Services. Všechna nahraná data budou také odstraněna z vlastního překladače.


## <a name="next-steps"></a>Další kroky

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
