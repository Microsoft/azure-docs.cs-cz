---
title: Správa trezoru klíčů Azure pomocí příkazového příkazového příkazu k příkazu k onomu – azure key vault | Dokumenty společnosti Microsoft
description: Tento článek slouží k automatizaci běžných úloh v trezoru klíčů pomocí příkazového příkazového příkazu Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 127f0cdfc8cecf9789a68210f4b7ce1927333cc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422582"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Správa trezoru klíčů pomocí příkazového příkazového příkazu Azure 

Tento článek popisuje, jak začít pracovat s Azure Key Vault pomocí Azure CLI.  Můžete vidět informace o:

- Jak vytvořit pevně tvrzený kontejner (trezor) v Azure
- Přidání klíče, tajného klíče nebo certifikátu do trezoru klíčů
- Registrace aplikace pomocí služby Azure Active Directory
- Povolení aplikace používat klíč nebo tajný klíč
- Nastavení rozšířených zásad přístupu trezoru klíčů
- Práce s moduly hardwarového zabezpečení (HSM)
- Odstranění trezoru klíčů a přidružených klíčů a tajných klíčů
- Různé příkazy rozhraní příkazového řádku Azure pro více platforem


Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Tento článek neobsahuje pokyny, jak napsat aplikaci Azure, která obsahuje jeden z kroků, který ukazuje, jak autorizovat aplikaci používat klíč nebo tajný klíč v trezoru klíčů.
>

Přehled azure key vault, najdete v tématu [Co je Azure Key Vault?](overview.md)) Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít příkazy Příkazového příkazu Azure v tomto článku, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní Azure command-line verze 2.0 nebo novější. Pokud chcete nainstalovat nejnovější verzi, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).
* Aplikace, která bude nakonfigurována tak, aby používala klíč nebo heslo, které vytvoříte v tomto článku. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Pokyny naleznete v přiloženém souboru Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Získání nápovědy k rozhraní příkazového řádku Azure pro různé platformy

Tento článek předpokládá, že jste obeznámeni s rozhraním příkazového řádku (Bash, Terminál, Příkazový řádek).

Parametr --help nebo -h lze použít k zobrazení nápovědy pro konkrétní příkazy. Alternativně Azure help [příkaz] [možnosti] formát lze také použít. Pokud si nejste jisti parametry potřebnými příkazem, podívejte se na nápovědu. Například všechny následující příkazy vrátí stejné informace:

```azurecli
az account set --help
az account set -h
```

Můžete si taky přečíst následující články, abyste se seznámili s Azure Resource Managerem v rozhraní příkazového řádku Azure pro více platforem:

