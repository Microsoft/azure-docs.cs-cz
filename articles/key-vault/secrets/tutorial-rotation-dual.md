---
title: Kurz rotace pro prostředky se dvěma sadami přihlašovacích údajů
description: V tomto kurzu se dozvíte, jak automatizovat rotaci tajného klíče pro prostředky, které používají dvě sady ověřovacích přihlašovacích údajů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d75ba091ff634bf613722e3a194407beeeda68fb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967230"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizace rotace tajného klíče pro prostředky, které mají dvě sady ověřovacích přihlašovacích údajů

Nejlepším způsobem, jak ověřit služby Azure, je použití [spravované identity](../general/authentication.md), ale v některých případech se nejedná o možnost. V těchto případech se používají přístupové klíče nebo hesla. Přístupové klíče a hesla byste měli často otáčet.

V tomto kurzu se dozvíte, jak automatizovat pravidelnou rotaci tajných kódů pro databáze a služby, které používají dvě sady ověřovacích přihlašovacích údajů. Konkrétně v tomto kurzu se dozvíte, jak otočit Azure Storage klíčů účtu uložených v Azure Key Vault jako tajné klíče. Použijete funkci aktivovanou Azure Event Grid oznámení. 

> [!NOTE]
> Klíče účtu úložiště se dají automaticky spravovat v Key Vault, pokud poskytnete tokeny sdíleného přístupového podpisu pro delegovaný přístup k účtu úložiště. K dispozici jsou služby, které vyžadují připojovací řetězce účtu úložiště s přístupovými klíči. V takovém případě doporučujeme toto řešení.

Toto je řešení rotace popsané v tomto kurzu: 

![Diagram znázorňující kruhové řešení](../media/secrets/rotation-dual/rotation-diagram.png)

V tomto řešení Azure Key Vault ukládá jednotlivé přístupové klíče účtu úložiště jako verze stejného tajného klíče a mezi primárním a sekundárním klíčem v následujících verzích. Pokud je jeden přístupový klíč uložený v nejnovější verzi tajného kódu, alternativní klíč se znovu vygeneruje a přidá se do Key Vault jako nová nejnovější verze tajného kódu. Řešení poskytuje Kompletní cyklus rotace aplikace k aktualizaci na nejnovější znovu vygenerovaný klíč. 

