---
title: Začínáme s Azure Cost Management pro partnery
description: V tomto článku se dozvíte, jak partneři používají funkce Azure Cost Management a jak jim umožňují Cost Management přístup pro své zákazníky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377691"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Začínáme s Azure Cost Management pro partnery

Azure Cost Management je nativně dostupná pro partnery, kteří se připojili k zákaznickým smlouvám Microsoftu. Tento článek vysvětluje, jak partneři používají funkce [Azure cost management](https://docs.microsoft.com/azure/cost-management/) . Také popisuje, jak partneři umožňují Cost Management přístupu pro své zákazníky. Zákazníci CSP můžou používat funkce Cost Management, když je povolí jejich partner CSP.

Partneři CSP používají Cost Management k těmto akcím:

- Pochopení fakturovaných nákladů a přidružení nákladů k zákazníkovi, předplatným, skupinám prostředků a službám.
- Získejte intuitivní pohled na náklady na Azure v rámci [analýzy nákladů](quick-acm-cost-analysis.md) díky funkcím, které vám umožní analyzovat náklady podle zákazníka, předplatného, skupiny prostředků, prostředku, měřiče, služby a mnoha dalších dimenzí.
- Prohlédněte si náklady na prostředky, které mají pro účely analýzy nákladů použité kredity pro partnery (PEC).
- Nastavení oznámení a automatizace pomocí programových [rozpočtů](tutorial-acm-create-budgets.md) a výstrah v případě, že náklady přesahují rozpočty
- Povolte zásady Azure Resource Manager, které poskytují zákazníkům přístup k datům Cost Management. Zákazníci si pak můžou zobrazit data nákladů na spotřebu pro svá předplatná podle [tarifů](https://azure.microsoft.com/pricing/calculator/)průběžných plateb.

Všechny funkce, které jsou dostupné v Azure Cost Management, jsou dostupné taky s rozhraními REST API. Použijte rozhraní API k automatizaci úloh správy nákladů.

## <a name="prerequisites"></a>Předpoklady

Azure Cost Management vyžaduje přístup pro čtení k fakturačnímu účtu nebo předplatnému. Přístup je možné udělit na libovolné úrovni výše než vašich prostředků, od fakturačního účtu nebo skupiny pro správu až po jednotlivé skupiny prostředků, kde spravujete své aplikace. Aby uživatelé předplatného viděli ceny a náklady, musí být pro fakturační účet povolené oprávnění zobrazit poplatky. Další informace o povolení a přiřazování přístupu k Azure Cost Management najdete v tématu [přiřazení přístupu k datům](assign-access-acm-data.md). Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management používá obory

Obory jsou místo, kde můžete spravovat fakturační data, mít role specifické pro platby, zobrazovat faktury a provádět Obecné řízení účtů. Role fakturace a účtu se spravují odděleně od oborů používaných pro správu prostředků, které používají RBAC. Aby bylo možné jasně odlišit záměr samostatných oborů, včetně rozdílů v řízení přístupu, označují se jako obory fakturace a rozsahy RBAC v uvedeném pořadí.

Informace o oborech fakturace a oborech RBAC a o tom, jak Správa nákladů pracuje s obory, najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Správa nákladů pomocí oborů fakturace partnerských klientů

Po připojení zákazníků k zákaznické smlouvě Microsoftu jsou ve vašem tenantovi k dispozici následující _fakturační rozsahy_ . Pomocí oborů můžete spravovat náklady v Cost Management.

### <a name="billing-account-scope"></a>Rozsah fakturačního účtu

Rozsah fakturačního účtu můžete použít k zobrazení nákladů na předběžnou daň ve všech vašich zákaznících a fakturačních profilech. Můžete si také zobrazit náklady na fakturu pro produkty založené na spotřebě pro zákazníky se zákaznickou smlouvou Microsoftu. Náklady na fakturu se zobrazí také pro zakoupené produkty pro zákazníky se smlouvou o poskytování služeb Microsoft Customer a CSP. V současné době je výchozí měnou pro zobrazení nákladů v oboru US dolary. Rozpočty nastavené pro obor jsou také v USD.

Bez ohledu na měnu vydanou zákazníky používají partneři obor k nastavení rozpočtů a správě nákladů v USD napříč zákazníky, předplatnými, prostředky a skupinami prostředků.

Partneři také filtruje náklady v konkrétní fakturační měně napříč zákazníky v zobrazení analýzy nákladů. Vyberte seznam **vlastní náklady** , abyste zobrazili náklady v podporovaných měnách fakturace zákazníků.

![Příklad zobrazující aktuální výběr nákladů pro měny](./media/get-started-partners/actual-cost-selector.png)

Pomocí [zobrazení amortizace nákladů](quick-acm-cost-analysis.md#customize-cost-views) v oborech fakturace můžete zobrazit náklady na amortizaci rezervovaných instancí napříč obdobím rezervace.

### <a name="billing-profile-scope"></a>Rozsah fakturačního profilu

Rozsah fakturačního profilu můžete použít k zobrazení nákladů na předběžnou daň ve fakturační měně všech vašich zákazníků pro všechny produkty a odběry, které jsou součástí faktury. Pomocí filtru **InvoiceID** můžete filtrovat náklady v profilu fakturace pro konkrétní fakturu. Filtr zobrazuje náklady na spotřebu a nákup produktu pro konkrétní fakturu. Můžete také filtrovat náklady na konkrétního zákazníka na faktuře, abyste viděli náklady před zdaněním.

Po připojení zákazníků k zákaznickým smlouvám Microsoftu obdržíte fakturu zákazníka, která zobrazuje poplatky za nárok a zakoupené produkty, jako je SaaS, Azure Marketplace a rezervace. Když se fakturuje ve stejné fakturační měně, Fakturuje se taky poplatky za Zákazníky, které nejsou zahrnuté v nové smlouvě o zákaznících Microsoftu.

Aby se vám pomohly sjednotit poplatky vůči faktuře s zákazníkem, rozsah fakturačních profilů vám umožní zobrazit všechny náklady, které budou pro zákazníky Fakturovatelné. Podobně jako u faktury se v oboru zobrazuje náklady pro každého zákazníka v nové smlouvě o zákaznících Microsoftu. Obor také uvádí všechny poplatky za produkty nároků na zákazníky v aktuální nabídce CSP.

Fakturační profil a obory fakturačního účtu jsou jenom ty, které ukazují poplatky za opravňující a zakoupené produkty.

Fakturační profily definují odběry, které jsou zahrnuty na faktuře. Fakturační profily představují funkční ekvivalent registrace smlouvy Enterprise. Zápis je obor, ve kterém jsou vygenerovány faktury. Podobně jsou nákupy, které nejsou založené na využití, například Azure Marketplace a rezervace, dostupné pouze v rozsahu fakturačního profilu.

V současné době je fakturační měna zákazníka výchozí měnou při prohlížení nákladů v rozsahu fakturačního profilu. Rozpočty nastavené v oboru fakturačního profilu jsou ve fakturační měně.

Partneři můžou pomocí oboru sjednotit faktury. A používají obor k nastavení rozpočtů v zúčtovací měně pro:

- Konkrétní filtrovaná faktura
- Zákazníka
- Předplatné
- Skupina prostředků
- Prostředek
- Služba Azure
- Měření
- ResellerMPNID

### <a name="customer-scope"></a>Obor zákazníka

Partneři využívají obor ke správě nákladů spojených se zákazníky, kteří jsou připojení ke Smlouvě o zákaznících Microsoftu. Obor umožňuje partnerům zobrazovat náklady na předběžnou daň pro konkrétního zákazníka. Můžete také filtrovat náklady před zdaněním u určitého předplatného, skupiny prostředků nebo prostředku.

Obor zákazníka neobsahuje zákazníky, kteří patří do aktuální nabídky CSP. Náklady na nárok, ne využití Azure, pro stávající zákazníky nabídky CSP jsou k dispozici na fakturačním účtu a v oborech profilů fakturačních, když použijete filtr zákazníka.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Přístup partnerů k oborům fakturace v Cost Management

Jenom uživatelé s rolemi **globální správce** a **Agent pro správu** můžou spravovat a zobrazovat náklady na fakturační účty, profily fakturace a zákazníky přímo v tenantovi Azure partnera. Další informace o rolích partnerského centra najdete v tématu [přiřazení rolí uživatelů a oprávnění](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>Povolit správu nákladů v tenantovi zákazníka

Partneři můžou povolit přístup k Cost Management, jakmile se zákazníci připojí k zákaznické smlouvě Microsoftu. Pak partneři potom můžou povolit zásadu, která zákazníkům umožní zobrazit své náklady vypočítané podle maloobchodních tarifů průběžných plateb. Náklady se zobrazují v fakturační měně zákazníka za využívání využívaného využití v rámci předplatného RBAC a skupin prostředků.

Když partner povolí zásady pro viditelnost nákladů, může spravovat a analyzovat náklady podle tarifů průběžných plateb pro každého uživatele, který Azure Resource Manager přístup k předplatnému. Prodejci a zákazníci, kteří mají vhodný přístup k předplatným služby Azure, mohou zobrazit náklady.

Bez ohledu na zásadu si partneři můžou náklady zobrazit i v případě, že mají přístup k předplatnému a skupině prostředků.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Povolit zásadám zobrazovat poplatky za využití Azure

Partneři pomocí následujících informací umožňují zásadám zobrazovat poplatky za využití Azure pro své zákazníky.

V Azure Portal se přihlaste k partnerskému tenantovi a klikněte na **cost management + fakturace**. Vyberte fakturační účet a pak klikněte na **zákazníci**. Seznam zákazníků je přidružený k fakturačnímu účtu.

V seznamu zákazníků vyberte zákazníka, kterému chcete dovolit zobrazit náklady.

![Vybrat zákazníky v Cost Management](./media/get-started-partners/customer-list.png)

V části **Nastavení**klikněte na **zásady**.

V této zásadě se účtují poplatky za **využití Azure** spojené s předplatnými pro vybraného zákazníka.
@no__t – 0Policy, aby si zákazníci mohli zobrazit poplatky za průběžné platby @ no__t-1

Pokud je zásada nastavená na **ne**, Azure cost management není k dispozici pro uživatele předplatného přidruženého k zákazníkovi. Pokud není povolená partnerem, jsou zásady viditelnosti nákladů ve výchozím nastavení pro všechny uživatele předplatného zakázané.

Když je zásada nákladů nastavená na **Ano**, uživatelé předplatného přidružení k klientovi zákazníka uvidí poplatky za využití podle tarifů průběžných plateb.

Když je zásada viditelnost nákladů povolená, všechny služby, které mají využití předplatného, budou zobrazovat náklady podle tarifů průběžných plateb. Využití rezervace se zobrazí s nulovými poplatky za skutečné a účtované náklady. Nákupy a nároky nejsou přidružené ke konkrétnímu předplatnému. Nákupy se tak nezobrazí v oboru předplatného.

Pokud chcete zobrazit náklady na tenanta zákazníka, otevřete Cost Management + fakturace a pak klikněte na fakturační účty. V seznamu fakturačních účtů klikněte na fakturační účet.

![Vybrat fakturační účet](./media/get-started-partners/select-billing-account.png)

V části **fakturace**klikněte na **předplatná Azure**a potom klikněte na zákazníka.

![Vybrat zákazníka předplatného Azure](./media/get-started-partners/subscriptions-select-customer.png)

Klikněte na **Analýza nákladů** a začněte kontrolovat náklady.
Analýza nákladů, rozpočty a výstrahy jsou nyní k dispozici pro rozsahy a skupiny prostředků v rámci nákladů na základě sazby.

![Zobrazení analýzy nákladů jako zákazníka ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Amortizacovaná zobrazení a skutečné náklady na rezervované instance v oborech RBAC zobrazují nulové poplatky. Náklady na rezervované instance se zobrazují pouze v oborech fakturace, kde byly nákupy provedeny.

## <a name="analyze-costs-in-cost-analysis"></a>Analýza nákladů při analýze nákladů

Partneři můžou prozkoumat a analyzovat náklady na analýzu nákladů napříč zákazníky pro konkrétního zákazníka nebo fakturu. Funkce Filter a Group by vám umožňují analyzovat náklady podle více polí, včetně:

| **Pole** | **Popis** | **Ekvivalentní sloupec v partnerském centru** |
| --- | --- | --- |
| PartnerTenantID | Identifikátor tenanta Azure Active Directory partnera | Partner Azure Active Directory TenantID volal jako ID partnera. Ve formátu identifikátoru GUID. |
| PartnerName | Název tenanta Azure Active Directory partnera | Název partnera |
| CustomerTenantID | Identifikátor tenanta Azure Active Directory předplatného zákazníka | ID organizace zákazníka. Například zákazník Azure Active Directory TenantID. |
| CustomerName | Název tenanta Azure Active Directory obsahujícího předplatné zákazníka | Název organizace zákazníka, jak je uvedeno v partnerském centru. Důležité pro sjednocení faktury s informacemi o systému. |
| ResellerMPNID | MPNID pro prodejce, který je přidružený k předplatnému | ID MPN prodejce záznamu pro předplatné. Není k dispozici pro aktuální aktivitu. |
| subscription ID | Jedinečný identifikátor vygenerovaný společností Microsoft pro předplatné Azure | Nevztahuje se |
| subscriptionName | Název předplatného Azure | Nevztahuje se |
| billingProfileID | Identifikátor fakturačního profilu. Seskupuje náklady v rámci faktur v jedné fakturační měně mezi zákazníky. | ID fakturační skupiny MCAPI partnera Používá se v požadavcích rozhraní API, ale nejsou zahrnuté v odpovědích. |
| invoiceID | ID faktury na faktuře, kde se konkrétní transakce zobrazí | Číslo faktury, kde se zobrazí zadaná transakce. |
| resourceGroup | Název skupiny prostředků Azure. Používá se ke správě životního cyklu prostředků. | Název skupiny prostředků. |
| partnerEarnedCreditRate | Zvýhodněná sazba se používá v případě, že je na základě připojení partnera pro správu partnerského serveru k dispozici kredit (PEC). | Míra získaného kreditu pro partnery (PEC). Například 0% nebo 15%. |
| partnerEarnedCreditApplied | Označuje, zda byl použit partnerský vydaný kredit. | Nevztahuje se |

V zobrazení [Analýza nákladů](quick-acm-cost-analysis.md) můžete také [ukládat zobrazení](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) a exportovat data do souborů [CSV a PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) .

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Zobrazení nákladů na prostředky PEC (Partnerd Credit)

V Azure Cost Management můžou partneři pomocí analýzy nákladů zobrazit náklady, které obdržely výhody PEC.

V Azure Portal se přihlaste k partnerskému tenantovi a vyberte **cost management + fakturace**. V části **cost management**klikněte na **Analýza nákladů**.

V zobrazení analýza nákladů se zobrazí náklady na fakturační účet partnera. Vyberte **Rozsah** podle potřeby pro partnera, konkrétního zákazníka nebo Fakturační profil, který bude odsouhlasit faktury.

V prstencovém grafu klikněte na rozevírací seznam a vyberte **PartnerEarnedCreditApplied** , abyste přešli na náklady na řadič PEC.

![Příklad ukazující, jak zobrazit kredit získaný partnerem](./media/get-started-partners/cost-analysis-pec1.png)

Pokud má vlastnost **PartnerEarnedCreditApplied** _hodnotu true_, mají přidružené náklady nárok na přístup správce k partnerům.

Pokud má vlastnost **PartnerEarnedCreditApplied** _hodnotu false_, přidružené náklady nevyhověly požadovanému nároku na kredit. Nebo zakoupená služba nemá nárok na kredit získaný prostřednictvím partnerů.

Data o využití služby normálně zabírají v Cost Management 8-24 hodin. Další informace najdete v tématu [Frekvence aktualizace dat využití se liší](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Kredity PEC se zobrazí během 48 hodin od doby přístupu v Azure Cost Management.


Pomocí možností **Group by** můžete také seskupit a filtrovat podle vlastnosti **PartnerEarnedCreditApplied** . Pomocí možností můžete prošetřit náklady, které mají a nemají PEC.

![Seskupit nebo filtrovat podle partnera – realizovaného kreditu](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Rozhraní REST API pro Cost Management

Partneři, nepřímá poskytovatelé a zákazníci můžou používat rozhraní Cost Management API popsaná v následujících částech pro běžné úlohy.

### <a name="azure-cost-management-apis-for-partners"></a>Rozhraní API pro Azure Cost Management pro partnery

Partneři a uživatelé s přístupem k oborům fakturace v partnerském tenantovi můžou používat následující rozhraní API.

#### <a name="to-get-a-list-of-billing-accounts"></a>Získání seznamu fakturačních účtů

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Získání seznamu zákazníků

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Získání seznamu předplatných

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Vytvoření nového předplatného

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Získání nebo stažení využití služeb Azure

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Získání seznamu profilů fakturace

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Získání nebo stažení ceníku pro spotřebované služby Azure

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Chcete-li získat náklady na zákazníky za poslední dva měsíce, seřazené podle měsíců

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Chcete-li získat náklady na předplatné Azure za poslední dva měsíce, seřazené podle měsíců

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Získání denních nákladů na aktuální měsíc

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Získání zásad pro zákazníky, kteří si chtějí zobrazit náklady

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Nastavení zásad pro zákazníky, kteří budou zobrazovat náklady

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>Rozhraní API pro Azure Cost Management nepřímým zprostředkovatelům

Nepřímá zprostředkovatelé s přístupem k rozsahům RBAC v tenantovi zákazníka můžou používat následující rozhraní API. Chcete-li začít, přihlaste se jako uživatel nebo pomocí instančního objektu.

#### <a name="to-get-the-billing-account-information"></a>Získání informací o fakturačním účtu

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Získání seznamu zákazníků

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Získání seznamu prodejců přidružených k danému zákazníkovi

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Získání seznamu předplatných s informacemi o prodejci

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Vytvoření předplatného

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>Rozhraní API pro Azure Cost Management pro zákazníky

Pro přístup k rozhraním API používají zákazníci následující informace. Pokud chcete začít, přihlaste se jako uživatel.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Získání nebo stažení informací o využití spotřeby Azure pomocí maloobchodních sazeb

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Další kroky
- [Zahájení analýzy nákladů](quick-acm-cost-analysis.md) v cost management
- [Vytváření a Správa rozpočtů](tutorial-acm-create-budgets.md) v cost management
