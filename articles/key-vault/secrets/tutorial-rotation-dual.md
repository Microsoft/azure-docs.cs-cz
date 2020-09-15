---
title: Kurz rotace pro prostředky se dvěma sadami přihlašovacích údajů
description: V tomto kurzu se dozvíte, jak automatizovat rotaci tajného klíče pro prostředky, které používají dvě sady ověřovacích přihlašovacích údajů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: f1a29866715c746e7a7eef82f11237bcbd159925
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086823"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatizace rotace tajného klíče pro prostředky se dvěma sadami ověřovacích přihlašovacích údajů

Nejlepším způsobem, jak ověřit služby Azure, je použití [spravované identity](../general/authentication.md), ale v některých případech se nejedná o možnost. V těchto případech se používají přístupové klíče nebo hesla. Přístupové klíče a hesla by se měly často otáčet.

V tomto kurzu se dozvíte, jak automatizovat pravidelnou rotaci tajných kódů pro databáze a služby, které používají dvě sady ověřovacích přihlašovacích údajů. Konkrétně tento kurz otočí Azure Storage klíčů účtu uložených v Azure Key Vault jako tajné klíče pomocí funkce aktivované Azure Event Gridm oznámením. :

> [!NOTE]
> Klíče účtu úložiště se dají automaticky spravovat v Key Vault tím, že poskytují tokeny sdíleného přístupového podpisu pro delegovaný přístup k účtu úložiště. Existují služby, které vyžadují připojovací řetězec účtu úložiště s přístupovým klíčem a pro tento scénář se toto řešení doporučuje.

![Diagram řešení rotace](../media/secrets/rotation-dual/rotation-diagram.png)

Ve výše uvedeném řešení Azure Key Vault ukládá jednotlivé přístupové klíče účtu úložiště jako verze stejného tajného klíče mezi primárním a sekundárním klíčem v následujících verzích. V případě, že je jeden přístupový klíč uložený v nejnovější verzi tajného kódu, alternativní klíč se znovu vygeneruje a přidají se Key Vault jako nová a nejnovější verze tajného kódu. Toto řešení poskytuje aplikacím celý cyklus rotace k aktualizaci na nejnovější obnovený klíč. 

1. 30 dní před datem vypršení platnosti tajného kódu Key Vault pro Event Grid publikovat událost blížící se vypršení platnosti.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod aplikace Function App, který se přihlásí k odběru události.
1. Aplikace Function App identifikuje alternativní klíč (jiný než poslední) a volá účet úložiště, který ho znovu vygeneruje.
1. Aplikace Function App přidá nový znovu vygenerovaný klíč, který bude Azure Key Vault jako nová verze tajného kódu.

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* Dva účty Azure Storage

Pokud nemáte existující Trezor klíčů a účty úložiště, můžete použít odkaz pod odkazem na nasazení:

