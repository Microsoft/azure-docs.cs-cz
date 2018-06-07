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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823618"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Nasazení clusteru Kubernetes Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

> [!Note]  
> Kubernetes kontejneru služby Azure (ACS) v zásobníku Azure je v privátní Preview verzi. Operátor vaší zásobník Azure bude muset požádat o přístup k položce Kubernetes Marketplace potřebná k provedení podle pokynů v tomto článku.

V následujícím článku zjistí pomocí šablony Azure Resource Manager řešení k nasazení a zřizování prostředků pro Kubernetes v rámci jediné koordinované operace. Budete potřebovat ke shromažďování požadované informace o instalaci aplikace Azure zásobníku generovat šablony a pak nasadit do cloudu.

## <a name="kubernetes-and-containers"></a>Kubernetes a kontejnery

Můžete nainstalovat Kubernetes pomocí šablony Azure Resource Manager generované modul kontejneru služby Azure (ACS) v Azure zásobníku. [Kubernetes](https://kubernetes.io) je systém open source pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. A [kontejneru](https://www.docker.com/what-container) se nachází v obrázku, podobně jako virtuální počítač. Na rozdíl od virtuálního počítače bitovou kopii kontejner obsahuje jenom prostředky, je nutné ho spustit aplikaci, například kód, modul runtime umožňující spouštění kódu, specifické knihovny a nastavení.

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

    Clusteru nelze nasadit do služby Azure zásobníku **správce** předplatné. Je nutné použít předplatné uživatele **. 

## <a name="create-a-service-principal-in-azure-ad"></a>Vytvoření instančního objektu ve službě Azure AD

1. Přihlaste se na globální [portál Azure](http://portal.azure.com).
2. Zkontrolujte, jestli jste přihlášení pomocí klienta Azure AD, který je přidružený k instanci Azure zásobníku.
3. Vytvořte aplikaci Azure AD.

    a. Vyberte **Azure Active Directory** > **+ registrace aplikace** > **nové registrace aplikace**.

    b. Zadejte **název** aplikace.

    c. Vyberte **webovou aplikaci nebo API**.

    d. Zadejte `http://localhost` pro **přihlašovací adresa URL**.

    c. Klikněte na možnost **Vytvořit**.

4. Poznamenejte si **ID aplikace**. ID budete potřebovat při vytváření clusteru. ID je odkazováno jako **ID klienta instanční objekt**.

5. Vyberte **nastavení** > **klíče**.

    a. Zadejte **popis**.

    b. Vyberte **je platné stále** pro **Expires**.

    c. Vyberte **Uložit**. Ujistěte se, poznamenejte si řetězec klíče. Klíče řetězec budete potřebovat při vytváření clusteru. Klíč je odkazováno jako **tajný klíč klienta instanční objekt**.



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

2. Vyberte **+ nový** > **výpočetní** > **Kubernetes clusteru**. Klikněte na možnost **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Vyberte **Základy** v vytvoření Kubernetes clusteru.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Zadejte **uživatelské jméno správce virtuálních počítačů Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

3. Zadejte **veřejný klíč SSH** použít k ověřování pro všechny počítače systému Linux vytvořené jako součást clusteru Kubernetes a DVM.

4. Zadejte **předpona DNS profil hlavní** , je jedinečný pro danou oblast. Toto musí být jedinečný pro oblast název, například `k8s-12345`. Zkuste zvolili, je stejná jako skupina prostředků název jako nejlepší postup.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

5. Zadejte **počet profilu fondu agentů**. Počet obsahuje počet agentů v clusteru. Může být od 1 do 4.

6. Zadejte **instanční objekt ClientId** slouží poskytovatelem cloudu Kubernetes Azure.

7. Zadejte **tajný klíč klienta instanční objekt** jste vytvořili při vytváření hlavní aplikace služby.

8. Zadejte **verze zprostředkovatele cloudu Kubernetes Azure**. Toto je verze pro poskytovatele Kubernetes Azure. Azure zásobníku uvolní vlastní Kubernetes sestavení pro každou verzi Azure zásobníku.

9. Vyberte vaše **předplatné** ID.

10. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

11. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci aplikace Azure zásobníku.

### <a name="specify-the-azure-stack-settings"></a>Zadejte nastavení Azure zásobníku

1. Vyberte **Azure zásobníku razítko nastavení**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Zadejte **klienta Arm Endpoint**. Toto je koncový bod Azure Resource Manager pro připojení k vytvoření skupiny prostředků clusteru Kubernetes. Musíte se k získání koncového bodu z vaší operátor zásobník Azure pro integrovaný systém. Pro Azure zásobníku Development Kit (ASDK), můžete použít `https://management.local.azurestack.external`.

3. Zadejte **ID klienta** pro klienta. Pokud potřebujete pomoc vyhledání této hodnoty, najdete v části [získání ID tenanta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Připojit ke clusteru

Nyní jste připraveni k připojení ke clusteru. Hlavní naleznete ve vaší skupině prostředků clusteru a názvem `k8s-master-<sequence-of-numbers>`. Slouží k připojení k hlavnímu serveru klientem SSH. Na hlavním serveru, můžete použít **kubectl**, klient Kubernetes příkazového řádku pro správu clusteru. Pokyny najdete v tématu [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Můžete také zjistit **Helm** Správce balíčků, které jsou užitečné pro instalaci a nasazení aplikací do clusteru. Pokyny k instalaci a použití Helm k vašemu clusteru, najdete v části [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Další postup

[Přidání clusteru s podporou Kubernetes do Marketplace (pro operátor Azure zásobníku)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
