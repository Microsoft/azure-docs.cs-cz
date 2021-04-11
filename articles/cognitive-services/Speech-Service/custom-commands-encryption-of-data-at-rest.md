---
title: Vlastní příkazy služby šifrování neaktivních dat
titleSuffix: Azure Cognitive Services
description: Vlastní příkazy šifrování dat v klidovém znění.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716580"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Šifrování vlastních příkazů pro neaktivní uložená data

Vlastní příkazy automaticky šifrují vaše data při trvalém ukládání do cloudu. Šifrování služby pro vlastní příkazy chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů v organizaci.

> [!NOTE]
> Služba Custom Commands (vlastní příkazy) automaticky nepovoluje šifrování pro prostředky LUIS přidružené k vaší aplikaci. V případě potřeby musíte povolit šifrování prostředku LUIS z [tohoto místa](../luis/encrypt-data-at-rest.md).

## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services
Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou zabezpečená ve výchozím nastavení, a abyste mohli využívat šifrování, nemusíte upravovat kód ani aplikace.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Když použijete vlastní příkazy, služba řeči bude ukládat následující data v cloudu:
* Konfigurace JSON za aplikací Custom Commands
* Klíč pro vytváření a předpověď LUIS

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. Své předplatné však můžete spravovat také s využitím vlastních šifrovacích klíčů. Klíče spravované zákazníkem (CMK), označované také jako klíče BYOK (Bring Your Own Key), nabízí větší flexibilitu při vytváření, obměně, zakazování a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat.


> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 27. června 2020. Pokud chcete používat CMK se službami Speech, budete muset vytvořit nový prostředek řeči. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete formulář žádosti o klíč Customer-Managed. Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou Speech Services budete muset vytvořit nový prostředek řeči z Azure Portal.
   > [!NOTE]
   > **Klíče spravované zákazníkem (CMK) jsou podporovány pouze pro vlastní příkazy.**
   >
   >  **Custom Speech a vlastní hlas stále podporují pouze vlastní úložiště (BYOS).**  [Další informace](speech-encryption-of-data-at-rest.md)
   >
   > Pokud používáte daný prostředek řeči pro přístup k této službě, musí být požadavky na dodržování předpisů splněné explicitně konfigurací BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s využitím Azure Key Vaultu

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek řeči a trezor klíčů musí být ve stejné oblasti a v rámci stejného Azure Active Directory klienta (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

Když se vytvoří nový prostředek pro rozpoznávání řeči, který se použije ke zřízení aplikace Custom Commands – data se vždycky šifrují pomocí klíčů spravovaných Microsoftem. Klíče spravované zákazníkem není možné povolit v době, kdy se prostředek vytvořil. Klíče spravované zákazníkem jsou uloženy v Azure Key Vault a trezor klíčů musí být zřízen pomocí zásad přístupu, které udělují klíčová oprávnění ke spravované identitě, která je přidružena k prostředku Cognitive Services. Spravovaná identita je k dispozici až po vytvoření prostředku pomocí cenové úrovně vyžadované pro CMK.

Povolením zákaznických klíčů taky povolíte [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md)přiřazenou systémem, což je funkce Azure AD. Po povolení spravované identity přiřazené systémem se tento prostředek zaregistruje ve službě Azure Active Directory. Po registraci bude spravované identitě udělen přístup k Key Vault vybranému během nastavení spravovaného klíče zákazníka. 

> [!IMPORTANT]
> Pokud zakážete spravované identity přiřazené systémem, odeberou se přístup k trezoru klíčů a veškerá data zašifrovaná pomocí klíčů zákazníka už nebudou dostupná. Jakékoli funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity v současné době nepodporují scénáře pro více adresářů. Při konfiguraci klíčů spravovaných zákazníkem v Azure Portal se spravovaná identita automaticky přiřadí v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře služby Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenáší do nového tenanta, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Použití klíčů spravovaných zákazníkem vyžaduje, aby byly v trezoru klíčů nastaveny dvě vlastnosti, **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale můžete je povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů.

> [!IMPORTANT]
> Pokud nemáte k dispozici možnost **obnovitelné odstranění** a **Nemazat** vlastnosti a odstraníte klíč, nebudete moci obnovit data v prostředku služby vnímání.

Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v částech s názvem **Povolení obnovitelného odstranění** a **Povolení funkce vyprázdnit ochranu** v jednom z následujících článků:

- [Jak používat obnovitelné odstranění pomocí prostředí PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Jak používat obnovitelné odstranění pomocí rozhraní](../../key-vault/general/key-vault-recovery.md)PŘÍKAZového řádku

Azure Storage šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Povolení klíčů spravovaných zákazníkem pro váš prostředek řeči

Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte ke zdroji řeči.
1. V okně **Nastavení** pro prostředek řeči klikněte na **šifrování**. Vyberte možnost **spravované klíče zákazníka** , jak je znázorněno na následujícím obrázku.

 ![Snímek obrazovky ukazující, jak vybrat spravované klíče zákazníka](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Zadat klíč

Po povolení klíčů spravovaných zákazníkem budete mít možnost zadat klíč, který chcete přidružit k prostředku Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Zadat klíč jako identifikátor URI

Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče a zobrazte nastavení této verze.
1. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](../media/cognitive-services-encryption/key-uri-portal.png)

1. V nastavení **šifrování** pro službu rozpoznávání řeči klikněte na možnost **zadat identifikátor URI klíče** .
1. Vložte identifikátor URI, který jste zkopírovali do pole **identifikátor URI klíče** .

1. Zadejte předplatné, které obsahuje Trezor klíčů.
1. Uložte provedené změny.

### <a name="specify-a-key-from-a-key-vault"></a>Zadat klíč z trezoru klíčů

Pokud chcete zadat klíč z trezoru klíčů, nejdřív se ujistěte, že máte Trezor klíčů, který obsahuje klíč. Pokud chcete zadat klíč z trezoru klíčů, použijte následující postup:

1. Zvolte možnost **vybrat z Key Vault** .
1. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky s možností klíče spravovaného zákazníkem](media/custom-commands/configure-cmk-fromKV.png)

