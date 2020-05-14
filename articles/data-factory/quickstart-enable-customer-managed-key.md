---
title: Šifrovat Azure Data Factory pomocí klíče spravovaného zákazníkem
description: Vylepšení zabezpečení Data Factory pomocí Bring Your Own Key (BYOK)
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: 3933edff3730b9c16ea3c129890c1a7d66cf5215
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117821"
---
# <a name="enhance-data-factory-security-and-configure-customer-managed-keys-with-azure-key-vault"></a>Vylepšení zabezpečení Data Factory a konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory šifruje neaktivní uložená data, včetně definic entit, všech dat ukládaných do mezipaměti během probíhajících běhů a dat v mezipaměti pro náhled dat. Ve výchozím nastavení jsou data zašifrovaná pomocí náhodně generovaného klíče Microsoft-spravovaných, který je jednoznačně přiřazený k vaší datové továrně. V případě dalších bezpečnostních záruk teď můžete Bring Your Own Key (BYOK) povolit funkce klíčů spravovaných zákazníkem v Azure Data Factory. Když zadáte klíč spravovaný zákazníkem, Data Factory k šifrování zákaznických dat použít __jak__ systémový klíč továrny, tak i CMK. Chybějící, protože by došlo k odepření přístupu k datům a továrně.

K ukládání klíčů spravovaných zákazníkem se vyžaduje Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Trezor klíčů a Data Factory musí být ve stejném regionu Azure Active Directory (Azure AD) a ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

> [!NOTE]
> Klíč spravovaný zákazníkem se teď dá nakonfigurovat jenom pro prázdné Data Factory: žádná propojená služba, žádný kanál, žádné datové sady, nic. Zvažte možnost povolit klíč spravovaný zákazníkem ihned po vytvoření výrobního oddělení.

## <a name="about-customer-managed-keys"></a>O klíčích spravovaných zákazníkem

Následující diagram ukazuje, jak Data Factory používá Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

  ![Diagram znázorňující, jak jsou klíče spravované zákazníkem v Azure Data Factory fungovat](media/quickstart-enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k Data Factory
1. Správce Data Factory povoluje v továrně funkci klíče spravované zákazníkem
1. Data Factory používá spravovanou identitu, která je přidružená k továrně pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory
1. Data Factory zabalí šifrovací klíč pro vytváření do klíče zákazníka v Azure Key Vault
1. Pro operace čtení a zápisu Data Factory posílá žádosti, aby Azure Key Vault rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Předpoklady – konfigurace Azure Key Vault a generování klíčů

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Povolit obnovitelné odstranění a Nemazat při Azure Key Vault

Použití klíčů spravovaných zákazníkem s Data Factory vyžaduje, aby byly v Key Vault nastaveny dvě vlastnosti, __obnovitelné odstranění__ a __nemazatelné__. Tyto vlastnosti lze povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů. Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v částech s názvem _Povolení obnovitelného odstranění_ a _Povolení funkce vyprázdnit ochranu_ v jednom z následujících článků:

- [Použití obnovitelného odstranění s využitím PowerShellu](../key-vault/general/soft-delete-powershell.md)
- [Jak používat obnovitelné odstranění pomocí rozhraní příkazového řádku](../key-vault/general/soft-delete-cli.md)

Pokud vytváříte novou Azure Key Vault prostřednictvím Azure Portal, __obnovitelné odstranění__ a __Nemazat__ lze povolit následujícím způsobem:

  ![Snímek obrazovky s povolením obnovitelného odstranění a vymazání ochrany při vytváření Key Vault](media/quickstart-enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-key-vault"></a>Udělení přístupu Data Factory Key Vault

Ujistěte se, že Azure Key Vault a Azure Data Factory jsou ve stejném tenantovi Azure Active Directory (Azure AD) a ve _stejné oblasti_. Z Azure Key Vault řízení přístupu udělte přístup k Identita spravované služby (MSI) Datové továrny následujícím oprávněním: _Get_, _Unwrap Key_a _Wrap Key_. Tato oprávnění jsou nutná pro povolení klíčů spravovaných zákazníkem v Data Factory.

  ![Snímek obrazovky povolení Data Factoryho přístupu k Key Vault](media/quickstart-enable-customer-managed-key/02-access-policy-factory-msi.png)

### <a name="generate-or-upload-customer-managed-key-to-key-vault"></a>Vygenerujte nebo nahrajte klíč spravovaný zákazníkem pro Key Vault

Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Data Factory šifrování podporují jenom 2048 klíčů RSA. Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).

  ![Snímek, který vygeneruje spravovaný klíč zákazníka](media/quickstart-enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

1. Ujistěte se, že je Data Factory prázdné: žádná propojená služba, žádný kanál ani žádná sada dat není žádná. V současné době nasazení klíče spravovaného zákazníkem do neprázdné továrny způsobí chybu.

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte na Azure Key Vault a vyberte nastavení klíče. Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče pro zobrazení nastavení.

1. Zkopírujte hodnotu pole identifikátor klíče, které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče z Key Vault](media/quickstart-enable-customer-managed-key/04-get-key-uri.png)

1. Spustit portál Azure Data Factory a použít navigační panel vlevo, přejít na Data Factory domovskou stránku

1. Klikněte na ikonu __Customer spravovaných Key__ .

    ![Snímek obrazovky s povolením spravovaného klíče zákazníka v Data Factory](media/quickstart-enable-customer-managed-key/05-cmk-configuration.png)

1. Zadejte identifikátor URI pro klíč spravovaný zákazníkem, který jste zkopírovali dříve.

1. Klikněte na __Uložit__ a pro Data Factory je povolené šifrování klíče Customer-spravovaných.

## <a name="update-key-version"></a>Aktualizovat verzi klíče

Když vytváříte novou verzi klíče, aktualizujte datovou továrnu, aby používala novou verzi. Použijte podobný postup, jak je popsáno v části _Povolení klíčů spravovaných zákazníkem_, včetně:

1. Vyhledání identifikátoru URI pro novou verzi klíče prostřednictvím portálu Azure Key Vault

1. Přejít na nastavení __klíče spravovaného zákazníkem__

1. Nahradit a vložit identifikátor URI pro nový klíč

1. Klikněte na __Uložit__ a Data Factory se teď zašifruje s novou verzí klíče.

## <a name="use-a-different-key"></a>Použít jiný klíč

Chcete-li změnit klíč používaný k šifrování Data Factory, je nutné ručně aktualizovat nastavení v Data Factory. Použijte podobný postup, jak je popsáno v části _Povolení klíčů spravovaných zákazníkem_, včetně:

1. Vyhledání identifikátoru URI pro nový klíč pomocí Azure Key Vaultového portálu

1. Přejít na nastavení __klíče Customer spravovaných__

1. Nahradit a vložit identifikátor URI pro nový klíč

1. Klikněte na __Uložit__ a Data Factory teď bude zašifrovat pomocí nového klíče.

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Po povolení funkce klíče spravovaného zákazníkem podle návrhu už nemůžete odebrat speciální krok zabezpečení. Pro šifrování továrny a dat vždycky očekáváme klíč poskytnutý zákazníkem.

## <a name="next-steps"></a>Další kroky

Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích.
