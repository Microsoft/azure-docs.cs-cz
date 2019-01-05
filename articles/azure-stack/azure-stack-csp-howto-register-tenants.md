---
title: Přidání tenantů pro využití a fakturaci ke službě Azure Stack | Dokumentace Microsoftu
description: Kroky přidání koncového uživatele do Azure stacku spravované pomocí Cloud Service Provider (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: be240ea17a8679d25623f971f7be5ea5c3ad2e8f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053230"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Přidání tenanta pro využití a fakturaci ke službě Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje kroky potřebné k přidání koncového uživatele do nasazení služby Azure Stack spravované pomocí Cloud Service Provider (CSP). Pokud nového tenanta používá prostředky, služby Azure Stack zprávy použití příslušného předplatného poskytovatele CSP.

Poskytovatelé CSP často nabízejí služby koncovým zákazníkům více (tenantů) na jejich nasazení Azure Stack. Přidání tenantů do registrace Azure Stack zajistí, že každý tenant využití hlášené. fakturovat se na požadované předplatné poskytovatele CSP. Pokud není dokončit kroky v tomto článku, použití tenanta se účtuje na předplatné použité při počáteční registraci Azure Stack. Před přidáním koncového zákazníka ke službě Azure Stack pro sledování využití a ke správě svého tenanta, je nutné nakonfigurovat služby Azure Stack jako odběratel CSP. Kroky a zdroje najdete v tématu [spravovat využití a fakturace pro Azure Stack jako poskytovatele cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md).

Následující obrázek znázorňuje kroky, které je potřeba k povolení nového zákazníka k používání služby Azure Stack a nastavení sledování pro odběratele CSP. Přidáním koncového zákazníka, máte také možnost spravovat prostředky ve službě Azure Stack. Máte dvě možnosti pro správu svých prostředků:

1. Můžete ochránit koncového zákazníka a zadejte přihlašovací údaje pro místní předplatnému služby Azure Stack pro koncové zákazníky.  
2. Koncového zákazníka můžete pracovat místně svoje předplatné a přidat CSP v roli hosta s oprávněními vlastníka.  

## <a name="steps-to-add-an-end-customer"></a>Postup přidání koncového zákazníka

![Nastavení poskytovatele cloudových služeb pro sledování využití a spravovat účty zákazníků end](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Vytvoření nového zákazníka v partnerském centru

V partnerském centru vytvořte nové předplatné Azure zákazníka. Pokyny najdete v tématu [přidání nového zákazníka](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Vytvořte předplatné Azure pro koncového zákazníka

Po vytvoření záznam zákazníka v partnerském centru, můžete je prodávat předplatná produktů v katalogu. Pokyny najdete v tématu [vytvoření, pozastavení nebo zrušení zákaznických předplatných](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vytvořit uživatele typu Host v adresáři koncový zákazník

Pokud koncového zákazníka spravuje svůj vlastní účet, vytvořte uživatele typu Host do svého adresáře a jim poslat informace. Koncový uživatel potom přidejte hostovaný a zvýšení úrovně oprávnění hostů **vlastníka** účtu Azure Stack CSP.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizace registrace pomocí předplatného zákazníka end

Aktualizujte svou registraci nového předplatného zákazníka. Azure hlásí využití zákazníka pomocí zákaznických identit z partnerského centra. Tento krok zajistí, že využití ze strany jednotlivých zákazníků je nahlášeno za jednotlivé předplatného poskytovatele CSP. To usnadňuje sledování uživatele využití a fakturace.

> [!NOTE]  
> Chcete-li provést tento krok, musíte mít [registrované služby Azure Stack](azure-stack-register.md).

1. Otevřete prostředí Windows PowerShell s řádku se zvýšenými oprávněními a spusťte:  
    `Add-AzureRmAccount`
2. Zadejte své přihlašovací údaje Azure.
3. V relaci prostředí PowerShell spusťte:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parametry nového-AzureRmResource Powershellu

Následující část popisuje parametry **New-AzureRmResource** rutiny:

| Parametr | Popis |
| --- | --- |
|registrationSubscriptionID | Předplatné Azure, která byla použita pro počáteční registrace Azure Stack.|
| customerSubscriptionID | Předplatné Azure (ne Azure Stack) patřící do zákazníků k registraci. Musí být vytvořen v nabídce zprostředkovatele kryptografických služeb; v praxi to znamená prostřednictvím partnerského centra. Pokud zákazník má více než jednoho tenanta Azure Active Directory, musí se vytvořit toto předplatné v tenantovi, který se použije k přihlášení do služby Azure Stack. ID předplatného zákazníka musí používat malá písmena. |
| resourceGroup | Skupina prostředků v Azure, ve kterém je uložené registrace. |
| registrationName | Název registrace služby Azure Stack. Jde o objekt uložená v Azure. |
| Vlastnosti | Určuje vlastnosti pro prostředek. Tento parametr použijte k určení hodnoty vlastností, které jsou specifické pro daný typ prostředku.

> [!NOTE]  
> Tenanti musí zaregistrovat každý Azure Stack využívají. Pokud budete mít dvě nasazení služby Azure Stack a tenanta používá obou z nich, je nutné aktualizovat počáteční registrace každého nasazení předplatného tenanta.

### <a name="onboard-tenant-to-azure-stack"></a>Připojení klienta ke službě Azure Stack

Konfigurace služby Azure Stack pro podporu uživatelů z více tenantů Azure AD používat služby v Azure stacku. Pokyny najdete v tématu [povolení víceklientské architektury v Azure stacku](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Vytvoření místního prostředku v tenantovi zákazníka end ve službě Azure Stack

Jakmile přidáte nového zákazníka ke službě Azure Stack nebo tenantovi zákazníka end povolila účtu hosta s oprávněními vlastníka, ověřte, že můžete vytvořit prostředek v rámci jejich tenanta. Například může [vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack](user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Další postup

- Pokud se spouštějí v procesu registrace, projděte chybové zprávy, najdete v článku [chybových zpráv registrace klienta](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
- Přečtěte si, jak koncového zákazníka může přidat, jako zprostředkovatel kryptografických služeb, jako správce pro tenanta služby Azure Stack, najdete v článku [povolit poskytovatele cloudové služby ke správě vašich předplatných Azure Stack](user/azure-stack-csp-enable-billing-usage-tracking.md).
