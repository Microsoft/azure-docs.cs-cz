---
title: Začínáme s Azure Cost Management pro partnery
description: V tomto článku se dozvíte, jak partneři používají funkce Azure Cost Management a jak jim umožňují Cost Management přístup pro své zákazníky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: a320a446bf6a2ff5d5d923961b2614970ffa70f9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988406"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Začínáme s Azure Cost Management pro partnery

Azure Cost Management je nativně k dispozici pro partnery, kteří zavedli registraci svých zákazníků do smlouvy o zákaznících Microsoftu a [zakoupili jste plán Azure](/partner-center/purchase-azure-plan). Tento článek vysvětluje, jak partneři používají funkce [Azure cost management](../index.yml) k zobrazení nákladů na předplatná v plánu Azure. Také popisuje, jak partneři umožňují Cost Management přístupu pro své zákazníky. Zákazníci můžou používat funkce Cost Management, když je povolí jejich partner CSP.

Partneři CSP používají Cost Management k těmto akcím:

- Pochopení fakturovaných nákladů a přidružení nákladů k zákazníkovi, předplatným, skupinám prostředků a službám.
- Získejte intuitivní pohled na náklady na Azure v rámci [analýzy nákladů](quick-acm-cost-analysis.md) díky funkcím, které vám umožní analyzovat náklady podle zákazníka, předplatného, skupiny prostředků, prostředku, měřiče, služby a mnoha dalších dimenzí.
- Prohlédněte si náklady na prostředky, které mají pro účely analýzy nákladů použité kredity pro partnery (PEC).
- Nastavení oznámení a automatizace pomocí programových [rozpočtů](tutorial-acm-create-budgets.md) a výstrah v případě, že náklady přesahují rozpočty
- Povolte zásady Azure Resource Manager, které poskytují zákazníkům přístup k datům Cost Management. Zákazníci si pak můžou zobrazit data nákladů na spotřebu pro svá předplatná podle [tarifů](https://azure.microsoft.com/pricing/calculator/)průběžných plateb.
- Exportujte své náklady a data o využití do objektu BLOB úložiště s předplatným s průběžnými platbami.

Tady je příklad znázorňující náklady pro všechny zákazníky.
![Příklad znázorňující náklady na všechny zákazníky](./media/get-started-partners/customer-costs1.png)

Tady je příklad, který ukazuje náklady na jednoho zákazníka.
![Příklad znázorňující náklady na jednoho zákazníka](./media/get-started-partners/customer-costs2.png)

Všechny funkce, které jsou dostupné v Azure Cost Management, jsou dostupné taky s rozhraními REST API. Použijte rozhraní API k automatizaci úloh správy nákladů.

## <a name="prerequisites"></a>Požadavky

Azure Cost Management je jako partner nativně dostupný jenom pro předplatná, která jsou v plánu Azure.

Pokud chcete povolit Azure Cost Management v Azure Portal, musíte potvrdit přijetí zákaznických smluv Microsoftu (jménem zákazníka) a převede zákazníka na plán Azure. V Azure Cost Management jsou k dispozici pouze náklady na předplatná, která jsou převedena do plánu Azure.

Azure Cost Management vyžaduje přístup pro čtení k fakturačnímu účtu nebo předplatnému.

Další informace o povolení a přiřazení přístupu k Azure Cost Management pro fakturační účet najdete v tématu [přiřazení rolí uživatelů a oprávnění](/partner-center/permissions-overview). Role **globálního správce** a **agenta správce** mohou spravovat náklady na fakturační účet.

Pro přístup k Azure Cost Management v oboru předplatného může každý uživatel s přístupem RBAC k předplatnému zobrazit náklady v maloobchodních sazbách (průběžné platby). Nicméně zásada viditelnosti nákladů pro tenanta zákazníka musí být povolená. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md).


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
- Měření
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

V Azure Portal se přihlaste k partnerskému tenantovi a vyberte **cost management + fakturace**. Vyberte fakturační účet a pak vyberte **zákazníci**. Seznam zákazníků je přidružený k fakturačnímu účtu.

V seznamu zákazníků vyberte zákazníka, kterému chcete dovolit zobrazit náklady.

![Vybrat zákazníky v Cost Management](./media/get-started-partners/customer-list.png)

V části **Nastavení**vyberte **zásady**.

V této zásadě se účtují poplatky za **využití Azure** spojené s předplatnými pro vybraného zákazníka.
![zásada, která zákazníkům umožní zobrazovat poplatky za průběžné platby](./media/get-started-partners/cost-management-billing-policies.png)

