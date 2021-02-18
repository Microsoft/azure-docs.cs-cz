---
title: Nastavení vývojového prostředí Azure Red Hat OpenShift
description: Tady jsou požadavky pro práci s Microsoft Azure Red Hat OpenShift.
keywords: nastavení nastavení Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9654711a1399c2708ce8c9150b79eefa73387c03
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635022"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Nastavení vývojového prostředí Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

K sestavování a spouštění aplikací Microsoft Azure Red Hat OpenShift budete potřebovat:

* Nainstalujte verzi 2.0.65 Azure CLI (nebo novější) (nebo použijte Azure Cloud Shell).
* Zaregistrujte se na `AROGA` funkci a přidružené poskytovatele prostředků.
* Vytvořte tenanta Azure Active Directory (Azure AD).
* Vytvořte objekt aplikace služby Azure AD.
* Vytvořte uživatele Azure AD.

Následující pokyny vás provedou všemi těmito požadavky.

## <a name="install-the-azure-cli"></a>Instalace Azure CLI

Azure Red Hat OpenShift vyžaduje rozhraní příkazového řádku Azure 2.0.65 nebo vyšší verze. Pokud jste už rozhraní příkazového řádku Azure nainstalovali, můžete zjistit, kterou verzi máte spuštěnou:

```azurecli
az --version
```

První řádek výstupu bude mít verzi rozhraní příkazového řádku (CLI), například `azure-cli (2.0.65)` .

Tady jsou pokyny pro [instalaci Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) , pokud potřebujete novou instalaci nebo upgrade.

Alternativně můžete použít [Azure Cloud Shell](../cloud-shell/overview.md). Pokud používáte Azure Cloud Shell, nezapomeňte vybrat prostředí **bash** , pokud plánujete postupovat podle kurzu [Vytvoření a Správa řady Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrovat poskytovatele a funkce

`Microsoft.ContainerService AROGA` `Microsoft.Solutions` `Microsoft.Compute` Předtím, `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` než nasadíte první cluster Azure Red Hat OpenShift, musí být do vašeho předplatného zaregistrovaná funkce,,, a.

Pokud chcete tyto poskytovatele a funkce zaregistrovat ručně, použijte následující pokyny z prostředí bash, pokud máte nainstalované rozhraní příkazového řádku nebo z relace Azure Cloud Shell (bash) v Azure Portal:

1. Pokud máte více předplatných Azure, zadejte příslušné ID předplatného:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zaregistrujte funkci Microsoft. ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zaregistrujte poskytovatele Microsoft. Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zaregistrujte poskytovatele Microsoft. Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. klíčů trezoru:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Obnovte registraci poskytovatele prostředků Microsoft. ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Vytvoření tenanta Azure Active Directory (Azure AD)

Služba Azure Red Hat OpenShift vyžaduje přidruženého tenanta Azure Active Directory (Azure AD), který představuje vaši organizaci a její vztah k Microsoftu. Váš tenant služby Azure AD umožňuje registrovat, sestavovat a spravovat aplikace a také používat další služby Azure.

Pokud Azure AD nemáte k použití jako tenanta pro cluster Azure Red Hat OpenShift nebo chcete vytvořit tenanta pro testování, postupujte podle pokynů v tématu [Vytvoření tenanta Azure AD pro cluster Azure Red Hat OpenShift](howto-create-tenant.md) a teprve potom pokračujte v této příručce.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Vytvoření uživatele služby Azure AD, skupiny zabezpečení a objektu aplikace

Azure Red Hat OpenShift vyžaduje oprávnění k provádění úloh v clusteru, jako je například konfigurace úložiště. Tato oprávnění jsou reprezentována prostřednictvím [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). Budete také chtít vytvořit nového uživatele služby Active Directory pro testování aplikací běžících ve vašem clusteru Azure Red Hat OpenShift.

Postupujte podle pokynů v části [vytvoření objektu aplikace služby Azure AD a uživatele](howto-aad-app-configuration.md) k vytvoření instančního objektu, VYGENERUJTE adresu URL tajného klíče klienta a zpětného volání ověřování pro vaši aplikaci a vytvořte novou skupinu zabezpečení Azure AD a uživatele pro přístup ke clusteru.

## <a name="next-steps"></a>Další kroky

Teď jste připraveni použít Azure Red Hat OpenShift!

Vyzkoušejte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli