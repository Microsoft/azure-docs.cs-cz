---
title: Přidání clusteru Kubernetes do Tržiště Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak přidat Kubernetes Cluster pro Azure Stack Marketplace.
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
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: ded2aa17fe9b8de2d8c8f662f5d99b1ce33a2b25
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634196"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Přidání clusteru Kubernetes do Tržiště Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!note]  
> Modul AKS (služby Azure Kubernetes Service) ve službě Azure Stack je ve verzi private preview. Požádat o přístup k položce Kubernetes Marketplace nutná k provádění pokynů v tomto článku [odeslat požadavek na získání přístupu](https://aka.ms/azsk8).

Kubernetes Cluster jako položky Marketplace nabízí svým uživatelům. Vaši uživatelé můžou nasazovat Kubernetes v rámci jediné koordinované operace.

Následující článek se zabývá pomocí šablony Azure Resource Manageru k nasazení a zřizování prostředkům pro samostatného clusteru Kubernetes. Než začnete, zkontrolujte Azure Stack a nastavení globální tenanta Azure. Shromážděte požadované informace o Azure Stack. Přidáte prostředky potřebné pro vašeho tenanta a na Azure Marketplace zásobníku. Clusteru závisí na serveru se systémem Ubuntu, vlastní skript a clusteru Kubernetes položky, které budou na webu Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Vytvořit plán, nabídky a předplatné

Vytvořte plán, nabídky a předplatné pro položky Marketplace clusteru Kubernetes. Můžete také použít existující plán a nabídnout.

1. Přihlaste se k [portál pro správu.](https://adminportal.local.azurestack.external)

1. Vytvoření plánu jako základní plán. Pokyny najdete v tématu [vytvoření plánu ve službě Azure Stack](azure-stack-create-plan.md).

1. Vytvořte nabídku. Pokyny najdete v tématu [vytvoření nabídky ve službě Azure Stack](azure-stack-create-offer.md).

1. Vyberte **nabízí**a najít vámi vytvořená nabídka.

1. Vyberte **přehled** v okně nabídky.

1. Vyberte **změnit stav**. Vyberte **veřejné**.

1. Vyberte **+ vytvořit prostředek** > **nabídky a plány** > **předplatné** vytvořit si nové předplatné.

    a. Zadejte **zobrazovaný název**.

    b. Zadejte **uživatele**. Použijte účet Azure AD přidružené k tenantovi.

    c. **Popis zprostředkovatele**

    d. Nastavte **adresář tenanta** do tenanta služby Azure AD pro Azure Stack. 

    e. Vyberte **nabízejí**. Vyberte název nabídky, která jste vytvořili. Poznamenejte si ID předplatného.

## <a name="add-an-ubuntu-server-image"></a>Přidat Ubuntu server image

Přidejte následující image Ubuntu Server na webu Marketplace:

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby**a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `UbuntuServer`.

1. Vyberte server s následující profil:
    - **Vydavatel**: Canonical
    - **Nabízejí**: UbuntuServer
    - **SKLADOVÁ POLOŽKA**: 16.04 LTS
    - **Verze**: 16.04.201802220

    > [!Note]  
    > Může být uvedeno více než jedna verze Ubuntu Server 16.04 LTS. Je potřeba přidat verzi, která odpovídá. Kubernetes Cluster vyžaduje přesné verze položky.

1. Vyberte **stáhnout.**

## <a name="add-a-custom-script-for-linux"></a>Přidání vlastních skriptů pro Linux

Přidání clusteru Kubernetes z Marketplace:

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Custom Script for Linux`.

1. Vyberte skript, který se následující profil:
    - **Nabízejí**: vlastní skript pro Linux 2.0
    - **Verze**: 2.0.3
    - **Vydavatel**: Microsoft Corp.

    > [!Note]  
    > Může být uvedeno více než jednu verzi vlastních skriptů pro Linux. Je potřeba přidat verzi, která odpovídá. Kubernetes Cluster vyžaduje přesné verze položky.

1. Vyberte **stáhnout.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Přidejte Kubernetes Cluster na webu Marketplace

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **přidat služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Kubernetes Cluster`.

1. Vyberte `Kubernetes Cluster`.

1. Vyberte **stáhnout.**

    > [!note]  
    > Může trvat 5 minut, další položky marketplace se zobrazí na webu Marketplace.

    ![Kubernetes Cluster](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Aktualizace nebo odebrání clusteru Kubernetes 

Při aktualizaci položky clusteru Kubernetes, musíte odebrat položku, která je na webu Marketplace. Potom postupujte podle pokynů v tomto článku a přidání clusteru Kubernetes na webu Marketplace.

Odebrání clusteru Kubernetes položky:

1. Poznamenejte si název aktuální položky, jako například `Microsoft.AzureStackKubernetesCluster.0.1.0`

1. Připojení k Azure Stack pomocí Powershellu.

1. Použijte následující rutiny Powershellu pro odebrání položky:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Další postup

[Nasazení clusteru Kubernetes do služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Přehled nabízených služeb ve službě Azure Stack](azure-stack-offer-services-overview.md)