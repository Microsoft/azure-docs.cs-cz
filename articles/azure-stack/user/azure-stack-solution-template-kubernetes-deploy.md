---
title: Nasazení clusteru Kubernetes Azure zásobníku | Microsoft Docs
description: Zjistěte, jak nasadit Kubernetes Cluster do protokolů Azure.
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
ms.openlocfilehash: 71d920ea1c143ed65510b77e1804e9c5e38cc180
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Nasazení clusteru Kubernetes Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

> [!note]  
> Kubernetes kontejneru služby Azure (ACS) v zásobníku Azure je v privátní Preview verzi. Operátor vaší zásobník Azure bude muset požádat o přístup k položce Kubernetes Marketplace potřebná k provedení podle pokynů v tomto článku.

V následujícím článku zjistí pomocí šablony Azure Resource Manager řešení k nasazení a zřizování prostředků pro Kubernetes v rámci jediné koordinované operace. Budete potřebovat ke shromažďování požadované informace o instalaci aplikace Azure zásobníku generovat šablony a pak nasadit do cloudu.

## <a name="kubernetes-and-containers"></a>Kubernetes a kontejnery

V zásobníku Azure můžete nainstalovat Kubernetes kontejneru služby Azure (ACS). [Kubernetes](https://kubernetes.io) je systém open source pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. A [kontejneru](https://www.docker.com/what-container) se nachází v obrázku, podobně jako virtuální počítač. Na rozdíl od virtuálního počítače, který bitovou kopii kontejneru je právě obsahuje prostředky, je nutné ho spustit aplikaci, například kód, modul runtime umožňující spouštění kódu, specifické knihovny a nastavení.

Můžete použít Kubernetes na:

- Vývoj nesmírně škálovatelná služba, lze upgradovat, aplikací, které lze nasadit v sekundách. 
- Návrh aplikace zjednodušit a zlepšit jeho spolehlivost různé Helm aplikace. [Helm](https://github.com/kubernetes/helm) je nástroj balení open source, který vám pomůže nainstalovat a spravovat životní cyklus aplikace Kubernetes.
- Snadno monitorování a Diagnostika stavu z vašich aplikací s měřítkem a upgradovat funkce.

## <a name="prerequisites"></a>Požadavky 

Abyste mohli začít, ujistěte se, máte příslušná oprávnění a že vaše zásobník Azure je připravený.

1. Ověřte, že můžete vytvořit aplikace v klientovi služby Azure Active Directory (Azure AD). Pro nasazení Kubernetes potřebujete tato oprávnění.

    Postup při kontrole vašich oprávnění naleznete v tématu [zkontrolujte Azure Active Directory oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Generovat SSH pár veřejného a soukromého klíče pro přihlášení do virtuálního počítače s Linuxem v Azure zásobníku. Veřejný klíč budete potřebovat při vytváření clusteru.

    Pokyny pro generování klíče, v tématu [vygenerování klíče SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Zkontrolujte, zda máte platné předplatné na portálu Azure zásobníku klienta, a zda máte dostatečný počet veřejných IP adres k dispozici pro přidání nové aplikace.

## <a name="create-a-service-principal-in-azure-ad"></a>Vytvoření instančního objektu ve službě Azure AD

1. Přihlaste se na globální [portál Azure](http://www.poartal.azure.com).
2. Zkontrolujte, jestli jste přihlášení pomocí klienta Azure AD související se zásobníkem Azure.
3. Vytvořte aplikaci Azure AD.

    a. Vyberte **Azure Active Directory** > **+ registrace aplikace** > **nové registrace aplikace**.

    b. Zadejte **název** aplikace.

    c. Vyberte **webovou aplikaci nebo rozhraní API**

    d. Zadejte `http://localhost` pro **přihlašovací adresa URL**.

    c. Klikněte na **Vytvořit**

4. Poznamenejte si **ID aplikace**. ID budete potřebovat při vytváření clusteru. ID je odkazováno jako **ID klienta instanční objekt**.

5. Vyberte **nastavení** > **klíče**.

    a. Zadejte **popis**.

    b. Vyberte **je platné stále** pro **Expires**.

    c. Vyberte **Uložit**. Ujistěte se, poznamenejte si řetězec klíče. Klíče řetězec budete potřebovat při vytváření clusteru. Klíč je odkazuje jako **tajný klíč klienta instanční objekt**.



## <a name="give-the-service-principal-access"></a>Poskytnout přístup k hlavní službě

Poskytněte přístup k hlavní službě k vašemu předplatnému, aby objekt zabezpečení můžete vytvářet prostředky.

1.  Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **uživatele odběry** > **+ přidat**.

3. Vyberte odběr, který jste vytvořili.

4. Vyberte **přístup k ovládacímu prvku (IAM)** > vyberte **+ přidat**.

5. Vyberte **vlastníka** role.

6. Vyberte název aplikace, které jsou vytvořené pro vaši službu objektu zabezpečení. Možná budete muset do vyhledávacího pole zadejte název.

7. Klikněte na **Uložit**.

## <a name="deploy-a-kubernetes-cluster"></a>Nasazení clusteru Kubernetes

1. Otevřete [portál Azure zásobníku](https://portal.local.azurestack.external).

2. Vyberte **+ nový** > **výpočetní** > **Kubernetes clusteru**.

    ![Nasadit šablonu řešení](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Vyberte **parametry** v nasazení řešení šablony.

    ![Nasadit šablonu řešení](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Zadejte **jméno správce Linuxu**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

3. Zadejte **veřejný klíč SSH** použít k ověřování pro všechny počítače systému Linux vytvořené jako součást clusteru Kubernetes a DVM.

4. Zadejte **klienta endpoint**. Toto je koncový bod Azure Resource Manager pro připojení k vytvoření skupiny prostředků clusteru Kubernetes. Musíte se k získání koncového bodu z vaší operátor zásobník Azure pro integrovaný systém. Pro Azure zásobníku Development Kit (ASDK), můžete použít `https://management.local.azurestack.external`.

5. Zadejte **ID klienta** pro klienta. Pokud potřebujete pomoc vyhledání této hodnoty, najdete v části [získání ID tenanta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Zadejte **předpona DNS hlavní profil** , je jedinečný pro danou oblast. Toto musí být jedinečný pro oblast název, například `k8s-12345`. Zkuste zvolili, je stejná jako skupina prostředků název jako nejlepší postup.

    > [!note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

7. Zadejte počet agentů v clusteru. Tato hodnota se označuje jako **počet profilu fondu agentů**. Může to být od 1 do 32

8. Zadejte **služby ID hlavní aplikace** slouží poskytovatelem cloudu Kubernetes Azure.

9. Zadejte **tajný klíč klienta hlavní služby** jste vytvořili při vytváření hlavní aplikace služby.

10. Zadejte **verze zprostředkovatele cloudu Kubernetes Azure**. Toto je verze pro poskytovatele Kubernetes Azure. Azure zásobníku uvolní vlastní Kubernetes sestavení pro každou verzi Azure zásobníku.

12. Vyberte **OK**.

### <a name="specify-the-solution-values"></a>Zadejte hodnoty řešení

1. Vyberte **předplatné**.

2. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

3. Zadejte umístění skupiny prostředků, jako například **místní**.

4. Vyberte **vytvořit.**

## <a name="connect-to-your-cluster"></a>Připojit ke clusteru

Nyní jste připraveni k připojení ke clusteru. Budete potřebovat **kubectl**, klient příkazového řádku Kubernetes. Najdete pokyny k připojení a správě clusteru v dokumentaci služby Azure kontejneru.   

Pokyny najdete v tématu [připojit ke clusteru](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## <a name="next-steps"></a>Další postup

[Přidání clusteru s podporou Kubernetes do Marketplace (pro operátor Azure zásobníku)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)