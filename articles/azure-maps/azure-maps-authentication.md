---
title: Metody ověřování | Mapy Microsoft Azure
description: V tomto článku se dozvíte o Azure Active Directory (Azure AD) a ověřování sdíleného klíče. Oba se používají pro služby Microsoft Azure Maps. Přečtěte si, jak získat Azure Maps klíč předplatného.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335699"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby ověřování požadavků: ověřování prostřednictvím sdíleného klíče a ověřování Azure Active Directory. Tento článek popisuje tyto metody ověřování, které vám pomůžou s implementací služby Azure Maps Services.

> [!NOTE]
> Abychom vylepšili zabezpečenou komunikaci s Azure Maps, teď podporujeme protokol TLS (Transport Layer Security) 1,2 a vychystáme podporu TLS 1,0 a 1,1. Aby nedošlo k přerušení služeb, **aktualizujte servery a aplikace tak, aby používaly protokol TLS 1,2 před 2. dubna 2020**.  Pokud aktuálně používáte TLS 1. x, vyhodnoťte připravenost TLS 1,2 a vytvořte plán migrace s testováním popsaným v tématu [řešení potíží](https://docs.microsoft.com/security/solving-tls1-problem)s protokolem TLS 1,0.

## <a name="shared-key-authentication"></a>Ověřování sdíleného klíče

 Primární a sekundární klíče jsou generovány po vytvoření účtu Azure Maps. Při volání Azure Maps s použitím ověřování pomocí sdíleného klíče doporučujeme použít primární klíč jako klíč předplatného. Ověřování pomocí sdíleného klíče předává klíč generovaný Azure Maps účtem ke službě Azure Maps. Pro každý požadavek na Azure Maps služby přidejte *klíč předplatného* jako parametr do adresy URL. Sekundární klíč lze použít ve scénářích, jako jsou například klíčové změny.  

Informace o zobrazení klíčů v Azure Portal najdete v tématu [Správa ověřování](https://aka.ms/amauthdetails).

> [!Tip]
> Doporučujeme pravidelně znovu generovat klíče. Máte k dispozici dva klíče, abyste mohli při opětovném generování druhé klávesy zachovat připojení s jedním klíčem. Když klíče znovu vygenerujete, budete muset aktualizovat všechny aplikace, které mají přístup k vašemu účtu, a to pomocí nových klíčů.

## <a name="authentication-with-azure-active-directory-preview"></a>Ověřování pomocí Azure Active Directory (Preview)

Azure Maps teď nabízí žádost o ověření pro služby Azure Maps pomocí [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD poskytuje ověřování na základě identity, včetně [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC slouží k udělení přístupu na úrovni uživatele, na úrovni skupiny nebo na úrovni aplikace k Azure Maps prostředkům. V dalších částech najdete koncepty a součásti Azure Maps integrace se službou Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Ověřování pomocí přístupových tokenů OAuth

Azure Maps přijímá přístupové tokeny **OAuth 2,0** pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps. Azure Maps také akceptuje tokeny pro:

* Uživatelé Azure AD
* Partnerské aplikace, které používají oprávnění delegované uživateli
* Spravované identity pro prostředky Azure

Azure Maps pro každý účet Azure Maps vygeneruje *jedinečný identifikátor (ID klienta)* . Tokeny ze služby Azure AD můžete vyžádat při kombinaci tohoto ID klienta s dalšími parametry. Pokud chcete požádat o token, zadejte hodnoty v následující tabulce na základě vašeho prostředí Azure.

| Prostředí Azure   | Koncový bod tokenu Azure AD |
| --------------------|-------------------------|
| Veřejný partnerský vztah Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Další informace o tom, jak nakonfigurovat Azure AD a žádat o tokeny pro Azure Maps, najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o žádosti o tokeny od Azure AD najdete v tématu [co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Vyžádání prostředků mapy Azure pomocí tokenů OAuth

Jakmile Azure AD obdrží token, Azure Maps odešle požadavek s následující hlavičkou požadovaných hlaviček:

| Hlavička požadavku    |    Hodnota    |
|:------------------|:------------|
| x-MS-Client-ID    | 30d7cc....9f55|
| Autorizace     | Nosič eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id`je identifikátor GUID založený na účtu Azure Maps, který se zobrazí na stránce ověřování Azure Maps.

Tady je příklad žádosti o Azure Maps trase, která používá token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informace o zobrazení ID klienta najdete v tématu [zobrazení podrobností o ověřování](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Řízení přístupu pomocí RBAC

V Azure AD použijte RBAC k řízení přístupu k zabezpečeným prostředkům. Nastavte účet Azure Maps a zaregistrujte Azure Maps tenanta Azure AD. Azure Maps podporuje řízení přístupu pro čtení pro jednotlivé uživatele služby Azure AD, skupiny, aplikace, prostředky Azure a služby Azure prostřednictvím spravovaných identit pro prostředky Azure. Na stránce Azure Mapsového portálu můžete nastavit RBAC pro zvolené role.

![Čtečka dat Azure Maps (Preview)](./media/azure-maps-authentication/concept.png)

Informace o zobrazení nastavení RBAC najdete v tématu [Konfigurace RBAC pro Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky a Azure Maps Azure

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytují služby Azure s automaticky spravovanou identitou, která může mít autorizaci pro přístup k Azure Maps službám. Mezi příklady spravovaných identit patří: Azure App Service, Azure Functions a Azure Virtual Machines.

## <a name="next-steps"></a>Další kroky

* Další informace o ověřování aplikace s využitím Azure AD a Azure Maps najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování Azure Maps Ovládací prvek Mapa a Azure AD najdete v tématu [použití Ovládací prvek Mapa Azure Maps](https://aka.ms/amaadmc).
