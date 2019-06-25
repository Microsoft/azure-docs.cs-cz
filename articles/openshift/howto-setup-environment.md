---
title: Nastavení vývojového prostředí Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zde jsou uvedeny požadavky pro práci s Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: nastavení systému Red hat openshift nastavit
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 21ccd7b2919714610dbd51c62701c4bb32d330d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808834"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Nastavení vývojového prostředí Azure Red Hat OpenShift

K vytváření a spouštění aplikací v Microsoft Azure Red Hat OpenShift, bude nutné:

* Koupit Azure rezervované instance virtuálních počítačů.
* Nainstalovat verzi 2.0.65 (nebo vyšší) z příkazového řádku Azure (nebo použijte Azure Cloud Shell).
* Zaregistrujte se `AROGA` funkce a poskytovatelů prostředků přidružené.
* Vytvoření tenanta Azure Active Directory (Azure AD).
* Vytvoření objektu aplikace Azure AD.
* Vytvoření uživatele služby Azure AD.

Postupujte podle následujících pokynů vás provede všechny tyto požadavky.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Nákup rezervované instance Azure Red Hat OpenShift aplikace uzly

Než budete moct použít Azure Red Hat OpenShift, budete muset zakoupit minimálně 4 uzly aplikace Azure Red Hat OpenShift vyhrazené, po jejichž uplynutí je budete moct zřizování clusterů.

Pokud jste zákazník Azure [koupit Azure Red Hat OpenShift rezervované instance](https://aka.ms/openshift/buy) prostřednictvím webu Azure portal. Po zakoupení, aktivuje vaše předplatné během 24 hodin.

Pokud si nejste zákazník Azure [kontaktujte prodej](https://aka.ms/openshift/contact-sales) a vyplňte formulář prodeje v dolní části stránky zahájíte proces.

Odkazovat [stránce s cenami Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) Další informace.

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Azure Red Hat OpenShift vyžaduje verzi 2.0.65 nebo vyšší rozhraní příkazového řádku Azure. Pokud jste již nainstalovali rozhraní příkazového řádku Azure, můžete zjistit, které verze máte spuštěním:

```bash
az --version
```

První řádek výstupu bude mít verzi rozhraní příkazového řádku, například `azure-cli (2.0.65)`.

Toto jsou pokyny pro [instalaci rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud budete potřebovat nové instalace nebo upgrade.

Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Pokud používáte Azure Cloud Shell, je potřeba vybrat možnost **Bash** prostředí, pokud budete chtít postupovat podle [vytvoření a Správa clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) série kurzů.

## <a name="register-providers-and-features"></a>Zaregistrovat poskytovatele a funkce

`Microsoft.ContainerService AROGA` Funkce `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` a `Microsoft.Network` zprostředkovatele musí být zaregistrovaný k vašemu předplatnému ručně před nasazením svůj první cluster Azure Red Hat OpenShift.

Ručně registrovat tyto poskytovatele a funkce, použijte následující pokyny z prostředí Bash, pokud jste nainstalovali rozhraní příkazového řádku nebo z relace Azure Cloud Shell (Bash) vašeho webu Azure Portal:

1. Pokud máte více předplatných Azure, zadejte ID předplatného odpovídající:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrovat funkci Microsoft.ContainerService AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zaregistrujte poskytovatele Microsoft.Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrace poskytovatele Microsoft.Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrace poskytovatele Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrace poskytovatele Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registrace poskytovatele Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aktualizujte registraci poskytovatele prostředků Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Vytvoření tenanta Azure Active Directory (Azure AD)

Služba Azure Red Hat OpenShift vyžaduje, aby přidružené tenanta Azure Active Directory (Azure AD), který představuje vaše organizace a její vztah Microsoftu. Svého tenanta Azure AD umožňuje zaregistrovat, sestavení a spravovat aplikace, jakož i využívat další služby Azure.

Pokud nemáte Azure AD pro účely vašeho clusteru Azure Red Hat OpenShift jako tenant nebo chcete vytvořit tenanta pro testování, postupujte podle pokynů v [vytvoření tenanta Azure AD pro váš cluster Azure Red Hat OpenShift](howto-create-tenant.md) před Pokračujte v tomto průvodci.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Vytvoření uživatele, skupiny zabezpečení a aplikace Azure AD objektu

Azure Red Hat OpenShift vyžaduje oprávnění k provádění úloh na clusteru, jako je například konfigurace úložiště. Tato oprávnění jsou představovány [instanční objekt služby](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Také budete chtít vytvořit nového uživatele služby Active Directory pro účely testování aplikace běžící v clusteru Azure Red Hat OpenShift.

Postupujte podle pokynů v [vytvoření objektu aplikace Azure AD a uživatel](howto-aad-app-configuration.md) k vytvoření instančního objektu, generovat URL klienta tajný klíč a ověřování zpětného volání pro vaši aplikaci a vytvořit novou skupinu zabezpečení Azure AD a uživatel pro přístup k cluster.

## <a name="next-steps"></a>Další postup

Teď můžete začít používat Azure Red Hat OpenShift.

Projděte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
