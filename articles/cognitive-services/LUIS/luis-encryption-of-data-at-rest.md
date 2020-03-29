---
title: Šifrování dat v klidovém stavu služby Language Understanding
titleSuffix: Azure Cognitive Services
description: Šifrování dat v klidovém stavu služby Language Understanding
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372334"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Šifrování dat v klidovém stavu služby Language Understanding

Služba Language Understanding automaticky šifruje vaše data, když jsou v cloudu trvalá. Šifrování služby Language Understanding service chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů.

## <a name="about-cognitive-services-encryption"></a>Šifrování služeb Cognitive Services

Data jsou šifrována a dešifrována pomocí [256bitového](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) šifrování AES kompatibilního se [standardem FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Šifrování a dešifrování jsou transparentní, což znamená, že šifrování a přístup jsou spravovány za vás. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód nebo aplikace, abyste využili výhod šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení vaše předplatné používá šifrovací klíče spravované společností Microsoft. K dispozici je také možnost spravovat předplatné pomocí vlastních klíčů. Klíče spravované zákazníkem (CMK) nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s trezorem klíčů Azure

K dispozici je také možnost spravovat předplatné pomocí vlastních klíčů. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.

K ukládání klíčů spravovaných zákazníky je nutné použít azure key vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Prostředek služeb Cognitive Services a trezor klíčů musí být ve stejné oblasti a ve stejném tenantovi Služby Azure Active Directory (Azure AD), ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Klíče spravované zákazníkem pro porozumění jazykům

Chcete-li požádat o možnost používat klíče spravované zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravované službou LUIS Service](https://aka.ms/cogsvc-cmk). Bude trvat přibližně 3-5 pracovních dnů, než se dozvíte o stavu vaší žádosti. V závislosti na poptávce můžete být umístěni do fronty a schváleni, jakmile bude k dispozici místo. Po schválení pro použití CMK s LUIS, budete muset vytvořit nový prostředek language understanding z portálu Azure a vyberte E0 jako cenovou úroveň. Nová skladová položka bude fungovat stejně jako skladová položka F0, která je již k dispozici s výjimkou cmk. Uživatelé nebudou moci upgradovat z F0 na novou sku E0.

E0 prostředky jsou k dispozici pouze pro vytváření služby a že úroveň E0 bude zpočátku podporována pouze v oblasti západní USA.

![Obrázek předplatného LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou v současné době k dispozici v oblasti **USA – západ.**

### <a name="limitations"></a>Omezení

Existují určitá omezení při použití vrstvy E0 s existujícími nebo dříve vytvořenými aplikacemi:

* Migrace na prostředek E0 bude blokována. Uživatelé budou moci migrovat své aplikace pouze do prostředků F0. Po migraci existujícího prostředku do F0 můžete vytvořit nový prostředek ve vrstvě E0. Další informace o [migraci](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)naleznete zde .  
* Přesunutí aplikací do nebo z prostředku E0 bude blokováno. Řešení tohoto omezení je exportovat existující aplikaci a importovat ji jako prostředek E0.
* Funkce kontrola pravopisu Bingu není podporována.
* Protokolování přenosů koncových uživatelů je zakázáno, pokud je vaše aplikace E0.
* Funkce priming řeči ze služby Azure Bot není podporována pro aplikace na úrovni E0. Tato funkce je dostupná prostřednictvím služby Azure Bot Service, která cmk nepodporuje.
* Funkce naplnění řeči z portálu vyžaduje Azure Blob Storage. Další informace naleznete v [tématu přineste si vlastní úložiště](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

Nový prostředek služeb Cognitive Services je vždy šifrován pomocí klíčů spravovaných společností Microsoft. Není možné povolit klíče spravované zákazníkem v době vytvoření prostředku. Klíče spravované zákazníkem jsou uloženy v trezoru klíčů Azure a trezor klíčů musí být zřízen zásadami přístupu, které udělují oprávnění klíče spravované identitě, která je přidružena k prostředku služeb Cognitive Services. Spravovaná identita je k dispozici pouze po vytvoření prostředku pomocí cenové úrovně pro CMK.

Informace o tom, jak používat klíče spravované zákazníky pomocí šifrování Azure Key Vault for Cognitive Services, najdete v tématu:

- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro kognitivní služby šifrování z portálu Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Povolení klíčů spravovaných zákazníky také umožní systém přiřazenou spravovanou identitu, což je funkce Azure AD. Po povolení systému přiřazené spravované identity bude tento prostředek zaregistrován ve službě Azure Active Directory. Po registraci bude spravovaná identita mít přístup k trezoru klíčů vybranému během nastavení klíče spravovaného zákazníkem. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Pokud zakážete systém přiřazené spravované identity, bude přístup k trezoru klíčů odebrán a všechna data zašifrovaná pomocí klíčů zákazníků již nebudou přístupná. Všechny funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity aktuálně nepodporují scénáře mezi adresáři. Když nakonfigurujete klíče spravované zákazníky na webu Azure Portal, spravovaná identita se automaticky přiřadí pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete **v tématu Přenos předplatného mezi adresáři Azure AD** v [častých dotazech a známými problémy se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníky v trezoru klíčů Azure

Chcete-li povolit klíče spravované zákazníkem, musíte k uložení klíčů použít trezor klíčů Azure. Je nutné povolit vlastnosti **Obnovitelné odstranění** a **Nevyčistit** v trezoru klíčů.

Šifrováním služeb Cognitive Services jsou podporovány pouze klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Otočení klíčů spravovaných zákazníkem

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Při otočení klíče je nutné aktualizovat prostředek služeb Cognitive Services, aby bylo nutné použít nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče na webu Azure Portal, najdete v části **Aktualizace verze klíče** v [tématu Konfigurace klíčů spravovaných zákazníky pro služby Cognitive Services pomocí portálu Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

Otočením klíče neaktivujete opětovné šifrování dat v prostředku. Neexistuje žádná další akce vyžaduje od uživatele.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, použijte PowerShell nebo Azure CLI. Další informace najdete [v tématu Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Zrušení přístupu účinně blokuje přístup ke všem datům v prostředku služeb Cognitive Services, protože šifrovací klíč není pro služby Cognitive Services přístupný.

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravované zákazníkem služby LUIS](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
