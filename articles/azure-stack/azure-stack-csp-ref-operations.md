---
title: Zaregistrovat klienty pro využití sledování ve službě Azure Stack | Dokumentace Microsoftu
description: Podrobnosti o operacích, které používají ke správě tenanta registrace a jak se sleduje využití tenanta ve službě Azure Stack.
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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: bb46881425398618df54288a9d2e6d65bb03dad4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058041"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Spravovat registraci klientů ve službě Azure Stack

*Platí pro: integrované systémy Azure Stack*

Tento článek obsahuje podrobnosti o operacích, které můžete použít ke správě své registrace tenanta a jak sledovat využití tenanta. Můžete najít podrobnosti o tom, jak přidat, seznamu nebo odebrat mapování tenanta. Ke správě využití sledování můžete použít PowerShell nebo koncové body rozhraní API pro fakturaci.

## <a name="add-tenant-to-registration"></a>Přidání tenanta k registraci

Pokud chcete přidat nového tenanta k registraci, aby jejich využití je nahlášeno za předplatné Azure spojené s jejich tenanta Azure Active Directory (Azure AD), pomocí této operace.

Můžete také pomocí této operace, pokud chcete změnit předplatné spojené s tenantem, můžete volat PUT/New-AzureRMResource znovu. Staré mapování se přepíšou.

Všimněte si, že pouze jedno předplatné Azure může být přidružené ke klientovi. Pokud se pokusíte o přidání druhého předplatného do existujícího tenanta, je přepsání první předplatné. 

### <a name="use-api-profiles"></a>Pomocí profilů rozhraní API

Rutiny v tomto článku vyžadují, aby profil aplikace rozhraní API při spuštění PowerShell. Rozhraní API profily představují sadu poskytovatele prostředků Azure a jejich verzí rozhraní API. Umožňují použít správnou verzi rozhraní API při interakci s více cloudy Azure, například při práci s globální Azure a Azure Stack. Profily jsou určena podle názvu, která odpovídá jejich datum vydání. V tomto článku, budete muset použít **2017-09-03** profilu.

Další informace o službě Azure Stack a profily rozhraní API najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](user/azure-stack-version-profiles.md). Pokyny na zprovoznění s profilem rozhraní API s využitím Powershellu najdete v tématu [profilů verzí API použijte pro prostředí PowerShell ve službě Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parametry

| Parametr                  | Popis |
|---                         | --- |
| registrationSubscriptionID | Předplatné Azure použité pro počáteční registraci. |
| customerSubscriptionID     | Předplatné Azure (ne Azure Stack) patřící do zákazníků k registraci. Musí být vytvořená v nabídce Cloud Service Provider (CSP). V praxi to znamená prostřednictvím partnerského centra. Pokud zákazník má více než jednoho tenanta, musí se vytvořit toto předplatné v tenantovi, který se použije k přihlášení do služby Azure Stack. |
| Skupina prostředků              | Skupina prostředků v Azure, ve kterém je uložené registrace. |
| registrationName           | Název registrace služby Azure Stack. Jde o objekt uložená v Azure. Název se obvykle formulář azurestack-CloudID, kde je CloudID ID cloudu nasazení Azure Stack. |

> [!Note]  
> Klienti musejí zaregistrovat u každé služby Azure Stack využívají. Pokud klient používá více než jeden Azure Stack, musíte aktualizovat počáteční registrace každého nasazení předplatného tenanta.

### <a name="powershell"></a>PowerShell

Pomocí rutiny New-AzureRmResource můžete provést upgrade prostředku registrace. Přihlaste se k Azure (`Add-AzureRmAccount`) pomocí účtu, který jste použili při počáteční registraci. Tady je příklad toho, jak přidat tenanta:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Volání rozhraní API

**Operace**: Vložit  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 201 vytvořili  
**Text odpovědi**: prázdné  

## <a name="list-all-registered-tenants"></a>Vypsat všechny registrované tenanty

Získání seznamu všech tenantů, které byly přidány na registraci.

 > [!Note]  
 > Pokud se nezaregistrovaly žádné klienty, neobdržíte žádné odpovědi.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | Předplatné Azure použité pro počáteční registraci.   |
| Skupina prostředků              | Skupina prostředků v Azure, ve kterém je uložené registrace.    |
| registrationName           | Název registrace služby Azure Stack. Jde o objekt uložená v Azure. Název je obvykle ve formě **azurestack**-***CloudID***, kde ***CloudID*** je ID cloudu nasazení Azure Stack.   |

### <a name="powershell"></a>PowerShell

Použijte rutinu Get-AzureRmResource vypsat všechny registrované tenanty. Přihlaste se k Azure (`Add-AzureRmAccount`) pomocí účtu, který jste použili při počáteční registraci. Tady je příklad toho, jak přidat tenanta:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete získat seznam všech mapování tenanta pomocí operace GET

**Operace**: získání  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 200  
**Text odpovědi**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Odebrat mapování tenanta

Odstraněním tenanta, který byl přidán do registrace. Pokud tohoto tenanta pořád používá prostředky ve službě Azure Stack, jejich použití se účtuje na předplatné použité v počáteční registrace Azure Stack.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | ID předplatného pro registraci.   |
| Skupina prostředků              | Skupina prostředků pro registraci.   |
| registrationName           | Název registrace.  |
| customerSubscriptionId     | ID předplatného zákazníka.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete odebrat mapování tenanta pomocí operace odstranění.

**Operace**: odstranit  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 204 žádný obsah.  
**Text odpovědi**: prázdné

## <a name="next-steps"></a>Další postup

 - Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](/azure-stack-billing-and-chargeback.md).
