---
title: Použití klíčů spravovaných zákazníkem k šifrování konfiguračních dat
description: Šifrování konfiguračních dat pomocí klíčů spravovaných zákazníkem
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930496"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Použití klíčů spravovaných zákazníkem k šifrování dat konfigurace aplikace
Konfigurace aplikace Azure [šifruje citlivé informace v klidovém umístění](../security/fundamentals/encryption-atrest.md). Použití klíčů spravovaných zákazníkem poskytuje rozšířenou ochranu dat tím, že vám umožní spravovat šifrovací klíče.  Při použití spravovaného šifrování klíčů se všechny citlivé informace v konfiguraci aplikace šifrují pomocí Azure Key Vaultho klíče zadaného uživatelem.  Tato možnost umožňuje otočit šifrovací klíč na vyžádání.  Také umožňuje odvolat přístup ke konfiguraci aplikací Azure pro citlivé informace tím, že odvolává přístup instance konfigurace aplikace k tomuto klíči.

## <a name="overview"></a>Přehled 
Konfigurace aplikací Azure šifruje citlivé informace v klidovém formátu pomocí 256 šifrovacího klíče AES, který poskytuje Microsoft. Každá instance konfigurace aplikace má vlastní šifrovací klíč, který spravuje služba a používá se k šifrování citlivých informací. Citlivé informace obsahují hodnoty nalezené v páru klíč-hodnota.  Pokud je povolená klíčová funkce spravovaná zákazníkem, používá konfigurace aplikace spravovanou identitu přiřazenou ke konfigurační instanci aplikace k ověření pomocí Azure Active Directory. Spravovaná identita pak zavolá Azure Key Vault a zabalí šifrovací klíč instance konfigurace aplikace. Zabalené šifrovací klíče se pak uloží a nezabalený šifrovací klíč se uloží do mezipaměti v konfiguraci aplikace po dobu jedné hodiny. Konfigurace aplikace aktualizuje nezabalenou verzi šifrovacího klíče instance aplikace každou hodinu. Tím zajistíte dostupnost za normálních provozních podmínek. 

>[!IMPORTANT]
> Pokud identita přiřazená instanci konfigurace aplikace už nemá oprávnění k rozbalení šifrovacího klíče instance, nebo pokud se spravovaný klíč trvale odstraní, pak už nebude možné dešifrovat citlivé informace uložené v instanci konfigurace aplikace. Použití funkce [obnovitelného odstranění](../key-vault/general/soft-delete-overview.md) Azure Key Vault snižuje riziko nechtěného odstranění šifrovacího klíče.

Když uživatelé povolí funkci spravovaného klíče zákazníka na své instanci konfigurace aplikace Azure, budou řídit schopnost služby přistupovat k citlivým informacím. Spravovaný klíč slouží jako kořenový šifrovací klíč. Uživatel může u své instance aplikace odvolat přístup ke svému spravovanému klíči změnou zásad přístupu trezoru klíčů. Když se tento přístup odvolá, konfigurace aplikace ztratí možnost dešifrovat data uživatelů během jedné hodiny. V tomto okamžiku instance konfigurace aplikace zakazuje všechny pokusy o přístup. Tato situace je obnovitelná tím, že službu udělí přístup ke spravovanému klíči znovu.  Během jedné hodiny bude konfigurace aplikace schopná dešifrovat uživatelská data a fungovat za běžných podmínek.

>[!NOTE]
>Všechna data konfigurace aplikace Azure jsou uložená po dobu až 24 hodin v izolované záloze. To zahrnuje nezabalený šifrovací klíč. Tato data nejsou okamžitě k dispozici týmu služby nebo služby. V případě nouzového obnovení bude konfigurace aplikace Azure znovu odvolat data ze spravovaného klíče.

## <a name="requirements"></a>Požadavky
Aby bylo možné úspěšně povolit klíčovou funkci spravovanou zákazníkům pro konfiguraci aplikace Azure, musí být k následující komponenty:
- Instance konfigurace aplikace Azure úrovně Standard
- Azure Key Vault se zapnutou funkcí ochrany před příčtením a vymazáním
- Klíč RSA nebo RSA-HSM v rámci Key Vault
    - Klíč nesmí mít vypršení platnosti, musí být povolen a musí mít povolené zalamování a rozbalení možností.

