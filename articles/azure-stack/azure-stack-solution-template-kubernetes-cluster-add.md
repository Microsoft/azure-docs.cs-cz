---
title: Přidání clusteru Kubernetes do zásobníku Azure Marketplace | Microsoft Docs
description: Zjistěte, jak přidat Kubernetes Cluster do Azure Marketplace zásobníku.
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: f74d378359ec66b76fd53b95ab898f1cec9b07f7
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Přidání clusteru Kubernetes do zásobníku Azure Marketplace

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

> [!note]  
> Kubernetes kontejneru služby Azure (ACS) v zásobníku Azure je v privátní Preview verzi. Chcete-li požádat o přístup k položce Kubernetes Marketplace potřebná k provedení podle pokynů v tomto článku [odeslat žádost o získání přístupu](https://aka.ms/azsk8).

Uživatelům můžete nabízet Kubernetes Cluster jako položku Marketplace. Vaši uživatelé můžete nasadit Kubernetes v rámci jediné koordinované operace.

V následujícím článku se podívat na pomocí šablony Azure Resource Manager k nasazení a zřizování prostředků pro cluster Kubernetes samostatné. Než začnete, zkontrolujte zásobník Azure a nastavení globální klientovi Azure. Shromážděte požadované informace o vaší zásobník Azure. Přidejte potřebné prostředky klienta a do Azure Marketplace zásobníku. Clusteru závisí na Ubuntu server, vlastní skript a Kubernetes clusteru položky, které budou v marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Vytvoření plánu, nabídku a předplatného

Vytvoření plánu, nabídku a odběr pro položku Kubernetes clusteru Marketplace. Můžete také použít existující plán a nabízejí.

1. Přihlaste se k [portálu pro správu.](https://adminportal.local.azurestack.external)

2. Vytvoření plánu jako základního plánu. Pokyny najdete v tématu [vytvořit plán v zásobníku Azure](azure-stack-create-plan.md).

3. Vytvořte nabídku. Pokyny najdete v tématu [vytvořit nabídku v zásobníku Azure](azure-stack-create-offer.md).

4. Vyberte **nabízí**a najít nabídku jste vytvořili.

5. Vyberte **přehled** v okně nabídka.

6. Vyberte **změnit stav**. Vyberte **veřejné**.

7. Vyberte **+ nový** > **nabízí a plány** > **předplatné** k vytvoření nové předplatné.

    a. Zadejte **zobrazovaný název**.

    b. Zadejte **uživatele**. Použijte účet Azure AD přidružený ke klientovi.

    c. **Popis zprostředkovatele**

    d. Nastavte **klienta Directory** klienta Azure AD pro vaši Azure zásobníku. 

    e. Vyberte **nabízejí**. Vyberte název nabídky, kterou jste vytvořili. Poznamenejte si ID předplatného.

## <a name="add-an-ubuntu-server-image"></a>Přidání Ubuntu server image

Přidejte následující obrázek Ubuntu Server Marketplace:

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu**.

3. Vyberte **+ přidat z Azure**.

4. Zadejte `UbuntuServer`.

5. Vyberte server s profilem následující:
    - **Vydavatel**: kanonickém tvaru
    - **Nabízejí**: UbuntuServer
    - **SKLADOVÁ POLOŽKA**: 16.04 LTS
    - **Verze**: 16.04.201802220

    > [!Note]  
    > Více než jedna verze Ubuntu Server 16.04 LTS nemusí být zobrazeny. Musíte přidat verzi, která odpovídá. Kubernetes Cluster vyžaduje přesnou verzi položky.

6. Vyberte **stáhnout.**

## <a name="add-a-custom-script-for-linux"></a>Přidat vlastní skript pro Linux

Přidejte Kubernetes Cluster z Marketplace:

1. Otevřete [portálu pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu**.

3. Vyberte **+ přidat z Azure**.

4. Zadejte `Custom Script for Linux`.

5. Vyberte skript s profilem následující:
    - **Nabízejí**: vlastní skript pro Linux 2.0
    - **Verze**: 2.0.3
    - **Vydavatel**: Microsoft Corp.

    > [!Note]  
    > Více než jedna verze vlastní skript pro Linux nemusí být zobrazeny. Musíte přidat verzi, která odpovídá. Kubernetes Cluster vyžaduje přesnou verzi položky.

6. Vyberte **stáhnout.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Přidejte Kubernetes Cluster na marketplace.

1. Otevřete [portálu pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu**.

3. Vyberte **+ přidat z Azure**.

4. Zadejte `Kubernetes Cluster`.

5. Vyberte `Kubernetes Cluster`.

6. Vyberte **stáhnout.**

    > [!note]  
    > To může trvat pět minut, než položku marketplace. než se objeví v Marketplace.

    ![Kubernetes clusteru](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Aktualizovat nebo odebrat Kubernetes clusteru 

Při aktualizaci položky Kubernetes clusteru, musíte odebrat položku, která je v Marketplace. Potom postupujte podle pokynů v tomto článku a přidání clusteru Kubernetes do marketplace.

Odebrání položky Kubernetes clusteru:

1. Poznamenejte si název aktuální položky, jako například `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Připojte k Azure zásobníku pomocí prostředí PowerShell.

3. Odebrání položky pomocí následující rutiny prostředí PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Další postup

[Nasazení clusteru Kubernetes Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Přehled nabídky služeb v Azure zásobníku](azure-stack-offer-services-overview.md)