[![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. V části **Skupina prostředků**vyberte **vytvořit novou**. Pojmenujte skupinu **akvrotation** a klikněte na **OK**.
1. Vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

    ![Vytvoření skupiny prostředků](../media/secrets/rotation-dual/dual-rotation-1.png)

Teď budete mít Trezor klíčů a dva účty úložiště. Tuto instalaci můžete ověřit v Azure CLI spuštěním následujícího příkazu:

```azurecli
az resource list -o table -g akvrotation
```

Výsledek bude vypadat přibližně na následujícím výstupu:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Vytvoření a nasazení funkce pro otočení klíče účtu úložiště

Dále vytvořte aplikaci funkcí s identitou spravovanou systémem, kromě dalších požadovaných součástí a nasaďte funkce pro rotaci klíčů účtu úložiště.

Funkce rotace aplikace funkcí vyžadují tyto komponenty a konfiguraci:
- Plán Azure App Service
- Účet úložiště vyžadovaný pro správu triggeru aplikace Function App
- Zásady přístupu pro přístup k tajným klíčům v Key Vault
- Přiřazení role služby operátora klíče účtu úložiště k aplikaci Function App pro přístup k přístupovým klíčům účtu úložiště
- Funkce rotace klíčů účtu úložiště s triggerem událostí a triggerem protokolu HTTP (rotace na vyžádání)
- Odběr události EventGrid pro událost **SecretNearExpiry**

1. Vyberte odkaz nasazení šablony Azure: 

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. V seznamu **Skupina prostředků** vyberte **akvrotation**.
1. Do pole **název účtu úložiště**zadejte název účtu úložiště s přístupovými klíči, které se mají otočit.
1. Do **Key Vault název**zadejte název trezoru klíčů.
1. Do **Function App název**zadejte název aplikace Function App.
1. Do **název tajného klíče**zadejte tajný název, kde se budou ukládat přístupové klíče.
1. Do pole **Adresa URL úložiště**zadejte umístění GitHubu pro kód funkce umístění ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ).
1. Vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

   ![Kontrola a vytvoření prvního účtu úložiště](../media/secrets/rotation-dual/dual-rotation-2.png)

Po dokončení předchozích kroků budete mít účet úložiště, serverovou farmu, aplikaci funkcí a Application Insights. Po dokončení nasazení by se měla zobrazit pod obrazovkou: ![ nasazení bylo dokončeno.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> V případě jakýchkoli selhání můžete kliknout na **znovu nasadit** a dokončit nasazení zbývajících komponent.


Šablony nasazení a kód funkcí rotace najdete na [GitHubu](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Přidání přístupového klíče k účtu úložiště pro Key Vault

Nejdřív nastavte zásady přístupu tak, aby uživatelům udělila oprávnění ke *správě tajných* kódů:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Nyní můžete vytvořit nový tajný klíč pomocí účtu úložiště přístupová klávesa jako hodnota. Budete také potřebovat ID prostředku účtu úložiště, dobu platnosti tajného klíče a ID klíče pro přidání do tajného kódu, takže funkce otáčení může znovu vygenerovat klíč v účtu úložiště.

Načtěte ID prostředku účtu úložiště. Hodnota se dá najít v části `id` Property.
```azurecli
az storage account show -n akvrotationstorage
```

Vypíše přístupové klíče účtu úložiště, aby se načetly hodnoty klíčů.

```azurecli
az storage account keys list -n akvrotationstorage 
```

Naplnit načtené hodnoty pro **key1Value** a **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Vytvoření tajného klíče s krátkým datem vypršení platnosti způsobí publikování `SecretNearExpiry` události během několika minut. tím se zase aktivuje funkce pro otočení tajného klíče.

Obnovení přístupových klíčů můžete ověřit tak, že načtete a porovnáte klíče účtu úložiště a Key Vault tajný klíč.

Tajné informace můžete zobrazit pomocí příkazu níže:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Všimněte si, že `CredentialId` se aktualizovala na alternativní `keyName` a `value` je znovu vygenerován ![ výstup pro první účet úložiště jako první.](../media/secrets/rotation-dual/dual-rotation-4.png)

Načtení přístupových klíčů pro ověření hodnoty
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Výstup seznamu AZ Storage Account Keys pro první účet úložiště](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Přidání dalších účtů úložiště pro rotaci

Stejnou aplikaci Function App lze znovu použít k otočení několika účtů úložiště. 

Přidání dalších klíčů účtu úložiště pro otočení do existující funkce vyžaduje:
- Přiřazení role služby operátora klíče účtu úložiště k aplikaci Function App pro přístup k přístupovým klíčům účtu úložiště
- Odběr události EventGrid pro událost **SecretNearExpiry**

1. Vyberte odkaz nasazení šablony Azure: 

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. V seznamu **Skupina prostředků** vyberte **akvrotation**.
1. Do pole **název účtu úložiště**zadejte název účtu úložiště s přístupovými klíči, které se mají otočit.
1. Do **Key Vault název**zadejte název trezoru klíčů.
1. Do **Function App název**zadejte název aplikace Function App.
1. Do **název tajného klíče**zadejte tajný název, kde se budou ukládat přístupové klíče.
1. Vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

   ![Kontrola a vytvoření druhého účtu úložiště](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Přidat další přístupový klíč k účtu úložiště pro Key Vault

Načtěte ID prostředku účtu úložiště. Hodnota se dá najít v části `id` Property.
```azurecli
az storage account show -n akvrotationstorage2
```

Vypíše přístupové klíče účtu úložiště, aby se načetla hodnota key2.

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Naplnit načtené hodnoty pro **key2Value** a **storageAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Zobrazit tajné informace pomocí příkazu níže:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Všimněte si, že `CredentialId` se aktualizovala na alternativní `keyName` a `value` je znovu vygenerován ![ výstup pro druhý účet úložiště v tajnosti AZ klíčů trezor.](../media/secrets/rotation-dual/dual-rotation-8.png)

Načtení přístupových klíčů pro ověření hodnoty
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Výstup seznamu AZ Storage Account Keys pro druhý účet úložiště](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Dostupné Key Vault funkce rotace dvou přihlašovacích údajů

- [Účet úložiště](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Další informace
- Přehled: [monitorování Key Vault s](../general/event-grid-overview.md) využitím Azure Event Grid
- Postupy: [Vytvoření první funkce v Azure Portal](../../azure-functions/functions-create-first-azure-function.md)
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](../general/event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)
