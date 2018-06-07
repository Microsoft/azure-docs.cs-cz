---
title: Hybridní Cloudová identita nakonfigurovat aplikace Azure a Azure zásobníku | Microsoft Docs
description: Zjistěte, jak nakonfigurovat hybridní Cloudová identita s aplikací Azure a Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808158"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Kurz: Konfigurace hybridní Cloudová identita pro aplikace Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Informace o konfiguraci hybridní Cloudová identita pro vaše aplikace Azure a Azure zásobníku.

Máte dvě možnosti pro poskytování přístupu k aplikacím v globální Azure a Azure zásobníku.

 * Pokud Azure zásobníku nepřetržité připojení k Internetu, můžete použít Azure Active Directory (Azure AD).
 * Když zásobník Azure je odpojený od Internetu, můžete použít Azure Directory Federated Services (AD FS).

Objekty služby slouží k udělení přístupu k aplikacím zásobník Azure za účelem nasazení nebo konfigurace pomocí Azure Resource Manager v zásobníku Azure.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> * Vytvoření hybridní identita ve globální Azure a Azure zásobníku
> * Načtení tokenu pro přístup k rozhraní API služby Azure zásobníku.

Musí mít oprávnění operátora zásobník Azure kroky v tomto kurzu.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvořit objekt služby pro Azure AD na portálu

Pokud jste nasadili zásobník Azure pomocí služby Azure AD jako úložiště identit, můžete vytvořit objekty služby stejně, jako je tomu u Azure. [Vytvořit objekty služby](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) článek ukazuje, jak provést kroky prostřednictvím portálu. Zkontrolujte, zda máte [požadovaná oprávnění Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) před zahájením.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvořit objekt služby pro službu AD FS pomocí prostředí PowerShell

Pokud jste nasadili zásobník Azure se službou AD FS, můžete použít PowerShell vytvoření instančního objektu, přiřazení role pro přístup a přihlašování z prostředí PowerShell pomocí danou identitu. [Vytvoření instančního objektu služby AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) ukazuje, jak provést požadované kroky, pomocí prostředí PowerShell.

## <a name="using-the-azure-stack-api"></a>Použití zásobníku rozhraní API v Azure

[Rozhraní API služby Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) kurz vás provede proces získání tokenu pro přístup k rozhraní API služby Azure zásobníku.

## <a name="connect-to-azure-stack-using-powershell"></a>Připojení k Azure zásobníku pomocí prostředí Powershell

Rychlý Start [získat nastavení a spuštění pomocí prostředí PowerShell v zásobníku Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) vás provede kroky potřebné k instalaci prostředí Azure PowerShell a připojte se k instalaci aplikace Azure zásobníku.

### <a name="prerequisites"></a>Požadavky

Instalace Azure zásobníku připojený k Azure Active Directory s předplatným, máte přístup. Pokud instalace zásobník Azure nemáte, můžete použít tyto pokyny k nastavení [Azure zásobníku Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Připojení k Azure zásobníku pomocí kódu

Pro připojení k Azure zásobníku pomocí kódu, použijte koncových bodů rozhraní API Správce Azure Resource Manager získat ověřování a graf koncové body pro instalaci zásobník Azure a pak provést ověření pomocí požadavky REST. Ukázková aplikace klienta můžete najít na [Githubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sadu Azure SDK pro vámi zvolený jazyk podporuje profily rozhraní API Azure, SDK nemusí fungovat s Azure zásobníku. Další informace o profilech API Azure najdete v tématu [spravovat profily verze rozhraní API](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) článku.

## <a name="next-steps"></a>Další postup

 - Další informace o zpracování identity v zásobníku Azure najdete v tématu [architektura Identity pro Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
