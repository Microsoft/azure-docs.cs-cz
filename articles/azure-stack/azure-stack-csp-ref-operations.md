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
ms.date: 1/08/2019
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 6d28eea434b081602f0e2455b22fcc58022a800e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117109"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Spravovat registraci klientů ve službě Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Tento článek obsahuje podrobnosti o registraci operace. Můžete použít tyto operace:
- Spravovat registrace tenanta
- Správa sledování využití tenanta

Můžete najít podrobnosti o tom, jak přidat, seznamu nebo odebrat mapování tenanta. Ke správě využití sledování můžete použít PowerShell nebo koncové body rozhraní API pro fakturaci. Můžete najít podrobnosti o tom, jak přidat, seznamu nebo odebrat mapování tenanta. Ke správě využití sledování můžete použít PowerShell nebo koncové body rozhraní API pro fakturaci.

## <a name="add-tenant-to-registration"></a>Přidání tenanta k registraci

Pokud chcete přidat nového tenanta k registraci pomocí operace. Využití tenanta je nahlášeno za předplatné Azure spojené s jejich tenanta Azure Active Directory (Azure AD).

Pokud chcete změnit předplatné spojené s tenantem, pomocí operace. Voláním PUT/New-AzureRMResource přepsat předchozí mapování.

V rámci jednoho předplatného Azure můžete přidružit tenanta. Pokud se pokusíte o přidání druhého předplatného do existujícího tenanta, je přepsání první předplatné.

### <a name="use-api-profiles"></a>Pomocí profilů rozhraní API

Registrace rutin vyžadují, aby profil aplikace rozhraní API při spuštění PowerShell. Rozhraní API profily představují sadu poskytovatele prostředků Azure a jejich verzí rozhraní API. Pomáhají vám při interakci s více cloudy Azure použít správnou verzi rozhraní API. Například pracujete s několika cloudy při práci s globální Azure a Azure Stack. Profily zadejte název, který odpovídá datum jejich vydání. Budete muset použít **2017-09-03** profilu.

Další informace o službě Azure Stack a profily rozhraní API najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](user/azure-stack-version-profiles.md). Pokyny na zprovoznění s profilem rozhraní API s využitím Powershellu najdete v tématu [profilů verzí API použijte pro prostředí PowerShell ve službě Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parametry

| Parametr                  | Popis |
|---                         | --- |
| registrationSubscriptionID | Předplatné Azure použité pro počáteční registraci. |
| customerSubscriptionID     | Předplatné Azure (ne Azure Stack) patřící do zákazníků k registraci. Musí být vytvořená v nabídce Cloud Service Provider (CSP) prostřednictvím partnerského centra. Pokud zákazník má více než jednoho tenanta, vytvoří předplatné pro příslušného tenanta pro přihlášení ke službě Azure Stack. |
| resourceGroup              | Skupina prostředků v Azure, ve kterém je uložené registrace. |
| registrationName           | Název registrace služby Azure Stack. Jde o objekt uložená v Azure. Název se obvykle formulář azurestack-CloudID, kde je CloudID ID cloudu nasazení Azure Stack. |

> [!Note]  
> Klienti musejí zaregistrovat u každé služby Azure Stack využívají. Pokud klient používá více než jeden Azure Stack, musíte aktualizovat počáteční registrace každého nasazení předplatného tenanta.

### <a name="powershell"></a>PowerShell

Pomocí rutiny New-AzureRmResource můžete provést upgrade prostředku registrace. Tady je příklad toho, jak přidat tenanta:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Volání rozhraní API

**Operace**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 201 – vytvořeno  
**Text odpovědi**: Prázdné  

## <a name="list-all-registered-tenants"></a>Vypsat všechny registrované tenanty

Získání seznamu všech tenantů, které byly přidány na registraci.

 > [!Note]  
 > Pokud se nezaregistrovaly žádné klienty, neobdržíte žádné odpovědi.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | Předplatné Azure použité pro počáteční registraci.   |
| resourceGroup              | Skupina prostředků v Azure, ve kterém je uložené registrace.    |
| registrationName           | Název registrace služby Azure Stack. Jde o objekt uložená v Azure. Název je obvykle ve formě **azurestack**-***CloudID***, kde ***CloudID*** je ID cloudu nasazení Azure Stack.   |

### <a name="powershell"></a>PowerShell

Použijte rutinu Get-AzureRmResource vypsat všechny registrované tenanty. Přihlaste se k Azure (`Add-AzureRmAccount`) pomocí účtu, který jste použili při počáteční registraci. Tady je příklad toho, jak přidat tenanta:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete získat seznam všech mapování tenanta pomocí operace GET

**Operace**: GET  
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
| resourceGroup              | Skupina prostředků pro registraci.   |
| registrationName           | Název registrace.  |
| customerSubscriptionId     | ID předplatného zákazníka.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete odebrat mapování tenanta pomocí operace odstranění.

**Operace**: DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 204 žádný obsah.  
**Text odpovědi**: Prázdné

## <a name="next-steps"></a>Další postup

 - Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
