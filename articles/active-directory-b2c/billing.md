---
title: Model fakturace pro Azure Active Directory B2C
description: Přečtěte si o modelu fakturace aktivních uživatelů Azure AD B2C (MAU) a o tom, jak povolit fakturaci pro konkrétní předplatné Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190003"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model fakturace pro Azure Active Directory B2C

Využití Azure Active Directory B2C (Azure AD B2C) se účtuje podle propojeného předplatného Azure a používá měsíční model fakturace aktivních uživatelů (MAU). Zjistěte, jak propojit prostředek Azure AD B2C s předplatným a jak funguje model fakturace MAU v následujících částech.

> [!IMPORTANT]
> Tento článek neobsahuje informace o cenách. Nejnovější informace o fakturaci využití a cenách najdete v [tématu Ceny Služby Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Měsíční fakturace aktivních uživatelů (MAU)

Azure AD B2C fakturace se měří podle počtu jedinečných uživatelů s aktivitou ověřování v rámci kalendářního měsíce, označované jako měsíční aktivní uživatelé (MAU) fakturace.

**listopadu 2019**se všem nově vytvořeným klientům Azure AD B2C účtují za měsíc aktivní uživatelé (MAU). Stávající klienti, kteří jsou [propojeni s předplatným](#link-an-azure-ad-b2c-tenant-to-a-subscription) na nebo po 01 Listopad 2019 se bude účtovat za měsíc aktivních uživatelů (MAU).

Pokud máte stávajícího klienta Azure AD B2C, který byl propojený s před 01 Listopad 2019, můžete provést jednu z následujících akcí:

* Upgrade na model měsíční fakturace aktivních uživatelů (MAU) nebo
* Mějte na modelu fakturace podle ověřování

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Upgrade na měsíční model fakturace aktivních uživatelů

Vlastníci předplatného Azure, kteří mají přístup pro správu k prostředku Azure AD B2C, můžou přepnout na model fakturace MAU. Možnosti fakturace jsou nakonfigurované ve vašem prostředku Azure AD B2C.

Přechod na měsíční fakturaci aktivních uživatelů (MAU) je **nevratný**. Jakmile převedete prostředek Azure AD B2C na fakturační model založený na MAU, nemůžete tento prostředek vrátit zpět do fakturačního modelu ověřování.

Tady je postup, jak provést přechod na fakturaci MAU pro existující prostředek Azure AD B2C:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako vlastník předplatného.
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář Azure AD B2C, který chcete upgradovat na fakturaci MAU.<br/>
    ![Filtr adresářů a předplatného na webu Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce **Přehled** klienta Azure AD B2C vyberte odkaz v části **Název prostředku**. Budete přesměrováni na prostředek Azure AD B2C ve vašem tenantovi Azure AD.<br/>
    ![Zvýrazněné propojení prostředků Azure AD B2C na webu Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na stránce **Přehled** prostředku Azure AD B2C v části **Fakturovatelné jednotky**vyberte odkaz **Na ověření (změnit na MAU).**<br/>
    ![Změna odkazu MAU zvýrazněného na webu Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Chcete-li dokončit upgrade na fakturaci MAU, vyberte **možnost Potvrdit.**<br/>
    ![Dialogové okno pro potvrzení fakturace na základě MAU na webu Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Co můžete očekávat při přechodu na fakturaci MAU z fakturace podle ověřování

Měření založené na MAU je povoleno, jakmile tuto změnu potvrdíte vy, vlastník předplatného nebo prostředku. Váš měsíční účet bude odrážet jednotky ověřování fakturované až do změny a nové jednotky MAU počínaje změnou.

Uživatelé nejsou dvakrát počítány během měsíce přechodu. Jedinečným aktivním uživatelům, kteří se ověří před změnou, se v kalendářním měsíci účtuje sazba za ověření. Titíž uživatelé nejsou zahrnuty do výpočtu MAU pro zbytek fakturačního cyklu předplatného. Například:

* Klient Contoso B2C má 1 000 uživatelů. V daném měsíci je aktivních 250 uživatelů. Správce předplatného změní z per-authentication na měsíční aktivní uživatele (MAU) na 10th v měsíci.
* Fakturace pro 1st-10th se účtuje pomocí modelu ověřování.
  * Pokud se během tohoto období přihlásí 100 uživatelů (1.-10.), budou tito uživatelé označeni jako *placení za měsíc*.
* Fakturace od 10th (efektivní čas přechodu) se účtuje v sazbě MAU.
  * Pokud se během tohoto období přihlašuje dalších 150 uživatelů (10.-30.), budou se účtovat pouze dalších 150 uživatelů.
  * Pokračující aktivita prvních 100 uživatelů nemá vliv na fakturaci po zbytek kalendářního měsíce.

Během fakturačního období přechodu se vlastník předplatného pravděpodobně zobrazí položky pro obě metody (pro ověřování a na MAU) ve výpisu z fakturace předplatného Azure:

* Položka pro použití až do data a času změny, která odráží ověřování.
* Položka pro použití po změně, která odráží měsíční aktivní uživatele (MAU).

Nejnovější informace o fakturaci využití a cenách pro Azure AD B2C najdete v [tématu Ceny Služby Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Propojení klienta Azure AD B2C s předplatným

Poplatky za využití pro Azure Active Directory B2C (Azure AD B2C) se účtují k předplatnému Azure. Když se vytvoří tenant AD B2C Azure, správce klienta musí explicitně propojit klienta Azure AD B2C s předplatným Azure.

Propojení předplatného je dosaženo vytvořením *prostředku* Azure AD B2C v rámci cílového předplatného Azure. Několik prostředků Azure AD B2C můžete vytvořit v jednom předplatném Azure, spolu s dalšími prostředky Azure, jako jsou virtuální počítače, účty úložiště a logic apps. Všechny prostředky v rámci předplatného můžete zobrazit tak, že přejdete do klienta Azure Active Directory (Azure AD), ke kterému je předplatné přidruženo.

Předplatné propojené s klientem Azure AD B2C se dá použít pro fakturaci využití Azure AD B2C nebo jiných prostředků Azure, včetně dalších prostředků Azure AD B2C. Nelze použít k přidání dalších služeb založených na licencích Azure nebo licencí Office 365 v rámci tenanta Azure AD B2C.

### <a name="prerequisites"></a>Požadavky

* [Předplatné Azure](https://azure.microsoft.com/free/)
* [Tenant Azure AD B2C,](tutorial-create-tenant.md) který chcete propojit s předplatným
  * Musíte být správcem klienta.
  * Klient už nesmí být propojen s předplatným.

### <a name="create-the-link"></a>Vytvoření odkazu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje předplatné Azure, které chcete použít *(ne* adresář obsahující tenanta Azure AD B2C).
1. Vyberte **Vytvořit**prostředek `Active Directory B2C` , zadejte do pole **Hledat marketplace** a pak vyberte **Azure Active Directory B2C**.
1. Vybrat **vytvořit**
1. Vyberte **Propojit existujícího klienta Azure AD B2C s mým předplatným Azure**.
1. V rozevíracím souboru vyberte **klienta Azure AD B2C.** Zobrazí se pouze klienti, pro které jste globální správce a které ještě nejsou propojeny s předplatným. Pole **název prostředku Azure AD B2C** je naplněn název domény klienta Azure AD B2C, který vyberete.
1. Vyberte aktivní **předplatné Azure,** jehož jste správcem.
1. V části **Skupina prostředků**vyberte **Vytvořit nové**a zadejte **umístění skupiny prostředků**. Nastavení skupiny prostředků zde nemají žádný vliv na umístění, výkon nebo stav fakturace klienta Azure AD B2C.
1. Vyberte **Vytvořit**.
    ![Stránka vytváření prostředků Azure AD B2C na webu Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Po dokončení těchto kroků pro klienta Azure AD B2C se vaše předplatné Azure účtuje v souladu s podrobnostmi o vaší smlouvě Azure Direct nebo Enterprise, pokud je to možné.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků klienta Azure AD B2C

Po vytvoření prostředku Azure AD B2C v předplatném Azure, měli byste vidět nový prostředek typu "Klient B2C" se zobrazí s ostatními prostředky Azure.

Tento zdroj můžete použít k:

* Přejděte k předplatnému a zkontrolujte fakturační údaje.
* Získání ID klienta klienta Azure AD B2C ve formátu GUID
* Přejděte na svého klienta Azure AD B2C
* Odeslání žádosti o podporu
* Přesunutí prostředku klienta Azure AD B2C do jiného předplatného nebo skupiny prostředků Azure

![Stránka nastavení prostředků B2C na webu Azure Portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionální omezení

Pokud jste ve svém předplatném zavedli místní omezení pro vytváření prostředků Azure, může vám toto omezení bránit ve vytváření prostředků Azure AD B2C.

Chcete-li tento problém zmírnit, uvolněte místní omezení.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Předplatná zprostředkovatelů cloudových řešení Azure (CSP)

Předplatná Azure Cloud Solution Providers (CSP) jsou podporovaná v Azure AD B2C. Funkce je dostupná pomocí rozhraní API nebo portálu Azure pro Azure AD B2C a pro všechny prostředky Azure. Správci předplatného CSP můžou propojovat, přesouvat a odstraňovat vztahy s Azure AD B2C, jak je tomu u jiných prostředků Azure.

Správa Azure AD B2C pomocí řízení přístupu na základě rolí není ovlivněna přidružení mezi tenantem Azure AD B2C a předplatným Azure CSP. Řízení přístupu založené na rolích je dosaženo pomocí rolí založených na tenantovi, nikoli rolí založených na předplatném.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Změna předplatného fakturace klienta Azure AD B2C

Tenanty Azure AD B2C lze přesunout do jiného předplatného, pokud existují zdrojové a cílové předplatná v rámci stejného klienta Azure Active Directory.

Informace o tom, jak přesunout prostředky Azure, jako je váš tenant Azure AD B2C, do jiného předplatného, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Než začnete přesun, přečtěte si celý článek, abyste plně pochopili omezení a požadavky pro takový krok. Kromě pokynů pro přesun prostředků obsahuje důležité informace, jako je kontrolní seznam před přesunutím a jak ověřit operaci přesunutí.

## <a name="next-steps"></a>Další kroky

Nejnovější informace o cenách najdete [v tématu Ceny Služby Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
