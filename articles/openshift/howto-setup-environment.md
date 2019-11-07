---
title: Nastavení vývojového prostředí Azure Red Hat OpenShift | Microsoft Docs
description: Tady jsou požadavky pro práci s Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: nastavení nastavení Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581602"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Nastavení vývojového prostředí Azure Red Hat OpenShift

K sestavování a spouštění aplikací Microsoft Azure Red Hat OpenShift budete potřebovat:

* Nainstalujte verzi 2.0.65 Azure CLI (nebo novější) (nebo použijte Azure Cloud Shell).
* Zaregistrujte se na funkci `AROGA` a přidružené poskytovatele prostředků.
* Vytvořte tenanta Azure Active Directory (Azure AD).
* Vytvořte objekt aplikace služby Azure AD.
* Vytvořte uživatele Azure AD.

Následující pokyny vás provedou všemi těmito požadavky.

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Azure Red Hat OpenShift vyžaduje rozhraní příkazového řádku Azure 2.0.65 nebo vyšší verze. Pokud jste už rozhraní příkazového řádku Azure nainstalovali, můžete zjistit, kterou verzi máte spuštěnou:

```bash
az --version
```

První řádek výstupu bude mít verzi CLI, například `azure-cli (2.0.65)`.

Tady jsou pokyny pro [instalaci Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , pokud potřebujete novou instalaci nebo upgrade.

Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Pokud používáte Azure Cloud Shell, nezapomeňte vybrat prostředí **bash** , pokud plánujete postupovat podle kurzu [Vytvoření a Správa řady Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrovat poskytovatele a funkce

Před nasazením prvního clusteru Azure Red Hat OpenShift musí být do vašeho předplatného ručně zaregistrovaná funkce `Microsoft.ContainerService AROGA` `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` a `Microsoft.Network`.

Pokud chcete tyto poskytovatele a funkce zaregistrovat ručně, použijte následující pokyny z prostředí bash, pokud máte nainstalované rozhraní příkazového řádku nebo z relace Azure Cloud Shell (bash) v Azure Portal:

1. Pokud máte více předplatných Azure, zadejte příslušné ID předplatného:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zaregistrujte funkci Microsoft. ContainerService AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zaregistrujte poskytovatele Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zaregistrujte poskytovatele Microsoft. Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Zaregistrujte poskytovatele Microsoft. klíčů trezoru:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Obnovte registraci poskytovatele prostředků Microsoft. ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Vytvoření tenanta Azure Active Directory (Azure AD)

Služba Azure Red Hat OpenShift vyžaduje přidruženého tenanta Azure Active Directory (Azure AD), který představuje vaši organizaci a její vztah k Microsoftu. Váš tenant služby Azure AD umožňuje registrovat, sestavovat a spravovat aplikace a také používat další služby Azure.

Pokud Azure AD nemáte k použití jako tenanta pro cluster Azure Red Hat OpenShift nebo chcete vytvořit tenanta pro testování, postupujte podle pokynů v tématu [Vytvoření tenanta Azure AD pro cluster Azure Red Hat OpenShift](howto-create-tenant.md) , než budete pokračovat v tomto průvodci. .

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Vytvoření uživatele služby Azure AD, skupiny zabezpečení a objektu aplikace

Azure Red Hat OpenShift vyžaduje oprávnění k provádění úloh v clusteru, jako je například konfigurace úložiště. Tato oprávnění jsou reprezentována prostřednictvím [instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Budete také chtít vytvořit nového uživatele služby Active Directory pro testování aplikací běžících ve vašem clusteru Azure Red Hat OpenShift.

Postupujte podle pokynů v části [vytvoření objektu aplikace služby Azure AD a uživatele](howto-aad-app-configuration.md) k vytvoření instančního objektu, VYGENERUJTE adresu URL tajného klíče klienta a zpětného volání ověřování pro vaši aplikaci a vytvořte novou skupinu zabezpečení Azure AD a uživatele pro přístup ke clusteru.

## <a name="next-steps"></a>Další kroky

Teď jste připraveni použít Azure Red Hat OpenShift!

Vyzkoušejte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