Pokud je zásada nastavená na **ne**, Azure cost management není k dispozici pro uživatele předplatného přidruženého k zákazníkovi. Pokud není povolená partnerem, jsou zásady viditelnosti nákladů ve výchozím nastavení pro všechny uživatele předplatného zakázané.

Když je zásada nákladů nastavená na **Ano**, uživatelé předplatného přidružení k klientovi zákazníka uvidí poplatky za využití podle tarifů průběžných plateb.

Když je zásada viditelnost nákladů povolená, všechny služby, které mají využití předplatného, budou zobrazovat náklady podle tarifů průběžných plateb. Využití rezervace se zobrazí s nulovými poplatky za skutečné a účtované náklady. Nákupy a nároky nejsou přidružené ke konkrétnímu předplatnému. Nákupy se tak nezobrazí v oboru předplatného.

Pokud chcete zobrazit náklady na tenanta zákazníka, otevřete Cost Management + fakturace a pak vyberte fakturační účty. V seznamu fakturačních účtů vyberte fakturační účet.

![Vybrat fakturační účet](./media/get-started-partners/select-billing-account.png)

V části **fakturace**vyberte **předplatná Azure**a pak vyberte zákazníka.

![Vybrat zákazníka předplatného Azure](./media/get-started-partners/subscriptions-select-customer.png)

Vyberte **Analýza nákladů** a spusťte kontrolu nákladů.
Analýza nákladů, rozpočty a výstrahy jsou k dispozici pro obory a rozsahy RBAC u předplatného s průběžnými platbami podle aktuálního využití.

![Zobrazení analýzy nákladů jako zákazníka ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Amortizacovaná zobrazení a skutečné náklady na rezervované instance v oborech RBAC zobrazují nulové poplatky. Náklady na rezervované instance se zobrazují pouze v oborech fakturace, kde byly nákupy provedeny.

## <a name="analyze-costs-in-cost-analysis"></a>Analýza nákladů při analýze nákladů

Partneři s přístupem k oborům fakturace v partnerském tenantovi můžou prozkoumat a analyzovat fakturované náklady na analýzu nákladů napříč zákazníky pro konkrétního zákazníka nebo fakturu. V zobrazení [Analýza nákladů](quick-acm-cost-analysis.md) můžete také [ukládat zobrazení](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) a exportovat data do [souborů CSV a PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Uživatelé RBAC s přístupem k předplatnému v tenantovi zákazníka můžou také analyzovat maloobchodní náklady na předplatná v tenantovi zákazníka, ukládat zobrazení a exportovat data do souborů CSV a PNG.

Pomocí funkcí Filter a Group by v analýze nákladů můžete analyzovat náklady podle několika polí. Pole specifická pro partnera jsou uvedena v následující části.

## <a name="data-fields"></a>Datová pole

V souborech podrobností o využití a Cost Management rozhraní API se nacházejí následující datová pole. Pokud je k dispozici, zobrazí se informace o ekvivalentu partnerského centra. Pro následující Tučná pole můžou partneři pomocí funkce filtrovat a seskupit podle funkcí analýzy nákladů analyzovat náklady podle několika polí. Tučná pole se vztahují jenom na zákaznické smlouvy Microsoftu, které partneři podporuje.

| **Název pole** | **Popis** | **Ekvivalent partnerského centra** |
| --- | --- | --- |
| invoiceId | ID faktury zobrazené na faktuře pro konkrétní transakci | Číslo faktury, kde se transakce zobrazuje |
| previousInvoiceID | Odkaz na původní fakturu je refundace (záporné náklady). Vyplněno pouze v případě, že existuje refundace. | Nevztahuje se |
| billingAccountName | Název fakturačního účtu, který představuje partnera. Účtují se všechny náklady na zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. | Nevztahuje se |
| billingAccountID | Identifikátor fakturačního účtu, který představuje partnera. | ID kořenového adresáře partnerského obchodu MCAPI Používá se v žádosti, ale není součástí odpovědi.|
| billingProfileID | Identifikátor pro fakturační profil, který seskupuje náklady napříč fakturami v jedné fakturační měně mezi zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. | ID fakturační skupiny MCAPI partnera Používá se v žádosti, ale není součástí odpovědi. |
| billingProfileName | Název fakturačního profilu, který seskupuje náklady napříč fakturami v jedné fakturační měně mezi zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. | Nevztahuje se |
| invoiceSectionName | Název projektu, který se účtuje na faktuře. Neplatí pro smlouvy o zákaznících Microsoftu, které jsou zaregistrované partnery. | Nevztahuje se |
| invoiceSectionID | Identifikátor projektu, který se účtuje na faktuře Neplatí pro smlouvy o zákaznících Microsoftu, které jsou zaregistrované partnery. | Nevztahuje se |
| **CustomerTenantID** | Identifikátor tenanta Azure Active Directory předplatného zákazníka. | ID organizace zákazníka – Azure Active Directory TenantID zákazníka. |
| **CustomerName** | Název tenanta Azure Active Directory pro předplatné zákazníka | Název organizace zákazníka, jak je znázorněno v partnerském centru. Důležité pro sjednocení faktury s informacemi o systému. |
| **CustomerTenantDomainName** | Název domény pro klienta Azure Active Directory předplatného zákazníka. | Zákazník Azure Active Directory doména klienta. |
| **PartnerTenantID** | Identifikátor tenanta Azure Active Directory partnera. | Partner Azure Active Directory ID klienta označované jako ID partnera ve formátu identifikátoru GUID. |
| **PartnerName** | Název partnerského Azure Active Directory tenanta. | Název partnera. |
| **ResellerMPNID** | MPNID pro prodejce, který je přidružený k předplatnému. | ID MPN prodejce na záznamu pro předplatné Není k dispozici pro aktuální aktivitu. |
| costCenter | Nákladové středisko přidružené k předplatnému. | Nevztahuje se |
| billingPeriodStartDate | Počáteční datum fakturačního období, jak je znázorněno na faktuře. | Nevztahuje se |
| billingPeriodEndDate | Datum ukončení fakturačního období, jak je znázorněno na faktuře. | Nevztahuje se |
| servicePeriodStartDate | Počáteční datum období hodnocení, kdy se vyhodnotilo využití služby za poplatky. Ceny služeb Azure se určují pro období hodnocení. | ChargeStartDate v partnerském centru. Datum zahájení fakturačního cyklu s výjimkou, kdy prezentují data dříve neplatných latentních dat z předchozího fakturačního cyklu. Čas je vždy začátek dne, 0:00. |
| servicePeriodEndDate | Koncové datum období, kdy bylo využití služby vyhodnoceno za poplatek. Ceny služeb Azure se určují na základě období hodnocení. | Nevztahuje se |
| date | Pro data o spotřebě Azure zobrazuje datum využití jako hodnocené. V případě rezervované instance se zobrazí datum nákupu. Pro periodické poplatky a jednorázové poplatky, jako je například Marketplace a podpora, se zobrazuje datum nákupu. | Nevztahuje se |
| productID | Identifikátor produktu, který má za sebou účtováné poplatky podle využití nebo nákupu. Je to zřetězený klíč productID a SKuID, jak je znázorněno v partnerském centru. | ID produktu |
| product | Název produktu, který účtuje náklady podle spotřeby nebo nákupu, jak je vidět na faktuře. | Název produktu v katalogu. |
| serviceFamily | Zobrazuje rodinu služeb pro zakoupené nebo účtované produkty. Například úložiště nebo výpočetní prostředky. | Nevztahuje se |
| productOrderID | Identifikátor prostředku nebo plánu Azure, ke kterému předplatné patří Například plán Azure. | Nevztahuje se |
| productOrderName | Název plánu Azure, ke kterému předplatné patří Například plán Azure. | Nevztahuje se|
| consumedService | Spotřebované služby (starší taxonomie), které se používají ve starších podrobnostech použití EA. | Služba zobrazená v partnerském centru. Například Microsoft. Storage, Microsoft. COMPUTE a Microsoft. operationalinsights. |
| meterID | Měřený identifikátor pro měřenou spotřebu. | ID použitého měřiče. |
| meterName | Určuje název měřiče pro měřenou spotřebu. | Název spotřebovaného měřiče. |
| meterCategory | Identifikuje službu nejvyšší úrovně pro použití. | Služba nejvyšší úrovně pro použití. |
| meterSubCategory | Definuje typ nebo podkategorii služby Azure, které můžou mít vliv na tuto sazbu. | Typ služby Azure, který může mít vliv na sazbu.|
| meterRegion | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra. | Regionální umístění datového centra pro služby, pokud je to možné a naplněné. |
| subscription ID | Jedinečný identifikátor vygenerovaný společností Microsoft pro předplatné Azure. | Nevztahuje se |
| subscriptionName | Název předplatného Azure. | Nevztahuje se |
| Doba účinnosti | Zobrazí období platnosti nabídky. Například rezervované instance zobrazují 12 měsíců z ročního období rezervované instance. Pro jednorázové nákupy nebo opakované nákupy se termín zobrazuje jeden měsíc pro SaaS, Azure Marketplace a podporu. Nedá se použít pro spotřebu v Azure. | Nevztahuje se |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ vydavatele, který identifikuje vydavatele jako první stranu, prodejce třetích stran nebo agenturu třetích stran. | Nevztahuje se |
| partNumber | Číslo součásti pro nevyužité rezervované instance a Azure Marketplace služby. | Nevztahuje se |
| publisherName | Jméno vydavatele služby, včetně vydavatelů společnosti Microsoft nebo třetích stran. | Název vydavatele produktu|
| reservationId | Identifikátor pro nákup rezervované instance | Nevztahuje se |
| reservationName | Název rezervované instance. | Nevztahuje se |
| reservationOrderId | ČísloObjednávky pro rezervovanou instanci. | Nevztahuje se |
| frequency | Četnost platby pro rezervovanou instanci. | Nevztahuje se |
| resourceGroup | Název skupiny prostředků Azure, která se používá pro správu prostředků životního cyklu. | Název skupiny prostředků. |
| instanceID (nebo) ResourceID | Identifikátor instance prostředku | Zobrazuje se jako ResourceURI, který obsahuje vlastnosti kompletního prostředku. |
| resourceLocation | Název umístění prostředku. | Umístění prostředku |
| Umístění | Normalizované umístění prostředku. | Nevztahuje se |
| effectivePrice | Efektivní Jednotková cena služby v cenové měně. Jedinečné pro produkt, rodinu služeb, měřič a nabídku. Používá se s cenami v ceníku pro fakturační účet. V případě, že jsou k dispozici vrstvené ceny nebo zahrnuté množství, zobrazuje se za ni smíšená cena za spotřebu. | Jednotková cena po provedení úprav. |
| Množství | Nakoupené nebo spotřebované měřené množství. Množství měřiče použitého během fakturačního období. | Počet jednotek Zajistěte, aby během odsouhlasení odpovídaly informacím v systému fakturace. |
| unitOfMeasure | Určuje jednotku, ve které se služba účtuje. Například GB a hodiny. | Určuje jednotku, ve které se služba účtuje. Například GB, hodiny a 10, tisících. |
| pricingCurrency | Měna, která definuje jednotkovou cenu | Měna v ceníku|
| billingCurrency | Měna definující fakturované náklady. | Měna geografické oblasti zákazníka |
| chargeType | Definuje typ poplatků, který představuje náklady v Azure Cost Management jako nákup a refundace. | Typ poplatků nebo úprav. Není k dispozici pro aktuální aktivitu. |
| costinBillingCurrency | ExtendedCost nebo promísící náklady před zdaněním v účtované měně. | Nevztahuje se |
| costinPricingCurrency | ExtendedCost nebo smíšené náklady před zdaněním v cenové měně za účelem korelace s cenami. | Nevztahuje se |
| **costinUSD** | Odhadované ExtendedCost nebo smíšené náklady před zdaněním v USD. | Nevztahuje se |
| **paygCostInBillingCurrency** | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby ve fakturační měně. K dispozici pouze v oborech RBAC. | Nevztahuje se |
| **paygCostInUSD** | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby v USD. K dispozici pouze v oborech RBAC. | Nevztahuje se |
| exchangeRate | Směnný kurz, který se používá k převodu z cenové měny na fakturační měnu | V partnerském centru se označuje jako PCToBCExchangeRate. Měna cenové měny pro fakturaci směnného kurzu.|
| exchangeRateDate | Datum směnného kurzu, který se používá k převodu z cenové měny na fakturační měnu. | V partnerském centru se označuje jako PCToBCExchangeRateDat. Datum směnného kurzu pro fakturační měnu.|
| isAzureCreditEligible | Uvádí, zda náklady mají nárok na platby prostřednictvím kreditů Azure. | Nevztahuje se |
| serviceInfo1 | Starší verze pole, která zachycuje volitelná metadata specifická pro službu. | Interní metadata služby Azure. |
| serviceInfo2 | Starší verze pole, která zachycuje volitelná metadata specifická pro službu. | Informace o službě. Například typ obrázku pro virtuální počítač a název poskytovatele internetových služeb pro ExpressRoute.|
| additionalInfo | Metadata konkrétních služeb. Například typ image u virtuálního počítače. | Jakékoli další informace, které nejsou pokryté v jiných sloupcích. Metadata specifická pro službu. Například typ image u virtuálního počítače.|
| značek | Značka, kterou přiřadíte k měřiči. Pomocí značek můžete seskupovat záznamy fakturace. Pomocí značek můžete například distribuovat náklady podle oddělení, které používá měřič. | Značky přidané zákazníkem|
| **partnerEarnedCreditRate** | Pokud je na základě přístupu k partnerovi pro správu partnerského serveru k dispozici kredity, je sazba uplatněna. | Míra získaného kreditu pro partnery (PEC). Například 0% nebo 15%. |
| **partnerEarnedCreditApplied** | Označuje, zda byl použit partnerský kredit. | Nevztahuje se |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Zobrazení nákladů na prostředky PEC (Partnerd Credit)

V Azure Cost Management můžou partneři pomocí analýzy nákladů zobrazit náklady, které obdržely výhody PEC.

V Azure Portal se přihlaste k partnerskému tenantovi a vyberte **cost management + fakturace**. V části **cost management**vyberte **Analýza nákladů**.

V zobrazení analýza nákladů se zobrazí náklady na fakturační účet partnera. Vyberte **Rozsah** podle potřeby pro partnera, konkrétního zákazníka nebo Fakturační profil, který bude odsouhlasit faktury.

V prstencovém grafu vyberte rozevírací seznam a vyberte **PartnerEarnedCreditApplied** pro přechod k podrobnostem o nákladech pec.

![Příklad ukazující, jak zobrazit kredit získaný partnerem](./media/get-started-partners/cost-analysis-pec1.png)

Pokud má vlastnost **PartnerEarnedCreditApplied** _hodnotu true_, mají přidružené náklady nárok na přístup správce k partnerům.

Pokud má vlastnost **PartnerEarnedCreditApplied** _hodnotu false_, přidružené náklady nevyhověly požadovanému nároku na kredit. Nebo zakoupená služba nemá nárok na kredit získaný prostřednictvím partnerů.

Data o využití služby normálně zabírají v Cost Management 8-24 hodin. Další informace najdete v tématu [Frekvence aktualizace dat využití se liší](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Kredity PEC se zobrazí během 48 hodin od doby přístupu v Azure Cost Management.


Pomocí možností **Group by** můžete také seskupit a filtrovat podle vlastnosti **PartnerEarnedCreditApplied** . Pomocí možností můžete prošetřit náklady, které mají a nemají PEC.

![Seskupit nebo filtrovat podle partnera – realizovaného kreditu](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportovat nákladová data do Azure Storage

Partneři s přístupem k oborům fakturace v partnerském tenantovi můžou exportovat své náklady a data o využití do objektu blob Azure Storage. Objekt BLOB musí být v rámci předplatného partnerského tenanta, který není předplatném sdílené služby nebo předplatným zákazníka. Pokud chcete povolit export nákladů na data, doporučujeme, abyste v partnerském tenantovi nastavili nezávislé předplatné s průběžnými platbami, aby se mohla hostovat vyexportovaná nákladová data. Účet úložiště pro export se vytvoří v objektu blob Azure Storage hostovaným v rámci předplatného s průběžnými platbami. V závislosti na rozsahu, ve kterém partner vytvoří export, se přidružená data automaticky exportují do účtu úložiště.

Uživatelé s přístupem RBAC k předplatnému můžou také exportovat nákladová data do objektu BLOB služby Azure Storage hostovaného v jakémkoli předplatném v tenantovi zákazníka.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Vytvoření exportu v partnerském tenantovi nebo tenantovi zákazníka

V Azure Portal se přihlaste k tenantovi partnera nebo zákazníkovi a vyberte **cost management + fakturace**. Vyberte příslušný obor, například fakturační účet, a pak vyberte **Analýza nákladů**. Po načtení stránky vyberte **exportovat**. Vyberte **Zobrazit všechny exporty** v části naplánovat export.

Potom vyberte **Přidat** a zadejte název a vyberte typ exportu. Vyberte kartu **úložiště** a zadejte požadované informace.

Při vytváření exportu v partnerském tenantovi vyberte předplatné s průběžnými platbami v partnerském tenantovi. Vytvořte účet Azure Storage pomocí tohoto předplatného.

Pro uživatele RBAC v tenantovi zákazníka vyberte předplatné v tenantovi zákazníka. Vytvořte účet Azure Storage pomocí předplatného.

Zkontrolujte obsah a pak vyberte **vytvořit** a naplánujte export.

Pokud chcete ověřit data v seznamu exportu, vyberte název účtu úložiště. Na stránce účet úložiště vyberte **kontejnery** a pak vyberte kontejner. Přejděte do odpovídající složky a vyberte soubor CSV. Vyberte **Stáhnout** a získejte soubor CSV a otevřete ho. Export exportovaných dat se podobá datovým datům, který se podobá podrobnostem o využití Azure Portal.

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
