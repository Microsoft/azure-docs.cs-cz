---
title: Ověření Azure Spring Cloud u trezoru klíčů v akcích GitHubu
description: Jak používat trezor klíčů s pracovním postupem CI/CD pro Azure Spring Cloud s akcemi GitHubu
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945472"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Ověření Azure Spring Cloud u trezoru klíčů v akcích GitHubu
Trezor klíčů je bezpečné místo pro uložení klíčů. Podnikoví uživatelé potřebují ukládat pověření pro prostředí CI/CD v oboru, který řídí. Klíč k získání pověření v trezoru klíčů by měl být omezen na obor prostředků.  Má přístup pouze k oboru trezoru klíčů, nikoli k celému oboru Azure. Je to jako klíč, který může otevřít pouze silnou krabici, ne hlavní klíč, který může otevřít všechny dveře v budově. Je to způsob, jak získat klíč s jiným klíčem, což je užitečné v pracovním postupu CICD. 

## <a name="generate-credential"></a>Generovat pověření
Chcete-li vygenerovat klíč pro přístup k trezoru klíčů, spusťte příkaz níže v místním počítači:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Obor určený parametrem `--scopes` omezuje přístup klíče k prostředku.  Může přistupovat pouze k silné krabici.

S výsledky:
```
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
Pak uložte výsledky do **tajných kódů** GitHubu, jak je popsáno v [části Nastavení úložiště GitHubu a ověření pomocí Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Přidání přístupových zásad pro pověření
Přihlašovací údaje, které jste vytvořili výše, mohou získat pouze obecné informace o trezoru klíčů, nikoli o obsahu, který ukládá.  Chcete-li získat tajné klíče uložené v trezoru klíčů, je třeba nastavit zásady přístupu pro pověření.

Přejděte na řídicí panel Trezor **klíčů** na portálu Azure Portal, klikněte **Apps** na **Type** nabídku **scope** **Řízení přístupu** a otevřete kartu **Přiřazení rolí.** `This resource`  Měli byste vidět pověření, které jste vytvořili v předchozím kroku:

 ![Nastavení zásad přístupu](./media/github-actions/key-vault1.png)

Zkopírujte název pověření, `azure-cli-2020-01-19-04-39-02`například . Otevřete nabídku **Zásady přístupu** a klikněte na +Přidat odkaz **Zásady přístupu.**  Vyberte `Secret Management` pro **šablonu**, pak vyberte **hlavní**. Vložte název pověření do **vstupního**/pole Hlavní**výběr:**

 ![Vyberte](./media/github-actions/key-vault2.png)

 Klepněte na tlačítko **Přidat** v dialogovém okně **Přidat zásady přístupu** a potom klepněte na tlačítko **Uložit**.

## <a name="generate-full-scope-azure-credential"></a>Generovat pověření Azure s plným rozsahem
Tohle je hlavní klíč k otevření všech dveří v budově. Postup je podobný předchozímu kroku, ale zde změníme rozsah pro generování hlavního klíče:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Opět platí, že výsledky:
```
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
Zkopírujte celý řetězec JSON.  Bo zpět na řídicí panel **Trezoru klíčů.** Otevřete nabídku **Tajné klíče** a klepněte na tlačítko **Generovat/importovat.** Zadejte tajný název, `AZURE-CRENDENTIALS-FOR-SPRING`například . Vložte řetězec pověření JSON do pole **Vstup pro hodnotu.** Můžete si všimnout, že vstupní pole hodnoty je jednořádkové textové pole, nikoli víceřádková textová oblast.  Můžete vložit kompletní Řetězec JSON tam.

 ![Pověření s úplným rozsahem](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Kombinování přihlašovacích údajů v akcích GitHubu
Nastavte pověření použitá při spuštění kanálu CICD:

```
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
        keyvault: "zlhe-test"
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
* [Akce Github u spring cloudu](./spring-cloud-howto-github-actions.md)