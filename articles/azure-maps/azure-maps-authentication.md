---
title: Metody ověřování pravosti | Mapy Microsoft Azure
description: V tomto článku se dozvíte o Azure Active Directory (Azure AD) a ověřování sdíleného klíče. Obě se používají pro služby Microsoft Azure Maps. Přečtěte si, jak získat klíč předplatného Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335699"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby ověřování požadavků: ověřování pomocí sdíleného klíče a ověřování azure active directory. Tento článek vysvětluje tyto metody ověřování, které vám pomůžou řídit implementaci služeb Azure Maps.

> [!NOTE]
> Abychom zlepšili zabezpečenou komunikaci s Azure Maps, teď podporujeme zabezpečení transportní vrstvy (TLS) 1.2 a vyřazujeme podporu pro TLS 1.0 a 1.1. Chcete-li se vyhnout přerušení služeb, **aktualizujte servery a aplikace tak, aby používaly tls 1.2 před dubnem 2, 2020**.  Pokud v současné době používáte TLS 1.x, vyhodnoťte připravenost TLS 1.2 a vytvořte plán migrace s testováním popsaným v [řešení problému TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Ověřování pomocí sdíleného klíče

 Primární a sekundární klíče se generují po vytvoření účtu Azure Maps. Doporučujeme použít primární klíč jako klíč předplatného při volání Azure Maps pomocí ověřování pomocí sdíleného klíče. Ověřování pomocí sdíleného klíče předá klíč generovaný účtem Azure Maps službě Azure Maps. Pro každý požadavek na služby Azure Maps přidejte *klíč předplatného* jako parametr na adresu URL. Sekundární klíč lze použít ve scénářích, jako jsou změny postupného klíče.  

Informace o zobrazení klíčů na webu Azure Portal najdete v tématu [Správa ověřování](https://aka.ms/amauthdetails).

> [!Tip]
> Doporučujeme pravidelně obnovovat klíče. Máte k dispozici dva klíče, takže můžete udržovat připojení s jedním klíčem při regeneraci druhého. Při regeneraci klíčů je třeba aktualizovat všechny aplikace, které přistupují k vašemu účtu, pomocí nových klíčů.

## <a name="authentication-with-azure-active-directory-preview"></a>Ověřování pomocí služby Azure Active Directory (Preview)

Azure Maps teď nabízí žádosti o ověření pro služby Azure Maps pomocí [Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Azure AD poskytuje ověřování na základě identity, včetně [řízení přístupu na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC se používá k udělení přístupu k prostředkům Azure Maps na úrovni uživatele, na úrovni skupiny nebo na úrovni aplikace. V dalších částech se popisují koncepty a součásti integrace Azure Maps s Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Ověřování pomocí přístupových tokenů OAuth

Azure Maps přijímá přístupové tokeny **OAuth 2.0** pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps. Azure Maps také přijímá tokeny pro:

* Uživatelé Azure AD
* Partnerské aplikace, které používají oprávnění delegovaná uživateli
* Spravované identity pro prostředky Azure

Azure Maps generuje *jedinečný identifikátor (ID klienta)* pro každý účet Azure Maps. Tokeny můžete požadovat z Azure AD při kombinaci tohoto ID klienta s dalšími parametry. Chcete-li požádat o token, zadejte hodnoty v následující tabulce na základě vašeho prostředí Azure.

| Prostředí Azure   | Koncový bod tokenu Azure AD |
| --------------------|-------------------------|
| Veřejný partnerský vztah Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Další informace o konfiguraci Azure AD a vyžádání tokenů pro Azure Maps najdete [v tématu Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o vyžádání tokenů z Azure AD najdete v tématu [Co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Vyžádání prostředků mapy Azure pomocí tokenů OAuth

Poté, co Azure AD obdrží token, Azure Maps odešle požadavek s následující sadou požadovaných hlaviček požadavku:

| Hlavička požadavku    |    Hodnota    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc....9f55|
| Autorizace     | Nosič eyJ0e....HNIVN |

> [!Note]
> `x-ms-client-id`je identifikátor GUID založený na účtu Azure Maps, který se zobrazí na stránce ověřování Azure Maps.

Tady je příklad požadavku na trasu Azure Maps, který používá token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informace o zobrazení ID klienta naleznete v [tématu Zobrazení podrobností o ověřování](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Řízení přístupu pomocí RBAC

Ve službě Azure AD použijte RBAC k řízení přístupu k zabezpečeným prostředkům. Nastavte si účet Azure Maps a zaregistrujte svého klienta Azure Maps Azure AD. Azure Maps podporuje řízení přístupu ke čtení pro jednotlivé uživatele Azure AD, skupiny, aplikace, prostředky Azure a služby Azure prostřednictvím spravovaných identit pro prostředky Azure. Na stránce portálu Azure Maps můžete nastavit RBAC pro vybrané role.

![Čtečka dat Azure Maps (preview)](./media/azure-maps-authentication/concept.png)

Informace o zobrazení nastavení RBAC najdete v [tématu Konfigurace RBAC pro Mapy Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky Azure a Mapy Azure

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytují služby Azure s automaticky spravovanou identitou, kterou můžou autorizace pro přístup ke službám Azure Maps. Některé příklady spravovaných identit: Azure App Service, Azure Functions a Azure Virtual Machines.

## <a name="next-steps"></a>Další kroky

* Další informace o ověřování aplikace pomocí Azure AD a Azure Maps najdete [v tématu Správa ověřování v Mapách Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování služby Azure Maps Map Control a Azure AD najdete [v tématu Použití ovládacího prvku map Azure Maps .](https://aka.ms/amaadmc)
