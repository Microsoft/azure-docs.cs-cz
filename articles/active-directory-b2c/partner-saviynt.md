---
title: Kurz pro konfiguraci Saviynt s využitím Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s Saviynt pro integraci mezi aplikacemi pro zjednodušení modernizace IT a zvýšení zabezpečení, zásad správného řízení a dodržování předpisů. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8406074933489e53e9235a8a6a05b68f1dd42a85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259132"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Saviynt s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny, jak integrovat Azure Active Directory (AD) B2C s [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). Platforma správce zabezpečení Saviynt poskytuje přehled, zabezpečení a zásady správného řízení, které dnes potřebují, na jediné sjednocené platformě. Saviynt zahrnuje riziko aplikace a zásady správného řízení, správu infrastruktury, správu privilegovaných účtů a analýzu rizik zákazníků.

V tomto ukázkovém kurzu nastavíte Saviynt tak, aby poskytovalo podrobné řízení přístupu na základě delegované správy pro Azure AD B2C uživatele. Saviynt provede následující kontroly, abyste zjistili, jestli má uživatel oprávnění ke správě Azure AD B2C uživatelů.

- Zabezpečení na úrovni funkcí, aby bylo možné zjistit, zda může uživatel provést určitou operaci. Můžete například vytvořit uživatele, aktualizovat uživatele, resetovat heslo uživatele atd.

- Zabezpečení na úrovni polí určuje, jestli uživatel může během operací správy uživatelů číst a zapisovat konkrétní atribut jiného uživatele. Agent helpdesku může například aktualizovat pouze telefonní číslo a všechny ostatní atributy jsou jen pro čtení.

- Zabezpečení na úrovni dat k určení, jestli uživatel může provádět určitou operaci na konkrétním uživateli. Například správce helpdesku pro oblast UK může spravovat pouze uživatele ve Velké Británii.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Tenant je propojený s vaším předplatným Azure.

- [Předplatné](https://saviynt.com/contact-us/) Saviynt

## <a name="scenario-description"></a>Popis scénáře

Integrace Saviynt zahrnuje tyto komponenty:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – identitu typu Business-to-Customer jako službu, která umožňuje vlastní kontrolu nad tím, jak se vaši zákazníci můžou registrovat, přihlašovat a spravovat svoje profily.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – platforma pro zásady správného řízení identit, která poskytuje jemně odstupňovanou delegovanou správu pro správu životního cyklu uživatelů a přístup k zásadám správného řízení Azure AD B2C uživatelů.  

- [Rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api) – toto rozhraní API poskytuje rozhraní pro Saviynt ke správě Azure AD B2Cch uživatelů a jejich přístupu v Azure AD B2C.

V následujícím diagramu architektury se zobrazuje implementace.

![Obrázek znázorňující diagram architektury saviynt](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Krok | Description |
|:-----| :-----------|
| 1. | Delegovaný správce spustí uživatelskou operaci spravovat Azure AD B2C pomocí Saviynt.
| 2. | Saviynt ověří pomocí svého autorizačního modulu, pokud může provést konkrétní operace.
| 3. | Autorizační modul Saviynt odesílá odpověď na úspěšnost nebo selhání autorizace.
| 4. | Saviynt umožňuje delegovanému správci provést požadovanou operaci.
| 5. | Saviynt vyvolává Microsoft Graph rozhraní API spolu s atributy uživatele pro správu uživatele v Azure AD B2C
| 6. | Rozhraní Microsoft Graph API pak vytvoří, aktualizuje nebo odstraní uživatele v Azure AD B2C.
| 7. | Azure AD B2C odešle odpověď na úspěch nebo neúspěch.
| 8. | Rozhraní Microsoft Graph API pak vrátí odpověď do Saviynt.

## <a name="onboard-with-saviynt"></a>Zprovoznění s Saviynt

1. Pokud chcete vytvořit účet Saviynt, kontaktujte [Saviynt](https://saviynt.com/contact-us/) .

2. Vytvořte zásady delegované správy a přiřaďte uživatele jako [delegované správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-concept-delegation) s různými rolemi.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Konfigurace Azure AD B2C pomocí Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Vytvoření aplikace Azure AD pro Saviynt

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/#home).

2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

4. Vyberte **Registrace aplikací**  >  **novou registraci**.

5. Zadejte název aplikace. Například Saviynt a vyberte **vytvořit**.

6. Přejít na **oprávnění rozhraní API** a vyberte **+ Přidat oprávnění.**

7. Zobrazí se stránka oprávnění API pro vyžádání. Vyberte kartu **rozhraní Microsoft API** a vyberte **Microsoft Graph** jako běžně používaná rozhraní API Microsoftu.

8. Přejít na další stránku a vybrat **oprávnění aplikace**.

9. Vyberte **adresář**a pak vyberte **adresář. Read. All** a **Directory. čitelná. všechna** zaškrtávací políčka.

10. Vyberte **Přidat oprávnění**. Zkontrolujte přidaná oprávnění.

11. Vyberte **udělit souhlas správce pro výchozí adresář**  >  **Uložit**.

12. Přejít na **certifikáty a tajné klíče** a vyberte **+ Přidat tajný klíč klienta**. Zadejte popis tajného klíče klienta, vyberte možnost vypršení platnosti a vyberte **Přidat**.

13. Tajný klíč se vygeneruje a zobrazí se v části tajný klíč klienta.

    >[!NOTE]
    > Později budete potřebovat tajný klíč klienta.

14. Přejít na **Přehled** a získat **ID klienta** a **ID**klienta.

15. K dokončení nastavení v Saviynt bude nutné ID tenanta, ID klienta a tajný klíč klienta.

### <a name="enable-saviynt-to-delete-users"></a>Povolit Saviynt odstraňování uživatelů

Níže uvedené kroky vysvětlují, jak povolit Saviynt k provádění operací odstraňování uživatelů v Azure AD B2C.

>[!NOTE]
>[Před udělením přístupu rolí správce k instančnímu objektu vyhodnoťte riziko.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

1. Nainstalujte nejnovější verzi modulu MSOnline PowerShellu na pracovní stanici nebo server s Windows.

2. Připojte se k modulu AzureAD PowerShellu a spusťte následující příkazy:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testování řešení

Přejděte do tenanta aplikace Saviynt a otestujte správu životního cyklu uživatelů a získejte přístup k případu použití zásad správného řízení.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

- [Vytvoření aplikace webového rozhraní API](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application)
