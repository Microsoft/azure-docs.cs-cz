---
title: Nastavení vývojového prostředí Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zde jsou uvedeny požadavky pro práci s Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: nastavení systému Red hat openshift nastavit
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 7c5fbf135c02abf04e90865e20e902a95174598c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078112"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Nastavení vývojového prostředí Azure Red Hat OpenShift

K vytváření a spouštění aplikací v Microsoft Azure Red Hat OpenShift, bude nutné:

* Koupit Azure rezervované instance virtuálních počítačů.
* Nainstalovat verzi 2.0.64 (nebo vyšší) z příkazového řádku Azure (nebo použijte Azure Cloud Shell).
* Zaregistrujte se `openshiftmanagedcluster` funkce a poskytovatelů prostředků přidružené.
* Vytvoření tenanta Azure Active Directory (Azure AD).
* Vytvoření objektu aplikace Azure AD.
* Vytvoření uživatele služby Azure AD.

Postupujte podle následujících pokynů vás provede všechny tyto požadavky.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Koupit Azure rezervované instance virtuálních počítačů

Než budete moct použít Azure Red Hat OpenShift, budete muset koupit Azure rezervované instance virtuálních počítačů.

Pokud jste zákazníkem Azure, tady na tom, jak [nákupní virtuálních počítačů Azure rezervované instance](https://aka.ms/openshift/buy). Rezervace snižuje vaše výdaje a prostřednictvím platíte předem za plně spravovaných služeb Azure. Odkazovat na [ *co jsou Azure rezervace* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Další informace o rezervacích a jak se ušetřit peníze.

Pokud si nejste zákazník Azure [kontaktujte prodej](https://aka.ms/openshift/contact-sales) a vyplňte formulář prodeje v dolní části stránky zahájíte proces.

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Azure Red Hat OpenShift vyžaduje verzi 2.0.64 nebo vyšší rozhraní příkazového řádku Azure. Pokud jste již nainstalovali rozhraní příkazového řádku Azure, můžete zjistit, které verze máte spuštěním:

```bash
az --version
```

První řádek výstupu bude mít verzi rozhraní příkazového řádku, například `azure-cli (2.0.64)`.

Toto jsou pokyny pro [instalaci rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud budete potřebovat nové instalace nebo upgrade.

Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Pokud používáte Azure Cloud Shell, je potřeba vybrat možnost **Bash** prostředí, pokud budete chtít postupovat podle [vytvoření a Správa clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) série kurzů.

## <a name="register-providers-and-features"></a>Zaregistrovat poskytovatele a funkce

`Microsoft.ContainerService openshiftmanagedcluster` Funkce `Microsoft.Solutions`, a `Microsoft.Network` zprostředkovatele musí být zaregistrovaný k vašemu předplatnému ručně před nasazením svůj první cluster Azure Red Hat OpenShift.

Ručně registrovat tyto poskytovatele a funkce, použijte následující pokyny z prostředí Bash, pokud jste nainstalovali rozhraní příkazového řádku nebo z relace Azure Cloud Shell (Bash) vašeho webu Azure Portal:.
1. Pokud máte více předplatných Azure, zadejte ID předplatného odpovídající:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Registrovat funkci openshiftmanagedcluster Microsoft.ContainerService:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Registrace poskytovatele Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Registrace poskytovatele Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Aktualizujte registraci poskytovatele prostředků Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Vytvoření tenanta Azure Active Directory (Azure AD)

Služba Azure Red Hat OpenShift vyžaduje, aby přidružené tenanta Azure Active Directory (Azure AD), který představuje vaše organizace a její vztah Microsoftu. Svého tenanta Azure AD umožňuje zaregistrovat, sestavení a spravovat aplikace, jakož i využívat další služby Azure.

Pokud nemáte Azure AD pro účely vašeho clusteru Azure Red Hat OpenShift jako tenant nebo chcete vytvořit tenanta pro testování, postupujte podle pokynů v [vytvoření tenanta Azure AD pro váš cluster Azure Red Hat OpenShift](howto-create-tenant.md) před Pokračujte v tomto průvodci.

## <a name="create-an-azure-ad-application-object-and-user"></a>Vytvoření objektu aplikace Azure AD a uživatel

Azure Red Hat OpenShift vyžaduje oprávnění k provádění úloh na clusteru, jako je například konfigurace úložiště. Tato oprávnění jsou představovány [instanční objekt služby](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) a vytvoří se při registraci Azure AD aplikace, které reprezentují úlohy, které chcete hostovat na Azure Red Hat OpenShift. Také budete chtít vytvořit nového uživatele služby Active Directory pro účely testování aplikace běžící v clusteru Azure Red Hat OpenShift.

Postupujte podle pokynů v [vytvoření objektu aplikace Azure AD a uživatel](howto-aad-app-configuration.md) zjistěte, jak vytvořit instanční objekt služby, generovat URL klienta tajný klíč a ověřování zpětného volání pro vaši aplikaci a vytvořit nového uživatele služby Active Directory pro účely testování.

## <a name="next-steps"></a>Další postup

Teď můžete začít používat Azure Red Hat OpenShift.

Projděte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli