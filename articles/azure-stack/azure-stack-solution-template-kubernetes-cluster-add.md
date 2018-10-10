---
title: Přidat Kubernetes pro Azure Stack Marketplace | Dokumentace Microsoftu
description: Zjistěte, jak přidat Kubernetes pro Azure Stack Marketplace.
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
ms.date: 10/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 9250e38eadb9d9b1e8bf0c09fa1a9a165e5e69f1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886258"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Přidat Kubernetes na webu Marketplace služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!note]  
> Kubernetes ve službě Azure Stack je ve verzi preview.

Uživatelům můžete nabídnout Kubernetes jako položky Marketplace. Vaši uživatelé můžou nasazovat Kubernetes v rámci jediné koordinované operace.

Následující článek se zabývá pomocí šablony Azure Resource Manageru k nasazení a zřizování prostředkům pro samostatného clusteru Kubernetes. Položka Marketplace clusteru Kubernetes 0.3.0 vyžaduje verzi služby Azure Stack. 1808. Než začnete, zkontrolujte Azure Stack a nastavení globální tenanta Azure. Shromážděte požadované informace o Azure Stack. Přidáte prostředky potřebné pro vašeho tenanta a na Azure Marketplace zásobníku. Clusteru závisí na serveru se systémem Ubuntu, vlastní skript a Kubernetes položky, které budou na webu Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Vytvořit plán, nabídky a předplatné

Vytvořte plán, nabídky a předplatné pro položky Kubernetes Marketplace. Můžete také použít existující plán a nabídnout.

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

1. Vyberte nejnovější verzi serveru s následující profil:
    - **Vydavatel**: Canonical
    - **Nabízejí**: UbuntuServer
    - **SKLADOVÁ POLOŽKA**: 16.04 LTS

1. Vyberte **stáhnout.**

## <a name="add-a-custom-script-for-linux"></a>Přidání vlastních skriptů pro Linux

Přidáte rozhraní Kubernetes na Marketplace:

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Custom Script for Linux`.

1. Vyberte skript, který se následující profil:
    - **Nabízejí**: vlastní skript pro Linux 2.0
    - **Verze**: 2.0.6
    - **Vydavatel**: Microsoft Corp.

    > [!Note]  
    > Může být uvedeno více než jednu verzi vlastních skriptů pro Linux. Je potřeba přidat verzi, která odpovídá. Kubernetes vyžaduje přesné verze položky.

1. Vyberte **stáhnout.**


## <a name="add-kubernetes-to-the-marketplace"></a>Přidat Kubernetes na webu Marketplace

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Kubernetes`.

1. Vyberte `Kubernetes Cluster`.

1. Vyberte **stáhnout.**

    > [!note]  
    > Může trvat 5 minut, další položky marketplace se zobrazí na webu Marketplace.

    ![Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualizace nebo odebrání rozhraní Kubernetes 

Při aktualizaci položky Kubernetes, musíte odebrat položku, která je na webu Marketplace. Potom postupujte podle pokynů v tomto článku a přidání Kubernetes na webu Marketplace.

Odebrat položku Kubernetes:

1. Připojení ke službě Azure Stack pomocí prostředí PowerShell jako operátor. Pokyny naleznete v tématu [připojit ke službě Azure Stack pomocí prostředí PowerShell jako operátor](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Aktuální položka clusteru Kubernetes najdete v galerii.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Poznamenejte si název aktuální položky, jako například `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. Použijte následující rutiny Powershellu pro odebrání položky:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Další postup

[Nasazení Kubernetes pro Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Přehled nabízených služeb ve službě Azure Stack](azure-stack-offer-services-overview.md)