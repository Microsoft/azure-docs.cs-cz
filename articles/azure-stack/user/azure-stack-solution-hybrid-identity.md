---
title: Konfigurace hybridní Cloudová identita s aplikacemi Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat hybridní Cloudová identita s aplikacemi Azure a Azure Stack.
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
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 7cea11f448f20ace2dd50454834e60ce3a3c3b70
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584264"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Kurz: Konfigurace hybridní cloudové identity pro aplikace Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak nakonfigurovat hybridní cloudové identity pro vaše aplikace Azure a Azure Stack.

Máte dvě možnosti pro poskytování přístupu k vaší aplikace v globálním Azure a Azure Stack.

 * Pokud má Azure Stack nepřetržité připojení k Internetu, můžete použít Azure Active Directory (Azure AD).
 * Když Azure Stack je připojený k Internetu, můžete použít Azure Directory Federated Services (AD FS).

Udělení přístupu k aplikacím Azure Stack pro účely nasazení nebo konfigurací pomocí Azure Resource Manageru ve službě Azure Stack pomocí instančních objektů.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření hybridní identity v globální službě Azure Stack a Azure
> - Získat token pro přístup k rozhraní API služby Azure Stack.

Musíte mít oprávnění operátor Azure stacku pro kroky v tomto kurzu.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Dokument White Paper [aspekty návrhu pro hybridní aplikace](https://aka.ms/hybrid-cloud-applications-pillars) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridní aplikace, minimalizovat problémy v produkčním prostředí.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvoření instančního objektu služby pro službu Azure AD na portálu

Pokud jste nasadili Azure Stack jako úložiště identit pomocí Azure AD, můžete vytvořit instanční objekty stejně jako pro Azure. [Vytvoření instančních objektů](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) článku se dozvíte, jak k provádění kroků na portálu. Zkontrolujte, že máte [požadovaná oprávnění Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) před zahájením.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvoření instančního objektu služby AD FS pomocí Powershellu

Pokud jste nasadili Azure Stack se službou AD FS, slouží k vytvoření instančního objektu, přiřazení role pro přístup a přihlásit z Powershellu pomocí tohoto identity prostředí PowerShell. [Vytvoření instančního objektu služby AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) ukazuje, jak provést požadované kroky pomocí Powershellu.

## <a name="using-the-azure-stack-api"></a>Pomocí služby Azure Stack rozhraní API

[Rozhraní API služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) kurz vás provede procesem načítání tokenu pro přístup k rozhraní API služby Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Připojení ke službě Azure Stack pomocí Powershellu

Rychlý Start [pro uvedení do provozu pomocí prostředí PowerShell ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) vás provede kroky potřebné k instalaci prostředí Azure PowerShell a připojte se k instalaci sady Azure Stack.

### <a name="prerequisites"></a>Požadavky

Instalace služby Azure Stack připojené ke službě Azure Active Directory s předplatným, které můžete přistupovat. Pokud nemáte k dispozici při instalaci Azure Stack, můžete použít tyto pokyny k nastavení [Azure Stack Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Připojení ke službě Azure Stack pomocí kódu

Pro připojení ke službě Azure Stack pomocí kódu, použijte k získání ověřování a koncových bodů grafu pro instalaci sady Azure Stack a pak provést ověření pomocí požadavky REST koncových bodů rozhraní API Azure Resource Manageru. Ukázková klientská aplikace můžete najít na [Githubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sada Azure SDK pro svůj jazyk podporuje profily rozhraní API Azure, SDK nemusí fungovat s Azure Stack. Další informace o profilech rozhraní API Azure, najdete v článku [Správa profilů verzí API](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) článku.

## <a name="next-steps"></a>Další postup

 - Další informace o zpracování identity ve službě Azure Stack, najdete v článku [architektury identit pro službu Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
