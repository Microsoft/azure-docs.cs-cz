---
title: Šifrovat Azure Data Factory pomocí klíče spravovaného zákazníkem
description: Vylepšení zabezpečení Data Factory pomocí Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: c6c376e44c6135a800e6f7e281f8ea85b828329a
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443864"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Šifrování Azure Data Factory pomocí klíčů spravovaných zákazníkem

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory šifruje neaktivní uložená data, včetně definic entit a všech dat uložených v mezipaměti v průběhu probíhajících běhů. Ve výchozím nastavení jsou data šifrována pomocí náhodně generovaného klíče spravovaného společností Microsoft, který je jednoznačně přiřazen k vaší datové továrně. V případě dalších záruk zabezpečení teď můžete Bring Your Own Key (BYOK) povolit funkce klíčů spravovaných zákazníkem v Azure Data Factory. Když zadáte klíč spravovaný zákazníkem, Data Factory k šifrování zákaznických dat použít __jak__ systémový klíč továrny, tak i CMK. Chybějící, protože by došlo k odepření přístupu k datům a továrně.

K ukládání klíčů spravovaných zákazníkem se vyžaduje Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Trezor klíčů a Data Factory musí být ve stejném regionu Azure Active Directory (Azure AD) a ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>O klíčích spravovaných zákazníkem

Následující diagram ukazuje, jak Data Factory používá Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagram znázorňující fungování klíčů spravovaných zákazníkem v Azure Data Factory.":::

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k Data Factory
1. Správce Data Factory povoluje v továrně funkci klíče spravované zákazníkem
1. Data Factory používá spravovanou identitu, která je přidružená k továrně pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory
1. Data Factory zabalí šifrovací klíč pro vytváření do klíče zákazníka v Azure Key Vault
1. Pro operace čtení a zápisu Data Factory posílá žádosti, aby Azure Key Vault rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

Existují dva způsoby, jak přidat šifrování spravovaného klíče zákazníka do datových továrn. Jedna je během vytváření výrobního prostředí v Azure Portal a druhá je po vytvoření výrobního prostředí v uživatelském rozhraní Data Factory.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Předpoklady – konfigurace Azure Key Vault a generování klíčů

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Povolit obnovitelné odstranění a Nemazat při Azure Key Vault

Použití klíčů spravovaných zákazníkem s Data Factory vyžaduje, aby byly v Key Vault nastaveny dvě vlastnosti, __obnovitelné odstranění__ a __nemazatelné__. Tyto vlastnosti lze povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů. Informace o tom, jak tyto vlastnosti v existujícím trezoru klíčů povolit, najdete v tématu [Azure Key Vault správy obnovení s ochranou pomocí obnovitelného odstranění a vyprázdnění](../key-vault/general/key-vault-recovery.md) .

Pokud vytváříte novou Azure Key Vault prostřednictvím Azure Portal, __obnovitelné odstranění__ a __Nemazat__ lze povolit následujícím způsobem:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Snímek obrazovky, který ukazuje, jak povolit ochranu před odstraněním a vyprázdněním ochrany při vytváření Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Udělení přístupu Data Factory Azure Key Vault

Ujistěte se, že Azure Key Vault a Azure Data Factory jsou ve stejném tenantovi Azure Active Directory (Azure AD) a ve _stejné oblasti_. Z Azure Key Vault řízení přístupu udělte data továrně následující oprávnění: _získat_, _Rozbalit klíč_ a _zabalit klíč_. Tato oprávnění jsou nutná pro povolení klíčů spravovaných zákazníkem v Data Factory.

