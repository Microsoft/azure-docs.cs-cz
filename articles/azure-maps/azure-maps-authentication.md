---
title: Ověřování pomocí služby Azure Maps | Dokumentace Microsoftu
description: Ověřování pomocí služby Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 22aba19e16e4349a5b495b307c9906f7ded5a636
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393676"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby, jak ověřit požadavky: Sdílený klíč a Azure Active Directory (Azure AD). Tento článek vysvětluje tyto metody ověřování jako návod pro vaši implementaci.

## <a name="shared-key-authentication"></a>Ověření pomocí sdíleného klíče

Ověření pomocí sdíleného klíče předá klíče generované účet Azure Maps u každé žádosti o Azure Maps.  Dva klíče jsou generovány, pokud je vytvoření účtu Azure Maps. Pro každý požadavek do služby Azure Maps klíč předplatného musí být přidán jako parametr na adresu URL.

> [!Tip]
> Doporučujeme pravidelně generovat. Dva klíče se součástí tak, aby se jeden klíč druhého moct udržet připojení. Když znovu vygenerujete klíče, je potřeba aktualizovat všechny aplikace přistupující k účet, který chcete použít nové klíče.

Informace o zobrazování svoje klíče najdete v tématu [zobrazit podrobnosti o ověřování](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Ověřování pomocí Azure Active Directory (Preview)

Azure nyní nabízí mapy [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integrace pro ověření žádosti o služby Azure Maps. Azure AD poskytuje ověřování na základě identity, včetně [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), udělit úrovni uživatelů nebo aplikací přístup k prostředkům Azure Maps. Následující části můžete vám pomůže pochopit pojmy a komponenty služby Azure Maps integrace se službou Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Ověřování pomocí přístupových tokenů OAuth

Azure Maps přijímá **OAuth 2.0** přístupové tokeny pro klienty Azure AD přidružené k předplatným Azure, které obsahuje účet Azure Maps. Azure Maps přijímá tokeny pro:

* Uživatelé Azure AD. 
* Partnerských aplikací, které používají oprávnění udělených uživateli.
* Spravované identity pro prostředky Azure.

Generuje Azure Maps *jedinečný identifikátor (ID klienta)* pro každý účet Azure Maps. Když zkombinujete tento ID klienta s další parametry, můžete požádat o tokeny ze služby Azure AD tak, že zadáte následující hodnotu:

```
https://login.microsoftonline.com
```
Další informace o tom, jak nakonfigurovat služby Azure AD a požádat o tokeny pro mapy Azure najdete v tématu [spravovat ověřování ve službě Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o vyžádání tokenů z Azure AD najdete v tématu [co je ověření?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Požadavky na prostředky Azure mapa s tokenů OAuth

Po přijetí tokenu z Azure AD, je možné odeslat žádost o k Azure Maps záhlaví sadou následující dvě požadované žádosti:

| Hlavička požadavku    |    Hodnota    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorizace     | Nosiče eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` je založené na účtu GUID Azure Maps se zobrazí na stránce ověřování Azure Maps.

Tady je příklad, který používá OAuth token požadavku trasy Azure Maps:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informace o zobrazení ID klienta najdete v tématu [zobrazit podrobnosti o ověřování](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Řízení přístupu pomocí RBAC

Azure AD umožňuje řídit přístup k prostředkům zabezpečeného pomocí RBAC. Po vytvoření účtu Azure Maps a zaregistrujte si vaši aplikaci Azure Maps Azure AD v rámci vašeho tenanta Azure AD, můžete nastavit RBAC pro uživatele, aplikace nebo prostředků Azure na stránce portálu účtu Azure Maps.

Azure Maps podporuje řízení přístupu pro čtení pro jednotlivé uživatele Azure AD, aplikací a služeb Azure pomocí spravované identity pro prostředky Azure.

![Azure Maps Data Reader (Preview)](./media/azure-maps-authentication/concept.png)

Informace o zobrazování nastavení RBAC najdete v tématu [konfigurace RBAC pro Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky Azure a Azure Maps

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytování služeb Azure (Azure App Service, Azure Functions, Azure Virtual Machines a tak dále) se automaticky spravovaná identita, která lze udělit oprávnění pro přístup ke službám Azure Maps.  

## <a name="next-steps"></a>Další postup

* Další informace o ověření aplikace v Azure AD a Azure Maps, naleznete v tématu [spravovat ověřování ve službě Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování Azure Maps mapový ovládací prvek a Azure AD, najdete v článku [použít mapový ovládací prvek Azure Maps](https://aka.ms/amaadmc).