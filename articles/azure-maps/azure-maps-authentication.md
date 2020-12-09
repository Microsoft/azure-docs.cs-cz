---
title: Ověřování pomocí map Microsoft Azure
titleSuffix: Azure Maps
description: 'Přečtěte si o dvou způsobech ověřování požadavků v Azure Maps: ověřování prostřednictvím sdíleného klíče a ověřování Azure Active Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d47e98273e696a4b6e827d8ebbc71a297f2861cb
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905311"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby ověření požadavků: ověřování prostřednictvím sdíleného klíče a ověřování [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) . Tento článek vysvětluje obě metody ověřování, které vám pomůžou s implementací služby Azure Maps Services.

> [!NOTE]
> Abychom vylepšili zabezpečenou komunikaci s Azure Maps, teď podporujeme protokol TLS (Transport Layer Security) 1,2 a vychystáme podporu TLS 1,0 a 1,1. Pokud aktuálně používáte TLS 1. x, vyhodnoťte připravenost TLS 1,2 a vytvořte plán migrace s testováním popsaným v tématu [řešení potíží](/security/solving-tls1-problem)s protokolem TLS 1,0.

## <a name="shared-key-authentication"></a>Ověřování sdíleného klíče

 Primární a sekundární klíče jsou generovány po vytvoření účtu Azure Maps. Při volání Azure Maps s ověřováním pomocí sdíleného klíče doporučujeme použít primární klíč jako klíč předplatného. Ověřování pomocí sdíleného klíče předává klíč generovaný Azure Maps účtem ke službě Azure Maps. Pro každý požadavek na Azure Maps služby přidejte *klíč předplatného* jako parametr do adresy URL. Sekundární klíč lze použít ve scénářích, jako jsou například klíčové změny.  

