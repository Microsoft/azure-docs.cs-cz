---
title: Spravovat pomocí rozhraní příkazového řádku Azure Key Vault | Microsoft Docs
description: Tento kurz použijte k automatizaci běžných úkolů v Key Vault pomocí rozhraní příkazového řádku 2.0
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
ms.date: 04/19/2018
ms.author: barclayn
ms.openlocfilehash: 95e35ed1f26a861ab934570fae613dda95fcb537
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="manage-key-vault-using-cli-20"></a>Spravovat pomocí rozhraní příkazového řádku 2.0 Key Vault

Tento článek popisuje, jak začít pracovat s Azure Key Vault pomocí Azure CLI 2.0. Zobrazí se informace na:
- Jak v Azure vytvořit zesílený kontejner (trezor)
- Jak ukládat a spravovat kryptografické klíče a tajné klíče v Azure. 
- Proces vytvoření trezoru, který obsahuje klíč nebo heslo, které pak můžete použít s aplikací Azure pomocí rozhraní příkazového řádku pro různé platformy Azure. 
- Aplikace jak pak můžete použít tento klíč nebo heslo.

Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

**Odhadovaný čas dokončení:** 20 minut

> [!NOTE]
> Tento kurz neobsahuje pokyny o tom, jak napsat aplikaci Azure, že jeden z kroků obsahuje, který ukazuje, jak k autorizaci aplikace pro použití klíče nebo tajného klíče v trezoru klíčů.
>

Přehled Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

* Předplatné Microsoft Azure. Pokud jeden nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Rozhraní příkazového řádku verze 2.0 nebo novější. Nainstalujte nejnovější verzi a připojení k předplatnému Azure najdete v tématu [instalace a konfigurace Azure napříč platformami rozhraní příkazového řádku 2.0](/cli/azure/install-azure-cli).
* Aplikaci, kterou nakonfigurujete pro použití klíče nebo hesla, které v tomto kurzu vytvoříte. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Pokyny naleznete v souvisejícím souboru Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Získání nápovědy pomocí rozhraní příkazového řádku Azure a platformy
V tomto kurzu se předpokládá, že jste obeznámeni s rozhraní příkazového řádku (Bash, terminálu, příkazového řádku)

--Parametr nápovědy nebo -h lze zobrazit nápovědu pro určité příkazy. Alternativně azure help [příkaz] [možnosti] formátu lze použít také k vrácení stejné informace. Například následující příkazy, které se všechny vrátí stejné informace:

```azurecli-interactive
az account set --help
az account set -h
```

Pokud máte pochybnosti o parametry vyžadované příkaz, v nápovědě k použití – Nápověda, – h nebo az help [příkaz].

Také si můžete přečíst následující kurzy a seznamte se s Azure Resource Manager v rozhraní příkazového řádku Azure napříč platformami:

