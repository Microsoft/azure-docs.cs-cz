---
title: Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD).
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 8d2d8d0595099081498202a3103caa10df8cf67d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657837"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Azure Stack odpojené scénář není aktuálně podporován ve verzi preview.

Můžete postupovat podle kroků v tomto článku pro nasazení a nastavit prostředky pro Kubernetes, při použití služby Azure Active Directory (Azure AD) jako vaše služba identity management, v jediném koordinované operace.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, ujistěte se, že máte správná oprávnění a že služby Azure Stack je připravena.

1. Ověřte, že můžete vytvářet aplikace ve vašem tenantovi Azure Active Directory (Azure AD). Tato oprávnění potřebných pro nasazení Kubernetes.

    Kontrolují se oprávnění, v tématu [zkontrolujte Azure Active Directory oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Generování veřejného a privátního klíče pár SSH pro přihlášení do virtuálního počítače s Linuxem v Azure stacku. Veřejný klíč budete potřebovat při vytváření clusteru.

    Generuje se klíč, v tématu [vygenerování klíče SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Zkontrolujte, zda máte platné předplatné na portálu Azure Stack tenant a, že máte dostatek veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Je nutné použít **uživatele** předplatného. 

1. Pokud jste Kubernetes Cluster v marketplace, obraťte se na správce služby Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nastavení instančního objektu v Azure. Služby, které poskytuje vaše aplikace přístup k prostředkům Azure Stack.

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

1. Vyberte **řízení přístupu (IAM)** > vyberte **přidat přiřazení role**.

1. Vyberte **Přispěvatel** role.

1. Vyberte název aplikace vytvořené pro vaši službu objektu zabezpečení. Bude pravděpodobně nutné do vyhledávacího pole zadejte název.

1. Klikněte na **Uložit**.

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > **Compute** > **clusteru Kubernetes**. Klikněte na možnost **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Základy

1. Vyberte **Základy** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte vaše **předplatné** ID.

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci sady Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Nastavení clusteru Kubernetes

1. Vyberte **nastavení clusteru Kubernetes** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat oblasti jedinečný název, jako například `k8s-12345`. Zkuste jste zvolili, je stejná jako skupina prostředků pojmenujte jako nejlepší praxe.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Vyberte **počet profilů fondu hlavní Kubernetes**. Počet obsahuje počet uzlů ve fondu hlavní. Může být od 1 do 7. Tato hodnota by měla být liché číslo.

1. Vyberte **The VMSize hlavních virtuálních počítačů Kubernetes**.

1. Vyberte **počet profil fond uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **profil úložiště**. Můžete zvolit **objektu Blob disku** nebo **spravovaného disku**. Toto nastavení určuje virtuální počítače uzlů virtuálních počítačů velikosti Kubernetes. 

1. Vyberte **Azure AD** pro **systém identit Azure Stack** pro vaši instalaci služby Azure Stack. 

1. Zadejte **ID klienta instančního objektu** slouží od poskytovatele cloudu Kubernetes Azure. ID klienta, který je identifikován jako ID aplikace při vytváření instančního objektu služby.

1. Zadejte **tajný kód klienta instančního objektu** , který jste vytvořili při vytváření instančního objektu služby.

1. Zadejte **verze zprostředkovatele služby Kubernetes Azure cloudu**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

### <a name="3-summary"></a>3. Souhrn

1. Vyberte souhrn. V okně zobrazí zprávu ověření pro nastavení konfigurace clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Zkontrolujte nastavení.

3. Vyberte **OK** k nasazení clusteru.

> [!TIP]  
>  Pokud máte dotazy k vašemu nasazení, můžete se zveřejněte svůj dotaz nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Další postup

[Připojení k vašemu clusteru](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)