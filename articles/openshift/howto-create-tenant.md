---
title: Vytvoření tenanta Azure AD pro Azure Red Hat OpenShift
description: Tady je postup vytvoření klienta Azure Active Directory (Azure AD) pro hostování Microsoft Azureho clusteru Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 775aa44043e9c8900bd0c040ab603736314ea34e
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469979"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Vytvoření tenanta Azure AD pro Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift vyžaduje tenanta [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) , ve kterém se má cluster vytvořit. *Tenant* je vyhrazená instance služby Azure AD, kterou vývojář organizace nebo aplikace obdrží, když vytvoří relaci se společností Microsoft tím, že se přihlásí k Azure, Microsoft Intune nebo Microsoft 365. Každý tenant služby Azure AD je jedinečný a oddělený od ostatních tenantů Azure AD a má vlastní pracovní a školní identitu a registrace aplikací.

Pokud ještě nemáte tenanta Azure AD, vytvořte ho podle těchto pokynů.

## <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Vytvoření tenanta:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který chcete přidružit k vašemu clusteru Azure Red Hat OpenShift.
2. Otevřete okno [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) pro vytvoření nového tenanta (označovaného také jako nový *Azure Active Directory*).
3. Zadejte **název organizace**.
4. Zadejte **počáteční název domény**. K tomuto se připojí *onmicrosoft.com* . Hodnotu pro *název organizace* můžete znovu použít zde.
5. Vyberte zemi nebo oblast, ve které se tenant vytvoří.
6. Klikněte na **Vytvořit**.
7. Po vytvoření tenanta Azure AD vyberte **kliknutím sem můžete spravovat odkaz na nový adresář** . Nový název tenanta by se měl zobrazit v pravém horním rohu Azure Portal:  

    ![Snímek obrazovky portálu se zobrazeným jménem klienta v pravém horním rohu][tenantcallout]  

8. Poznamenejte si *ID tenanta* , abyste později mohli určit, kde se má vytvořit cluster Azure Red Hat OpenShift. Na portálu byste teď měli vidět okno Přehled Azure Active Directory pro nového tenanta. Vyberte **vlastnosti** a ZKOPÍRUJTE hodnotu **ID adresáře**. Tato hodnota se na tuto hodnotu odkazuje jako `TENANT` v kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Zdroje a prostředky

Další informace o [klientech Azure AD](../active-directory/develop/quickstart-create-new-tenant.md)najdete v [dokumentaci k Azure Active Directory](../active-directory/index.yml) .

## <a name="next-steps"></a>Další kroky

Naučte se, jak vytvořit instanční objekt, vygenerovat adresu URL tajného klíče klienta a zpětného volání ověřování a vytvořit nového uživatele Active Directory pro testování aplikací v clusteru Azure Red Hat OpenShift.

[Vytvoření uživatele a objektu aplikace Azure AD](howto-aad-app-configuration.md)