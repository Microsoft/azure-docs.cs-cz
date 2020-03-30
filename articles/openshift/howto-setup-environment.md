---
title: Nastavení vývojového prostředí Azure Red Hat OpenShift
description: Tady jsou předpoklady pro práci s Microsoft Azure Red Hat OpenShift.
keywords: red hat openshift nastavení nastavení
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477030"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Nastavení vývojového prostředí Azure Red Hat OpenShift

Chcete-li vytvářet a spouštět aplikace Microsoft Azure Red Hat OpenShift, budete muset:

* Nainstalujte verzi 2.0.65 (nebo vyšší) azure cli (nebo použijte Azure Cloud Shell).
* Zaregistrujte `AROGA` se pro funkci a přidružené zprostředkovatele prostředků.
* Vytvořte klienta Azure Active Directory (Azure AD).
* Vytvořte aplikační objekt Azure AD.
* Vytvořte uživatele Azure AD.

Následující pokyny vás provedou všemi těmito předpoklady.

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Azure Red Hat OpenShift vyžaduje verzi 2.0.65 nebo vyšší z Azure CLI. Pokud jste už nainstalovali azure cli, můžete zkontrolovat, kterou verzi máte spuštěním:

```azurecli
az --version
```

První řádek výstupu bude mít například `azure-cli (2.0.65)`verzi CLI .

Tady jsou pokyny pro [instalaci příkazového příkazu k onomu Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pokud požadujete novou instalaci nebo upgrade.

Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Při použití Prostředí Azure Cloud Shell, nezapomeňte vybrat **prostředí Bash,** pokud máte v plánu sledovat spolu s [vytvořit a spravovat Azure Red Hat OpenShift clusteru](tutorial-create-cluster.md) výukové řady.

## <a name="register-providers-and-features"></a>Registrace poskytovatelů a funkcí

Funkce `Microsoft.ContainerService AROGA` , `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage`, `Microsoft.KeyVault` a `Microsoft.Network` zprostředkovatelé musí být registrovány do vašeho předplatného ručně před nasazením prvního clusteru Azure Red Hat OpenShift.

Chcete-li tyto zprostředkovatele a funkce zaregistrovat ručně, použijte následující pokyny z prostředí Bash, pokud jste nainstalovali cli nebo z relace Azure Cloud Shell (Bash) na vašem webu Azure Portal:

1. Pokud máte více předplatných Azure, zadejte příslušné ID předplatného:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zaregistrujte funkci Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zaregistrujte poskytovatele Microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zaregistrujte poskytovatele Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Zaregistrujte poskytovatele Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Zaregistrujte poskytovatele služby Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Zaregistrujte poskytovatele Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aktualizujte registraci poskytovatele prostředků Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Vytvoření klienta Služby Azure Active Directory (Azure AD)

Služba Azure Red Hat OpenShift vyžaduje přidruženého klienta Azure Active Directory (Azure AD), který představuje vaši organizaci a její vztah k Microsoftu. Váš klient Azure AD umožňuje registrovat, vytvářet a spravovat aplikace a také používat další služby Azure.

Pokud nemáte Azure AD použít jako tenanta pro váš cluster Azure Red Hat OpenShift, nebo chcete vytvořit klienta pro testování, postupujte podle pokynů v [příkazech Vytvořit tenanta Azure AD pro váš cluster Azure Red Hat OpenShift](howto-create-tenant.md) před pokračováním v této příručce.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Vytvoření uživatele, skupiny zabezpečení Azure AD a aplikačního objektu

Azure Red Hat OpenShift vyžaduje oprávnění k provádění úloh v clusteru, jako je například konfigurace úložiště. Tato oprávnění jsou reprezentována prostřednictvím [instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Budete také chtít vytvořit nového uživatele služby Active Directory pro testování aplikací spuštěných v clusteru Azure Red Hat OpenShift.

Postupujte podle pokynů v [části Vytvoření objektu aplikace Azure AD a uživatele](howto-aad-app-configuration.md) k vytvoření instančního objektu služby, generování adresy URL klienta pro volání a ověřování pro vaši aplikaci a vytvoření nové skupiny zabezpečení Azure AD a uživatele pro přístup ke clusteru.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni k použití Azure Red Hat OpenShift!

Vyzkoušejte výukový program:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