* [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
* [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Připojte se ke svým předplatným
K přihlášení pomocí účtu organizace, použijte následující příkaz:

```azurecli
az login -u username@domain.com -p password
```

nebo pokud se chcete přihlásit interaktivně zadáním

```azurecli
az login
```

Máte-li více předplatných a chcete určit jedno konkrétní, které se má použít pro Azure Key Vault, zadejte následující příkaz k zobrazení předplatných pro váš účet:

```azurecli
az account list
```

Poté pro určení předplatného, které se má použít, zadejte:

```azurecli
az account set --subscription <subscription name or ID>
```

Další informace o konfiguraci rozhraní příkazového řádku pro různé platformy Azure najdete v tématu [nainstalovat rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků
Pokud používáte Azure Resource Manager, všechny související prostředky vytváří v uvnitř skupiny prostředků. Pro účely tohoto kurzu vytvoříme novou skupinu prostředků, ContosoResourceGroup'.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

První parametr je název skupiny prostředků a druhý parametr je umístění. Pokud chcete získat seznam všech možných umístění zadejte:

```azurecli
az account list-locations
``` 

Pokud potřebujete více informací, zadejte: 

```azurecli
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>Registrace poskytovatele prostředků Key Vault
Při pokusu o vytvoření nového trezoru klíčů se může zobrazit chyba "předplatné není zaregistrované používání oboru názvů"Microsoft.KeyVault"". Pokud se zobrazí zpráva, ujistěte se, že poskytovatel prostředků Key Vault je zaregistrován v rámci vašeho předplatného:

```azurecli
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
To jenom je nutné provést jednou za předplatné.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Použití `az keyvault create` příkaz pro vytvoření trezoru klíčů. Tento skript má tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisné umístění.

Příklad:

- Pokud použijete název trezoru **ContosoKeyVault**
- Název skupiny prostředků **ContosoResourceGroup**
- Umístění **východní Asie**

Zadejte příkaz:

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste právě vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **název**: V tomto příkladu to je ContosoKeyVault. Tento název pro jiné příkazy Key Vault budete používat.
* **vaultUri**: Toto je příklad https://contosokeyvault.vault.azure.net. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Zatím k tomu není oprávněn nikdo jiný.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Přidejte k trezoru klíč nebo tajný klíč

Pokud chcete Azure Key Vault vytvořila softwarově chráněný klíč pro vás, použijte `az key create` příkaz a zadejte následující příkaz:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Nicméně pokud máte existující klíč v soubor .pem uložený jako místního souboru do souboru s názvem softkey.pem, který chcete nahrát do Azure Key Vault, zadejte následující příkaz pro import klíče z. Soubor PEM, který chrání klíč softwarem ve službě Key Vault:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

Nyní můžete odkazovat klíč, který jste vytvořili nebo nahrán do Azure Key Vault, pomocí jeho identifikátoru URI. Použít **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** vždy získáte aktuální verzi a používat **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** Chcete-li získat konkrétní verzi.

Chcete-li přidat tajný klíč k úložišti, který je hesla s názvem SQLPassword a hodnotou z Pa$ $w0rd do Azure Key Vault, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Nyní na toto heslo, které jste přidali do Azure Key Vault, můžete odkazovat pomocí jeho identifikátoru URI. Použít **https://ContosoVault.vault.azure.net/secrets/SQLPassword** vždy získáte aktuální verzi a používat **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** Chcete-li získat konkrétní verzi.

Podívejme se na klíč nebo tajný klíč, který jste právě vytvořili:

* Chcete-li zobrazit klíč, zadejte: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Chcete-li zobrazit tajný klíč, zadejte: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registrujte aplikaci s Azure Active Directory
Tento krok obvykle provádí vývojář na samostatném počítači. Není specifické pro Azure Key Vault, ale je zde uveden, aby sledování.

> [!IMPORTANT]
> Pro dokončení kurzu musí být váš účet, trezor i aplikace, kterou budete v tomto kroku registrovat, ve stejném adresáři Azure.
>
>

Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory. K tomu je potřeba, aby majitel aplikace nejprve zaregistroval aplikaci ve vlastním Azure Active Directory. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** 
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Záleží na aplikaci, jak je k tomu nakonfigurovaná. Pro [ukázkovou aplikaci Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) nastavuje majitel tyto hodnoty v souboru app.config.

Podrobné pokyny k registraci aplikace v Azure Active Directory měli přečtěte si článek s názvem [integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) nebo [použití portálu k vytvoření Azure Active Aplikace Directory a objektu služby, které mají přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md) zaregistrovat aplikaci v Azure Active Directory:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Nalevo klikněte na **registrace aplikací**. Pokud se registrace aplikací nezobrazí, klikněte na **Další služby** a vyhledejte je tam.  
[!NOTE]
Musíte vybrat adresář obsahující předplatné Azure, které jste použili pro vytvoření trezoru klíčů. 
3. Klikněte na **Registrace nové aplikace**.
4. V okně **Vytvořit** zadejte název vaší aplikace a poté vyberte **WEBOVÁ APLIKACE NEBO WEBOVÉ ROZHRANÍ API** (výchozí možnost) a zadejte **PŘIHLAŠOVACÍ ADRESU URL** pro vaši webovou aplikaci. Pokud tyto informace v tuto chvíli nemáte, můžete ho nastavit pro tento krok (například můžete zadat http://test1.contoso.com ). Nezáleží na tom, zda tyto weby existují. 

    ![Registrace nové aplikace](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Ověřte, že pokud se v nastavení nezobrazila položka **klíče**, zvolili jste **WEBOVÁ APLIKACE NEBO WEBOVÉ ROZHRANÍ API**.

5. Klikněte na tlačítko **Vytvořit**.
6. Po dokončení registrace aplikace se zobrazí seznam registrovaných aplikací. Najděte aplikaci, který jste právě zaregistrovali, a klikněte na ni.
7. Klikněte v okně **Registrovaná aplikace** a zkopírujte **ID aplikace**.
8. Klikněte na **Veškerá nastavení**.
9. V okně **Nastavení** klikněte na **klíče**.
9. Do pole **Popis klíče** zadejte popis, vyberte dobu trvání a potom klikněte na **ULOŽIT**. Stránka se obnoví a zobrazí hodnotu klíče. 
10. Informace v polích **ID aplikace** a **Klíč** použijete v dalším kroku k nastavení oprávnění pro váš trezor.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizujte aplikaci pro použití klíče nebo tajného klíče

Chcete-li autorizovat aplikaci pro přístup ke klíči nebo tajný klíč v trezoru, použijte `az keyvault set-policy` příkaz.

Například pokud je název vaší trezoru ContosoKeyVault a aplikace, které chcete autorizovat má hodnotu 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed ID klienta a chcete aplikaci autorizovat pro dešifrování a podepisování pomocí klíčů ve vašem trezoru, spusťte následující:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Chcete-li tu samou aplikaci autorizovat pro čtení tajných klíčů v trezoru, spusťte následující:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Pokud chcete použít modul hardwarového zabezpečení (HSM)

Pro lepší kontrolu můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy neopustí hranice HSM. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud se vás tento požadavek netýká, přeskočte tuto část a přejděte k části [Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů](#delete-the-key-vault-and-associated-keys-and-secrets).

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

Pokud již nepotřebujete trezor klíčů a klíč nebo tajný klíč, který obsahuje, můžete trezor klíčů odstranit pomocí `az keyvault delete` příkaz:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Ostatní příkazy rozhraní příkazového řádku Azure a platformy

Další příkazy, že může využít ke správě Azure Key Vault.

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
