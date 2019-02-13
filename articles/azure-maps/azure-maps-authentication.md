---
title: Ověřování pomocí služby Azure Maps | Dokumentace Microsoftu
description: Ověřování pomocí služby Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119267"
---
# <a name="authentication-with-azure-maps"></a>Ověřování v Azure Maps

Azure Maps podporuje dva způsoby, jak ověřovat žádosti. Sdílený klíč nebo Azure Active Directory (Azure AD) nabídka různé metody k autorizaci jednotlivé požadavky odeslané do Azure Maps. Cílem tohoto článku je vysvětlit obě metody ověřování mohou pomoci vaší implementace ověřování.

## <a name="shared-key-authentication"></a>Ověřování sdíleným klíčem

Ověření pomocí sdíleného klíče spoléhá na předávání klíče účet generovaných map Azure spolu s každou žádostí do Azure Maps.  Dva klíče jsou generovány, pokud je vytvoření účtu Azure Maps.  Každý požadavek do služby Azure Maps vyžaduje klíč předplatného, které mají být přidány jako parametr do adresy URL.

> [!Tip]
> Doporučujeme pravidelně generovat. Dva klíče jsou poskytovány tak, aby mohli spravovat připojení pomocí jednoho klíče a druhého. Když znovu vygenerujete klíče, je nutné aktualizovat všechny aplikace přistupující k tomuto účtu používaly tyto nové klíče.

Chcete-li zobrazit svoje klíče najdete v článku [ověřovacích údajů](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Ověřování pomocí Azure Active Directory (Preview)

Azure nyní nabízí mapy [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integrace pro ověřování požadavků na služby Azure Maps.  Azure AD poskytuje, včetně ověřování na základě identifikovat [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) udělit uživatele nebo aplikace úroveň přístupu k prostředkům Azure Maps. Cílem tohoto článku je k vám pomůže pochopit pojmy a komponenty integrace Azure Maps Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Ověřování pomocí přístupových tokenů OAuth

Azure Maps přijímá **OAuth 2.0** přístupové tokeny pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps.  Azure Maps přijímá tokeny pro:

* Uživatelé Azure AD 
* Pomocí oprávnění udělených uživateli aplikací třetích stran.
* Spravované identity pro prostředky Azure

Generuje Azure Maps `unique identifier (client ID)` pro každý účet Azure Maps.  ID klienta je v kombinaci s další parametry, mohou být požadována tokenů z Azure AD zadáním hodnoty níže:

```
https://login.microsoftonline.com
```
Další informace o tom, jak nakonfigurovat služby Azure AD a požádat o tokeny pro mapy Azure najdete v tématu [jak chcete spravovat ověřování](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o vyžádání tokenů z Azure AD, najdete v části [základní informace o ověřování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Žádost o Azure mapování prostředků pomocí tokenů OAuth

Po token je získaných ze služby Azure AD, je možné odeslat žádost o k Azure Maps záhlaví sadou následující dvě požadované žádosti:

| Hlavička požadavku    |    Hodnota    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorizace     | Nosiče eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` je účet Azure Maps založený na identifikátor GUID zobrazený na stránce ověřování Azure Maps

Tady je příklad požadavku trasy Azure Maps pomocí tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

ID klienta naleznete v tématu [ověřovacích údajů](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Řízení přístupu pomocí řízení přístupu na základě rolí (RBAC)

Klíčovou funkcí služby Azure AD je řízení přístupu k zabezpečeným prostředkům přes RBAC. Po vytvoření účtu Azure Maps a zaregistrovat aplikaci Azure Maps Azure AD v rámci vašeho tenanta Azure AD, máte nyní možnost konfigurace RBAC pro uživatele, aplikace nebo prostředků Azure v rámci stránky portálu účtu Azure mapa. 

Azure Maps aktuálně podporuje řízení přístupu pro čtení pro jednotlivé uživatele služby Azure AD, aplikace nebo služby Azure pomocí spravované identity pro prostředky Azure.

![Koncept](./media/azure-maps-authentication/concept.png)

Chcete-li zobrazit nastavení RBAC, naleznete v tématu [postupy konfigurace RBAC pro Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky Azure a Azure Maps

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytování služeb Azure (Azure App service, Azure Functions, virtuální počítače, atd.) se automaticky spravované identitou, která může být oprávnění pro přístup ke službám Azure Maps.  

## <a name="next-steps"></a>Další postup

* Další informace o ověření aplikace v Azure AD a Azure Maps, naleznete v tématu [jak chcete spravovat ověřování](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování Azure Maps, ovládací prvek mapa a Azure AD, najdete v článku [Azure AD a Azure Maps mapový ovládací prvek](https://aka.ms/amaadmc).