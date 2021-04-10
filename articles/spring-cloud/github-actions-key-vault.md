---
title: Ověřování Azure jaře cloudu s Key Vault v akcích GitHubu
description: Použití trezoru klíčů s pracovním postupem CI/CD pro jarní Cloud v Azure s akcemi GitHubu
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877930"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Ověřování Azure jaře cloudu s Key Vault v akcích GitHubu

**Tento článek se týká:** ✔️ Java ✔️ C #

Trezor klíčů je bezpečné místo pro ukládání klíčů. Podnikoví uživatelé potřebují ukládat přihlašovací údaje pro prostředí CI/CD v oboru, který řídí. Klíč pro získání přihlašovacích údajů v trezoru klíčů by měl být omezený na obor prostředků.  Má přístup jenom k oboru trezoru klíčů, ne k celému oboru Azure. Je to jako klíč, který může otevřít pouze silné pole, nikoli hlavní klíč, který může otevřít všechny dveře v budově. Je to způsob, jak získat klíč s jiným klíčem, který je užitečný pro CICD pracovní postup. 

## <a name="generate-credential"></a>Generovat přihlašovací údaje
Pokud chcete vygenerovat klíč pro přístup k trezoru klíčů, spusťte níže uvedený příkaz na svém místním počítači:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Rozsah určený `--scopes` parametrem omezuje přístup klíče k prostředku.  Může přistupovat jenom k silnému poli.

S výsledky:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Pak výsledky uložte do **tajných** kódů GitHubu, jak je popsáno v tématu [Nastavení úložiště GitHubu a ověření pomocí Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Přidat zásady přístupu pro přihlašovací údaje
Přihlašovací údaje, které jste vytvořili výše, můžou získat obecné informace o Key Vault, nikoli obsah, který ukládá.  Pro získání tajných kódů uložených v Key Vault musíte nastavit zásady přístupu pro přihlašovací údaje.

V Azure Portal přejděte na řídicí panel **Key Vault** , klikněte na nabídku **řízení přístupu** a pak otevřete kartu **přiřazení rolí** . Vyberte **aplikace** pro **typ** a `This resource` **Rozsah**.  Měli byste vidět přihlašovací údaje, které jste vytvořili v předchozím kroku:

 ![Nastavení zásad přístupu](./media/github-actions/key-vault1.png)

Zkopírujte název přihlašovacích údajů, například `azure-cli-2020-01-19-04-39-02` . Otevřete nabídku **zásady přístupu** , klikněte na **+ Přidat odkaz zásady přístupu** .  Vyberte možnost `Secret Management` pro **šablonu** a pak vyberte **objekt zabezpečení**. Vložte název přihlašovacích údajů do **objektu zabezpečení** / **Vybrat** vstupní pole:

 ![Vyberte](./media/github-actions/key-vault2.png)

 Klikněte na tlačítko **Přidat** v dialogovém okně **Přidat zásadu přístupu** a pak klikněte na **Uložit**.

## <a name="generate-full-scope-azure-credential"></a>Generování přihlašovacích údajů Azure v plném rozsahu
Toto je hlavní klíč pro otevření všech dveří v budově. Postup je podobný předchozímu kroku, ale v tomto článku změníte obor, který vygeneruje hlavní klíč:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Znovu, výsledky:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Zkopírujte celý řetězec JSON.  Bo zpátky na **Key Vault** řídicí panel. Otevřete nabídku **tajné klíče** a pak klikněte na tlačítko **Generovat/importovat** . Zadejte název tajného kódu, například `AZURE-CREDENTIALS-FOR-SPRING` . Vložte řetězec přihlašovacích údajů JSON do vstupního pole **Value (hodnota** ). Můžete si všimnout, že vstupní pole hodnoty je jednořádkové textové pole místo víceřádkové textové oblasti.  Do tohoto pole můžete vložit celý řetězec JSON.

 ![Úplné přihlašovací údaje oboru](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Kombinování přihlašovacích údajů v akcích GitHubu
Nastavte přihlašovací údaje, které se použijí, když se CICD kanál spustí:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Další kroky
* [Akce GitHubu na jaře Cloud](./spring-cloud-howto-github-actions.md)
