---
title: Registrovat klienty pro sledování v Azure zásobníku využití | Microsoft Docs
description: Podrobnosti o operacích, které používají ke správě klienta registrace a jak je sledovat využití klienta v zásobníku Azure.
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: fbdf4023bc70f1ad05dd52ac1eabe95b12be9be2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Spravovat registraci klientů v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek obsahuje informace o operacích, které můžete použít ke správě vašeho klienta registrace, a jak je sledovat využití klienta. Můžete najít podrobnosti o tom, jak přidat, seznamu nebo odebrat mapování klienta. Prostředí PowerShell nebo koncové body fakturace rozhraní API můžete použít ke správě vašeho používání sledování.

## <a name="add-tenant-to-registration"></a>Přidat klienta k registraci

Tuto operaci použít, pokud chcete přidat nového klienta registrace, takže je jejich využití v části předplatné Azure spojené s jejich klienta Azure Active Directory (Azure AD).

Můžete také použít tuto operaci Pokud chcete změnit předplatné spojené s klienta, můžete volat PUT/New-AzureRMResource znovu. Původní mapování se přepíše.

Všimněte si, že může být pouze jedno předplatné přidružený klienta. Pokud se pokusíte přidat do existujícího klienta druhého předplatného, první předplatné je přepsání. 


| Parametr                  | Popis |
|---                         | --- |
| registrationSubscriptionID | Předplatné Azure, která byla použita pro počáteční registrace. |
| customerSubscriptionID     | Předplatné Azure (ne Azure Stack) patřící do zákazníka k registraci. Musí být vytvořený v nabídku poskytovatele cloudové služby (CSP). V praxi to znamená přes Partnerské centrum. Pokud zákazník má více než jednoho klienta, musí být vytvořeny toto předplatné v klientovi, který se použije k přihlášení do Azure zásobníku. |
| Skupina prostředků              | Skupina prostředků v Azure, ve kterém je uložený registrace. |
| registrationName           | Název registrace do Azure zásobníku. Je objekt uložená v Azure. Název je obvykle v protokol CloudID-formuláře azurestack, kde je CloudID ID cloudu Azure zásobníku nasazení. |

> [!Note]  
> Klienti musí být registrované s každou zásobník Azure používají. Pokud klient používá více než jeden zásobník Azure, budete muset aktualizovat počáteční registrace každého nasazení s předplatným klienta.

### <a name="powershell"></a>PowerShell

Pomocí rutiny New-AzureRmResource aktualizovat zdroj registrace. Přihlaste se k Azure (`Connect-AzureRmAccount`) pomocí účtu, který jste použili pro počáteční registraci. Tady je příklad toho, jak přidat klienta:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Volání rozhraní API

**Operace**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 201 vytvořen  
**Text odpovědi**: prázdný  

## <a name="list-all-registered-tenants"></a>Zobrazí seznam všech registrovaných klientů

Získání seznamu všech klientů, které byly přidány na registraci.

 > [!Note]  
 > Pokud byly zaregistrovány žádné klienty, neobdržíte odpověď.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | Předplatné Azure, která byla použita pro počáteční registrace.   |
| Skupina prostředků              | Skupina prostředků v Azure, ve kterém je uložený registrace.    |
| registrationName           | Název registrace do Azure zásobníku. Je objekt uložená v Azure. Název je obvykle ve formě **azurestack**-***CloudID***, kde ***CloudID*** je ID cloudu Azure zásobníku nasazení.   |

### <a name="powershell"></a>PowerShell

Použijte rutinu Get-AzureRmResovurce seznam všech registrovaných klientů. Přihlaste se k Azure (`Connect-AzureRmAccount`) pomocí účtu, který jste použili pro počáteční registraci. Tady je příklad toho, jak přidat klienta:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete získat seznam všech mapování klienta pomocí operace GET

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

## <a name="remove-a-tenant-mapping"></a>Odeberte klienta mapování

Můžete odebrat klienta, který byl přidán na registraci. Pokud tento klient stále používá prostředky v Azure zásobníku, jejich využití je zodpovědné za předplatné použité v počáteční registraci Azure zásobníku.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | ID odběru pro registraci.   |
| Skupina prostředků              | Skupinu prostředků pro registraci.   |
| registrationName           | Název registrace.  |
| customerSubscriptionId     | ID předplatného zákazníka.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete odebrat mapování klienta pomocí operace odstranění.

**Operace**: odstranění  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 204 žádný obsah  
**Text odpovědi**: prázdný

## <a name="next-steps"></a>Další postup

 - Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](/azure-stack-billing-and-chargeback.md).
