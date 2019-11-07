---
title: Začínáme s Azure Cost Management pro partnery
description: V tomto článku se dozvíte, jak partneři používají funkce Azure Cost Management a jak jim umožňují Cost Management přístup pro své zákazníky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 611b3e608d9b0de9423c861ec70e9fc2e7ad67d5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720755"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Začínáme s Azure Cost Management pro partnery

Azure Cost Management je nativně dostupná pro partnery, kteří se připojili k zákaznickým smlouvám Microsoftu. Tento článek vysvětluje, jak partneři používají funkce [Azure cost management](https://docs.microsoft.com/azure/cost-management/) . Také popisuje, jak partneři umožňují Cost Management přístupu pro své zákazníky. Zákazníci můžou používat funkce Cost Management, když je povolí jejich partner CSP.

Partneři CSP používají Cost Management k těmto akcím:

- Pochopení fakturovaných nákladů a přidružení nákladů k zákazníkovi, předplatným, skupinám prostředků a službám.
- Získejte intuitivní pohled na náklady na Azure v rámci [analýzy nákladů](quick-acm-cost-analysis.md) díky funkcím, které vám umožní analyzovat náklady podle zákazníka, předplatného, skupiny prostředků, prostředku, měřiče, služby a mnoha dalších dimenzí.
- Prohlédněte si náklady na prostředky, které mají pro účely analýzy nákladů použité kredity pro partnery (PEC).
- Nastavení oznámení a automatizace pomocí programových [rozpočtů](tutorial-acm-create-budgets.md) a výstrah v případě, že náklady přesahují rozpočty
- Povolte zásady Azure Resource Manager, které poskytují zákazníkům přístup k datům Cost Management. Zákazníci si pak můžou zobrazit data nákladů na spotřebu pro svá předplatná podle [tarifů](https://azure.microsoft.com/pricing/calculator/)průběžných plateb.

Tady je příklad znázorňující náklady pro všechny zákazníky.
![Příklad znázorňující náklady na všechny zákazníky](./media/get-started-partners/customer-costs1.png)

Tady je příklad, který ukazuje náklady na jednoho zákazníka.
![Příklad znázorňující náklady na jednoho zákazníka](./media/get-started-partners/customer-costs2.png)

Všechny funkce, které jsou dostupné v Azure Cost Management, jsou dostupné taky s rozhraními REST API. Použijte rozhraní API k automatizaci úloh správy nákladů.

## <a name="prerequisites"></a>Požadavky

Azure Cost Management vyžaduje přístup pro čtení k fakturačnímu účtu nebo předplatnému. Přístup je možné udělit na libovolné úrovni výše než vašich prostředků, od fakturačního účtu nebo skupiny pro správu až po jednotlivé skupiny prostředků, kde spravujete své aplikace. Další informace o povolení a přiřazení přístupu k Azure Cost Management pro fakturační účet najdete v tématu [přiřazení rolí uživatelů a oprávnění](/partner-center/permissions-overview). Role **globálního správce** a **agenta správce** mohou spravovat náklady na fakturační účet.

Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management používá obory

Obory jsou místo, kde můžete spravovat fakturační data, mít role specifické pro platby, zobrazovat faktury a provádět Obecné řízení účtů. Role fakturace a účtu se spravují odděleně od oborů používaných pro správu prostředků, které používají RBAC. Aby bylo možné jasně odlišit záměr samostatných oborů, včetně rozdílů v řízení přístupu, označují se jako obory fakturace a rozsahy RBAC v uvedeném pořadí.

Informace o oborech fakturace a oborech RBAC a o tom, jak Správa nákladů pracuje s obory, najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Správa nákladů pomocí oborů fakturace partnerských klientů

Po připojení zákazníků k zákaznické smlouvě Microsoftu jsou ve vašem tenantovi k dispozici následující _fakturační rozsahy_ . Pomocí oborů můžete spravovat náklady v Cost Management.

### <a name="billing-account-scope"></a>Rozsah fakturačního účtu

Rozsah fakturačního účtu můžete použít k zobrazení nákladů na předběžnou daň ve všech vašich zákaznících a fakturačních profilech. Náklady na fakturu se zobrazují jenom v zákaznických produktech Microsoftu v rámci smlouvy o zákaznících na bázi spotřeby. Náklady na fakturu se ale zobrazují pro zakoupené produkty pro zákazníky jak na základě smlouvy o zákaznících Microsoftu, tak i v nabídce CSP. V současné době je výchozí měnou pro zobrazení nákladů v oboru US dolary. Rozpočty nastavené pro obor jsou také v USD.

Bez ohledu na různé měny účtované zákazníky používají partneři obor fakturačního účtu k nastavení rozpočtů a správě nákladů v USD napříč zákazníky, předplatnými, prostředky a skupinami prostředků.

Partneři také filtruje náklady v konkrétní fakturační měně napříč zákazníky v zobrazení analýzy nákladů. Vyberte seznam **vlastní náklady** , abyste zobrazili náklady v podporovaných měnách fakturace zákazníků.

![Příklad zobrazující aktuální výběr nákladů pro měny](./media/get-started-partners/actual-cost-selector.png)

Pomocí [zobrazení amortizace nákladů](quick-acm-cost-analysis.md#customize-cost-views) v oborech fakturace můžete zobrazit náklady na amortizaci rezervovaných instancí napříč obdobím rezervace.

### <a name="billing-profile-scope"></a>Rozsah fakturačního profilu

Rozsah fakturačního profilu můžete použít k zobrazení nákladů na předběžnou daň ve fakturační měně všech vašich zákazníků pro všechny produkty a odběry, které jsou součástí faktury. Pomocí filtru **InvoiceID** můžete filtrovat náklady v profilu fakturace pro konkrétní fakturu. Filtr zobrazuje náklady na spotřebu a nákup produktu pro konkrétní fakturu. Můžete také filtrovat náklady na konkrétního zákazníka na faktuře, abyste viděli náklady před zdaněním.

Po připojení zákazníků k zákaznickým smlouvám Microsoftu obdržíte fakturu, která zahrnuje všechny poplatky za všechny produkty (spotřeba, nákupy a nároky) pro tyto zákazníky na zákaznickou smlouvu Microsoftu. Při fakturaci ve stejné měně zahrnují tyto faktury také poplatky za nárok a zakoupené produkty, jako je SaaS, Azure Marketplace a rezervace pro zákazníky, kteří jsou stále v nabídce CSP.

Aby se vám pomohly sjednotit poplatky vůči faktuře s zákazníkem, rozsah fakturačních profilů vám umožní zobrazit všechny náklady, které budou pro zákazníky Fakturovatelné. Podobně jako u faktury se v oboru zobrazuje náklady pro každého zákazníka v nové smlouvě o zákaznících Microsoftu. Obor také uvádí všechny poplatky za produkty nároků na zákazníky v aktuální nabídce CSP.

Jedinými platnými obory jsou účetní profil a obory fakturačního účtu, které obsahují poplatky za oprávnění a nákup produktů, jako jsou Azure Marketplace a nákupy rezervací.

Fakturační profily definují odběry, které jsou zahrnuty na faktuře. Fakturační profily představují funkční ekvivalent registrace smlouvy Enterprise. Fakturační profil je obor, ve kterém jsou faktury vygenerovány.

V současné době je fakturační měna zákazníka výchozí měnou při prohlížení nákladů v rozsahu fakturačního profilu. Rozpočty nastavené v oboru fakturačního profilu jsou ve fakturační měně.

Partneři můžou pomocí oboru sjednotit faktury. A používají obor k nastavení rozpočtů ve fakturační měně pro následující položky:

- Konkrétní filtrovaná faktura
- Zákazník
- Předplatné
- Skupina prostředků
- Prostředek
- Služba Azure
- Měřič
- ResellerMPNID

### <a name="customer-scope"></a>Obor zákazníka

Partneři využívají obor ke správě nákladů spojených se zákazníky, kteří jsou připojení ke Smlouvě o zákaznících Microsoftu. Obor umožňuje partnerům zobrazovat náklady na předběžnou daň pro konkrétního zákazníka. Můžete také filtrovat náklady před zdaněním u určitého předplatného, skupiny prostředků nebo prostředku.

Obor zákazníka neobsahuje zákazníky, kteří patří do aktuální nabídky CSP. Obor obsahuje jenom zákazníky, kteří mají zákaznickou smlouvu Microsoftu. Náklady na nárok, ne využití Azure, pro stávající zákazníky nabídky CSP jsou k dispozici na fakturačním účtu a v oborech profilů fakturačních, když použijete filtr zákazníka.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Přístup partnerů k oborům fakturace v Cost Management

Jenom uživatelé s rolemi **globální správce** a **Agent pro správu** můžou spravovat a zobrazovat náklady na fakturační účty, profily fakturace a zákazníky přímo v tenantovi Azure partnera. Další informace o rolích partnerského centra najdete v tématu [přiřazení rolí uživatelů a oprávnění](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Povolit správu nákladů v tenantovi zákazníka

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
![zásada, která zákazníkům umožní zobrazovat poplatky za průběžné platby](./media/get-started-partners/cost-management-billing-policies.png)

Pokud je zásada nastavená na **ne**, Azure cost management není k dispozici pro uživatele předplatného přidruženého k zákazníkovi. Pokud není povolená partnerem, jsou zásady viditelnosti nákladů ve výchozím nastavení pro všechny uživatele předplatného zakázané.

Když je zásada nákladů nastavená na **Ano**, uživatelé předplatného přidružení k klientovi zákazníka uvidí poplatky za využití podle tarifů průběžných plateb.

Když je zásada viditelnost nákladů povolená, všechny služby, které mají využití předplatného, budou zobrazovat náklady podle tarifů průběžných plateb. Využití rezervace se zobrazí s nulovými poplatky za skutečné a účtované náklady. Nákupy a nároky nejsou přidružené ke konkrétnímu předplatnému. Nákupy se tak nezobrazí v oboru předplatného.

Pokud chcete zobrazit náklady na tenanta zákazníka, otevřete Cost Management + fakturace a pak klikněte na fakturační účty. V seznamu fakturačních účtů klikněte na fakturační účet.

![Vybrat fakturační účet](./media/get-started-partners/select-billing-account.png)

V části **fakturace**klikněte na **předplatná Azure**a potom klikněte na zákazníka.

![Vybrat zákazníka předplatného Azure](./media/get-started-partners/subscriptions-select-customer.png)

Klikněte na **Analýza nákladů** a začněte kontrolovat náklady.
Analýza nákladů, rozpočty a výstrahy jsou k dispozici pro obory a rozsahy RBAC u předplatného s průběžnými platbami podle aktuálního využití.

![Zobrazení analýzy nákladů jako zákazníka ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Amortizacovaná zobrazení a skutečné náklady na rezervované instance v oborech RBAC zobrazují nulové poplatky. Náklady na rezervované instance se zobrazují pouze v oborech fakturace, kde byly nákupy provedeny.

## <a name="analyze-costs-in-cost-analysis"></a>Analýza nákladů při analýze nákladů

Partneři můžou prozkoumat a analyzovat náklady na analýzu nákladů napříč zákazníky pro konkrétního zákazníka nebo fakturu.

Následující pole jsou k dispozici v souborech s podrobnostmi o využití a Cost Management rozhraní API. Pomocí funkcí Filter a Group by v analýze nákladů můžete analyzovat náklady podle několika polí. Úplný seznam polí najdete v tématu [cost management datových polí](understand-cost-mgt-data.md#cost-management-data-fields).

| Název pole | Popis |
| --- | --- |
| CustomerTenantID | Identifikátor tenanta Azure Active Directory předplatného zákazníka&#39;s. |
| CustomerName | Název tenanta Azure Active Directory pro předplatné zákazníka&#39;s. |
| CustomerTenantDomainName | Název domény pro klienta Azure Active Directory předplatného zákazníka&#39;s. |
| PartnerTenantID | Identifikátor pro tenanta Azure Active Directory&#39;partnera s. |
| PartnerName | Název partnerského Azure Active Directory tenanta. |
| ResellerMPNID | MPNID pro prodejce, který je přidružený k předplatnému. |
| costinUSD | Odhadované ExtendedCost nebo smíšené náklady před zdaněním v USD. |
| paygCostInBillingCurrency | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby ve fakturační měně. K dispozici pouze v oborech RBAC. |
| paygCostInUSD | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby v USD. K dispozici pouze v oborech RBAC. |
| partnerEarnedCreditRate | Pokud je na základě přístupu k partnerovi pro správu partnerského serveru k dispozici kredity, je sazba uplatněna. |
| partnerEarnedCreditApplied | Označuje, zda byl použit partnerský kredit. |

V zobrazení [Analýza nákladů](quick-acm-cost-analysis.md) můžete také [ukládat zobrazení](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) a exportovat data do [souborů CSV a PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

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

Partneři a zákazníci můžou používat rozhraní API pro Cost Management, která jsou popsaná v následujících částech, pro běžné úlohy.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management rozhraní API – přímé a nepřímo poskytovatelé

Partneři s přístupem k oborům fakturace v partnerském tenantovi můžou pomocí následujících rozhraní API zobrazit fakturované náklady.

Rozhraní API v oboru předplatného můžou být volána partnerem bez ohledu na nákladové zásady, pokud mají přístup k předplatnému. Jiní uživatelé, kteří mají přístup k předplatnému, jako je zákazník nebo prodejce, můžou volat rozhraní API až poté, co partner povolí zásady nákladů pro tenanta zákazníka.


#### <a name="to-get-a-list-of-billing-accounts"></a>Získání seznamu fakturačních účtů

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Získání seznamu zákazníků

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Získání seznamu předplatných

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Získání seznamu faktur po určitou dobu

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Volání rozhraní API vrací pole faktur, které má prvky podobné následujícímu kódu JSON.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Použijte předchozí hodnotu pole vráceného ID a nahraďte ji v následujícím příkladu jako obor pro dotaz na podrobnosti o využití.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

V příkladu se vrátí záznamy o využití spojené s konkrétní fakturou.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Získání zásad pro zákazníky, kteří si chtějí zobrazit náklady

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Nastavení zásad pro zákazníky, kteří budou zobrazovat náklady

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Získání využití služeb Azure pro fakturační účet

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Stažení využívání služeb Azure na zákazníka

Následující volání Get je asynchronní operace.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Zavoláním identifikátoru URI `Location` vráceného v odpovědi na kontrolu stavu operace. Po *dokončení*stavu obsahuje vlastnost `downloadUrl` odkaz, který můžete použít ke stažení vygenerované sestavy.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Získání nebo stažení ceníku pro spotřebované služby Azure

Nejprve použijte následující příspěvek.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Pak zavolejte hodnotu vlastnosti asynchronní operace. Příklad:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Předchozí volání Get vrátí odkaz ke stažení obsahující ceník.


#### <a name="to-get-aggregated-costs"></a>Získání agregovaných nákladů

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Vytvoření rozpočtu pro partnera

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Vytvoření rozpočtu pro zákazníka

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Odstranit rozpočet

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Další kroky
- [Zahájení analýzy nákladů](quick-acm-cost-analysis.md) v cost management
- [Vytváření a Správa rozpočtů](tutorial-acm-create-budgets.md) v cost management
