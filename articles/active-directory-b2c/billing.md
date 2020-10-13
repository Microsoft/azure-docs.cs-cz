---
title: Model fakturace pro Azure Active Directory B2C
description: Přečtěte si o modelu fakturace B2C's měsíčně aktivních uživatelů Azure AD (MAU), o tom, jak propojit klienta Azure AD B2C k předplatnému Azure a jak vybrat vhodné ceny úrovně Premium.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 416e2c767b5afd40fea38e6f75fcd3f01440b49a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255331"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model fakturace pro Azure Active Directory B2C

Ceny za Azure Active Directory B2C (Azure AD B2C) jsou založené na měsíčních aktivních uživatelích (MAU), což je počet jedinečných uživatelů s aktivitou ověřování v kalendářním měsíci. Tento model fakturace se vztahuje na klienty Azure AD B2C a [spolupráci uživatelů Azure AD hosta (B2B)](https://docs.microsoft.com/azure/active-directory/external-identities/external-identities-pricing). MAU účtování pomáhá snižovat náklady tím, že nabízí bezplatnou úroveň a flexibilní a předvídatelné ceny. V tomto článku se dozvíte o fakturaci MAU, propojování klientů Azure AD B2C k předplatnému a změně cenové úrovně.

> [!IMPORTANT]
> Tento článek neobsahuje informace o cenách. Nejnovější informace o fakturaci a cenách využití najdete v tématu [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="what-do-i-need-to-do"></a>Co musím udělat?

Pokud chcete využít výhod fakturace MAU, musí být váš tenant Azure AD B2C propojený s předplatným Azure. Pokud chcete používat funkce Azure AD B2C Premium P2, jako je podmíněný přístup na základě rizika, může být taky potřeba přepnout klienta Azure AD B2C na jinou cenovou úroveň.

|Pokud je váš tenant:  |Musíte:  |
|---------|---------|
| Tenant Azure AD B2C se už účtuje podle MAU.     | Není potřeba dělat nic. Když se uživatelé přihlásí k vašemu Azure AD B2C tenanta, automaticky se vám bude účtovat pomocí fakturačního modelu založeného na MAU.        |
| Tenant Azure AD B2C ještě není propojený s předplatným.     |  [Propojte klienta Azure AD B2C k předplatnému](#link-an-azure-ad-b2c-tenant-to-a-subscription) , abyste mohli aktivovat MAUou fakturaci.     |
| Tenant Azure AD B2C propojený s předplatným před 1. listopadu 2019    | [Přepněte na MAU (doporučeno)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants), nebo si zajistěte, aby byl model fakturace podle ověření.     |
| Tenant Azure AD B2C a chcete používat prémiové funkce (jako podmíněný přístup na základě rizik)    | [Přejděte na cenovou úroveň Azure AD](#change-your-azure-ad-pricing-tier) , která podporuje funkce, které chcete použít.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>O modelu fakturace měsíčně aktivních uživatelů (MAU)

MAU fakturace se projeví u klientů Azure AD B2C od **1. listopadu 2019**. Každý Azure AD B2C tenant, který jste vytvořili a propojili s předplatným v nebo po tomto datu, se účtuje na základě MAU. Pokud máte klienta Azure AD B2C, který nebyl propojený s předplatným, budete ho muset provést nyní. Pokud máte stávajícího klienta Azure AD B2C, který byl propojen s předplatným před 1. listopadu 2019, doporučujeme upgradovat na model fakturace měsíčně aktivních uživatelů (MAU) nebo můžete zůstat na modelu fakturace za ověřování.
  
Váš tenant Azure AD B2C musí být taky propojený s příslušnou cenovou úrovní Azure na základě funkcí, které chcete použít. Prémiové funkce vyžadují [ceny Azure AD B2C Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Pokud používáte nové funkce, možná budete muset upgradovat cenovou úroveň. Například podmíněný přístup budete muset pro svého tenanta vybrat cenovou úroveň Azure AD B2C Premium P2.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Propojení klienta Azure AD B2C k předplatnému

Poplatky za využití Azure Active Directory B2C (Azure AD B2C) se účtují do předplatného Azure. Musíte explicitně propojit klienta Azure AD B2C k předplatnému Azure vytvořením *prostředku* Azure AD B2C v rámci cílového předplatného Azure. V jednom předplatném Azure se dá vytvořit několik prostředků Azure AD B2C společně s dalšími prostředky Azure, jako jsou virtuální počítače, účty úložiště a Logic Apps. Všechny prostředky v rámci předplatného můžete zobrazit tak, že na tenanta Azure Active Directory (Azure AD) přiřadíte k předplatnému.

Předplatné propojené s klientem Azure AD B2C se dá použít k fakturaci využití Azure AD B2C nebo dalších prostředků Azure, včetně dalších prostředků Azure AD B2C. Nedá se použít k přidání dalších služeb založených na licencích Azure nebo licencí Office 365 v rámci tenanta Azure AD B2C.

### <a name="prerequisites"></a>Požadavky

* [Předplatné Azure](https://azure.microsoft.com/free/)
* [Azure AD B2C tenanta](tutorial-create-tenant.md) , kterého chcete propojit s předplatným
  * Musíte být správcem tenanta.
  * Tenant už nesmí být propojený s předplatným.

### <a name="create-the-link"></a>Vytvořit odkaz

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. V horní nabídce vyberte filtr **adresář + odběr** a pak vyberte adresář, který obsahuje předplatné Azure, které chcete použít (*ne* adresář obsahující Azure AD B2C tenanta).
3. Vyberte **vytvořit prostředek**, zadejte `Active Directory B2C` do pole **Hledat na Marketplace** a pak vyberte **Azure Active Directory B2C**.
4. Vyberte **Vytvořit**.
5. Vyberte **propojit existujícího tenanta Azure AD B2C s předplatným Azure**.
6. Z rozevíracího seznamu vyberte **klienta Azure AD B2C** . Zobrazují se jenom klienti, pro které jste globální správce a kteří ještě nejsou propojení s předplatným. Do pole **Azure AD B2C název prostředku** se naplní název domény Azure AD B2C tenanta, kterého jste vybrali.
7. Vyberte aktivní **předplatné** Azure, které jste správcem.
8. V části **Skupina prostředků**vyberte **vytvořit novou**a zadejte **umístění skupiny prostředků**. Toto nastavení skupiny prostředků nemá žádný vliv na váš Azure AD B2C umístění tenanta, výkon ani stav fakturace.
9. Vyberte **Vytvořit**.

    ![Stránka pro vytvoření prostředku Azure AD B2C v Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Po dokončení těchto kroků pro klienta Azure AD B2C se vaše předplatné Azure účtuje na základě přímých nebo smlouva Enterprisech detailů Azure, pokud jsou k dispozici.

## <a name="change-your-azure-ad-pricing-tier"></a>Změna cenové úrovně Azure AD

Tenant musí být propojený s příslušnou cenovou úrovní Azure na základě funkcí, které chcete používat s vaším klientem Azure AD B2C. Prémiové funkce vyžadují Azure AD B2C Premium P1 nebo P2, jak je popsáno v [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/). V některých případech budete muset při používání nových funkcí upgradovat cenovou úroveň. Pokud například chcete použít ochranu identity, podmíněný přístup na základě rizik a jakékoli budoucí možnosti Premium P2 s Azure AD B2C, budete muset pro svého tenanta vybrat cenovou úroveň Azure AD B2C Premium P2.

Pokud chcete změnit cenovou úroveň, postupujte podle těchto kroků.

1. Přihlaste se k portálu Azure.

2. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje předplatné Azure, se kterým je váš TENANT Azure B2C*propojený (nevybírejte* samotného klienta Azure AD B2C).

3. Do vyhledávacího pole v horní části portálu zadejte název vašeho tenanta Azure AD B2C. Pak ve výsledcích hledání v části **prostředky**vyberte tenanta.

4. Na stránce **Přehled** prostředků v části **cenová úroveň**vyberte **změnit**.

   ![Změna cenové úrovně](media/billing/change-pricing-tier.png)
 
5. Vyberte cenovou úroveň zahrnující funkce, které chcete povolit.

   ![Výběr cenové úrovně](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Přepnout na MAU fakturace (od do listopadu 2019 Azure AD B2C tenantů)

Pokud jste klienta Azure AD B2C propojili s předplatným před **1. listopadu 2019**, použije se předchozí model fakturace podle ověřování. Doporučujeme upgradovat na model fakturace měsíčně aktivních uživatelů (MAU). Možnosti fakturace se konfigurují v prostředku Azure AD B2C.

Faktura přechodu na měsíční aktivní uživatele (MAU) je **nevratná**. Po převedení Azure AD B2C prostředku na model fakturace založeného na MAU nemůžete tento prostředek vrátit zpět na model fakturace podle ověření.

Tady je postup, jak převést MAU na fakturaci na stávající prostředek Azure AD B2C:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako vlastník předplatného s přístupem správce k prostředku Azure AD B2C.

2. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte Azure AD B2C adresář, který chcete upgradovat na MAU.<br/>

    ![Filtr adresářů a předplatných v Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.

4. Na stránce **Přehled** tenanta Azure AD B2C vyberte odkaz v části **název prostředku**. Jste přesměrováni na prostředek Azure AD B2C ve vašem tenantovi služby Azure AD.<br/>

    ![Odkaz na prostředek Azure AD B2C zvýrazněný v Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Na stránce **Přehled** prostředku Azure AD B2C v části **Fakturovatelné jednotky**vyberte odkaz na **jedno ověření (změnit na MAU)** .<br/>

    ![Změnit na MAU propojení zvýrazněné v Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Kliknutím na **Potvrdit** dokončete upgrade na MAUou fakturaci.<br/>

    ![Potvrzovací dialogové okno fakturace založené na MAU v Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Co očekávat při přechodu na MAU fakturace z fakturace na základě ověření

Měření založené na MAU je povolené, jakmile budete vy, předplatné nebo vlastníka prostředků, potvrďte změnu. Vaše měsíční vyúčtování bude odrážet jednotky ověřování účtované do změny a nové jednotky MAU, které začínají změnou.

Uživatelům se během přechodného měsíce nepočítá dvakrát. Jedinečným aktivním uživatelům, kteří se před změnou ověřují, se za kalendářní měsíc účtuje sazba za ověřování. Tyto stejné uživatele nejsou zahrnuté do výpočtu MAU pro zbytek fakturačního cyklu předplatného. Například:

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

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků tenanta Azure AD B2C

Po vytvoření prostředku Azure AD B2C v rámci předplatného Azure by se měl zobrazit nový prostředek typu "tenant B2C" s ostatními prostředky Azure.

Tento prostředek můžete použít k těmto akcím:

* Přejděte do předplatného, kde najdete informace o fakturaci.
* Získá ID tenanta Azure AD B2C tenanta ve formátu GUID.
* Přejít na svého tenanta Azure AD B2C
* Odeslat žádost o podporu
* Přesunutí prostředku tenanta Azure AD B2C do jiného předplatného nebo skupiny prostředků Azure

### <a name="regional-restrictions"></a>Oblastní omezení

Pokud jste ve svém předplatném stanovili oblastní omezení pro vytváření prostředků Azure, může vám toto omezení zabránit v vytváření prostředku Azure AD B2C.

Pokud chcete tento problém zmírnit, uvolněte místní omezení.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Předplatná Poskytovatelé cloudových řešení Azure (CSP)

Předplatná poskytovatele Cloud Solution Provider (CSP) jsou podporovaná v Azure AD B2C. Tato funkce je dostupná pomocí rozhraní API nebo Azure Portal pro Azure AD B2C a pro všechny prostředky Azure. Správci předplatného CSP můžou relace propojit, přesunout a odstranit Azure AD B2C tak, jak se používají v jiných prostředcích Azure.

Správa Azure AD B2C pomocí řízení přístupu na základě role není ovlivněna přidružením mezi klientem Azure AD B2C a předplatným CSP Azure. Řízení přístupu na základě rolí se dosahuje pomocí rolí na základě klientů, nikoli rolí na základě předplatného.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Změna předplatného fakturace klienta Azure AD B2C

### <a name="move-using-azure-resource-manager"></a>Přesunout pomocí Azure Resource Manager

Azure AD B2C klienty lze přesunout do jiného předplatného pomocí Azure Resource Manager Pokud zdrojové a cílové odběry existují v rámci stejného Azure Active Directory tenanta.

Informace o tom, jak přesunout prostředky Azure jako tenanta Azure AD B2C do jiného předplatného, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Než začnete s přesunem, nezapomeňte si přečíst celý článek, abyste plně pochopili omezení a požadavky pro takové přesunutí. Kromě pokynů pro přesunutí prostředků obsahuje důležité informace, jako je například kontrolní seznam před přesunem a ověření operace přesunutí.

### <a name="move-by-unlinking-and-relinking"></a>Přesunout propojením a přepojováním

Pokud jsou zdrojová a cílová předplatná přidružená k různým klientům Azure Active Directory, nemůžete provést přesun prostřednictvím Azure Resource Manager, jak je vysvětleno výše. Stejný výsledek však můžete přesto dosáhnout zrušením propojení tenanta Azure AD B2C ze zdrojového předplatného a jeho přepropojením s cílovým předplatným. Tato metoda je bezpečná, protože jediný objekt, který odstraníte, je *fakturační odkaz*, nikoli tenant Azure AD B2C sám. Nebudou ovlivněni žádní uživatelé, aplikace, toky uživatelů atd.

1. V samotném Azure AD B2C adresáři [Pozvěte uživatele typu Host](user-overview.md#guest-user) z cílového klienta služby Azure AD (ten, se kterým je cílový odběr Azure propojený), a ujistěte se, že tento uživatel má v Azure AD B2C roli **globálního správce** .
1. Přejděte do *prostředku Azure* , který představuje Azure AD B2C ve vašem zdrojovém předplatném Azure, jak je vysvětleno v části [správa prostředků klientů Azure AD B2C](#manage-your-azure-ad-b2c-tenant-resources) výše. Neprovádějte přepnutí na vlastního tenanta Azure AD B2C.
1. Na stránce **Přehled** vyberte tlačítko **Odstranit** . Tím *nedojde* k odstranění souvisejících uživatelů ani aplikací klienta Azure AD B2C. Pouze odebere fakturační propojení ze zdrojového předplatného.
1. Přihlaste se k Azure Portal pomocí uživatelského účtu, který jste přidali jako správce v Azure AD B2C v kroku 1. Pak přejděte do cílového předplatného Azure, které je propojené s cílovým Azure Active Directory tenant. 
1. Pomocí výše uvedeného postupu [Vytvoření odkazu](#create-the-link) znovu vytvořte fakturační odkaz v cílovém předplatném.
1. Váš prostředek Azure AD B2C se teď přesunul do cílového předplatného Azure (propojený s cílovým Azure Active Directory) a bude se účtovat přes toto předplatné od této chvíle.

## <a name="next-steps"></a>Další kroky

Nejnovější informace o cenách najdete v článku [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).