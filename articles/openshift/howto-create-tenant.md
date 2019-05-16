---
title: Vytvoření tenanta Azure AD pro Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Tady je postup vytvoření tenanta Azure Active Directory (Azure AD) pro hostování vašeho clusteru Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 04d710f4d60b776f8059d87ea4d009bed6f7f8ba
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551721"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Vytvoření tenanta Azure AD pro Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift vyžaduje [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) tenanta, ve kterém chcete vytvořit cluster. A *tenanta* vyhrazenou instancí služby Azure AD, který vývojář aplikace nebo organizace obdrží při vytvoření vztahu se společností Microsoft registrací do Azure, Microsoft Intune nebo Microsoft 365. Každý tenant Azure AD se liší a klienti odděleně od ostatní služby Azure AD a má vlastní pracovní a školní identity a registrace aplikací.

Pokud ještě nemáte tenanta služby Azure AD, postupujte podle těchto pokynů a vytvořte si ho.

## <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Pokud chcete vytvořit tenanta:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu, který chcete přidružit ke s vaším clusterem Azure Red Hat OpenShift.
2. Otevřít [okno Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) vytvořit nového tenanta (označovaný také jako nové *Azure Active Directory*).
3. Poskytují **název organizace**.
4. Poskytují **počáteční název domény**. To nebude mít *onmicrosoft.com* připojí k němu. Můžete znovu použít hodnotu *název organizace* tady.
5. Zvolte zemi nebo oblast, kde budou vytvářeny tenanta.
6. Klikněte na možnost **Vytvořit**.
7. Po vytvoření tenanta Azure AD, vyberte **Pokud chcete nový adresář spravovat, klikněte sem** odkaz. Název nového tenanta má být zobrazen v pravém horním rohu webu Azure portal:  

    ![Snímek obrazovky zobrazující název tenanta v pravém horním rohu portálu][tenantcallout]  

8. Poznamenejte si *ID tenanta* tak můžete dále určit, kde chcete vytvořit cluster Azure Red Hat OpenShift. Na portálu měli byste vidět okno přehledu služby Azure Active Directory pro vašeho nového tenanta. Vyberte **vlastnosti** a zkopírujte hodnotu pro vaše **ID adresáře**. Společnost Microsoft bude odkazovat na tuto hodnotu jako `TENANT` v [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Prostředky

Podívejte se na [dokumentace ke službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) Další informace o [tenantů Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvořit instanční objekt služby, generovat adresu URL pro zpětné volání tajný klíč a ověřování klienta a vytvořit nového uživatele služby Active Directory pro účely testování aplikací v clusteru Azure Red Hat OpenShift.

[Vytvoření objektu aplikace Azure AD a uživatel](howto-aad-app-configuration.md)