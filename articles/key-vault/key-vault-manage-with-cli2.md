---
title: Správa služby Azure Key Vault pomocí rozhraní příkazového řádku – Azure Key Vault | Dokumentace Microsoftu
description: V tomto článku použijte k automatizaci běžných úkolů ve službě Key Vault pomocí rozhraní příkazového řádku Azure
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: barclayn
ms.openlocfilehash: 11ace1b5cce742579256d08ecfe9d9a7412d3d7c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822489"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Správa služby Key Vault pomocí rozhraní příkazového řádku Azure 

Tento článek popisuje, jak začít pracovat se službou Azure Key Vault pomocí rozhraní příkazového řádku Azure.  Můžete zobrazit informace na:

- Jak v Azure vytvořit zesílený kontejner (trezor)
- Přidání klíče, tajné nebo certifikát do služby key vault
- Registrace aplikace v Azure Active Directory
- Autorizace aplikace pro použití klíče nebo tajného klíče
- Nastavení trezoru klíčů pokročilé zásady přístupu
- Práce s moduly hardwarového zabezpečení (HSM)
- Odstraňuje se trezor klíčů, přidružených klíčů a tajných kódů
- Příkazy různé rozhraní příkazového řádku Azure Cross-Platform


Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Tento článek neobsahuje pokyny, jak psát aplikace Azure, jedním z kroků obsahuje, který ukazuje, jak autorizovat aplikaci pro použití klíče nebo tajného klíče v trezoru klíčů.
>

Přehled služby Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-whatis.md)
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Použití příkazů rozhraní příkazového řádku Azure v tomto článku, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní příkazového řádku Azure verze 2.0 nebo novější. Pokud chcete nainstalovat nejnovější verzi, najdete v článku [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).
* Aplikace, která nakonfigurujete pro použití klíče nebo hesla, které vytvoříte v tomto článku. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Pokyny najdete v tématu zahrnutý soubor Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Získání nápovědy pomocí rozhraní příkazového řádku Azure Cross-Platform

Tento článek předpokládá, že jste obeznámeni s rozhraním příkazového řádku (Bash, terminálu, příkazový řádek).

--Pomoc nebo -h parametr je možné zobrazit nápovědu k určité příkazy. Alternativně Azure help [příkaz] [možnosti] formátu lze použít také příliš. Pokud máte pochybnosti o parametry vyžadované příkazu, najdete v nápovědě. Například následující příkazy, které se všechny vrátí stejné informace:

```azurecli
az account set --help
az account set -h
```

Také si můžete přečíst následující články a seznamte se s Azure Resource Managerem v rozhraní příkazového řádku Azure Cross-Platform:

* [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
* [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak v Azure vytvořit zesílený kontejner (trezor)

Trezory služby jsou zabezpečené kontejnery se opírá o modulech hardwarového zabezpečení. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů. V dalších krocích vytvoříte trezor.

### <a name="connect-to-your-subscriptions"></a>Připojte se ke svým předplatným

Interaktivní přihlášení, použijte následující příkaz:

```azurecli
az login
```
K přihlášení pomocí účtu organizace, můžete předat uživatelské jméno a heslo.

```azurecli
az login -u username@domain.com -p password
```

Pokud máte více než jedno předplatné a je nutné zadat, které se má použít, zadejte následující příkaz k zobrazení předplatných pro váš účet:

```azurecli
az account list
```

Zadejte předplatné s parametrem předplatného.

```azurecli
az account set --subscription <subscription name or ID>
```

Další informace o konfiguraci rozhraní příkazového řádku pro různé platformy Azure najdete v tématu [instalace Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků

Pokud používáte Azure Resource Manageru, všechny související prostředky vytváří v uvnitř skupiny prostředků. Vytvoření trezoru klíčů v existující skupinu prostředků. Pokud chcete použít novou skupinu prostředků, můžete vytvořit nový.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

První parametr je název skupiny prostředků a druhý parametr je umístění. Chcete-li získat seznam všech možných zadejte umístění:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrace poskytovatele prostředků služby Key Vault

 Může zobrazit chyba "předplatné není zaregistrované používání oboru názvů"Microsoft.KeyVault"" při pokusu o vytvoření nového trezoru klíčů. Pokud se tato chyba zobrazí, ujistěte se, že je ve vašem předplatném zaregistrovaný poskytovatel prostředků této služby Key Vault. Jedná se o jednorázovou operaci u každého odběru.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Použití `az keyvault create` příkaz pro vytvoření trezoru klíčů. Tento skript má tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisná poloha.

Chcete-li vytvořit nový trezor s názvem **ContosoKeyVault**, ve skupině prostředků **ContosoResourceGroup**, nacházejícím se v **východní Asie** umístění, zadejte: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, kterou jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **Název**: V tomto příkladu je název ContosoKeyVault. Budete používat tento název pro další příkazy Key Vault.
* **vaultUri**: V tomto příkladu je identifikátor URI https://contosokeyvault.vault.azure.net. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Ještě není oprávněn nikdo jiný.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Přidání klíče, tajné nebo certifikát do služby key vault

Pokud chcete Azure Key Vault vytvořila softwarově chráněný klíč pro vás, použijte `az key create` příkazu.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Pokud máte existující klíč v souboru .pem, nahrajte ho do služby Azure Key Vault. Můžete k ochraně klíče se software nebo modulu hardwarového zabezpečení. Použijte následující postup k importovali klíč ze souboru .pem a chránit je pomocí softwaru:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "Pa$$w0rd" --protection software
```

Teď můžete odkazovat na klíč, který vytvoříte nebo nahrajete do služby Azure Key Vault pomocí jeho identifikátoru URI. Použití **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** vždy získáte aktuální verzi. Použití https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] získat tuto konkrétní verzi. Příklad: **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Přidání tajného klíče do trezoru, což je hesla s názvem SQLPassword a hodnotou Pa$ $w0rd na trezory klíčů Azure. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "Pa$$w0rd"
```

Na toto heslo odkazujete pomocí jeho identifikátoru URI. Použití **https://ContosoVault.vault.azure.net/secrets/SQLPassword** vždy získáte aktuální verzi a https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] získat tuto konkrétní verzi. Příklad: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importujte certifikát do trezoru pomocí .pem nebo .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "Pa$$w0rd"
```

Podívejme se klíč, tajný klíč nebo certifikát, který jste vytvořili:

* Chcete-li zobrazit vaše klíče, zadejte: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Chcete-li zobrazit tajné klíče, zadejte: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Pokud chcete zobrazit certifikáty, zadejte: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrace aplikace v Azure Active Directory

Tento krok obvykle provádí vývojář na samostatném počítači. Není specifická pro Azure Key Vault, ale je zde uveden, sledování. K dokončení registrace aplikace, váš účet, trezor a aplikace musí být ve stejném adresáři Azure.

Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory.  Vlastník aplikace ji musíte zaregistrovat v Azure Active Directory nejprve. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** (také označované jako ID klienta AAD nebo ID aplikace)
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Konfigurace aplikace získá token bude záviset na aplikaci. Pro [ukázkovou aplikaci Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) nastavuje majitel tyto hodnoty v souboru app.config.

Podrobné pokyny k registraci aplikace v Azure Active Directory si můžete projít v článcích [integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [použití portálu k vytvoření Azure Active Adresář aplikace a instanční objekt, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md), a [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Pro registraci aplikace ve službě Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "Pa$$w0rd" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizace aplikace pro použití klíče nebo tajného klíče

Chcete-li autorizovat aplikaci přístup k klíče nebo tajného klíče v trezoru, použijte `az keyvault set-policy` příkazu.

Například pokud je trezor s názvem ContosoKeyVault, má aplikace appID hodnotu 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed a chcete autorizovat aplikaci k dešifrování a podepisování pomocí klíčů v trezoru, použijte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Pro stejnou aplikaci autorizujte pro čtení tajných klíčů v trezoru, zadejte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Nastavení trezoru klíčů pokročilé zásady přístupu

Použití [az keyvault update](/cli/azure/keyvault#az-keyvault-update) povolit pokročilé zásady pro trezor klíčů.

 Povolení služby Key Vault pro nasazení: Umožňuje virtuálních počítačů k načítání certifikátů uložených jako tajné kódy z trezoru.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Povolení služby Key Vault pro šifrování disku: Požaduje se při použití trezoru pro Azure Disk encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Povolení služby Key Vault pro šablonu nasazení: Umožňuje Resource Manageru k načítání tajných kódů z trezoru.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Práce s moduly hardwarového zabezpečení (HSM)

Jistotu importujte nebo generujte klíče v modulech hardwarového zabezpečení (HSM), které nikdy neopustí hranice modulu hardwarového zabezpečení. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud tento požadavek neplatí pro vás, přeskočte tuto část a přejděte k odstranění trezoru klíčů, přidružených klíčů a tajných kódů.

Chcete-li vytvořit těchto klíčů chráněných pomocí HSM, musíte mít předplatné trezoru, který podporuje klíče chráněné HSM.

Když vytvoříte trezor klíčů, přidejte parametr "sku":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tohoto trezoru můžete přidat klíče chráněné softwarem (jak jsme ukázali výše) a klíče chráněné pomocí HSM. Pokud chcete vytvořit klíč chráněný HSM, nastavte parametr Destination roven na "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Následující příkaz můžete použít pro import klíče ze souboru .pem ve vašem počítači. Tento příkaz importuje klíč do HSM ve službě Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Další příkaz importuje balíček „přineste si vlastní klíč“ (BYOK). To vám umožní vygenerovat klíč v místním HSM a jeho přenesení do HSM ve službě Key Vault, aniž by klíč opustil hranice HSM.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Podrobnější pokyny o tom, jak generovat tento balíček BYOK naleznete v tématu [použití HSM-Protected klíčů se službou Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Odstraňuje se trezor klíčů, přidružených klíčů a tajných kódů

Pokud již nepotřebujete trezor klíčů a jeho klíče nebo tajné kódy, můžete odstranit trezor klíčů s použitím `az keyvault delete` příkaz:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Příkazy různé rozhraní příkazového řádku Azure Cross-Platform

Další příkazy, které můžou být užitečné pro správu služby Azure Key Vault.

Tento příkaz vypíše tabelární zobrazení všech klíčů a vybraných vlastností:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Tento příkaz zobrazí úplný seznam vlastností pro zadaný klíč:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tento příkaz vypíše tabelární zobrazení všech názvů tajných klíčů a vybraných vlastností:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Tady je příklad odebrání určitého klíče:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tady je příklad odebrání určitého tajného klíče:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Další postup

- Kompletní rozhraní příkazového řádku Azure reference pro příkazy služby key vault najdete v části [odkaz na rozhraní příkazového řádku Key Vault](/cli/azure/keyvault).

- Programátorské reference najdete v části [– Příručka pro vývojáře Azure Key Vault](key-vault-developers-guide.md)

- Informace o Azure Key Vault a modulů hardwarového zabezpečení, najdete v části [použití HSM-Protected klíčů se službou Azure Key Vault](key-vault-hsm-protected-keys.md).
