---
title: Šifrování konfiguračních dat pomocí klíčů spravovaných zákazníkem
description: Šifrování konfiguračních dat pomocí klíčů spravovaných zákazníkem
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457429"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Šifrování dat konfigurace aplikace pomocí klíčů spravovaných zákazníkem
Azure App Configuration [šifruje citlivé informace v klidovém stavu](../security/fundamentals/encryption-atrest.md). Použití klíčů spravovaných zákazníkem poskytuje lepší ochranu dat tím, že umožňuje spravovat šifrovací klíče.  Při použití šifrování spravovaného klíče jsou všechny citlivé informace v konfiguraci aplikace zašifrovány klíčem Azure Key Vault, který poskytuje uživatelem.  To poskytuje možnost otočit šifrovací klíč na vyžádání.  Poskytuje také možnost odvolat přístup azure app configuration k citlivým informacím zrušením přístupu instance konfigurace aplikace ke klíči.

## <a name="overview"></a>Přehled 
Azure App Configuration šifruje citlivé informace v klidovém stavu pomocí 256bitového šifrovacího klíče AES poskytovaného společností Microsoft. Každá instance konfigurace aplikace má svůj vlastní šifrovací klíč spravovaný službou a používaný k šifrování citlivých informací. Citlivé informace zahrnují hodnoty nalezené v párech klíč-hodnota.  Pokud je povolena funkce klíče spravovaného zákazníkem, konfigurace aplikace používá spravovanou identitu přiřazenou instanci Konfigurace aplikace k ověření pomocí služby Azure Active Directory. Spravovaná identita pak volá Azure Key Vault a zabalí šifrovací klíč instance Konfigurace aplikace. Zabalený šifrovací klíč je pak uložen a nezabalený šifrovací klíč je uložen v rámci konfigurace aplikace po dobu jedné hodiny. Konfigurace aplikace aktualizuje nezabalenou verzi šifrovacího klíče instance Konfigurace aplikace každou hodinu. Tím je zajištěna dostupnost za běžných provozních podmínek. 

>[!IMPORTANT]
> Pokud identita přiřazená instanci Konfigurace aplikace již není oprávněna rozbalit šifrovací klíč instance nebo pokud je spravovaný klíč trvale odstraněn, nebude již možné dešifrovat citlivé informace uložené v instanci Konfigurace aplikace. Použití funkce [obnovitelného odstranění](../key-vault/general/overview-soft-delete.md) služby Azure Key Vault zmírňuje pravděpodobnost náhodného odstranění šifrovacího klíče.

Když uživatelé povolí možnost i klienta spravovaného klíče v instanci konfigurace aplikace Azure, řídí možnost služby přístup k jejich citlivým informacím. Spravovaný klíč slouží jako kořenový šifrovací klíč. Uživatel může odvolat přístup instance konfigurace aplikace ke spravovanému klíči změnou zásad přístupu k trezoru klíčů. Při odvolání tohoto přístupu, konfigurace aplikace ztratí možnost dešifrovat uživatelská data do jedné hodiny. V tomto okamžiku instance Konfigurace aplikace zakáže všechny pokusy o přístup. Tato situace je obnovitelná tím, že znovu udělí přístup ke službě spravovanému klíči.  Během jedné hodiny bude konfigurace aplikace schopna dešifrovat uživatelská data a pracovat za normálních podmínek.

>[!NOTE]
>Všechna data konfigurace aplikací Azure se ukládají až 24 hodin v izolované záloze. To zahrnuje nezabalený šifrovací klíč. Tato data nejsou okamžitě k dispozici servisnímu nebo servisnímu týmu. V případě nouzového obnovení se konfigurace aplikací Azure znovu odvolá z dat spravovaného klíče.

## <a name="requirements"></a>Požadavky
Následující součásti jsou nutné k úspěšnému povolení funkce klíče spravovaného zákazníkem pro konfiguraci aplikací Azure:
- Instance konfigurace aplikace Azure se standardní úrovní
- Azure Key Vault s povolenými funkcemi pro odstranění a vymazání
- Klíč RSA nebo RSA-HSM v trezoru klíčů
    - Platnost klíče nesmí vypršet, musí být povolen a musí mít povoleny možnosti obtékání i rozbalení.