Informace o zobrazení klíčů v Azure Portal najdete v tématu [Správa ověřování](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Z bezpečnostních důvodů se doporučuje střídat mezi primárním a sekundárním klíčem. Pokud chcete otočit klíče, aktualizujte aplikaci tak, aby používala sekundární klíč, nasaďte a pak stisknutím tlačítka cyklus/obnovení vedle primárního klíče Vygenerujte nový primární klíč. Starý primární klíč bude zakázán. Další informace o rotaci klíčů najdete v tématu [nastavení Azure Key Vault s použitím rotace a auditování klíčů](../key-vault/secrets/tutorial-rotation-dual.md) .

## <a name="azure-ad-authentication"></a>Ověřování Azure AD

U předplatných Azure se poskytuje tenant Azure AD, který umožňuje jemně odstupňované řízení přístupu. Azure Maps nabízí ověřování pro služby Azure Maps pomocí Azure AD. Azure AD poskytuje ověřování na základě identity pro uživatele a aplikace zaregistrované v tenantovi Azure AD.

Azure Maps přijímá přístupové tokeny **OAuth 2,0** pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps. Azure Maps také akceptuje tokeny pro:

* Uživatelé Azure AD
* Partnerské aplikace, které používají oprávnění delegované uživateli
* Spravované identity pro prostředky Azure

Azure Maps pro každý účet Azure Maps vygeneruje *jedinečný identifikátor (ID klienta)* . Tokeny ze služby Azure AD můžete vyžádat při kombinaci tohoto ID klienta s dalšími parametry.

Další informace o tom, jak nakonfigurovat Azure AD a žádat o tokeny pro Azure Maps, najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

Obecné informace o ověřování ve službě Azure AD najdete v tématu [co je ověřování?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky a Azure Maps Azure

[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) poskytují služby Azure pomocí automaticky spravovaného objektu zabezpečení založeného na aplikaci, který se může ověřit ve službě Azure AD. Díky řízení přístupu na základě role v Azure (Azure RBAC) může být objekt zabezpečení spravované identity autorizovaný pro přístup k Azure Maps službám. Mezi příklady spravovaných identit patří: Azure App Service, Azure Functions a Azure Virtual Machines. Seznam spravovaných identit najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Konfigurace ověřování Azure AD pro aplikace

Aplikace se budou ověřovat pomocí tenanta Azure AD s použitím jednoho nebo více podporovaných scénářů poskytovaných službou Azure AD. Každý scénář aplikace Azure AD představuje různé požadavky založené na obchodních potřebách. Některé aplikace můžou vyžadovat přihlašování uživatelů a jiné aplikace můžou vyžadovat přihlášení k aplikacím. Další informace najdete v tématu [toky ověřování a scénáře aplikací](../active-directory/develop/authentication-flows-app-scenarios.md).

Poté, co aplikace získá přístupový token, sada SDK nebo aplikace pošle požadavek HTTPS s následující sadou požadovaných hlaviček HTTP kromě dalších REST API hlaviček protokolu HTTP:

| Název záhlaví    | Hodnota               |
| :------------- | :------------------ |
| x-MS-Client-ID | 30d7cc....9f55        |
| Autorizace  | Nosič eyJ0e... HNIVN |

> [!NOTE]
> `x-ms-client-id` je identifikátor GUID založený na účtu Azure Maps, který se zobrazí na stránce ověřování Azure Maps.

Tady je příklad žádosti o Azure Maps trase, která používá token nosiče Azure AD OAuth:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Informace o zobrazení ID klienta najdete v tématu [zobrazení podrobností o ověřování](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Ověřování pomocí řízení přístupu na základě role

Azure Maps podporuje přístup ke všem objektům zabezpečení pro [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) , včetně: jednotlivých uživatelů Azure AD, skupin, aplikací, prostředků Azure a spravovaných identit Azure. Hlavním typům je udělena sada oprávnění, která se označuje také jako definice role. Definice role poskytuje oprávnění REST API akcí. Použití přístupu na jeden nebo více účtů Azure Maps se označuje jako obor. Při použití objektu zabezpečení, definice role a oboru se vytvoří přiřazení role. 

V dalších částech najdete koncepty a součásti Azure Maps integrace se službou Azure RBAC. V rámci procesu nastavení účtu Azure Maps je adresář služby Azure AD přidružený k předplatnému Azure, ke kterému se nachází účet Azure Maps. 

Při konfiguraci služby Azure RBAC zvolíte objekt zabezpečení a použijete ho pro přiřazení role. Další informace o přidání přiřazení rolí na Azure Portal najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Výběr definice role

Následující typy definic rolí existují k podpoře scénářů aplikací.

| Definice role Azure       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Čtečka dat Azure Maps      | Poskytuje přístup k neměnným Azure Maps rozhraní REST API.                                                       |
| Přispěvatel dat Azure Maps | Poskytuje přístup k proměnlivým rozhraním REST API Azure Maps. Proměnlivost je definována akcemi: Write a DELETE. |
| Definice vlastní role      | Vytvořte vytvořenou roli pro povolení flexibilního omezeného přístupu k Azure Maps rozhraní REST API.                      |

Některé Azure Maps služby mohou vyžadovat zvýšená oprávnění k provádění akcí zápisu nebo odstranění v Azure Maps rozhraní REST API. Pro služby, které poskytují akce zápisu nebo odstranění, je vyžadována role Přispěvatel dat Azure Maps. Následující tabulka popisuje, které služby Azure Maps Přispěvatel dat platí pro použití akcí zápisu nebo odstranění v dané službě. Pokud je ve službě používána pouze akce čtení, je možné místo Azure Maps Přispěvatel dat použít Azure Maps data Reader.

| Služba Azure Maps | Definice role Azure Maps  |
| :----------------- | :-------------------------- |
| Data               | Přispěvatel dat Azure Maps |
| tvůrce            | Přispěvatel dat Azure Maps |
| Prostorové            | Přispěvatel dat Azure Maps |

Informace o zobrazení nastavení služby Azure RBAC najdete v tématu [Konfigurace služby Azure RBAC pro Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Definice vlastních rolí

Jedním z aspektů zabezpečení aplikací je použít princip nejnižších oprávnění. Tento princip předpokládá, že by měl objekt zabezpečení mít povolený jenom přístup, který je povinný, a nemá žádný další přístup. Vytváření vlastních definic rolí může podporovat případy použití, které pro řízení přístupu vyžadují další členitost. Pokud chcete vytvořit definici vlastní role, můžete vybrat konkrétní datové akce, které se mají zahrnout nebo vyloučit z definice.

Vlastní definice role se pak dá použít v přiřazení role pro libovolný objekt zabezpečení. Další informace o definicích vlastních rolí Azure najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

Tady je několik ukázkových scénářů, ve kterých můžou vlastní role zlepšit zabezpečení aplikací.

| Scénář                                                                                                                                                                                                                 | Akce s daty vlastní role                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Veřejná nebo interaktivní přihlašovací webová stránka s dlaždicemi základní mapy a bez dalších rozhraní REST API.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Aplikace, která vyžaduje pouze reverzní geografické kódování a žádná další rozhraní REST API.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Role pro objekt zabezpečení, který žádá o čtení dat map na základě tvůrce Azure Maps a základní mapy rozhraní REST API.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Role pro objekt zabezpečení, který vyžaduje čtení, zápis a odstraňování dat map založených na tvůrci. To může být definováno jako role editoru mapování dat, ale neumožňuje přístup k dalším rozhraním REST API, jako jsou základní mapové dlaždice. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Porozumění oboru

Při vytváření přiřazení role je tato definice definovaná v rámci hierarchie prostředků Azure. V horní části hierarchie je [skupina pro správu](../governance/management-groups/overview.md) a nejnižší je prostředek Azure, třeba účet Azure Maps.
Přiřazení role ke skupině prostředků může povolit přístup k několika účtům Azure Maps nebo prostředkům ve skupině.

> [!TIP]
> Obecné doporučení Microsoftu je přiřadit přístup k oboru účtu Azure Maps, protože brání **neúmyslnému přístupu k ostatním účtům Azure Maps** existujícím ve stejném předplatném Azure.

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure RBAC najdete v tématu.
> [!div class="nextstepaction"]
> [Řízení přístupu na základě role v Azure](../role-based-access-control/overview.md)

Další informace o ověřování aplikace pomocí Azure AD a Azure Maps najdete v tématu.
> [!div class="nextstepaction"]
> [Správa ověřování v Azure Maps](./how-to-manage-authentication.md)

Další informace o ověřování Ovládací prvek Mapa Azure Maps s Azure AD najdete v tématu.
> [!div class="nextstepaction"]
> [Použití Azure Maps Ovládací prvek Mapa](./how-to-use-map-control.md)