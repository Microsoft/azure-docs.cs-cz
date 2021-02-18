---
title: 'Rychlý Start: Vytvoření první statické lokality pomocí statického Web Apps Azure pomocí rozhraní příkazového řádku'
description: Naučte se nasadit statickou lokalitu do Azure static Web Apps pomocí Azure CLI.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 0de65c4546f5e87377b119370ee629f5a365b24e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650524"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Rychlý Start: Vytvoření první statické lokality pomocí Azure CLI

Služba Azure static Web Apps publikuje web do produkčního prostředí tím, že vytváří aplikace z úložiště GitHub. V tomto rychlém startu nasadíte webovou aplikaci do statických webových aplikací Azure pomocí Azure CLI.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Požadavky

- Účet [GitHub](https://github.com)
- [Osobní přístupový token GitHubu](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- Účet [Azure](https://portal.azure.com)
- Nainstalované rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) (verze 2.8.0 a vyšší)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

V dalším kroku přepněte do nové složky pomocí následujícího příkazu.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

Teď, když je úložiště vytvořené, můžete vytvořit statickou webovou aplikaci z Azure CLI.

> [!IMPORTANT]
> Ujistěte se, že jste v terminálu ve složce _My-First-static-Web-App_ .

1. Přihlaste se k Azure CLI pomocí následujícího příkazu.

    ```azurecli
    az login
    ```

1. Vytvořte novou statickou webovou aplikaci z úložiště.

    # <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Tuto hodnotu nahraďte existujícím názvem skupiny prostředků Azure.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Nahraďte tuto hodnotu vaším uživatelským jménem GitHubu.

    - `<LOCATION>`: Nahraďte tuto hodnotu nejbližším umístěním. Mezi možnosti patří: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ a _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Tuto hodnotu nahraďte [tokenem osobního přístupu GitHubu](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) , který jste předtím vygenerovali.

    Vytvořenou aplikaci teď můžete zobrazit v Azure.

1. Otevřete [Azure Portal](https://portal.azure.com).

1. V horním vyhledávacím panelu vyhledejte text- **First-web-static-App** .

1. Vyberte **My-First-web-static-App**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete instanci služby Azure static Web Apps odstranit spuštěním následujícího příkazu:

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
