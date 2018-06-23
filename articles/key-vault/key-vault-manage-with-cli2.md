---
title: Spravovat pomocí rozhraní příkazového řádku Azure Key Vault | Microsoft Docs
description: Pomocí tohoto článku pro automatizaci běžných úkolů v Key Vault pomocí rozhraní příkazového řádku 2.0
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: barclayn
ms.openlocfilehash: 8c2501b5e89e81709de074c0b0c93b317ecebd7b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316590"
---
# <a name="manage-key-vault-using-cli-20"></a>Spravovat pomocí rozhraní příkazového řádku 2.0 Key Vault

Tento článek popisuje, jak začít pracovat s Azure Key Vault pomocí Azure CLI 2.0. Zobrazí se informace na:
- Jak v Azure vytvořit zesílený kontejner (trezor)
- Jak ukládat a spravovat kryptografické klíče a tajné klíče v Azure. 
- Použití Azure CLI pro vytvoření trezoru.
- Vytváření klíč nebo heslo, které pak můžete použít s aplikací Azure. 
- Jak aplikace můžete použít vytvořený klíč nebo heslo.

Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).


> [!NOTE]
> Tento článek neobsahuje pokyny o tom, jak napsat aplikaci Azure, že jeden z kroků obsahuje, který ukazuje, jak k autorizaci aplikace pro použití klíče nebo tajného klíče v trezoru klíčů.
>

Přehled Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Požadavky
V tomto článku použít příkazy rozhraní příkazového řádku Azure, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní příkazového řádku verze 2.0 nebo novější. Chcete-li nainstalovat nejnovější verzi, přečtěte si téma [instalace a konfigurace Azure napříč platformami rozhraní příkazového řádku 2.0](/cli/azure/install-azure-cli).
* Aplikace, která nakonfigurujete pro použití klíče nebo hesla, které vytvoříte v tomto článku. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Pokyny najdete v tématu součástí souboru Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Získání nápovědy pomocí rozhraní příkazového řádku Azure a platformy
Tento článek předpokládá, že jste obeznámeni s rozhraní příkazového řádku (Bash, terminálu, příkazového řádku).

--Parametr nápovědy nebo -h lze zobrazit nápovědu pro určité příkazy. Alternativně Azure pomůže [příkaz], [možnosti] můžete také použít formát příliš. Pokud máte pochybnosti o parametry vyžadované příkaz, naleznete v nápovědě. Například následující příkazy, které se všechny vrátí stejné informace:

```azurecli-interactive
az account set --help
az account set -h
```

Také si můžete přečíst v následujících článcích a seznamte se s Azure Resource Manager v rozhraní příkazového řádku Azure napříč platformami:

