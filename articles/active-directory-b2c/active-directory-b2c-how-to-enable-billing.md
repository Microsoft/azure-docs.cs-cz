---
title: Model fakturace pro Azure Active Directory B2C
description: Přečtěte si o modelu fakturace B2C's měsíčně aktivních uživatelů Azure AD (MAU) a o tom, jak povolit fakturaci pro konkrétní předplatné Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 32897994e4ef22845b1d19d6e0eb3e6b2c968c01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930732"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model fakturace pro Azure Active Directory B2C

Využití Azure Active Directory B2C (Azure AD B2C) se účtuje na propojené předplatné Azure a používá model fakturace měsíčně aktivních uživatelů (MAU). Naučte se, jak propojit prostředek Azure AD B2C k předplatnému a jak model fakturace MAU funguje v následujících oddílech.

> [!IMPORTANT]
> Tento článek neobsahuje informace o cenách. Nejnovější informace o fakturaci a cenách využití najdete v tématu [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Fakturace aktivních uživatelů (MAU) měsíčně

Azure AD B2C se fakturuje podle počtu jedinečných uživatelů s aktivitou ověřování v kalendářním měsíci, který se označuje jako měsíční fakturace aktivních uživatelů (MAU).

Od **1. listopadu 2019**se všechna nově vytvořená Azure AD B2C klienti účtují za měsíčně aktivních uživatelů (MAU). Stávajícím klientům, kteří jsou [propojeni s předplatným](#link-an-azure-ad-b2c-tenant-to-a-subscription) nebo po 1. listopadu 2019, bude účtováno měsíčně aktivních uživatelů (MAU).

Pokud máte stávajícího klienta Azure AD B2C, který byl propojen s předplatným před 1. listopadu 2019, můžete zvolit jednu z následujících akcí:

* Upgradujte na model fakturace měsíčně aktivních uživatelů (MAU) nebo
* Zůstat na modelu fakturace za ověřování

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Přejít na model fakturace aktivních uživatelů měsíčně

Vlastníci předplatného Azure, kteří mají přístup pro správu k prostředku Azure AD B2C, můžou přejít na model fakturace MAU. Možnosti fakturace se konfigurují v prostředku Azure AD B2C.

Faktura přechodu na měsíční aktivní uživatele (MAU) je **nevratná**. Po převedení Azure AD B2C prostředku na model fakturace založeného na MAU nemůžete tento prostředek vrátit zpět na model fakturace podle ověření.

Tady je postup, jak převést MAU na fakturaci na stávající prostředek Azure AD B2C:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako vlastník předplatného.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte Azure AD B2C adresář, který chcete upgradovat na MAU.<br/>
    ![adresář a filtr předplatných v Azure Portal](media/active-directory-b2c-how-to-enable-billing/portal-mau-01-select-b2c-directory.png)
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce **Přehled** tenanta Azure AD B2C vyberte odkaz v části **název prostředku**. Jste přesměrováni na prostředek Azure AD B2C ve vašem tenantovi služby Azure AD.<br/>
    odkaz na prostředek ![Azure AD B2C zvýrazněn v Azure Portal](media/active-directory-b2c-how-to-enable-billing/portal-mau-02-b2c-resource-link.png)
1. Na stránce **Přehled** prostředku Azure AD B2C v části **Fakturovatelné jednotky**vyberte odkaz na **jedno ověření (změnit na MAU)** .<br/>
    v Azure Portal ![změnit na odkaz MAU](media/active-directory-b2c-how-to-enable-billing/portal-mau-03-change-to-mau-link.png)
1. Kliknutím na **Potvrdit** dokončete upgrade na MAUou fakturaci.<br/>
    Dialog potvrzení fakturace na základě MAU ![v Azure Portal](media/active-directory-b2c-how-to-enable-billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Co očekávat při přechodu na MAU fakturace z fakturace na základě ověření

Měření založené na MAU je povolené, jakmile budete vy, předplatné nebo vlastníka prostředků, potvrďte změnu. Vaše měsíční vyúčtování bude odrážet jednotky ověřování účtované do změny a nové jednotky MAU, které začínají změnou.

Uživatelé se během přechodného měsíce nepočítají dvakrát. Jedinečným aktivním uživatelům, kteří se před změnou ověřují, se za kalendářní měsíc účtuje sazba za ověřování. Tyto stejné uživatele nejsou zahrnuté do výpočtu MAU pro zbývající část fakturačního cyklu předplatného. Příklad:

* Tenant společnosti Contoso B2C má 1 000 uživatelů. 250 uživatelů v daném měsíci jsou aktivní. Správce předplatného se od desátého dne v měsíci změní z ověřování na měsíčně aktivních uživatelů (MAU).
* Fakturace za 1. den se účtuje podle modelu ověřování.
  * Pokud se 100 uživatelů přihlásí během této doby (1.10), budou se tyto uživatele označit jako *placené za měsíc*.
* Fakturace od desátého (efektivního času přechodu) se účtuje podle MAU sazby.
  * Pokud se během tohoto období přihlásí dalších 150 uživatelů (desáté 30), účtují se jenom další 150.
  * Pokračující aktivita prvních 100 uživatelů neovlivní fakturaci za zbytek kalendářního měsíce.

Během fakturačního období přechodu se vlastník předplatného zobrazí v prohlášení o fakturaci předplatného Azure položky pro obě metody (ověřování a MAU):

* Položka pro použití až do data a času změny, která odráží podle ověření.
* Položka pro použití po změně, která odráží měsíc aktivních uživatelů (MAU).

Nejnovější informace o fakturaci využití a cenách pro Azure AD B2C najdete v tématu [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Propojení klienta Azure AD B2C k předplatnému

Poplatky za využití Azure Active Directory B2C (Azure AD B2C) se účtují do předplatného Azure. Při vytvoření tenanta Azure AD B2C, musí správce klienta pro explicitní propojení tenanta Azure AD B2C s předplatným Azure.

Odkaz na předplatné se dosahuje vytvořením *prostředku* Azure AD B2C v rámci cílového předplatného Azure. V jednom předplatném Azure se dá vytvořit několik prostředků Azure AD B2C společně s dalšími prostředky Azure, jako jsou virtuální počítače, účty úložiště a Logic Apps. Všechny prostředky v rámci předplatného můžete zobrazit tak, že na tenanta Azure Active Directory (Azure AD) přiřadíte k předplatnému.

Předplatné propojené s klientem Azure AD B2C se dá použít k fakturaci využití Azure AD B2C nebo dalších prostředků Azure, včetně dalších prostředků Azure AD B2C. Nelze použít pro přidání další služby Azure na základě licence nebo licence Office 365 v rámci tenanta Azure AD B2C.

### <a name="prerequisites"></a>Požadavky

* [Předplatné Azure](https://azure.microsoft.com/free/)
* [Azure AD B2C tenanta](active-directory-b2c-get-started.md) , kterého chcete propojit s předplatným
  * Musíte být správcem tenanta.
  * Tenant už nesmí být propojený s předplatným.

### <a name="create-the-link"></a>Vytvořit odkaz

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a pak vyberte adresář, který obsahuje předplatné Azure, které chcete použít (*ne* adresář obsahující Azure AD B2C tenanta).
1. Vyberte **vytvořit prostředek**, do pole **Hledat na Marketplace** zadejte `Active Directory B2C` a pak vyberte **Azure Active Directory B2C**.
1. Vyberte **Vytvořit**.
1. Vyberte **propojit existujícího tenanta Azure AD B2C s předplatným Azure**.
1. Z rozevíracího seznamu vyberte **klienta Azure AD B2C** . Zobrazují se jenom klienti, pro které jste globální správce a kteří ještě nejsou propojení s předplatným. Do pole **Azure AD B2C název prostředku** se naplní název domény Azure AD B2C tenanta, kterého jste vybrali.
1. Vyberte aktivní **předplatné** Azure, které jste správcem.
1. V části **Skupina prostředků**vyberte **vytvořit novou**a zadejte **umístění skupiny prostředků**. Toto nastavení skupiny prostředků nemá žádný vliv na váš Azure AD B2C umístění tenanta, výkon ani stav fakturace.
1. Vyberte **Vytvořit**.
    ![stránka pro vytváření prostředků Azure AD B2C v Azure Portal](./media/active-directory-b2c-how-to-enable-billing/portal-01-create-b2c-resource-page.png)

Po dokončení těchto kroků pro klienta Azure AD B2C se vaše předplatné Azure bude účtovat podle údajů přímo v Azure Direct nebo smlouva Enterprise, pokud jsou k dispozici.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků tenanta Azure AD B2C

Po vytvoření prostředku Azure AD B2C v rámci předplatného Azure by se měl zobrazit nový prostředek typu "tenant B2C" s ostatními prostředky Azure.

Můžete použít tento prostředek na:

* Přejděte do předplatného, kde najdete informace o fakturaci.
* Získá ID tenanta Azure AD B2C tenanta ve formátu GUID.
* Přejděte do svého tenanta Azure AD B2C
* Odeslání žádosti o podporu
* Přesunutí prostředku tenanta Azure AD B2C do jiného předplatného nebo skupiny prostředků Azure

![Stránka nastavení prostředků B2C v Azure Portal](./media/active-directory-b2c-how-to-enable-billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Oblastní omezení

Pokud jste ve svém předplatném stanovili oblastní omezení pro vytváření prostředků Azure, může vám toto omezení zabránit v vytváření prostředku Azure AD B2C.

Pokud chcete tento problém zmírnit, uvolněte místní omezení.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Předplatná Poskytovatelé cloudových řešení Azure (CSP)

Předplatná poskytovatele Cloud Solution Provider (CSP) jsou podporovaná v Azure AD B2C. Tato funkce je dostupná pomocí rozhraní API nebo Azure Portal pro Azure AD B2C a pro všechny prostředky Azure. Správci předplatného CSP můžou relace propojit, přesunout a odstranit Azure AD B2C tak, jak se používají v jiných prostředcích Azure.

Správa Azure AD B2C pomocí řízení přístupu na základě role není ovlivněna přidružením mezi klientem Azure AD B2C a předplatným CSP Azure. Řízení přístupu na základě rolí se dosahuje pomocí rolí na základě klientů, nikoli rolí na základě předplatného.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Změna předplatného fakturace klienta Azure AD B2C

Pokud zdrojové a cílové odběry existují v rámci stejného Azure Active Directory tenanta, můžete Azure AD B2C klienty přesunout do jiného předplatného.

Informace o tom, jak přesunout prostředky Azure jako tenanta Azure AD B2C do jiného předplatného, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Než začnete s přesunem, nezapomeňte si přečíst celý článek, abyste plně pochopili omezení a požadavky pro takové přesunutí. Kromě pokynů pro přesunutí prostředků obsahuje důležité informace, jako je například kontrolní seznam před přesunem a ověření operace přesunutí.

## <a name="next-steps"></a>Další kroky

Kromě kontroly údajů o využití a fakturaci v rámci vybraného předplatného Azure si můžete projít podrobné sestavy o využití pomocí [rozhraní API pro vytváření sestav využití](active-directory-b2c-reference-usage-reporting-api.md).