1. Uložte provedené změny.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, aktualizujte prostředek řeči tak, aby používal novou verzi. Postupujte takto:

1. Přejděte ke zdroji řeči a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.
1. Uložte provedené změny.

## <a name="use-a-different-key"></a>Použít jiný klíč

Pokud chcete změnit klíč, který se používá pro šifrování, postupujte podle těchto kroků:

1. Přejděte ke zdroji řeči a zobrazte nastavení **šifrování** .
1. Zadejte identifikátor URI nového klíče. Alternativně můžete vybrat Trezor klíčů a zvolit nový klíč.
1. Uložte provedené změny.

## <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Když je klíč otočen, je nutné aktualizovat prostředek řeči tak, aby používal nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče v Azure Portal, najdete v tématu [aktualizace verze klíče](#update-the-key-version).

Otočení klíče neaktivuje opětovné šifrování dat v prostředku. Od uživatele není vyžadována žádná další akce.

## <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](/powershell/module/az.keyvault//) nebo rozhraní příkazového [řádku Azure Key Vault](/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v prostředku Cognitive Services, protože šifrovací klíč není přístupný Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš prostředek řeči se pak zašifruje pomocí klíčů spravovaných Microsoftem. K zakázání klíčů spravovaných zákazníkem použijte tento postup:

1. Přejděte ke zdroji řeči a zobrazte nastavení **šifrování** .
1. Zrušte zaškrtnutí políčka u nastavení **použít vlastní klíč** .

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč Customer-Managed řeči](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](../../key-vault/general/overview.md)
* [Co jsou spravované identity](../../active-directory/managed-identities-azure-resources/overview.md)