* [Instalace azure cli](/cli/azure/install-azure-cli)
* [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak vytvořit pevně tvrzený kontejner (trezor) v Azure

Trezory jsou zabezpečené kontejnery podporované moduly hardwarového zabezpečení. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů. V dalších krocích vytvoříte úschovnu.

### <a name="connect-to-your-subscriptions"></a>Připojte se ke svým předplatným

Chcete-li se přihlásit interaktivně, použijte následující příkaz:

```azurecli
az login
```
Chcete-li se přihlásit pomocí účtu organizace, můžete předat své uživatelské jméno a heslo.

```azurecli
az login -u username@domain.com -p password
```

Pokud máte více než jedno předplatné a potřebujete určit, které mají být používány, podívejte se na následující předplatná svého účtu:

```azurecli
az account list
```

Zadejte odběr s parametrem předplatného.

```azurecli
az account set --subscription <subscription name or ID>
```

Další informace o konfiguraci rozhraní příkazového řádku Azure pro více platforem najdete [v tématu Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Vytvořte novou skupinu prostředků

Při použití Správce prostředků Azure se všechny související prostředky vytvoří uvnitř skupiny prostředků. Trezor klíčů můžete vytvořit v existující skupině prostředků. Pokud chcete použít novou skupinu prostředků, můžete vytvořit novou.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

První parametr je název skupiny prostředků a druhý parametr je umístění. Chcete-li získat seznam všech možných umístění typu:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrace zprostředkovatele prostředků trezoru klíčů

 Při pokusu o vytvoření nového trezoru klíčů se může zobrazit chyba "Odběr není registrován pro použití oboru názvů Microsoft.KeyVault". Pokud se tato zpráva zobrazí, ujistěte se, že poskytovatel prostředků key vault je registrován ve vašem předplatném. Jedná se o jednorázovou operaci u každého odběru.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Pomocí `az keyvault create` příkazu vytvořte trezor klíčů. Tento skript má tři povinné parametry: název skupiny prostředků, název trezoru klíčů a geografické umístění.

Chcete-li vytvořit nový trezor s názvem **ContosoKeyVault**, ve skupině prostředků **ContosoResourceGroup**, která se nachází v umístění **Východní Asie** , zadejte: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **Název**: V příkladu je název ContosoKeyVault. Tento název použijete pro další příkazy trezoru klíčů.
* **vaultUri**: V příkladu https://contosokeyvault.vault.azure.netje identifikátor URI . Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Zatím nikdo jiný nemá povolení.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Přidání klíče, tajného klíče nebo certifikátu do trezoru klíčů

Pokud chcete, aby azure key vault vytvořit softwarem `az key create` chráněný klíč pro vás, použijte příkaz.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Pokud máte existující klíč v souboru .pem, můžete jej nahrát do úložiště klíčů Azure. Klíč můžete chránit pomocí softwaru nebo softwaru snímkového systému. Tento příklad importuje klíč ze souboru .pem a chrání jej softwarem pomocí hesla "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Nyní můžete odkazovat na klíč, který jste vytvořili nebo odeslali do služby Azure Key Vault, pomocí jeho identifikátoru URI. Slouží **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** k vždy získat aktuální verzi. Použijte https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] pro získání této konkrétní verze. Například **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Přidejte tajný klíč do trezoru, což je heslo s názvem SQLPassword a které má hodnotu "hVFkk965BuUv" do trezorů klíčů Azure. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odkazujte na toto heslo pomocí identifikátoru URI. Slouží **https://ContosoVault.vault.azure.net/secrets/SQLPassword** k vždy získat aktuální verzi a https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] získat tuto konkrétní verzi. Například **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importujte certifikát do úložiště pomocí .pem nebo .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Podívejme se na klíč, tajný klíč nebo certifikát, který jste vytvořili:

* Chcete-li zobrazit klíče, zadejte: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Chcete-li zobrazit vaše tajemství, zadejte: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Chcete-li zobrazit certifikáty, zadejte: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrace aplikace pomocí služby Azure Active Directory

Tento krok obvykle provádí vývojář na samostatném počítači. Není specifická pro Azure Key Vault, ale je součástí tady, pro povědomí. K dokončení registrace aplikace musí být váš účet, trezor a aplikace ve stejném adresáři Azure.

Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory.  Vlastník aplikace musí nejprve zaregistrovat ve službě Azure Active Directory. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** (označované také jako ID klienta AAD nebo appID)
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Jak je aplikace nakonfigurována pro získání tokenu, bude záviset na aplikaci. Pro [ukázkovou aplikaci trezoru klíčů](https://www.microsoft.com/download/details.aspx?id=45343)nastaví vlastník aplikace tyto hodnoty v souboru app.config.

Podrobné kroky k registraci aplikace pomocí služby Azure Active Directory byste si měli přečíst články s názvem [Integrace aplikací s Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md), Pomocí [portálu vytvořte aplikaci azure active directory a instanční objekt, který má přístup k prostředkům,](../../active-directory/develop/howto-create-service-principal-portal.md)a [vytvořit hlavní server služby Azure pomocí rozhraní příkazového příkazu Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Registrace aplikace ve službě Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Povolení aplikace používat klíč nebo tajný klíč

Chcete-li aplikaci autorizovat pro přístup ke `az keyvault set-policy` klíči nebo tajnému klíči v úschovně, použijte příkaz.

Pokud je například název úložiště ContosoKeyVault, aplikace má appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed a chcete autorizovat aplikaci k dešifrování a podepsání pomocí klíčů v trezoru, použijte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Chcete-li povolit stejnou aplikaci ke čtení tajných kódů v trezoru, zadejte následující příkaz:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a>Nastavení rozšířených zásad přístupu trezoru klíčů

Pomocí [aktualizace az keyvault](/cli/azure/keyvault#az-keyvault-update) povolte pokročilé zásady pro trezor klíčů.

 Povolit trezor klíčů pro nasazení: Umožňuje virtuálním počítačům načítat certifikáty uložené jako tajné klíče z trezoru.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Povolení trezoru klíčů pro šifrování disku: Vyžadováno při použití trezoru pro šifrování disku Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Povolit trezor klíčů pro nasazení šablony: Umožňuje Správci prostředků načítat tajné kódy z úložiště.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Práce s moduly hardwarového zabezpečení (HSM)

Pro větší jistotu můžete importovat nebo generovat klíče z modulů hardwarového zabezpečení (HSM), které nikdy neopustí hranice modulu hardwarového zabezpečení. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud se vás tento požadavek netýká, přeskočte tuto část a přejděte k části Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů.

Pro vytvoření těchto klíčů chráněných pomocí HSM musíte mít předplatné trezoru s podporou klíčů chráněných pomocí HSM.

Při vytváření trezoru klíčů přidejte parametr 'sku':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tohoto trezoru můžete přidat klíče chráněné softwarem (jak jsme ukázali výše) a klíče chráněné pomocí HSM. Chcete-li vytvořit klíč chráněný hstem, nastavte parametr Destination na "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Pomocí následujícího příkazu můžete importovat klíč ze souboru PEM v počítači. Tento příkaz importuje klíč do HSM ve službě Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Další příkaz importuje balíček "přineste si vlastní klíč" (BYOK). To vám umožní vygenerovat klíč v místním HSM a jeho přenesení do HSM ve službě Key Vault, aniž by klíč opustil hranice HSM.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Podrobnější pokyny o tom, jak generovat tento balíček BYOK, naleznete v [tématu Jak používat klíče chráněné hsm s trezorem klíčů Azure](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Odstranění trezoru klíčů a přidružených klíčů a tajných klíčů

Pokud již trezor klíčů a jeho klíče nebo tajné klíče nepotřebujete, `az keyvault delete` můžete trezor klíčů odstranit pomocí příkazu:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Různé příkazy rozhraní příkazového řádku Azure pro více platforem

Další příkazy, které se vám mohou hodit pro správu azure key vault.

Tento příkaz obsahuje seznam tabulkových zobrazení všech kláves a vybraných vlastností:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Tento příkaz zobrazí úplný seznam vlastností zadaného klíče:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tento příkaz obsahuje seznam tabulkových zobrazení všech tajných názvů a vybraných vlastností:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Tady je příklad odebrání konkrétního klíče:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Tady je příklad, jak odebrat konkrétní tajný klíč:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Další kroky

- Úplný odkaz na příkazy Azure CLI pro příkazy trezoru klíčů najdete v tématu [Odkaz na příkaz cli trezoru klíčů](/cli/azure/keyvault).

- Odkazy na programování najdete [v příručce pro vývojáře azure key vaultu.](developers-guide.md)

- Informace o azure key vault a hsm, naleznete v [tématu jak používat klíče chráněné hsm s trezorem klíčů Azure](../keys/hsm-protected-keys.md).