Po nakonfigurování těchto zdrojů zůstanou dva kroky, aby konfigurace aplikace Azure používala klíč Key Vault:
1. Přiřazení spravované identity k instanci konfigurace aplikace Azure
2. Udělte identitě `GET` , `WRAP` a `UNWRAP` oprávněním v zásadách přístupu k cílovému Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Povolení šifrování klíčů spravovaného zákazníkem pro instanci konfigurace aplikace Azure
Začněte tím, že budete potřebovat správnou nakonfigurovanou instanci konfigurace aplikace Azure. Pokud ještě nemáte k dispozici instanci konfigurace aplikace, postupujte podle jednoho z těchto rychlých startů a nastavte jednu z nich:
- [Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure](quickstart-aspnet-core-app.md)
- [Vytvoření aplikace .NET Core pomocí konfigurace aplikace Azure](quickstart-dotnet-core-app.md)
- [Vytvoření aplikace .NET Framework s využitím konfigurace aplikace Azure](quickstart-dotnet-app.md)
- [Vytvoření aplikace pružiny v jazyce Java pomocí konfigurace aplikace Azure](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění instrukcí příkazového řádku v tomto článku.  Má předinstalované běžné nástroje Azure, včetně .NET Core SDK. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

### <a name="create-and-configure-an-azure-key-vault"></a>Vytvoření a konfigurace Azure Key Vault
1. Vytvořte Azure Key Vault pomocí Azure CLI.  Všimněte si, že `vault-name` a `resource-group-name` jsou zadány uživatelem a musí být jedinečné.  `contoso-vault` `contoso-resource-group` V těchto příkladech používáme a.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Povolí pro Key Vault ochranu obnovitelného odstranění a vyprázdnění. Nahraďte názvy Key Vault ( `contoso-vault` ) a skupiny prostředků ( `contoso-resource-group` ) vytvořené v kroku 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Vytvořte Key Vault klíč. Zadejte `key-name` pro tento klíč jedinečný a nahraďte názvy Key Vault ( `contoso-vault` ) vytvořené v kroku 1. Určete, zda dáváte přednost `RSA` nebo `RSA-HSM` šifrování.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Výstup z tohoto příkazu zobrazuje ID klíče ("Kid") pro vygenerovaný klíč.  Poznamenejte si ID klíče pro pozdější použití v tomto cvičení.  ID klíče má formát: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  ID klíče má tři důležité komponenty:
    1. Identifikátor URI Key Vault: https://{My Trezor klíčů}. trezor. Azure. NET
    1. Key Vault název klíče: {název klíče}
    1. Verze Key Vault klíče: {Key Version}

1. Vytvořte spravovanou identitu přiřazenou systémem pomocí Azure CLI a nahraďte název vaší instance konfigurace vaší aplikace a skupiny prostředků použité v předchozích krocích. Spravovaná identita se použije pro přístup ke spravovanému klíči. Používáme `contoso-app-config` k ilustraci názvu instance konfigurace aplikace:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    Výstup tohoto příkazu zahrnuje ID objektu zabezpečení ("principalId") a ID tenanta ("tenandId") identity přiřazené systémem.  Tato ID se použijí pro udělení přístupu identit ke spravovanému klíči.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Spravovaná identita instance konfigurace aplikace Azure potřebuje k tomuto klíči přístup, aby bylo možné provést ověření klíče, šifrování a dešifrování. Konkrétní sada akcí, ke kterým potřebuje přístup, zahrnuje: `GET` , `WRAP` a `UNWRAP` pro klíče.  Udělení přístupu vyžaduje ID objektu zabezpečení spravované identity instance konfigurace aplikace. Tato hodnota byla získána v předchozím kroku. Zobrazuje se níže jako `contoso-principalId` . Udělte oprávnění ke spravovanému klíči pomocí příkazového řádku:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Jakmile má instance konfigurace aplikace Azure přístup ke spravovanému klíči, můžeme povolit klíčovou funkci spravovanou zákazníkem ve službě pomocí rozhraní příkazového řádku Azure CLI. Během postupu vytváření klíčů odvolat následující vlastnosti: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Vaše instance konfigurace aplikace Azure je teď nakonfigurovaná tak, aby používala klíč spravovaný zákazníkem uloženým v Azure Key Vault.

## <a name="next-steps"></a>Další kroky
V tomto článku jste nakonfigurovali instanci konfigurace aplikace Azure tak, aby pro šifrování používala klíč spravovaný zákazníkem.  Naučte se [integrovat službu se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md).
