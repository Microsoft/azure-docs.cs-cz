---
title: Zkontrolujte Azure podnikového zápisu fakturace dat pomocí rozhraní REST API | Microsoft Docs
description: Naučte se používat rozhraní API REST Azure ke kontrole podnikové registrace fakturační informace.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063821"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Zkontrolujte fakturace podnikové registrace pomocí rozhraní REST API

Zkontrolujte a spravovat náklady na Azure Azure Nápověda rozhraní API pro vytváření sestav.

Zde zjistíte načíst aktuální faktury přidružený účet zápisu enterprise.

Načtení aktuálního:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Vytvoření žádosti  

`{enrollmentID}` Parametr je povinná a musí obsahovat ID registrace pro účet Enterprise (EA).

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastavte na platnou `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuální fakturační cyklus. Synchronní volání z důvodů výkonu nevrátil informace pro poslední měsíc.  Můžete také zavolat [rozhraní API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) vrátit data 36 měsíců.


## <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam podrobné náklady pro váš účet je vrátit stavovým kódem 200 (OK).

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Každá položka v **data** představuje zpoplatněny:

|Vlastnost Response.|Popis|
|----------------|----------|
|**Náklady** | Určená, v měny vhodné pro umístění datového centra. |
|**subscriptionGuid** | Globálně jedinečné ID pro předplatné. | 
|**departmentId** | ID pro oddělení, pokud existuje. |
|**Datum** | Datum, kdy byla fakturována zřizování. |
|**značek** | Řetězce formátu JSON obsahující značky přidružené k předplatnému. |
|**resourceGroup**|Název skupiny prostředků, který obsahuje objekt, který vám účtovány náklady. |
|**nextLink**| Pokud nastavíte, určuje adresu URL pro další "stránky" podrobnosti. Prázdné, pokud stránka je poslední. |  
||
  
ID oddělení, skupin prostředků, značky a související pole jsou definované správcem EA.  

V tomto příkladu je zkratka; v tématu [získat podrobnosti o využití](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) úplný popis každého pole odpovědi. 

Další stavové kódy označují chybový stav. V těchto případech objektu odpovědi vysvětluje, proč zpracování žádosti se nezdařilo.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Další postup 
- Zkontrolujte [Enterprise Přehled vytváření sestav](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [Enterprise fakturace rozhraní REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
