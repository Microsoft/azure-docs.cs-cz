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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 9a025716c2bb6266c1c1c552a2d0791b39429cac
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Kurz: Konfigurace hybridní Cloudová identita s aplikací Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Máte dvě možnosti pro udělení přístupu k aplikacím v globální Azure i Azure protokolů. Pokud Azure zásobníku potom připojení k Internetu, můžete použít Azure Active Directory (Azure AD). Když zásobník Azure je odpojený od Internetu, můžete použít Azure Directory Federated Services (AD FS). Objekty služby slouží k udělení přístupu k aplikacím v Azure zásobníku z důvodu nasazení nebo konfigurace prostřednictvím Správce prostředků Azure v zásobníku Azure. 

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> * Vytvoření hybridní identita ve globální Azure a Azure zásobníku
> * Načítání tokenu pro přístup k rozhraní API služby Azure zásobníku.

Tyto kroky nutné, aby oprávnění operátora zásobník Azure.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvořit objekt služby pro Azure AD na portálu

Pokud jste nasadili zásobník Azure pomocí služby Azure AD jako úložiště identit, můžete vytvořit objekty služby stejně, jako je tomu u Azure. [Tento dokument](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) ukazuje, jak provést kroky prostřednictvím portálu. Zkontrolujte, zda máte [požadovaná oprávnění Azure AD](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) před zahájením.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvořit objekt služby pro službu AD FS pomocí prostředí PowerShell

Pokud jste nasadili zásobník Azure se službou AD FS, můžete použít PowerShell vytvoření instančního objektu, přiřazení role pro přístup a přihlašování z prostředí PowerShell pomocí danou identitu. [Tento dokument](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) ukazuje, jak provést kroky, pomocí prostředí PowerShell.

## <a name="using-the-azure-stack-api"></a>Použití zásobníku rozhraní API v Azure

[V tomto kurzu](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) vás provede procesem proces získání tokenu pro přístup k rozhraní API služby Azure zásobníku.

## <a name="connect-to-azure-stack-using-powershell"></a>Připojení k Azure zásobníku pomocí prostředí Powershell

Tady je ukázkový skript pomocí koncepty výukové v tomto dokumentu pro připojení k vaší zásobník Azure.

### <a name="prerequisites"></a>Požadavky

Instalace Azure zásobníku připojený k Azure Active Directory s předplatným, máte přístup. Pokud instalace zásobník Azure nemáte, můžete použít tyto pokyny k nastavení [Azure zásobníku Development Kit](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[V tomto kurzu](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) vás provede kroky potřebné k instalaci prostředí Azure PowerShell a připojte se k instalaci aplikace Azure zásobníku.

#### <a name="connect-to-azure-stack-using-code"></a>Připojení k Azure zásobníku pomocí kódu

Pro připojení k Azure zásobníku pomocí kódu, použijte koncových bodů rozhraní API Správce Azure Resource Manager získat ověřování a graf koncové body pro instalaci zásobník Azure a pak provést ověření pomocí požadavky REST. Můžete najít ukázkové aplikace [zde](https://github.com/shriramnat/HybridARMApplication).

> [!note]  
Pokud sadu Azure SDK pro vámi zvolený jazyk podporuje profily rozhraní API Azure, SDK nemusí fungovat s Azure zásobníku. Další informace o profilech API Azure, přejděte [zde](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Další postup

 - Další informace o zpracování identity v zásobníku Azure najdete v tématu [architektura Identity pro Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
