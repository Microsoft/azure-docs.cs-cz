---
title: Nasazení Kubernetes pro Azure Stack | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: a6e1acf3b9e69f32a8c175310134c534dbf8c561
ms.sourcegitcommit: b34df37d1ac36161b377ba56c2f7128ba7327f3f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46876612"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Nasazení Kubernetes pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Operátor Azure stacku bude muset požádat o přístup k položce Marketplace clusteru Kubernetes, která je nutná k provádění pokynů v tomto článku.

Následujícím článku se podíváme na pomocí šablony Azure Resource Manageru řešení k nasazení a zřizování prostředků pro Kubernetes v rámci jediné koordinované operace. Budete potřebovat získat požadované informace o instalaci Azure Stack, generovat šablony a pak nasadit do cloudu. Všimněte si, že šablona není stejný spravované služby AKS nabízí globální Azure, ale blíže služby ACS.

## <a name="kubernetes-and-containers"></a>Kubernetes a kontejnerů

Můžete nainstalovat Kubernetes pomocí šablon Azure Resource Manageru generovaných ACS-Engine ve službě Azure Stack. [Kubernetes](https://kubernetes.io) je open source systém pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. A [kontejneru](https://www.docker.com/what-container) je součástí bitové kopie, podobně jako virtuální počítač. Na rozdíl od virtuálního počítače image kontejneru obsahuje pouze prostředky, které potřebuje ke spuštění aplikace, jako je například kód runtime umožňující spouštění kódu, konkrétní knihovny a nastavení.

Můžete použít Kubernetes:

- Vývoj široce škálovatelné upgradovatelných, aplikací, které je možné nasadit v řádu sekund. 
- Zjednodušit návrh aplikace a zvýšit jeho spolehlivost různými aplikacemi Helm. [Příkaz Helm](https://github.com/kubernetes/helm) je balení open source nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes.
- Snadno monitorovat a diagnostikovat stav vašich aplikací díky škálování a upgradu funkce.

## <a name="prerequisites"></a>Požadavky 

Abyste mohli začít, ujistěte se, že máte správná oprávnění a že služby Azure Stack je připravena.

1. Ověřte, že můžete vytvářet aplikace ve vašem tenantovi Azure Active Directory (Azure AD). Tato oprávnění potřebných pro nasazení Kubernetes.

    Kontrolují se oprávnění, v tématu [zkontrolujte Azure Active Directory oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Generování veřejného a privátního klíče pár SSH pro přihlášení do virtuálního počítače s Linuxem v Azure stacku. Veřejný klíč budete potřebovat při vytváření clusteru.

    Generuje se klíč, v tématu [vygenerování klíče SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Zkontrolujte, zda máte platné předplatné na portálu Azure Stack tenant a, že máte dostatek veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Je nutné použít předplatné uživatele **. 

## <a name="create-a-service-principal-in-azure-ad"></a>Vytvoření instančního objektu ve službě Azure AD

1. Přihlaste se na globální [webu Azure portal](http://portal.azure.com).

1. Zkontrolujte, že jste přihlášení pomocí tenanta Azure AD přidružené k instanci služby Azure Stack. Vaše přihlášení můžete přepnout kliknutím na ikonu filtru v panelu nástrojů webu Azure.

    ![Vyberte tenanta AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Vytvořte aplikaci Azure AD.

    a. Vyberte **Azure Active Directory** > **+ registrace aplikací** > **registrace nové aplikace**.

    b. Zadejte **název** aplikace.

    c. Vyberte **webovou aplikaci nebo API**.

    d. Zadejte `http://localhost` pro **přihlašovací adresa URL**.

    c. Klikněte na možnost **Vytvořit**.

1. Poznamenejte si **ID aplikace**. ID budete potřebovat při vytváření clusteru. ID je odkazováno jako **ID klienta instančního objektu**.

1. Vyberte **nastavení** > **klíče**.

    a. Zadejte **popis**.

    b. Vyberte **nikdy nevyprší platnost** pro **Expires**.

    c. Vyberte **Uložit**. Ujistěte se, poznamenejte si řetězec klíče. Řetězec klíče budete potřebovat při vytváření clusteru. Klíč se odkazuje jako **tajný kód klienta instančního objektu**.


## <a name="give-the-service-principal-access"></a>Poskytnout přístup instančního objektu služby

Poskytněte přístup instančního objektu služby do vašeho předplatného, tak, aby objekt zabezpečení může vytvářet prostředky.

1.  Přihlaste se k [portálu Azure Stack](https://portal.local.azurestack.external/).

1. Vyberte **všechny služby** > **předplatná**.

1. Vyberte předplatné vytvořené operátor pro použití clusteru Kubernetes.

1. Vyberte **řízení přístupu (IAM)** > vyberte **+ přidat**.

1. Vyberte **Přispěvatel** role.

1. Vyberte název aplikace vytvořené pro vaši službu objektu zabezpečení. Bude pravděpodobně nutné do vyhledávacího pole zadejte název.

1. Klikněte na **Uložit**.

## <a name="deploy-a-kubernetes"></a>Nasazení Kubernetes

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > **Compute** > **clusteru Kubernetes**. Klikněte na možnost **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. Vyberte **Základy** v vytvoření Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat oblasti jedinečný název, jako například `k8s-12345`. Zkuste jste zvolili, je stejná jako skupina prostředků pojmenujte jako nejlepší praxe.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Zadejte **počet profil fond agentů**. Počet obsahuje počet agentů v clusteru. Může být od 1 do 4.

1. Zadejte **ID klienta instančního objektu** slouží od poskytovatele cloudu Kubernetes Azure.

1. Zadejte **tajný kód klienta instančního objektu** , který jste vytvořili při vytváření instančního objektu aplikace služby.

1. Zadejte **verze zprostředkovatele služby Kubernetes Azure cloudu**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

1. Vyberte vaše **předplatné** ID.

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci sady Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Zadejte nastavení služby Azure Stack

1. Vyberte **nastavení služby Azure Stack razítko**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Zadejte **koncový bod Arm Tenanta**. Toto je koncový bod Azure Resource Manageru pro připojení k vytvoření skupiny prostředků pro Kubernetes cluster. Je potřeba koncový bod pro integrovaný systém získat vaše operátory Azure stacku. Pro Azure Stack Development Kit (ASDK), můžete použít `https://management.local.azurestack.external`.


## <a name="connect-to-your-cluster"></a>Připojení k vašemu clusteru

Nyní jste připraveni k připojení k vašemu clusteru. Hlavní najdete ve vaší skupině prostředků clusteru a názvem `k8s-master-<sequence-of-numbers>`. Pro připojení k hlavnímu serveru pomocí klienta SSH. Na hlavním serveru, můžete použít **kubectl**, klienta příkazového řádku Kubernetes pro správu clusteru. Pokyny najdete v tématu [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Můžete také zjistit **Helm** Správce balíčků, které jsou užitečné pro instalaci a nasazování aplikací do clusteru. Pokyny k instalaci a pomocí Helm clusteru najdete v tématu [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Další postup

[Přidání Kubernetes na webu Marketplace (pro operátory Azure stacku)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
