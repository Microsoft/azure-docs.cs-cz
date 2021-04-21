---
title: Správa Azure Key Vault pomocí rozhraní příkazového řádku Azure Key Vault | Microsoft Docs
description: Tento článek slouží k automatizaci běžných úloh v Key Vault pomocí rozhraní příkazového řádku Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b29b668b21c375dd1202652b5093526f648c300
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749595"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Správa Key Vault pomocí Azure CLI 

Tento článek popisuje, jak začít pracovat s Azure Key Vault pomocí Azure CLI.  Můžete si prohlédnout následující informace:

- Jak v Azure vytvořit zesílený kontejner (trezor)
- Přidání klíče, tajného klíče nebo certifikátu do trezoru klíčů
- Registrace aplikace pomocí Azure Active Directory
- Autorizace aplikace pro použití klíče nebo tajného klíče
- Nastavují se zásady rozšířeného přístupu trezoru klíčů.
- Práce s moduly hardwarového zabezpečení (HSM)
- Odstranění trezoru klíčů a přidružených klíčů a tajných klíčů
- Různé příkazy rozhraní příkazového řádku pro více platforem Azure


Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Tento článek neobsahuje pokyny, jak napsat aplikaci Azure, kterou zahrnuje některý z kroků, který ukazuje, jak autorizovat aplikaci pro použití klíče nebo tajného klíče v trezoru klíčů.
>

Přehled Azure Key Vault najdete v tématu [co je Azure Key Vault?](overview.md)). Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat příkazy rozhraní příkazového řádku Azure CLI v tomto článku, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní Azure Command-Line verze 2,0 nebo novější. Informace o instalaci nejnovější verze najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
* Aplikace, která bude nakonfigurována pro použití klíče nebo hesla, které vytvoříte v tomto článku. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Pokyny najdete v zahrnutém souboru Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Získání nápovědě k rozhraní Command-Line pro více platforem Azure

V tomto článku se předpokládá, že jste obeznámeni s rozhraním příkazového řádku (bash, terminál, příkazový řádek).

Parametr--help nebo-h lze použít k zobrazení nápovědu pro konkrétní příkazy. Alternativně lze také použít formát Azure Help [příkaz] [možnosti]. Pokud si nejste jistí, jaké parametry potřebuje příkaz, podívejte se na nápovědu. Například následující příkazy vrátí stejné informace:

```azurecli
az account set --help
az account set -h
```

Můžete si také přečíst následující články, které vám pomůžou seznámit se s Azure Resource Manager v rozhraní Command-Line platformy Azure pro více platforem:

