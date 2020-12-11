---
title: 'Rychlý Start: registrace a vystavení webového rozhraní API | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu zaregistrujeme webové rozhraní API s platformou Microsoft identity a nakonfigurujete jeho obory, čímž klientům vystavíte přístup k prostředkům rozhraní API na základě oprávnění.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4ebae7e97f9128bb9302e9076e71dd3164f0c29e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030956"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Rychlý Start: Konfigurace aplikace k vystavení webového rozhraní API

V tomto rychlém startu zaregistrujete webové rozhraní API s platformou Microsoft identity a zpřístupníte ho klientským aplikacím přidáním ukázkového oboru. Díky registraci webového rozhraní API a jeho zpřístupnění prostřednictvím oborů můžete poskytnout přístup k prostředkům na základě oprávnění autorizovaným uživatelům a klientským aplikacím, které mají přístup k vašemu rozhraní API.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným – [vytvořit účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Dokončení [rychlého startu: nastavení tenanta](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registrace webového rozhraní API

Pokud chcete poskytnout vymezený přístup k prostředkům ve webovém rozhraní API, musíte nejdřív zaregistrovat rozhraní API s platformou Microsoft identity.

1. Proveďte kroky v části **Registrace aplikace** v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity](quickstart-register-app.md).
1. Přeskočte oddíly **Přidat identifikátor URI pro přesměrování** a **Konfigurovat nastavení platformy** . Pro webové rozhraní API nemusíte konfigurovat identifikátor URI přesměrování, protože není interaktivně přihlášený žádný uživatel.
1. Přeskočte si oddíl **Přidat přihlašovací údaje** pro teď. Jenom v případě, že rozhraní API přistupuje k rozhraní API pro příjem dat, bude potřebovat vlastní přihlašovací údaje, scénář, který není popsaný v tomto článku.

Díky zaregistrovanému webovému rozhraní API jste připraveni přidat obory, které může váš kód rozhraní API použít k zajištění podrobných oprávnění pro uživatele vašeho rozhraní API.

## <a name="add-a-scope"></a>Přidání oboru

Kód v klientské aplikaci požaduje oprávnění k provádění operací definovaných vaším webovým rozhraním API předáním přístupového tokenu spolu se svými požadavky na chráněný prostředek (webové rozhraní API). Vaše webové rozhraní API pak provede požadovanou operaci pouze v případě, že přístupový token, který obdrží, obsahuje obory vyžadované pro operaci.

Nejprve pomocí těchto kroků vytvořte příklad oboru s názvem `Employees.Read.All` :

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud máte přístup k více klientům, vyberte v horní nabídce možnost **adresář a filtr předplatného** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: a vyberte klienta, který obsahuje registraci klientské aplikace.
1. Vyberte **Azure Active Directory**  >  **Registrace aplikací** a pak vyberte registraci aplikace vašeho rozhraní API.
1. Vyberte **zveřejnit rozhraní API**  >  **Přidat obor**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="V Azure Portal se vystavuje podokno API pro registraci aplikace":::

1. Pokud jste ještě nenakonfigurovali **identifikátor URI ID aplikace** , budete vyzváni k jeho zadání.

   Identifikátor URI ID aplikace funguje jako předpona pro rozsahy, na které budete odkazovat v kódu rozhraní API, a musí být globálně jedinečný. Můžete použít zadanou výchozí hodnotu, která je ve formuláři `api://<application-client-id>` , nebo zadat čitelnější identifikátor URI, jako je například `https://contoso.com/api` .

1. V dalším kroku zadejte atributy oboru v podokně **Přidat obor** . Pro tento návod můžete použít ukázkové hodnoty nebo zadat vlastní.

    | Pole | Popis | Příklad |
    |-------|-------------|---------|
    | **Název oboru** | Název vašeho oboru. Společná konvence vytváření názvů oborů je `resource.operation.constraint` . | `Employees.Read.All` |
    | **Kdo může vyjádřit souhlas** | Zda je možné tento obor zaslat uživatelům nebo zda je vyžadován souhlas správce. Pro oprávnění s vyššími privilegii vyberte možnost **Jen správci**. | **Správci a uživatelé** |
    | **Zobrazovaný název souhlasu správce** | Krátký popis účelu oboru, který uvidí jenom správci. | `Read-only access to Employee records` |
    | **Popis souhlasu správce** | Podrobnější popis oprávnění uděleného oborem, který uvidí jenom správci. | `Allow the application to have read-only access to all Employee data.` |
    | **Zobrazovaný název souhlasu uživatele** | Krátký popis účelu oboru Zobrazuje se uživatelům pouze v případě, že jste nastavili **, kdo může udělit souhlas** **správcům a uživatelům**. | `Read-only access to your Employee records` |
    | **Popis souhlasu uživatele** | Podrobnější popis oprávnění uděleného oborem. Zobrazuje se uživatelům pouze v případě, že jste nastavili **, kdo může udělit souhlas** **správcům a uživatelům**. | `Allow the application to have read-only access to your Employee data.` |

1. Nastavte **stav** na **povoleno** a pak vyberte **Přidat obor**.

1. Volitelné Chcete-li potlačit zobrazení výzvy ke souhlasu uživatelů vaší aplikace s definovanými obory, můžete *předběžně autorizovat* , aby klientská aplikace měla přístup k webovému rozhraní API. Předem autorizujte *pouze* ty klientské aplikace, kterým důvěřujete, protože uživatelé nebudou mít možnost odmítnout souhlas.
    1. V části **autorizované klientské aplikace** vyberte **Přidat klientskou aplikaci** .
    1. Zadejte **aplikaci (ID klienta)** klientské aplikace, kterou chcete předběžně autorizovat. Například u webové aplikace, kterou jste předtím zaregistrovali.
    1. V části **autorizované obory** vyberte obory, pro které chcete potlačit zobrazování výzev k vyjádření souhlasu, a pak vyberte **Přidat aplikaci**.

    Pokud jste postupovali podle tohoto volitelného kroku, klientská aplikace je nyní předem autorizovanými klientskými aplikacemi (DPS) a uživatelé nebudou při přihlašování k tomuto souhlasu vyzváni k jejich souhlasu.

## <a name="add-a-scope-requiring-admin-consent"></a>Přidání rozsahu vyžadujícího souhlas správce

Pak přidejte další vzorový obor s názvem `Employees.Write.All` , ke kterému můžou udělit souhlas jenom správci. Obory, které vyžadují souhlas správce, se obvykle používají pro poskytování přístupu k vyššímu privilegovanému provozu a často klientské aplikace, které běží jako back-end služby nebo procesy, které se neinteraktivně přihlašovat k uživateli.

Pokud chcete přidat `Employees.Write.All` vzorový obor, postupujte podle kroků v části [Přidání oboru](#add-a-scope) a v podokně **Přidat obor** zadejte tyto hodnoty:

| Pole                          | Příklad hodnoty                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Název oboru**                 | `Employees.Write.All`                                              |
| **Kdo může vyjádřit souhlas**            | **Pouze správci**                                                    |
| **Zobrazovaný název souhlasu správce** | `Write access to Employee records`                                 |
| **Popis souhlasu správce**  | `Allow the application to have write access to all Employee data.` |
| **Zobrazovaný název souhlasu uživatele**  | *Žádné (nechat prázdné)*                                               |
| **Popis souhlasu uživatele**   | *Žádné (nechat prázdné)*                                               |

## <a name="verify-the-exposed-scopes"></a>Ověření vystavených oborů

Pokud jste úspěšně přidali oba příklady oborů popsané v předchozích částech, zobrazí se v podokně **Zobrazit rozhraní API** registrace aplikace webového rozhraní API podobně jako na tomto obrázku:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Snímek obrazovky s podoknem vystavení rozhraní API, ve kterém jsou dva vystavené obory":::

Jak je znázorněno na obrázku, úplný řetězec oboru je zřetězení **identifikátoru URI ID aplikace** webového rozhraní API a **názvu** oboru oboru.

Pokud je například identifikátor URI ID aplikace webového rozhraní API `https://contoso.com/api` a název oboru je `Employees.Read.All` , úplný rozsah je:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Použití vystavených oborů

V dalším článku této série nakonfigurujete registraci klientské aplikace s přístupem k vašemu webovému rozhraní API a obory, které jste definovali podle pokynů v tomto článku.

Jakmile je registraci klientské aplikace udělené oprávnění pro přístup k webovému rozhraní API, může být klient vystavený přístupového tokenu OAuth 2,0 platformou Microsoft identity. Když klient volá webové rozhraní API, prezentuje token přístupu, jehož `scp` deklarace identity () je nastavená na oprávnění, která jste zadali v registraci aplikace klienta.

Další obory můžete podle potřeby zveřejnit později. Vezměte v úvahu, že vaše webové rozhraní API může vystavovat několik oborů přidružených k několika operacím. Váš prostředek může řídit přístup k webovému rozhraní API za běhu vyhodnocením deklarací Scope ( `scp` ) v rámci přístupového tokenu OAuth 2,0, který obdrží.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili své webové rozhraní API tak, že nakonfigurujete jeho obory, nakonfigurujte registraci klientské aplikace s oprávněním pro přístup k oborům.

> [!div class="nextstepaction"]
> [Konfigurace registrace aplikace pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