* Pokud chcete přidat šifrování spravovaného klíče zákazníka [po vytvoření továrny v Data Factory uživatelském rozhraní](#post-factory-creation-in-data-factory-ui), ujistěte se, že identita spravované služby (MSI) objektu pro vytváření dat má tato tři oprávnění k Key Vault
* Pokud chcete přidat šifrování spravovaného klíče zákazníka [během doby vytváření továrny Azure Portal](#during-factory-creation-in-azure-portal), ujistěte se, že uživatelem přiřazená identita (UA-mi) má tato tři oprávnění pro Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Snímek obrazovky ukazující, jak povolit Data Factory přístup k Key Vault":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Vygenerujte nebo nahrajte klíč spravovaný zákazníkem pro Azure Key Vault

Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů. Můžete také použít rozhraní API Azure Key Vault k vygenerování klíčů. Data Factory šifrování podporují jenom 2048 klíčů RSA. Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Snímek obrazovky ukazující, jak vygenerovat Customer-Managed klíč":::

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

### <a name="post-factory-creation-in-data-factory-ui"></a>Vytvoření po vytvoření továrny v uživatelském rozhraní Data Factory

Tato část vás provede procesem přidání šifrování spravovaného klíče zákazníka v Data Factory uživatelském rozhraní _po_ vytvoření továrny.

> [!NOTE]
> Klíč spravovaný zákazníkem se dá nakonfigurovat jenom pro prázdnou datovou továrnu. Datová továrna nemůže obsahovat žádné prostředky, jako jsou propojené služby, kanály a toky dat. Doporučuje se povolit klíč spravovaný zákazníkem hned po vytvoření výrobního oddělení.

> [!IMPORTANT]
> Tento přístup nefunguje se spravovanými továrnami s povolenými virtuálními sítěmi. Pokud chcete tyto továrny šifrovat, zvažte prosím [alternativní trasu](#during-factory-creation-in-azure-portal).

1. Ujistěte se, že Identita spravované služby (MSI) služby Data Factory má Key Vault oprávnění _získat_, _Rozbalit klíč_ a _zabalit klíč_ .

1. Ujistěte se, že je Data Factory prázdné. Datová továrna nemůže obsahovat žádné prostředky, jako jsou propojené služby, kanály a toky dat. V současné době nasazení klíče spravovaného zákazníkem do neprázdné továrny způsobí chybu.

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte na Azure Key Vault a vyberte nastavení klíče. Vyberte požadovaný klíč a potom vyberte klíč, ve kterém chcete zobrazit jeho verze. Vyberte verzi klíče pro zobrazení nastavení.

1. Zkopírujte hodnotu pole identifikátor klíče, které poskytuje snímek obrazovky s IDENTIFIKÁTORem URI :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="pro získání identifikátoru URI klíče z Key Vault.":::

1. Spusťte Azure Data Factory portál a pomocí navigačního panelu vlevo přejděte na Data Factory Portál pro správu

1. Klikněte na ikonu __klíč zákazníka spravovaných__ :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="snímek obrazovky, jak povolit klíč spravovaný zákazníkem v uživatelském rozhraní Data Factory.":::

1. Zadejte identifikátor URI pro klíč spravovaný zákazníkem, který jste zkopírovali dříve.

1. Klikněte na __Uložit__ a pro Data Factory je povolené šifrování klíče Customer-spravovaných.

### <a name="during-factory-creation-in-azure-portal"></a>Při vytváření továrny v Azure Portal

Tato část vás provede kroky pro přidání šifrování spravovaného klíče zákazníka v Azure Portal _během_ nasazování továrny.

Aby bylo možné objekt pro vytváření šifrovat, Data Factory musí nejdřív načíst klíč spravovaný zákazníkem z Key Vault. Vzhledem k tomu, že nasazení továrny stále probíhá, Identita spravované služby (MSI) ještě není k dispozici pro ověřování pomocí Key Vault. Pro použití tohoto přístupu zákazník potřebuje k datové továrně přiřadit spravovanou identitu přiřazenou uživatelem (UA-MI). Budeme předpokládat role definované v UA-MI a ověřit pomocí Key Vault.

Další informace o spravované identitě přiřazené uživateli najdete v tématu [spravované](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) identity a [přiřazování rolí pro spravovanou identitu přiřazenou uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Ujistěte se, že uživatelem přiřazená identita (UA-MI) má oprávnění _získat_, _Rozbalit klíč_ a _zabalit klíč_ , aby Key Vault

1. Na kartě __Upřesnit__ zaškrtněte políčko _Povolit šifrování pomocí obrazovky se spravovaným klíčem zákazníka_ na 
   :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="kartě Upřesnit pro prostředí vytváření datových továrn v Azure Portal.":::

1. Zadejte adresu URL pro spravovaný klíč zákazníka uloženou v Key Vault

1. Pro ověření pomocí Key Vault vyberte příslušnou spravovanou identitu přiřazenou uživatelem.

1. Pokračovat s nasazením továrny

## <a name="update-key-version"></a>Aktualizovat verzi klíče

Když vytváříte novou verzi klíče, aktualizujte datovou továrnu, aby používala novou verzi. Použijte podobný postup, jak je popsáno v části [Data Factory uživatelského rozhraní](#post-factory-creation-in-data-factory-ui), včetně:

1. Vyhledání identifikátoru URI pro novou verzi klíče prostřednictvím portálu Azure Key Vault

1. Přejít na nastavení __klíče spravovaného zákazníkem__

1. Nahradit a vložit identifikátor URI pro nový klíč

1. Klikněte na __Uložit__ a Data Factory se teď zašifruje s novou verzí klíče.

## <a name="use-a-different-key"></a>Použít jiný klíč

Chcete-li změnit klíč používaný k šifrování Data Factory, je nutné ručně aktualizovat nastavení v Data Factory. Použijte podobný postup, jak je popsáno v části [Data Factory uživatelského rozhraní](#post-factory-creation-in-data-factory-ui), včetně:

1. Vyhledání identifikátoru URI pro nový klíč pomocí Azure Key Vaultového portálu

1. Přejít na nastavení __klíče Customer spravovaných__

1. Nahradit a vložit identifikátor URI pro nový klíč

1. Klikněte na __Uložit__ a Data Factory teď bude zašifrovat pomocí nového klíče.

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Po povolení funkce klíče spravovaného zákazníkem podle návrhu už nemůžete odebrat speciální krok zabezpečení. Pro šifrování továrny a dat vždycky očekáváme klíč poskytnutý zákazníkem.

## <a name="next-steps"></a>Další kroky

Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích.