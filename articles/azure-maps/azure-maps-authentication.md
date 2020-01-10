---
title: Ověřování pomocí Azure Maps | Microsoft Docs
description: Azure Active Directory (Azure AD) nebo ověřování pomocí sdíleného klíče pro používání služeb Microsoft Azure Maps. Přečtěte si, jak získat Azure Maps klíč předplatného.
author: walsehgal
ms.author: v-musehg
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a58436063009b732a15e74c8a3fc3f95b8df29cf
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834198"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby ověřování požadavků: Shared Key and Azure Active Directory (Azure AD). Tento článek vysvětluje tyto metody ověřování, které vám pomůžou s implementací.

## <a name="shared-key-authentication"></a>Ověřování sdíleného klíče

Ověřování pomocí sdíleného klíče předává klíče vygenerované Azure Mapsm účtem s každým požadavkem Azure Maps. Pro každý požadavek na Azure Maps služby je nutné přidat *klíč předplatného* do adresy URL jako parametr. Primární a sekundární klíče jsou generovány po vytvoření účtu Azure Maps. Při volání Azure Maps pomocí ověřování pomocí sdíleného klíče doporučujeme použít primární klíč jako klíč předplatného. Sekundární klíč lze použít ve scénářích, jako jsou například klíčové změny.  

Informace o zobrazení klíčů v Azure Portal najdete v tématu [Správa ověřování](https://aka.ms/amauthdetails).

> [!Tip]
> Doporučujeme pravidelně znovu generovat klíče. Máte k dispozici dva klíče, abyste mohli při opětovném generování druhé klávesy zachovat připojení s jedním klíčem. Když znovu vygenerujete klíče, budete muset aktualizovat všechny aplikace, které přistupují k účtu, aby používaly nové klíče.



## <a name="authentication-with-azure-active-directory-preview"></a>Ověřování pomocí Azure Active Directory (Preview)

Azure Maps teď nabízí integraci [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) pro ověřování žádostí pro služby Azure Maps Services. Azure AD poskytuje ověřování na základě identity, včetně [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), pro udělení přístupu na úrovni uživatele, na úrovni skupin a na úrovni aplikace Azure Maps prostředkům. Následující části vám můžou porozumět konceptům a součástem Azure Maps integrace se službou Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Ověřování pomocí přístupových tokenů OAuth

Azure Maps přijímá přístupové tokeny **OAuth 2,0** pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps. Azure Maps akceptuje tokeny pro:

* Uživatelé Azure AD. 
* Partnerské aplikace, které používají oprávnění delegované uživateli
* Spravované identity pro prostředky Azure.

Azure Maps pro každý účet Azure Maps vygeneruje *jedinečný identifikátor (ID klienta)* . Když zkombinujete toto ID klienta s dalšími parametry, můžete požádat o tokeny ze služby Azure AD zadáním hodnot v následující tabulce v závislosti na prostředí Azure.

| Prostředí Azure   | Koncový bod tokenu Azure AD |
| --------------------|-------------------------|
| Veřejný partnerský vztah Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Další informace o tom, jak nakonfigurovat Azure AD a žádat o tokeny pro Azure Maps, najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o žádosti o tokeny od Azure AD najdete v tématu [co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Vyžádání prostředků mapy Azure pomocí tokenů OAuth

Po přijetí tokenu z Azure AD se dá odeslat žádost do Azure Maps s následujícími dvěma požadovanými hlavičkami požadavků:

| Hlavička požadavku    |    Hodnota    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorizace     | Nosič eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` je identifikátor GUID založený na účtu Azure Maps, který se zobrazí na stránce ověřování Azure Maps.

Tady je příklad žádosti o Azure Maps trase, která používá token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informace o zobrazení ID klienta najdete v tématu [zobrazení podrobností o ověřování](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Řízení přístupu pomocí RBAC

Azure AD vám umožňuje řídit přístup k zabezpečeným prostředkům pomocí RBAC. Po vytvoření účtu Azure Maps a registraci Azure Maps aplikace služby Azure AD v rámci tenanta Azure AD můžete nastavit RBAC pro uživatele, skupinu, aplikaci nebo prostředek Azure na stránce portálu Azure Maps účtu.

Azure Maps podporuje řízení přístupu pro čtení pro jednotlivé uživatele, skupiny, aplikace a služby Azure AD prostřednictvím spravovaných identit pro prostředky Azure.

![Čtečka dat Azure Maps (Preview)](./media/azure-maps-authentication/concept.png)

Informace o zobrazení nastavení RBAC najdete v tématu [Konfigurace RBAC pro Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky a Azure Maps Azure

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytují služby azure (Azure App Service, Azure Functions, Azure Virtual Machines atd.) s automaticky spravovanou identitou, která může být autorizovaná pro přístup k Azure Maps službám.  

## <a name="next-steps"></a>Další kroky

* Další informace o ověřování aplikace s využitím Azure AD a Azure Maps najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování Azure Maps Ovládací prvek Mapa a Azure AD najdete v tématu [použití Ovládací prvek Mapa Azure Maps](https://aka.ms/amaadmc).
