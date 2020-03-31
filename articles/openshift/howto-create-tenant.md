---
title: Vytvoření klienta Azure AD pro Azure Red Hat OpenShift
description: Tady je postup, jak vytvořit klienta Azure Active Directory (Azure AD) pro hostování clusteru Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243689"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Vytvoření klienta Azure AD pro Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift vyžaduje klienta [Azure Active Directory (Azure AD),](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) ve kterém můžete vytvořit cluster. *Tenant* je vyhrazená instance Azure AD, kterou organizace nebo vývojář aplikace obdrží při vytváření vztahu s Microsoftem přihlášením do Azure, Microsoft Intune nebo Microsoftu 365. Každý klient Azure AD je odlišný a oddělený od ostatních klientů Azure AD a má své vlastní pracovní a školní identity a registrace aplikací.

Pokud ještě nemáte klienta Azure AD, postupujte podle těchto pokynů k jeho vytvoření.

## <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Vytvoření klienta:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který chcete přidružit k clusteru Azure Red Hat OpenShift.
2. Otevřete [okno Azure Active Directory a](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) vytvořte nového klienta (označovaný také jako nový Azure Active *Directory).*
3. Zadejte **název organizace**.
4. Zadejte **počáteční název domény**. K tomu bude *připojeno onmicrosoft.com.* Zde můžete znovu použít hodnotu *názvu organizace.*
5. Zvolte zemi nebo oblast, kde bude vytvořen nájemce.
6. Klikněte na **Vytvořit**.
7. Po vytvoření klienta Azure AD vyberte **Kliknutím sem pro správu nového** odkazu na adresář. Váš nový název klienta by se měl zobrazit v pravém horním části portálu Azure:  

    ![Snímek obrazovky portálu s názvem klienta v pravém horním][tenantcallout]  

8. Poznamenejte si *ID klienta,* abyste později mohli určit, kde chcete vytvořit cluster Azure Red Hat OpenShift. Na portálu byste teď měli vidět okno přehledu služby Azure Active Directory pro vašeho nového tenanta. Vyberte **vlastnosti** a zkopírujte hodnotu **id adresáře**. Budeme odkazovat na `TENANT` tuto hodnotu jako v [vytvoření azure red hat openshift clusteru](tutorial-create-cluster.md) kurzu.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Prostředky

Další informace o [klientech Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)najdete v [dokumentaci](https://docs.microsoft.com/azure/active-directory/) k Azure Active Directory .

## <a name="next-steps"></a>Další kroky

Zjistěte, jak vytvořit instanční objekt, vygenerovat adresu URL klienta pro tajné a ověřovací volání a vytvořit nového uživatele služby Active Directory pro testování aplikací v clusteru Azure Red Hat OpenShift.

[Vytvoření uživatele a objektu aplikace Azure AD](howto-aad-app-configuration.md)