* [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
* [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Připojte se ke svým předplatným

Interaktivně přihlásit, použijte následující příkaz:

```azurecli
az login
```
K přihlášení pomocí účtu organizace, abyste mohli předávat svoje uživatelské jméno a heslo.

```azurecli
az login -u username@domain.com -p password
```

Pokud máte více než jedno předplatné a je nutné zadat, který se použije, zadejte následující příkaz pro zobrazení předplatných pro váš účet:

```azurecli
az account list
```

Zadejte předplatné s parametrem předplatného.

```azurecli
az account set --subscription <subscription name or ID>
```

Další informace o konfiguraci rozhraní příkazového řádku pro různé platformy Azure najdete v tématu [nainstalovat rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků
Pokud používáte Azure Resource Manager, všechny související prostředky vytváří v uvnitř skupiny prostředků. Trezor klíčů můžete vytvořit v existující skupinu prostředků. Pokud chcete použít novou skupinu prostředků, můžete vytvořit nový.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

První parametr je název skupiny prostředků a druhý parametr je umístění. Pokud chcete získat seznam všech možných umístění zadejte:

```azurecli
az account list-locations
``` 

## <a name="register-the-key-vault-resource-provider"></a>Registrace poskytovatele prostředků Key Vault
 Může zobrazit chyba "předplatné není zaregistrované používání oboru názvů"Microsoft.KeyVault"" při pokusu o vytvoření nového trezoru klíčů. Pokud se zobrazí zpráva, zkontrolujte, zda že tohoto zprostředkovatele prostředku Key Vault je zaregistrován v rámci vašeho předplatného. Jedná se o jednorázovou operaci u každého odběru.

```azurecli
az provider register -n Microsoft.KeyVault
```


## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Použití `az keyvault create` příkaz pro vytvoření trezoru klíčů. Tento skript má tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisné umístění.

Chcete-li vytvořit nové úložiště s názvem **ContosoKeyVault**, ve skupině prostředků **ContosoResourceGroup**, zdržují v **východní Asie** umístění, zadejte: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **název**: V příkladu je název ContosoKeyVault. Tento název pro jiné příkazy Key Vault, budete používat.
* **vaultUri**: V tomto příkladu, je identifikátor URI https://contosokeyvault.vault.azure.net. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Ještě není oprávněn nikdo jiný.

## <a name="add-a-key-secret-or-certificate-to-the-key-vault"></a>Přidat klíč, tajný klíč nebo certifikát do trezoru klíčů

Pokud chcete Azure Key Vault vytvořila softwarově chráněný klíč pro vás, použijte `az key create` příkaz.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Pokud máte existující klíč v soubor .pem, můžete ho nahrát do Azure Key Vault. Můžete k ochraně klíče s softwaru nebo HSM. Použijte následující postupy k importu klíče ze souboru .pem a chránit pomocí softwaru:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

Nyní můžete odkazovat klíč, který jste vytvořili nebo nahrán do Azure Key Vault, pomocí jeho identifikátoru URI. Použití **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** k vždy získáte aktuální verzi. Použití https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] Chcete-li získat konkrétní verzi. Příklad: **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Přidáte tajný klíč k úložišti, což je hesla s názvem SQLPassword a hodnotou z Pa$ $w0rd na Azure klíč trezorů. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Toto heslo odkazujete pomocí jeho identifikátoru URI. Použití **https://ContosoVault.vault.azure.net/secrets/SQLPassword** vždycky získat aktuální verzi a https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] Chcete-li získat konkrétní verzi. Příklad: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importujte certifikátu do úložiště pomocí .pem nebo .pfx.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Podívejme se klíč, tajný klíč nebo certifikát, který jste vytvořili:

* Chcete-li zobrazit vaše klíče, zadejte: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Chcete-li zobrazit vaše tajné klíče, zadejte: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Chcete-li zobrazit certifikáty, zadejte: 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registrujte aplikaci s Azure Active Directory
Tento krok obvykle provádí vývojář na samostatném počítači. Není specifické pro Azure Key Vault, ale je zde uveden, aby sledování. K dokončení registrace aplikace, váš účet, trezor a aplikace musí být ve stejném adresáři Azure.

Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory.  Majitel aplikace ji musíte zaregistrovat v Azure Active Directory nejprve. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** (také označované jako ID klienta AAD nebo appID)
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Konfigurace aplikace získá token bude záviset na aplikaci. Pro [ukázkovou aplikaci Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) nastavuje majitel tyto hodnoty v souboru app.config.

Podrobné pokyny k registraci aplikace v Azure Active Directory zkontrolujte v článcích [integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [použití portálu k vytvoření Azure Active Aplikace Directory a objektu služby, které mají přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md), a [vytvořit objekt služby Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Zaregistrovat aplikaci v Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizujte aplikaci pro použití klíče nebo tajného klíče

Chcete-li autorizovat aplikaci pro přístup ke klíči nebo tajný klíč v trezoru, použijte `az keyvault set-policy` příkaz.

Například pokud je název vaší trezoru ContosoKeyVault, aplikace má appID hodnotu 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, a chcete aplikaci autorizovat pro dešifrování a podepisování pomocí klíčů ve vašem trezoru, použijte následující příkaz:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Pro stejnou aplikaci autorizovat pro čtení tajných klíčů v trezoru, zadejte následující příkaz:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Trezor klíčů sadu pokročilé zásady přístupu 
Použití [az keyvault aktualizace](/cli/azure/keyvault#az-keyvault-update) povolit pokročilé zásady pro trezor klíčů. 

 Povolit Key Vault pro nasazení: umožňuje virtuálních počítačů k načtení certifikátů uložené jako tajné klíče z trezoru.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Povolit Key Vault pro šifrování disku: při použití úložiště pro Azure Disk encryption vyžaduje.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Povolit Key Vault pro šablonu nasazení: Resource Manager umožňuje načíst tajné klíče z trezoru.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Pokud chcete použít modul hardwarového zabezpečení (HSM)

Pro lepší kontrolu můžete importovat nebo generovat klíče z modulů hardwarového zabezpečení (HSM), které nikdy neopustí hranice HSM. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud se vás tento požadavek netýká, přeskočte tuto část a přejděte k části [Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů](#delete-the-key-vault-and-associated-keys-and-secrets).

Pro vytvoření těchto klíčů chráněných pomocí HSM, musíte mít předplatné trezoru s podporou klíčů chráněných pomocí HSM.

Když vytvoříte keyvault, přidejte parametr 'sku':

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Do tohoto trezoru můžete přidat klíče chráněné softwarem (jak jsme ukázali výše) a klíče chráněné pomocí HSM. Chcete-li vytvořit klíč chráněný HSM, nastavte parametr cílové na "HSM":

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Můžete následující příkaz pro import klíče ze souboru .pem ve vašem počítači. Tento příkaz importuje klíč do HSM ve službě Key Vault:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

Další příkaz importuje balíček „přineste si vlastní klíč“ (BYOK). To vám umožní vygenerovat klíč v místním HSM a jeho přenesení do HSM ve službě Key Vault, aniž by klíč opustil hranice HSM.

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Podrobnější pokyny o tom, jak generovat tento balíček BYOK naleznete v tématu [použití HSM-Protected klíčů s Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů

Pokud již nepotřebujete trezor klíčů a jeho klíčů nebo tajných klíčů, můžete trezor klíčů odstranit pomocí `az keyvault delete` příkaz:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Ostatní příkazy rozhraní příkazového řádku Azure a platformy

Další příkazy, které mohou být užitečné pro správu Azure Key Vault.

Tento příkaz vypíše tabulkové zobrazení všech klíčů a vybraných vlastností:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Tento příkaz zobrazí úplný seznam vlastností pro zadaný klíč:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Tento příkaz vypíše tabulkové zobrazení všech názvů tajných klíčů a vybraných vlastností:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Tady je příklad odebrání určitého klíče:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Tady je příklad odebrání určitého tajného klíče:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Další postup

- Úplný referenční rozhraní příkazového řádku Azure pro příkazy trezoru klíčů, najdete v části [klíč trezoru rozhraní příkazového řádku odkaz](/cli/azure/keyvault).

- Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md).

- Informace o Azure Key Vault a moduly hardwarového zabezpečení najdete v tématu [použití HSM-Protected klíčů s Azure Key Vault](key-vault-hsm-protected-keys.md).