1. Třicet dní před datem vypršení platnosti tajného kódu Key Vault publikuje událost blížící se vypršení platnosti Event Grid.
1. Event Grid zkontroluje odběry událostí a pomocí HTTP POST zavolá koncový bod aplikace Function App, který se přihlásí k odběru události.
1. Aplikace Function App identifikuje alternativní klíč (ne poslední) a volá účet úložiště, který ho má znovu vygenerovat.
1. Aplikace Function App přidá nový znovu vygenerovaný klíč, který se Azure Key Vault jako nová verze tajného kódu.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. [Vytvořte si ho zdarma.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Cloud Shell](https://shell.azure.com/)Azure. Tento kurz používá Cloud Shell portálu s prostředím PowerShell env.
* Azure Key Vault
* Dva účty úložiště Azure.

Tento odkaz na nasazení můžete použít, pokud nemáte existující Trezor klíčů a stávající účty úložiště:

[![Odkaz, který je označený jako nasazený do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. V části **Skupina prostředků** vyberte **vytvořit novou**. Pojmenujte skupinu **vaultrotation** a pak vyberte **OK**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

    ![Snímek obrazovky, který ukazuje, jak vytvořit skupinu prostředků.](../media/secrets/rotation-dual/dual-rotation-1.png)

Teď budete mít Trezor klíčů a dva účty úložiště. Tuto instalaci můžete ověřit v Azure CLI nebo Azure PowerShell spuštěním tohoto příkazu:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Výsledek bude vypadat přibližně podobně jako tento výstup:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Vytvoření a nasazení funkce pro otočení klíče

V dalším kroku vytvoříte aplikaci funkcí s identitou spravovanou systémem, a to i s dalšími požadovanými součástmi. Také nasadíte funkci otáčení pro klíče účtu úložiště.

Funkce rotace aplikace Function App vyžaduje následující komponenty a konfiguraci:
- Plán Azure App Service
- Účet úložiště pro správu triggerů aplikace Function App
- Zásady přístupu pro přístup k tajným klíčům v Key Vault
- Role služby operátora klíče účtu úložiště přiřazená k aplikaci Function App, aby mohla získat přístup k přístupovým klíčům účtu úložiště
- Funkce rotace klíče s triggerem události a triggerem protokolu HTTP (rotace na vyžádání)
- Event Grid odběr událostí pro událost **SecretNearExpiry**

1. Vyberte odkaz nasazení šablony Azure: 

   [![Odkaz na nasazení šablony Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. V seznamu **Skupina prostředků** vyberte **vaultrotation**.
1. V poli **RG účtu úložiště** zadejte název skupiny prostředků, ve které se nachází váš účet úložiště. Pokud je váš účet úložiště ve stejné skupině prostředků, kde jste nasadili funkci střídání klíčů, ponechte výchozí hodnotu **[resourceName (). name]** .
1. Do pole **název účtu úložiště** zadejte název účtu úložiště, který obsahuje přístupové klávesy, které se mají otočit. Pokud používáte účet úložiště vytvořený v části [požadavky](#prerequisites), ponechte výchozí hodnotu **[Concat (Source (). Name, ' Storage ')]** .
1. Do pole **Key Vault RG** zadejte název skupiny prostředků, ve které se nachází Trezor klíčů. Pokud váš Trezor klíčů už existuje ve stejné skupině prostředků, ve které nasadíte funkci střídání klíčů, ponechte výchozí hodnotu **[resourceName (). name]** .
1. Do pole **Key Vault název** zadejte název trezoru klíčů. Pokud používáte Trezor klíčů vytvořený v části [požadavky](#prerequisites), ponechte výchozí hodnotu **[Concat (resourceName (). Name, '-KV ')]]** .
1. V poli **typ plánu App Service** vyberte plán hostování. **Plán Premium** je nutný jenom v případě, že je váš Trezor klíčů za bránou firewall.
1. Do pole **Function App název** zadejte název aplikace Function App.
1. Do pole **tajný název** zadejte název tajného klíče, kam budete ukládat přístupové klávesy.
1. Do pole **Adresa URL úložiště** zadejte umístění GitHubu kódu funkce. V tomto kurzu můžete použít **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

   ![Snímek obrazovky, který ukazuje, jak vytvořit a nasadit funkci](../media/secrets/rotation-dual/dual-rotation-2.png)

Po dokončení předchozích kroků budete mít účet úložiště, serverovou farmu, aplikaci funkcí a Application Insights. Po dokončení nasazení se zobrazí tato stránka:

   ![Snímek obrazovky zobrazující stránku nasazení je dokončený.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Pokud dojde k chybě, můžete vybrat možnost **znovu nasadit** a dokončit tak nasazení součástí.


Šablony nasazení a kód pro funkci otočení najdete v [ukázkách Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Přidání přístupových klíčů k účtu úložiště pro Key Vault

Nejdřív nastavte zásady přístupu tak, aby udělily oprávnění ke **správě tajných** kódů vašemu uživatelskému objektu zabezpečení:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Nyní můžete vytvořit nový tajný klíč pomocí přístupového klíče účtu úložiště jako jeho hodnota. Pro přidání do tajného kódu budete také potřebovat ID prostředku účtu úložiště, dobu platnosti tajného klíče a ID klíče, aby funkce otáčení mohla znovu vygenerovat klíč v účtu úložiště.

Určete ID prostředku účtu úložiště. Tuto hodnotu můžete najít ve `id` Vlastnosti.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Vypíše přístupové klíče účtu úložiště, abyste mohli získat klíčové hodnoty:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Přidejte tajný klíč do trezoru klíčů s datem vypršení platnosti nastaveným na zítra, období platnosti pro 60 dní a ID prostředku účtu úložiště. Spusťte tento příkaz a použijte načtené hodnoty pro `key1Value` a `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Nad rámec tajného klíče se událost aktivuje `SecretNearExpiry` během několika minut. Tato událost zase aktivuje funkci pro otočení tajného kódu s vypršenou platností nastavenou na 60 dní. V této konfiguraci se událost SecretNearExpiry aktivuje každých 30 dní (30 dní před vypršením platnosti) a funkce rotace bude střídavě prostřídána mezi klíč1 a key2.

Opětovné vygenerování přístupových klíčů můžete ověřit načtením klíče účtu úložiště a Key Vault tajného kódu a jejich porovnání.

Informace o tajných klíčích získáte pomocí tohoto příkazu:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Všimněte si, že `CredentialId` je aktualizována na alternativní `keyName` a která `value` je znovu vygenerována:

![Snímek obrazovky zobrazující výstup příkazu z tajného klíče trezoru klíčů pro první účet úložiště](../media/secrets/rotation-dual/dual-rotation-4.png)

Načtěte přístupové klávesy pro porovnání hodnot:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Všimněte si, že `value` klíč je stejný jako tajný klíč v trezoru klíčů:

![Snímek obrazovky, který zobrazuje výstup příkazu seznamu klíčů účtu úložiště a z prvního účtu úložiště.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Přidání účtů úložiště pro otočení

Stejnou aplikaci Function App můžete použít k otočení klíčů pro více účtů úložiště. 

K přidání klíčů účtu úložiště do existující funkce pro rotaci potřebujete:
- Role služby operátora klíče účtu úložiště přiřazená k aplikaci Function App, aby mohla získat přístup k přístupovým klíčům účtu úložiště.
- Event Grid odběr událostí pro událost **SecretNearExpiry**

1. Vyberte odkaz nasazení šablony Azure: 

   [![Odkaz na nasazení šablony Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. V seznamu **Skupina prostředků** vyberte **vaultrotation**.
1. V poli **RG účtu úložiště** zadejte název skupiny prostředků, ve které se nachází váš účet úložiště. Pokud je váš účet úložiště ve stejné skupině prostředků, kde jste nasadili funkci střídání klíčů, ponechte výchozí hodnotu **[resourceName (). name]** .
1. Do pole **název účtu úložiště** zadejte název účtu úložiště, který obsahuje přístupové klávesy, které se mají otočit.
1. Do pole **Key Vault RG** zadejte název skupiny prostředků, ve které se nachází Trezor klíčů. Pokud váš Trezor klíčů už existuje ve stejné skupině prostředků, ve které nasadíte funkci střídání klíčů, ponechte výchozí hodnotu **[resourceName (). name]** .
1. Do pole **Key Vault název** zadejte název trezoru klíčů.
1. Do pole **Function App název** zadejte název aplikace Function App.
1. Do pole **tajný název** zadejte název tajného klíče, kam budete ukládat přístupové klávesy.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

   ![Snímek obrazovky, který ukazuje, jak vytvořit další účet úložiště.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Přidat další přístupový klíč k účtu úložiště pro Key Vault

Určete ID prostředku účtu úložiště. Tuto hodnotu můžete najít ve `id` Vlastnosti.
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Vypíše přístupové klíče účtu úložiště, abyste mohli získat hodnotu key2:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Přidejte tajný klíč do trezoru klíčů s datem vypršení platnosti nastaveným na zítra, období platnosti pro 60 dní a ID prostředku účtu úložiště. Spusťte tento příkaz a použijte načtené hodnoty pro `key2Value` a `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Informace o tajných klíčích získáte pomocí tohoto příkazu:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Všimněte si, že `CredentialId` je aktualizována na alternativní `keyName` a která `value` je znovu vygenerována:

![Snímek obrazovky zobrazující výstup příkazu z tajného klíče trezoru klíčů pro druhý účet úložiště](../media/secrets/rotation-dual/dual-rotation-8.png)

Načtěte přístupové klávesy pro porovnání hodnot:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Všimněte si, že `value` klíč je stejný jako tajný klíč v trezoru klíčů:

![Snímek obrazovky, který zobrazuje výstup příkazu seznamu klíčů účtu úložiště a z druhého účtu úložiště.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Funkce rotace Key Vault pro dvě sady přihlašovacích údajů

Šablona funkcí rotace pro dvě sady přihlašovacích údajů a několik připravených na použití funkcí:

- [Šablona projektu](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Účet úložiště](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Databáze Cosmos](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Nad rámec funkcí rotace vytvoří člen komunity a ne společnost Microsoft. Azure Functions komunity nejsou podporované v rámci programu nebo služby podpory Microsoftu a jsou dostupné tak, jak jsou, bez záruky jakéhokoli druhu.

## <a name="next-steps"></a>Další kroky

- Kurz: [rotace tajných klíčů pro jednu sadu přihlašovacích údajů](./tutorial-rotation.md)
- Přehled: [monitorování Key Vault s](../general/event-grid-overview.md) využitím Azure Event Grid
- Postupy: [Vytvoření první funkce v Azure Portal](../../azure-functions/functions-get-started.md)
- Postupy: [příjem e-mailu při změně Key Vaultch tajných klíčů](../general/event-grid-logicapps.md)
- Referenční dokumentace: [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)