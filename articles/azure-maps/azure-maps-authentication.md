---
title: Metody ověřování
titleSuffix: Azure Maps
description: V tomto článku se dozvíte o ověřování Azure Active Directory a sdíleného klíče. Oba se používají pro služby Microsoft Azure Maps. Přečtěte si, jak získat Azure Maps klíč předplatného.
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe79b630291959ce4dc8b4743127986088a876ae
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987535"
---
# <a name="authentication-with-azure-maps"></a>Ověřování s využitím Azure Maps

Azure Maps podporuje dva způsoby ověření požadavků: ověřování prostřednictvím sdíleného klíče a ověřování [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) . Tento článek popisuje tyto metody ověřování, které vám pomůžou s implementací služby Azure Maps Services.

> [!NOTE]
> Abychom vylepšili zabezpečenou komunikaci s Azure Maps, teď podporujeme protokol TLS (Transport Layer Security) 1,2 a vychystáme podporu TLS 1,0 a 1,1. Aby nedošlo k přerušení služeb, **aktualizujte servery a aplikace tak, aby používaly protokol TLS 1,2 před 2. dubna 2020**.  Pokud aktuálně používáte TLS 1. x, vyhodnoťte připravenost TLS 1,2 a vytvořte plán migrace s testováním popsaným v tématu [řešení potíží](https://docs.microsoft.com/security/solving-tls1-problem)s protokolem TLS 1,0.

## <a name="shared-key-authentication"></a>Ověřování sdíleného klíče

 Primární a sekundární klíče jsou generovány po vytvoření účtu Azure Maps. Při volání Azure Maps s použitím ověřování pomocí sdíleného klíče doporučujeme použít primární klíč jako klíč předplatného. Ověřování pomocí sdíleného klíče předává klíč generovaný Azure Maps účtem ke službě Azure Maps. Pro každý požadavek na Azure Maps služby přidejte *klíč předplatného* jako parametr do adresy URL. Sekundární klíč lze použít ve scénářích, jako jsou například klíčové změny.  

Informace o zobrazení klíčů v Azure Portal najdete v tématu [Správa ověřování](https://aka.ms/amauthdetails).

> [!Tip]
> Doporučujeme pravidelně znovu generovat klíče. Máte k dispozici dva klíče, abyste mohli při opětovném generování druhé klávesy zachovat připojení s jedním klíčem. Když klíče znovu vygenerujete, budete muset aktualizovat všechny aplikace, které mají přístup k vašemu účtu, a to pomocí nových klíčů.

## <a name="azure-ad-authentication"></a>Ověřování Azure AD

U předplatných Azure se poskytuje tenant Azure AD, který umožňuje jemně odstupňované řízení přístupu. Azure Maps nabízí ověřování pro služby Azure Maps pomocí Azure AD. Azure AD poskytuje ověřování na základě identity pro uživatele a aplikace zaregistrované v tenantovi Azure AD.

Azure Maps přijímá přístupové tokeny **OAuth 2,0** pro klienty Azure AD přidružené k předplatnému Azure, které obsahuje účet Azure Maps. Azure Maps také akceptuje tokeny pro:

* Uživatelé Azure AD
* Partnerské aplikace, které používají oprávnění delegované uživateli
* Spravované identity pro prostředky Azure

Azure Maps pro každý účet Azure Maps vygeneruje *jedinečný identifikátor (ID klienta)* . Tokeny ze služby Azure AD můžete vyžádat při kombinaci tohoto ID klienta s dalšími parametry.

Další informace o tom, jak nakonfigurovat Azure AD a žádat o tokeny pro Azure Maps, najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Obecné informace o ověřování ve službě Azure AD najdete v tématu [co je ověřování?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Spravované identity pro prostředky a Azure Maps Azure

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) poskytují služby Azure pomocí automaticky spravovaného objektu zabezpečení založeného na aplikaci, který se může ověřit ve službě Azure AD. Díky řízení přístupu na základě role (RBAC) může být objekt zabezpečení spravované identity autorizovaný pro přístup k Azure Maps službám. Mezi příklady spravovaných identit patří: Azure App Service, Azure Functions a Azure Virtual Machines. Seznam spravovaných identit najdete v tématu [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

### <a name="configuring-application-azure-ad-authentication"></a>Konfigurace ověřování Azure AD pro aplikace

Aplikace se budou ověřovat pomocí tenanta Azure AD s použitím jednoho nebo více podporovaných scénářů poskytovaných službou Azure AD. Každý scénář aplikace Azure AD představuje různé požadavky založené na obchodních potřebách. Některé aplikace můžou vyžadovat přihlašování uživatelů a jiné aplikace můžou vyžadovat přihlášení k aplikacím. Další informace najdete v tématu [toky ověřování a scénáře aplikací](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios).

Poté, co aplikace získá přístupový token, sada SDK nebo aplikace pošle požadavek HTTPS s následující sadou požadovaných hlaviček HTTP kromě dalších REST API hlaviček protokolu HTTP:

| Název záhlaví    | Hodnota               |
| :------------- | :------------------ |
| x-MS-Client-ID | 30d7cc....9f55        |
| Autorizace  | Nosič eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id`je identifikátor GUID založený na účtu Azure Maps, který se zobrazí na stránce ověřování Azure Maps.

Tady je příklad žádosti o Azure Maps trase, která používá token nosiče Azure AD OAuth:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Informace o zobrazení ID klienta najdete v tématu [zobrazení podrobností o ověřování](https://aka.ms/amauthdetails).

## <a name="authorization-with-role-based-access-control"></a>Ověřování pomocí řízení přístupu na základě role

Azure Maps podporuje přístup ke všem objektům zabezpečení pro [řízení přístupu na základě role](https://docs.microsoft.com/azure/role-based-access-control/overview) Azure, včetně: jednotlivých uživatelů Azure AD, skupin, aplikací, prostředků Azure a spravovaných identit Azure. Hlavním typům je udělena sada oprávnění, která se označuje také jako definice role. Definice role poskytuje oprávnění REST API akcí. Použití přístupu na jeden nebo více účtů Azure Maps se označuje jako obor. Při použití objektu zabezpečení, definice role a oboru se vytvoří přiřazení role. 

V dalších částech najdete koncepty a součásti Azure Maps integrace s řízením přístupu na základě role Azure AD. V rámci procesu nastavení účtu Azure Maps je adresář služby Azure AD přidružený k předplatnému Azure, ke kterému se nachází účet Azure Maps. 

Při konfiguraci služby Azure RBAC zvolíte objekt zabezpečení a použijete ho pro přiřazení role. Další informace o přidání přiřazení rolí na Azure Portal najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

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

Informace o zobrazení nastavení RBAC najdete v tématu [Konfigurace RBAC pro Azure Maps](https://aka.ms/amrbac).

#### <a name="custom-role-definitions"></a>Definice vlastních rolí

Jedním z aspektů zabezpečení aplikací je použít princip nejnižších oprávnění. Princip předpokládá, že by měl objekt zabezpečení mít povolený jenom přístup, který se vyžaduje, a žádný další přístup. Vytváření vlastních definic rolí může podporovat případy použití, které pro řízení přístupu vyžadují další členitost. Pokud chcete vytvořit definici vlastní role, můžete vybrat konkrétní akce dat, které chcete zahrnout nebo vyloučit z definice. 

Vlastní definice role se pak dá použít v přiřazení role pro libovolný objekt zabezpečení. Další informace o definicích vlastních rolí Azure najdete v tématu [vlastní role Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Tady je několik ukázkových scénářů, ve kterých můžou vlastní role zlepšit zabezpečení aplikací.

| Scénář                                                                                                                                                                                                                 | Akce s daty vlastní role                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Veřejná nebo interaktivní přihlašovací webová stránka s dlaždicemi základní mapy a bez dalších rozhraní REST API.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Aplikace, která vyžaduje pouze reverzní geografické kódování a žádná další rozhraní REST API.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Role pro objekt zabezpečení, který žádá o čtení dat map na základě tvůrce Azure Maps a základní mapy rozhraní REST API.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Role pro objekt zabezpečení, který vyžaduje čtení, zápis a odstraňování dat map založených na tvůrci. To může být definováno jako role editoru mapování dat, ale neumožňuje přístup k dalším rozhraním REST API, jako jsou základní mapové dlaždice. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Porozumění oboru

Při vytváření přiřazení role je tato definice definovaná v rámci hierarchie prostředků Azure. V horní části hierarchie je [skupina pro správu](https://docs.microsoft.com/azure/governance/management-groups/overview) a nejnižší je prostředek Azure, například účet Azure Maps.
Přiřazení role ke skupině prostředků může povolit přístup k několika účtům Azure Maps nebo prostředkům ve skupině.

> [!Tip]
> Obecné doporučení Microsoftu přiřazuje přístup k oboru Azure Maps účtu, protože brání **neúmyslnému přístupu k ostatním účtům Azure Maps** existujícím ve stejném předplatném Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o RBAC najdete v tématu [Přehled řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) .

* Další informace o ověřování aplikace s využitím Azure AD a Azure Maps najdete v tématu [Správa ověřování v Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Další informace o ověřování Azure Maps Ovládací prvek Mapa a Azure AD najdete v tématu [použití Ovládací prvek Mapa Azure Maps](https://aka.ms/amaadmc).