* [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
* [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak v Azure vytvořit zesílený kontejner (trezor)

Trezory jsou zabezpečené kontejnery zajištěné moduly hardwarového zabezpečení. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných kódů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů. V dalších krocích vytvoříte trezor.

### <a name="connect-to-your-subscriptions"></a>Připojte se ke svým předplatným

Pokud se chcete interaktivně přihlásit, použijte následující příkaz:

```azurecli
az login
```
Pokud se chcete přihlásit pomocí účtu organizace, můžete předat svoje uživatelské jméno a heslo.

```azurecli
az login -u username@domain.com -p password
```

Pokud máte více než jedno předplatné a potřebujete určit, která z nich se má použít, zadejte následující příkaz pro zobrazení předplatných pro váš účet:

```azurecli
az account list
```

Zadejte předplatné s parametrem předplatného.

```azurecli
az account set --subscription <subscription name or ID>
```

Další informace o konfiguraci rozhraní Command-Line pro různé platformy Azure najdete v tématu [instalace Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků

Při použití Azure Resource Manager se všechny související prostředky vytvoří v rámci skupiny prostředků. Trezor klíčů můžete vytvořit v existující skupině prostředků. Pokud chcete použít novou skupinu prostředků, můžete vytvořit novou.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Prvním parametrem je název skupiny prostředků a druhý parametr je umístění. Pokud chcete získat seznam všech možných umístění, zadejte:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrace poskytovatele prostředků Key Vault

 Při pokusu o vytvoření nového trezoru klíčů se může zobrazit chyba "předplatné není zaregistrované pro používání oboru názvů" Microsoft. Key trezor "". Pokud se zobrazí tato zpráva, ujistěte se, že je ve vašem předplatném zaregistrovaný Key Vault poskytovatel prostředků. Jedná se o jednorázovou operaci u každého odběru.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Pomocí `az keyvault create` příkazu vytvořte Trezor klíčů. Tento skript má tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisnou polohu.

Pokud chcete vytvořit nový trezor s názvem **ContosoKeyVault**, zadejte do skupiny prostředků  **ContosoResourceGroup**, která je umístěná v umístění **východní Asie** : 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **název**: v tomto příkladu je název ContosoKeyVault. Tento název použijete pro jiné Key Vault příkazy.
* **vaultUri**: v tomto příkladu je identifikátor URI https://contosokeyvault.vault.azure.net . Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Od tohoto konce je nikdo jiný autorizovaný.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Přidání klíče, tajného klíče nebo certifikátu do trezoru klíčů

Pokud chcete, Azure Key Vault pro vás vytvořit klíč chráněný softwarem, použijte `az key create` příkaz.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Pokud máte existující klíč v souboru. pem, můžete ho nahrát na Azure Key Vault. Můžete se rozhodnout chránit klíč pomocí softwaru nebo HSM. Tento příklad importuje klíč ze souboru. pem a chrání ho pomocí softwaru pomocí hesla "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Nyní můžete odkazovat na klíč, který jste vytvořili nebo nahráli do Azure Key Vault, pomocí jeho identifikátoru URI. `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey`K získání aktuální verze vždycky použijte. K získání této konkrétní verze použijte protokol https://[název trezoru klíčů]. trezor. Azure. NET/Keys/[KeyName]/[klíč-Unique-ID]. Například, `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Přidejte do trezoru tajný klíč, což je heslo s názvem SQLPassword, které má hodnotu "hVFkk965BuUv" pro trezory klíčů Azure. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odkažte na toto heslo pomocí jeho identifikátoru URI. Pokud chcete **https://ContosoVault.vault.azure.net/secrets/SQLPassword** získat konkrétní verzi, použijte k tomu vždycky získat aktuální verzi a https://[název trezoru]. trezor. Azure. NET/Secret/[tajný klíč-název]/[tajný kód-jedinečné ID]. Příklad: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importujte certifikát do trezoru pomocí souboru. pem nebo. pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Pojďme si prohlédnout klíč, tajný klíč nebo certifikát, který jste vytvořili:

* Chcete-li zobrazit klíče, zadejte: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* K zobrazení tajných kódů zadejte: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Chcete-li zobrazit certifikáty, zadejte: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrace aplikace pomocí Azure Active Directory

Tento krok obvykle provádí vývojář na samostatném počítači. Nejedná se o Azure Key Vault, ale je zde k dispozici pro povědomí. K dokončení registrace aplikace musí být váš účet, trezor a aplikace ve stejném adresáři Azure.

Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory.  Vlastník aplikace musí nejprve zaregistrovat v Azure Active Directory. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** (označované také jako ID klienta AAD nebo AppID)
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Způsob, jakým je aplikace nakonfigurovaná tak, aby získala token, bude záviset na aplikaci. V případě [ukázkové aplikace Key Vault](https://www.microsoft.com/download/details.aspx?id=45343)nastaví vlastník aplikace tyto hodnoty v souboru app.config.

Podrobné pokyny k registraci aplikace v Azure Active Directory najdete v článcích s názvem [integrování aplikací pomocí Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md), a [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Registrace aplikace v Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizace aplikace pro použití klíče nebo tajného klíče

K autorizaci aplikace pro přístup ke klíči nebo tajnému klíči v trezoru použijte `az keyvault set-policy` příkaz.

Například pokud je název trezoru ContosoKeyVault, aplikace má appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed a vy chcete autorizovat aplikaci k dešifrování a podepsání klíčů v trezoru, použijte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Chcete-li autorizovat stejnou aplikaci pro čtení tajných klíčů v trezoru, zadejte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Nastavují se zásady rozšířeného přístupu trezoru klíčů.

Pokud chcete povolit pokročilé zásady pro Trezor klíčů, použijte [AZ Key trezor Update](/cli/azure/keyvault#az-keyvault-update) .

 Povolit Key Vault pro nasazení: umožňuje virtuálním počítačům načíst certifikáty uložené jako tajné klíče z trezoru.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Povolit Key Vault pro šifrování disku: vyžaduje se při použití trezoru pro Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Povolit Key Vault pro nasazení šablony: umožňuje Správce prostředků načtení tajných kódů z trezoru.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Práce s moduly hardwarového zabezpečení (HSM)

Pro zvýšení zabezpečení můžete importovat nebo generovat klíče z modulů hardwarového zabezpečení (HSM), které nikdy nezanechají hranici HSM. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud se vás tento požadavek netýká, přeskočte tuto část a přejděte k části Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů.

Pro vytvoření těchto klíčů chráněných pomocí HSM musíte mít předplatné trezoru s podporou klíčů chráněných pomocí HSM.

Při vytváření trezoru klíčů přidejte parametr SKU:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tohoto trezoru můžete přidat klíče chráněné softwarem (jak jsme ukázali výše) a klíče chráněné pomocí HSM. Pokud chcete vytvořit klíč chráněný HSM, nastavte parametr Destination na ' HSM ':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Pomocí následujícího příkazu můžete importovat klíč ze souboru. pem na vašem počítači. Tento příkaz importuje klíč do HSM ve službě Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Další příkaz importuje balíček "Přineste si vlastní klíč" (BYOK). To vám umožní vygenerovat klíč v místním HSM a jeho přenesení do HSM ve službě Key Vault, aniž by klíč opustil hranice HSM.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Podrobnější pokyny o tom, jak tento balíček BYOK vygenerovat, najdete v článku [Jak používat HSM-Protected klíče s Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Odstranění trezoru klíčů a přidružených klíčů a tajných klíčů

Pokud už Trezor klíčů a jeho klíče nebo tajné klíče nepotřebujete, můžete Trezor klíčů odstranit pomocí `az keyvault delete` příkazu:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Různé příkazy rozhraní příkazového řádku pro více platforem Azure

Další příkazy, které můžete najít užitečné při správě Azure Key Vault.

Tento příkaz vypíše seznam všech klíčů a vybraných vlastností v tabulkovém zobrazení:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Tento příkaz zobrazí úplný seznam vlastností pro zadaný klíč:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tento příkaz vypíše tabulkové zobrazení všech tajných názvů a vybraných vlastností:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Tady je příklad, jak odebrat konkrétní klíč:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tady je příklad, jak odebrat konkrétní tajný klíč:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Další kroky

- Kompletní Reference k rozhraní příkazového řádku Azure CLI pro příkazy trezoru klíčů najdete v tématu [Key Vault reference CLI](/cli/azure/keyvault).

- Odkazy na programování najdete [v tématu Azure Key Vault příručka pro vývojáře](developers-guide.md) .

- Informace o Azure Key Vault a HSM najdete v tématu [použití klíčů HSM-Protected s Azure Key Vault](../keys/hsm-protected-keys.md).