Jakmile jsou tyto prostředky nakonfigurovány, zůstávají dva kroky, které umožňují konfiguraci aplikace Azure používat klíč trezoru klíčů:
1. Přiřazení spravované identity k instanci konfigurace aplikace Azure
2. Udělte `GET`identitě `WRAP` `UNWRAP` a oprávnění mj.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Povolení šifrování klíčů spravovaných zákazníkem pro instanci konfigurace aplikace Azure
Chcete-li začít, budete potřebovat správně nakonfigurovanou instanci konfigurace aplikace Azure. Pokud ještě nemáte k dispozici instanci konfigurace aplikace, postupujte podle jednoho z těchto rychlých startů a nastavte jednu:
- [Vytvoření aplikace ASP.NET Core s konfigurací aplikací Azure](quickstart-aspnet-core-app.md)
- [Vytvoření aplikace .NET Core s konfigurací aplikací Azure](quickstart-dotnet-core-app.md)
- [Vytvoření aplikace .NET Framework s konfigurací aplikací Azure](quickstart-dotnet-app.md)
- [Vytvoření aplikace Java Spring s konfigurací aplikací Azure](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů příkazového řádku v tomto článku.  Má společné nástroje Azure předinstalované, včetně sady .NET Core SDK. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Další informace o Azure Cloud Shellu najdete v [naší dokumentaci](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Vytvoření a konfigurace trezoru klíčů Azure
1. Vytvořte trezor klíčů Azure pomocí příkazového příkazu k řešení Azure.  Všimněte `vault-name` si, že oba a `resource-group-name` jsou za předpokladu, uživatelem a musí být jedinečné.  Používáme `contoso-vault` `contoso-resource-group` a v těchto příkladech.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Povolte obnovitelné odstranění a ochranu proti vymazání trezoru klíčů. Nahraďte názvy trezoru klíčů`contoso-vault`(`contoso-resource-group`) a skupiny prostředků ( ) vytvořené v kroku 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Vytvořte klíč trezoru klíčů. Zadejte `key-name` jedinečný klíč a nahraďte názvy`contoso-vault`trezoru klíčů ( ) vytvořeného v kroku 1. Určete, `RSA` zda `RSA-HSM` dáváte přednost nebo šifrování.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Výstup z tohoto příkazu zobrazuje ID klíče ("kid") pro generovaný klíč.  Poznamenejte si ID klíče, které chcete použít později v tomto cvičení.  ID klíče má formulář: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`.  ID klíče má tři důležité součásti:
    1. Identifikátor URI trezoru klíčů: https://{trezor klíčů}.vault.azure.net
    1. Název klíče trezoru: {Název klíče}
    1. Verze klíče trezoru klíčů: {Key version}

1. Vytvořte systém přiřazenou spravovanou identitu pomocí příkazového příkazu k nastavení nastavení nastavení počítače Azure a nahraďte název instance konfigurace aplikace a skupiny prostředků použité v předchozích krocích. Spravovaná identita bude použita pro přístup ke spravovanému klíči. Používáme `contoso-app-config` k ilustraci názvu instance konfigurace aplikace:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    Výstup tohoto příkazu zahrnuje id jistiny ("principalId") a ID klienta ("tenandId") přiřazené identity systému.  To se použije k udělení přístupu identity ke spravovanému klíči.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Spravovaná identita instance Konfigurace aplikace Azure potřebuje přístup ke klíči k provedení ověření klíče, šifrování a dešifrování. Konkrétní sada akcí, ke kterým potřebuje `GET` `WRAP`přístup, `UNWRAP` zahrnuje: , , a pro klíče.  Udělení přístupu vyžaduje ID jistiny spravované identity instance konfigurace aplikace. Tato hodnota byla získána v předchozím kroku. To je uvedeno `contoso-principalId`níže jako . Udělte oprávnění spravovanému klíči pomocí příkazového řádku:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Jakmile bude mít instance Konfigurace aplikace Azure přístup ke spravovanému klíči, můžeme ve službě povolit funkci klíče spravovaného zákazníkem pomocí velmoklíči azure. Odvolání následující vlastnosti zaznamenané během `key name` `key vault URI`kroků vytváření klíčů: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Instance konfigurace aplikace Azure je teď nakonfigurovaná tak, aby používala klíč spravovaný zákazníkem uložený v trezoru klíčů Azure.

## <a name="next-steps"></a>Další kroky
V tomto článku jste nakonfigurovali instanci Konfigurace aplikace Azure tak, aby k šifrování používala klíč spravovaný zákazníkem.  Přečtěte si, jak [integrovat službu s Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).