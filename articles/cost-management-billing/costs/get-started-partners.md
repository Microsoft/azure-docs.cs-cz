---
title: Začínáme se službou Azure Cost Management pro partnery
description: V tomto článku je vysvětlené, jak partneři používají funkce služby Azure Cost Management a jak k ní umožní přístup svým zákazníkům.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 8a04cb2c590bc4f0104a93ac7c7c2cf7b71a4662
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602178"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Začínáme se službou Azure Cost Management pro partnery

Azure Cost Management mají přirozeně k dispozici přímí partneři, jejichž zákazníci přešli na Smlouvu se zákazníkem Microsoftu a mají [koupený plán Azure](/partner-center/purchase-azure-plan). V tomto článku je vysvětlené, jak můžou partneři používat funkce služby [Azure Cost Management](../index.yml) k zobrazení nákladů na předplatné v plánu Azure. V článku je také popsané, jak můžou partneři umožnit přístup ke službě Cost Management svým zákazníkům za maloobchodní ceny.

V případě přímých partnerů a nepřímých poskytovatelů mají globální správci a agenti správy přístup ke službě Cost Management v tenantovi partnera a spravovat náklady na úrovni s fakturovaných cen.

Prodejci a zákazníci mají přístup ke službě Cost Management v tenantovi zákazníka a vidí náklady pro předplatná, kde se náklady vypočítávají a zobrazují v maloobchodních sazbách. K zobrazení nákladů ale musí mít přístup Azure RBAC k předplatnému v tenantovi zákazníka. Pro tenanta zákazníka je potřeba povolit zásadu viditelnosti nákladů.

Zákazníci můžou používat funkce služby Cost Management, pokud jim ji zpřístupní jejich partner CSP.

Jak partneři CSP používají Cost Management:

- K pochopení fakturovaných nákladů a jejich přidružení k zákazníkovi, předplatným, skupinám prostředků a službám.
- K získání intuitivního přehledu o nákladech Azure v [analýze nákladů](quick-acm-cost-analysis.md) díky funkcím, které analyzují náklady podle zákazníka, předplatného, skupiny prostředků, prostředku, měřiče, služby a dalších různých dimenzí.
- K zobrazení nákladů na prostředek, u kterých je v analýze nákladů použitý kredit získaný partnerem (PEC).
- K nastavení oznámení a automatizace s použitím programových [rozpočtů](tutorial-acm-create-budgets.md) a výstrah v případě, že rozpočty překročí náklady.
- K povolení zásady Azure Resource Manageru, která zákazníkům umožní přístup k datům služby Cost Management. Zákazníci si pak můžou ve svých předplatných prohlížet data o nákladech spotřebovaných podle [tarifů s průběžnými platbami](https://azure.microsoft.com/pricing/calculator/).
- K exportu svých dat o nákladech a využití do úložiště objektů blob s předplatným založeným na průběžných platbách.

V tomto příkladu se zobrazují náklady za všechny zákazníky.

[![Příklad nákladů za všechny zákazníky](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

V tomto příkladu se zobrazují náklady jednoho zákazníka.

[![Příklad nákladů pro jednoho zákazníka](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Všechny funkce, které jsou dostupné ve službě Azure Cost Management, jsou také k dispozici v rozhraních REST API. Tato rozhraní API můžete použít k automatizaci úloh při řízení nákladů.

## <a name="prerequisites"></a>Požadavky

Pro partnery je služba Azure Cost Management nativně dostupná jenom u předplatných, která patří k plánu Azure.

Pokud chcete na webu Azure Portal povolit Azure Cost Management, musíte mít od zákazníka potvrzenou smlouvu se zákazníkem Microsoftu (jménem zákazníka) a musíte zákazníka převést na plán Azure. Ve službě Azure Cost Management jsou k dispozici jenom náklady na předplatná, která jsou převedená na plán Azure.

Azure Cost Management vyžaduje přístup pro čtení fakturačního účtu nebo předplatného.

Další informace o tom, jak službě Azure Cost Management povolit a přiřadit přístup k fakturačnímu účtu, najdete v tématu o [přiřazení uživatelských rolí a oprávnění](/partner-center/permissions-overview). Ve fakturačním účtu smí náklady spravovat role **globálního správce** a **agenta správy**.

Přístup ke službě Azure Cost Management v rozsahu předplatného může mít každý uživatel, který má k předplatnému přístup Azure RBAC. Takový uživatel může prohlížet náklady odpovídající maloobchodním tarifům (průběžné platby). Pro tenanty zákazníků je ale potřeba povolit [zásadu viditelnosti nákladů](#enable-the-policy-to-view-azure-usage-charges). Kompletní seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md).

## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management používá rozsahy

V rozsazích můžete spravovat fakturační data, mít zvláštní role pro platby, zobrazovat faktury a obecně spravovat účet. Role pro fakturaci a účet se spravují nezávisle na rozsazích používaných ke správě prostředků, které používají Azure RBAC. Abychom mohli jasně rozlišit účel těchto oddělených rozsahů, včetně rozdílů v řízení přístupu, označují se jako rozsahy fakturace a rozsahy Azure RBAC.

Pokud chcete porozumět rozsahům fakturace a rozsahům Azure RBAC a tomu, jak je používat k řízení nákladů, přečtete si téma [Vysvětlení a práce s rozsahy](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Správa nákladů s rozsahy fakturace partnerského tenanta

Jakmile vaši zákazníci uzavřou smlouvu se zákazníkem Microsoftu, budete mít ve svém tenantovi k dispozici následující _rozsahy fakturace_. Tyto rozsahy můžete použít ke správě nákladů ve službě Cost Management.

### <a name="billing-account-scope"></a>Rozsah fakturačního účtu

Rozsah fakturačního účtu můžete použít k zobrazení nákladů před zdaněním u všech svých zákazníků a fakturačních profilů. Fakturované náklady se zobrazují jenom u produktů spotřebovávaných zákazníkem podle smlouvy se zákazníkem Microsoftu. Fakturované náklady se ale zobrazují jak pro zakoupené produkty zákazníků s uzavřenou smlouvou se zákazníkem Microsoftu nebo s nabídkou CSP. V současnosti se k zobrazení nákladů v daném rozsahu jako výchozí měna používá americký dolar. Rozpočty, které jsou nastavené pro rozsah, jsou také v USD.

Bez ohledu na různé měny, ve kterých se používají při fakturaci, používají partneři rozsah fakturačního účtu k nastavení rozpočtů a správě nákladů v USD pro své zákazníky, předplatná, prostředky a skupiny prostředků.

V zobrazení analýzy nákladů partneři také můžou filtrovat náklady podle určité fakturační měny u zákazníků. Pokud chcete zobrazit náklady v podporovaných měnách, ve kterých se fakturuje, vyberte seznam **skutečných nákladů**.

![Příklad znázorňuje výběr měn u skutečných nákladů](./media/get-started-partners/actual-cost-selector.png)

Zobrazení [amortizovaných nákladů](quick-acm-cost-analysis.md#customize-cost-views) můžete v rozsazích fakturace použít k zobrazení rezervované instance amortizovaných nákladů v rezervačním období.

### <a name="billing-profile-scope"></a>Rozsah fakturačního profilu

Rozsah fakturačního profilu použijte k zobrazení nákladů před zdaněním ve fakturační měně za všechny své zákazníky, produkty a předplatná uvedená na faktuře. K filtrování nákladů ve fakturačním profilu pro určitou fakturu použijte filtr **InvoiceID** (IDfaktury). Filtr zobrazuje náklady na spotřebu a nákup produktu k určité faktuře. Náklady také můžete filtrovat za určitého zákazníka na faktuře, pokud chcete zobrazit náklady před zdaněním.

Jakmile zákazníky přimějete k uzavření smlouvy se zákazníkem Microsoftu, přijde vám faktura, ve které budou všechny poplatky za všechny produkty (spotřeba, nákupy a nároky) těchto zákazníků s uzavřenou smlouvou se zákazníkem Microsoftu. Pokud se fakturuje ve stejné měně, obsahují tyto faktury také poplatky za nárokované a zakoupené produkty, jako je SaaS, Azure Marketplace a rezervace zákazníků, kteří ještě stále využívají nabídku klasickou nabídku CSP (nikoli plán Azure).

Aby bylo možné odsouhlasit poplatky s fakturou zákazníka, umožňuje rozsah fakturačního profilu zobrazit všechny náklady kumulované na faktuře za vaše zákazníky. Podobně jako na faktuře jsou i v rozsahu zobrazené náklady za každého zákazníka s novou smlouvou se zákazníkem Microsoftu. Rozsah také zobrazuje všechny poplatky za nárokované produkty zákazníka, které zůstávají aktuální v nabídce CSP.

Rozsahy fakturačního profilu a fakturačního účtu jsou jediné platné rozsahy, ve kterých se zobrazují poplatky za nárokované a zakoupené produkty, například Azure Marketplace nebo rezervační nákupy.

Fakturační profily definují předplatná, která jsou uvedená na faktuře. Fakturační profily funkčně odpovídají registraci smlouvy Enterprise. Fakturační profil je rozsah, za který se generují faktury.

V současnosti se k zobrazení nákladů v rozsahu fakturačního profilu jako výchozí měna používá fakturační měna. Rozpočty, které jsou nastavené v rozsahu fakturačního profilu, jsou ve fakturační měně.

Partneři můžou tento rozsah použít k odsouhlasení faktur. Rozsah také používají k nastavení rozpočtů ve fakturační měně pro následující položky:

- Vyfiltrovaná určitá faktura
- Zákazník
- Předplatné
- Skupina prostředků
- Prostředek
- Služba Azure
- Měřič
- ResellerMPNID

### <a name="customer-scope"></a>Rozsah zákazníka

Partneři používají tento rozsah ke správě nákladů přidružených k zákazníkům, kteří uzavřeli smlouvu se zákazníkem Microsoftu. Rozsah umožňuje partnerům zobrazit náklady před zdaněním za určitého zákazníka ve fakturační měně. Náklady před zdaněním také můžete filtrovat podle určitého předplatného, skupiny prostředků nebo prostředku.

Rozsah zákazníka nezahrnuje zákazníky, kteří využívají aktuální nabídku CSP. Rozsah zahrnuje jenom zákazníky, kteří mají smlouvu se zákazníkem Microsoftu. Náklady na nároky, nikoli na využití Azure, pro aktuální zákazníky s nabídkou CSP, jsou k dispozici v rozsazích fakturačního účtu a fakturačního profilu, když použijete filtr zákazníka. Rozpočty, které jsou nastavené v tomto rozsahu, jsou ve fakturační měně.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Přístup partnerů k rozsahům fakturace ve službě Cost Management

Spravovat a zobrazovat náklady za fakturační účty, fakturační profily a zákazníky přímo v partnerském tenantovi Azure můžou jenom uživatelé s oprávněním **globálního správce** nebo **agenta správy**. Další informace o rolích Partnerského centra najdete v tématu [Přiřazení uživatelských rolí a oprávnění](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Povolení správy nákladů pro předplatná tenantů zákazníků

Partneři můžou zákazníkům umožnit přístup ke službě Cost Management, až když podepíšou smlouvu se zákazníkem Microsoftu. Partneři potom mohou povolit zásadu, která zákazníkům umožní zobrazovat náklady na využití služeb Azure vypočítané podle maloobchodních tarifů s průběžnými platbami. Náklady se zobrazují ve fakturační měně zákazníka. Jsou to náklady na využití v rozsahu spotřebovaného předplatného Azure RBAC a spotřebovaných rozsahů skupin prostředků.

Pokud partner povolí zásadu viditelnosti nákladů, může kterýkoli uživatel, který má v Azure Resource Manageru přístup k předplatnému, spravovat a analyzovat náklady podle tarifů s průběžnými platbami. V podstatě to znamená, že prodejci a zákazníci, kteří mají příslušný přístup Azure RBAC k předplatným Azure, uvidí náklady.

Pokud globální správci a agenti správy mají přístup k předplatnému a skupině prostředků, mohou zobrazovat náklady na předplatná bez ohledu na zásady.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Povolení zásady zobrazovat poplatky za použití Azure

Abyste mohli tyto zásady zobrazit a aktualizovat, musíte být členem skupiny **agentů pro správu**. Pokud chcete povolit zásady, které zákazníkům umožňují zobrazovat poplatky za používání Azure, použijte následující informace.

Na webu Azure Portal se přihlaste k *partnerskému tenantovi* a vyberte **Správa nákladů a fakturace**. V oblasti Obor fakturace vyberte příslušný rozsah fakturace a potom vyberte **Zákazníci**. Seznam zákazníků se vztahuje k fakturačnímu účtu. *Pokud jste se omylem přihlásili k zákaznickému tenantovi, seznam **Zákazníci** se nezobrazí.*

V seznamu zákazníků vyberte zákazníka, kterému chcete povolit prohlížet náklady.

[![Výběr zákazníků ve službě Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

V části **Nastavení** vyberte **Zásady**.

Aktuální zásada viditelnosti nákladů se zobrazuje pro poplatky za **využití Azure** spojené s předplatnými vybraného zákazníka.
![Zásada umožňující zákazníkům prohlížet poplatky při průběžných platbách](./media/get-started-partners/cost-management-billing-policies.png)

Pokud je zásada nastavená na **Ne**, nebudou mít uživatelé předplatného, kteří jsou přidružení k zákazníkovi, k dispozici Azure Cost Management. Ve výchozím nastavení je zásada viditelnosti nákladů zakázaná pro všechny uživatele předplatného, pokud ji partner nepovolí.

Pokud je nákladová zásada nastavená na **Ano**, můžou uživatelé předplatného přidružení k zákaznickému tenantovi zobrazovat poplatky za využití podle tarifů s průběžnými platbami.

Pokud je zásada viditelnosti nákladů povolená, zobrazí se u všech služeb, které využívají předplatné, náklady podle tarifů s průběžnými platbami. U využitých rezervací se zobrazí nulové poplatky, a to jak u skutečných, tak u amortizovaných nákladů. Nákupy a nároky nejsou přidružené ke konkrétnímu předplatnému. To znamená, že se nákupy v rozsahu předplatného nezobrazují. Globální správce nebo agent pro správu přímého partnera nebo nepřímého poskytovatele může k nastavení zásad viditelnosti nákladů jednotlivých zákazníků ve velkém využít také [rozhraní API pro aktualizaci zákazníků](/rest/api/billing/2019-10-01-preview/policies/updatecustomer).

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Zobrazení nákladů na předplatná v tenantovi zákazníka

Pokud chcete zobrazit náklady pro předplatné, otevřete v Azure tenanta **Správa nákladů a fakturace** příslušného zákazníka. Pokud chcete začít s kontrolou nákladů, pro požadované předplatné vyberte **Analýza nákladů**. V tenantovi zákazníka si můžete zobrazit náklady pro jednotlivá předplatná samostatně.

[![Zobrazení analýzy nákladů jako zákazník ](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

Analýza nákladů, rozpočty a upozornění jsou k dispozici pro rozsahy Azure RBAC předplatného a skupin prostředků. Použijí se náklady podle tarifů s průběžnými platbami.

V amortizačních zobrazeních a ve skutečných nákladech na rezervované instance v rozsazích Azure RBAC se zobrazí nulové poplatky. Náklady na nákup nároků, jako jsou rezervované instance a poplatky za Marketplace, se zobrazují jenom v rozsazích fakturace tenanta partnera, ve kterých se nákupy uskutečnily.

Maloobchodní ceny použité pro výpočet nákladů uvedených v tomto zobrazení jsou stejné jako ceny uváděné v cenové kalkulačce Azure pro všechny zákazníky. Uvedené náklady nezahrnují slevy ani kredity, které partner může mít, například získané partnerské kredity, slevy založené na úrovni a globální slevy za služby.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analýza nákladů

Partneři, kteří mají v partnerském tenantovi přístup k rozsahům fakturace, můžou v Analýze nákladů zkoumat a analyzovat fakturované náklady, a to buď pro všechny zákazníky, určitého zákazníka nebo fakturu. V zobrazení [Analýza nákladů](quick-acm-cost-analysis.md) také můžete [ukládat zobrazení](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) a exportovat data do [souborů CSV a PNG](quick-acm-cost-analysis.md#download-usage-data).

Uživatelé Azure RBAC s přístupem k předplatnému také můžou v zákaznickém tenantovi analyzovat maloobchodní náklady předplatných, ukládat zobrazení a exportovat data do souborů CSV a PNG.

Při analýze nákladů můžete používat funkce filtrů a skupin, abyste mohli náklady analyzovat podle různých polí. Specifická pole partnerů jsou zobrazená v další části.

## <a name="data-fields"></a>Datová pole

Následující datová pole najdete v souborech s podrobnostmi o využití a v rozhraních API služby Cost Management. Uvádíme zde i odpovídající informace z partnerského centra, pokud jsou k dispozici. Pokud jsou následující pole uvedena tučně, můžou partneři při analýze nákladů používat funkce filtrů a skupin, aby mohli náklady analyzovat podle více polí. Tučná pole platí pouze u smluv se zákazníkem Microsoftu podporovaných partnery.

| **Název pole** | **Popis** | **Ekvivalent v partnerském centru** |
| --- | --- | --- |
| invoiceId | ID faktury zobrazené na faktuře v určité transakci. | Číslo faktury, kde se transakce zobrazuje. |
| previousInvoiceID | Odkaz na původní fakturu v případě, že jde o refundaci (záporné náklady). Pole je vyplněné, jen pokud jde o refundaci. | – |
| billingAccountName | Název fakturačního účtu, který představuje partnera. Přibývají na něm všechny náklady zákazníků, kteří mají podepsanou smlouvu se zákazníkem Microsoftu i zákazníků CSP, kteří si koupili nároky, jako je SaaS nebo Azure Marketplace, a rezervace. | – |
| billingAccountID | Identifikátor fakturačního účtu, který představuje partnera. | ID kořenového obchodu partnera MCAPI. Používá se v požadavku, ale není zahrnuté do odpovědi.|
| billingProfileID | Identifikátor fakturačního profilu, kde se seskupují náklady z faktur v jednotné fakturační měně za všechny zákazníky s uzavřenou smlouvou se zákazníkem Microsoftu a za zákazníky CSP, kteří nakupují nároky, například SaaS nebo Azure Marketplace, a rezervace. | ID fakturační skupiny partnera MCAPI. Používá se v požadavku, ale není zahrnuté do odpovědi. |
| billingProfileName | Název fakturačního profilu, kde se seskupují náklady z faktur v jednotné fakturační měně za zákazníky s uzavřenou smlouvou se zákazníkem Microsoftu a za zákazníky CSP, kteří nakupují nároky, například SaaS nebo Azure Marketplace, a rezervace. | – |
| invoiceSectionName | Název projektu účtovaného na faktuře. Neplatí pro smlouvy se zákazníkem Microsoftu uzavřené partnery. | – |
| invoiceSectionID | Identifikátor projektu účtovaného na faktuře. Neplatí pro smlouvy se zákazníkem Microsoftu uzavřené partnery. | – |
| **CustomerTenantID** | Identifikátor tenanta Azure Active Directory zákaznického předplatného. | ID organizace zákazníka – ID tenanta Azure Active Directory zákazníka. |
| **CustomerName** | Název tenanta Azure Active Directory zákaznického předplatného. | Název organizace zákazníka zobrazený v partnerském centru. Tento údaj je důležitý k odsouhlasení faktury s informacemi ve vašem systému. |
| **CustomerTenantDomainName** | Název domény tenanta Azure Active Directory zákaznického předplatného. | Zákazníkova doména tenanta Azure Active Directory. |
| **PartnerTenantID** | Identifikátor partnerského tenanta Azure Active Directory. | ID partnerského tenanta Azure Active Directory označovaného jako ID partnera (ve formátu GUID). |
| **PartnerName** | Název partnerského tenanta Azure Active Directory. | Název partnera. |
| **ResellerMPNID** | Identifikátor MPNID prodejce přidruženého k předplatnému. | Identifikátor MPN ID prodejce na záznamu předplatného. Pro aktuální aktivitu není k dispozici. |
| costCenter | Nákladové centrum přidružené k předplatnému. | – |
| billingPeriodStartDate | Počáteční datum fakturačního období, které je uvedené na faktuře. | – |
| billingPeriodEndDate | Koncové datum fakturačního období, které je uvedené na faktuře. | – |
| servicePeriodStartDate | Počáteční datum hodnoceného období, kdy byly za používání služby účtovány poplatky. Ceny služeb Azure se určují pro hodnocené období. | V partnerském centru je to ChargeStartDate.  Počáteční datum fakturačního cyklu. Neplatí pro zatím neúčtovaná data o latentním využití z předchozího fakturačního cyklu. Čas je vždy začátek dne, tj. 0:00. |
| servicePeriodEndDate | Konečné datum období, za které se vyhodnocují poplatky za používání služby. Ceny služeb Azure se určují na základě hodnoceného období. | – |
| date | U dat o spotřebě Azure se zobrazuje hodnocené datum použití. U rezervovaných instancí se zobrazuje datum nákupu. U pravidelných i jednorázových poplatků, třeba za Marketplace a podporu, se zobrazí datum nákupu. | – |
| productID | Identifikátor produktu, kterému se načítají poplatky za spotřebu nebo nákup. Je to klíč vzniklý zřetězením hodnot productID a SKuID, který je zobrazený v partnerském centru. | ID produktu. |
| product | Název produktu, u kterého se načítají poplatky za jeho spotřebu nebo nákup a které jsou uvedené na faktuře. | Název produktu je v katalogu. |
| serviceFamily | Zobrazuje řadu služeb koupeného nebo účtovaného produktu. Příklad: Úložiště nebo Výpočetní prostředky. | – |
| productOrderID | Identifikátor prostředku nebo název plánu Azure, ke kterému předplatné patří. Příklad: Plán Azure. | CommerceSubscriptionID |
| productOrderName | Název plánu Azure, ke kterému předplatné patří. Příklad: Plán Azure. | –|
| consumedService | Spotřebovávaná služba (starší taxonomie) tak, jak se používala v podrobnostech o použití ve starší smlouvě Enterprise. | Služba zobrazená v partnerském centru. Příklad: Microsoft.Storage, Microsoft.Compute nebo microsoft.operationalinsights. |
| meterID | Identifikátor měřiče používaného k měření spotřeby. | ID použitého měřiče. |
| meterName | Určuje název měřiče použitého k měření spotřeby. | Název použitého měřiče. |
| meterCategory | Identifikuje službu nejvyšší úrovně, které se toto využití týká. | Služba nejvyšší úrovně, které se využití týká. |
| meterSubCategory | Definuje typ nebo dílčí kategorii služby Azure, která ovlivňuje tarif. | Typ služby Azure, který ovlivňuje tarif.|
| meterRegion | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra. | Regionální umístění datového centra služeb, pokud je to možné a je vyplněné. |
| subscription ID | Jedinečný identifikátor předplatného Azure generovaný Microsoftem. | EntitlementID |
| subscriptionName | Název předplatného Azure. | – |
| Označení | Zobrazí období platnosti nabídky. Například u rezervovaných instancí se zobrazuje 12 měsíců ročního období rezervované instance. Při jednorázových a opakovaných nákupech se v období zobrazí jeden měsíc SaaS, Azure Marketplace a podpory. Neplatí to pro používání Azure. | – |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ vydavatele, který identifikuje, jestli jde o prvotního vydavatele, externího prodejce nebo externího zprostředkovatele. | – |
| partNumber | Číslo součásti u nepoužité rezervované instance a u služeb Azure Marketplace. | – |
| publisherName | Název vydavatele služby, včetně Microsoftu nebo jiných externích vydavatelů. | Název vydavatele produktu.|
| reservationId | Identifikátor zakoupené rezervované instance. | – |
| reservationName | Název rezervované instance. | – |
| reservationOrderId | ID objednávky rezervované instance. | – |
| frequency | Frekvence plateb za rezervovanou instanci. | – |
| resourceGroup | Název skupiny prostředků Azure používané ke správě jeho životního cyklu. | Název skupiny prostředků. |
| instanceID (nebo) ResourceID | Identifikátor instance prostředku. | Zobrazuje se jako identifikátor ResourceURI, který obsahuje úplné vlastnosti prostředku. |
| resourceLocation | Název umístění prostředku. | Umístění prostředku. |
| Umístění | Normální umístění prostředku. | – |
| effectivePrice | Platná jednotková cena služby v ceníkové měně. Liší se podle produktu, řady služeb, měřiče a nabídky. Používá se jako ceníková cena pro fakturační účet. Pokud se cena odvíjí od úrovně služby nebo zahrnuje množství, zobrazí se smíšená cena za spotřebu. | Jednotková cena po provedených úpravách. |
| Množství | Koupené nebo spotřebované naměřené množství. Množství spotřebované na daném měřiči ve fakturačním období. | Počet jednotek. Při odsouhlasení ověřte, jestli odpovídá informacím ve vašem fakturačním systému. |
| unitOfMeasure | Určuje jednotku, po které se služba účtuje. Například GB nebo hodiny. | Určuje jednotku, po které se služba účtuje. Například GB, hodiny nebo desetitisíce. |
| pricingCurrency | Měna, ve které je definovaná jednotková cena. | Měna v ceníku.|
| billingCurrency | Měna, ve které se definují fakturované náklady. | Měna definovaná jako fakturovaná měna na faktuře. |
| chargeType | Definuje typ poplatku, kterou náklad představuje ve službě Azure Cost Management. Může jít třeba o nákup nebo refundaci. | Typ poplatku nebo úpravy. Pro aktuální aktivitu není k dispozici. |
| costinBillingCurrency | Celkové nebo smíšené náklady před zdaněním ve fakturované měně. | – |
| costinPricingCurrency | Celkové nebo smíšené náklady před zdaněním v ceníkové měně kvůli porovnání cen. | – |
| **costinUSD** | Odhadované celkové nebo smíšené náklady před zdaněním v USD. | – |
| **paygCostInBillingCurrency** | Zobrazuje náklady, pokud jsou v maloobchodních cenách. Zobrazuje ceny ve fakturační měně při průběžných platbách. K dispozici jenom u rozsahů Azure RBAC. | – |
| **paygCostInUSD** | Zobrazuje náklady, pokud jsou v maloobchodních cenách. Zobrazuje ceny v USD při průběžných platbách. K dispozici jenom u rozsahů Azure RBAC. | – |
| exchangeRate | Směnný kurz používaný k převodu ceníkové měny na fakturační měnu. | V partnerském centru se označuje jako PCToBCExchangeRate. Směnný kurz ceníkové měny k fakturační měně.|
| exchangeRateDate | Datum směnného kurzu použitého k převodu ceníkové měny na fakturační měnu. | V partnerském centru se označuje jako PCToBCExchangeRateDat. Datum směnného kurzu ceníkové ceny k fakturační měně.|
| isAzureCreditEligible | Určuje, jestli je možné k úhradě nákladů použít kredit Azure. | – |
| serviceInfo1 | Starší pole, které obsahuje volitelná metadata týkající se služby. | Interní metadata služby Azure. |
| serviceInfo2 | Starší pole, které obsahuje volitelná metadata týkající se služby. | Informace o službě. Například typ image u virtuálního počítače nebo název poskytovatele internetové služby ExpressRoute.|
| additionalInfo | Metadata konkrétních služeb. Například typ image u virtuálního počítače. | Další informace, které nejsou uvedené v jiných sloupcích. Metadata konkrétních služeb. Například typ image u virtuálního počítače.|
| tags | Značka, kterou přiřadíte měřiči. Značky můžete použít k seskupení fakturačních záznamů. Značky můžete například použít k rozdělení nákladů podle oddělení, které daný měřič používá. | Značky přidané zákazníkem.|
| **partnerEarnedCreditRate** | Použitá sazba slevy, pokud existuje kredit získaný partnerem (PEC), který je založený na přístupu připojeného správce partnera. | Sazba kreditu získaného partnerem (PEC). Například 0 nebo 15 %. |
| **partnerEarnedCreditApplied** | Určuje, jestli byl získaný kredit partnera použit. | – |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Zobrazení nákladů na prostředky se získaným kreditem partnera (PEC)

Ve službě Azure Cost Management můžou partneři používat analýzu nákladů k zobrazení nákladů s uplatněným výhodnějším kreditem partnera PEC.

Na webu Azure Portal se přihlaste k partnerskému tenantovi a vyberte **Správa nákladů a fakturace**. V části **Správa nákladů** vyberte **Analýza nákladů**.

V zobrazení Analýza nákladů se zobrazují náklady fakturačního účtu partnera. Podle potřeby vyberte **rozsah** odpovídající partnerovi, určitému zákazníkovi nebo fakturačnímu profilu, abyste mohli odsouhlasit faktury.

V prstencovém grafu vyberte rozevírací seznam a pak vyberte **PartnerEarnedCreditApplied**, abyste se dostali k podrobnostem o cenách PEC.

![Příklad ukazuje, jak zobrazit kredit získaný partnerem](./media/get-started-partners/cost-analysis-pec1.png)

Pokud je vlastnost **PartnerEarnedCreditApplied** nastavena na hodnotu _True_, zohledňují přidružené náklady výhody získané přístupem partnera coby správce.

Pokud je vlastnost **PartnerEarnedCreditApplied** nastavena na hodnotu _False_, znamená to, že přidružené náklady nedosahují požadované výše, aby vznikl nárok na kredit. Nebo u koupené služby nejde uplatnit dosažený kredit partnera.

Zobrazení dat o využití služeb ve službě Cost Management obvykle trvá 8–24 hodin. Další informace najdete v tématu [Aktualizace a uchovávání údajů o využití a nákladech](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Kredit PEC se zobrazí do 48 hodin od okamžiku přístupu ke službě Azure Cost Management.


K filtrování a seskupení také můžete použít vlastnost **PartnerEarnedCreditApplied** a možnost **Seskupit podle**. Tyto možnosti můžete použít ke kontrole nákladů, u kterých je nebo není kredit PEC.

![Seskupení nebo filtrování podle získaného kreditu partnera](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Export dat o nákladech do Azure Storage

Partneři, kteří mají v partnerském tenantovi přístup k rozsahům fakturace, můžou exportovat data o nákladech a využití do úložiště objektů blob v Azure. Objekt blob musí být v předplatném partnerského tenanta, který nepatří k [předplatnému sdílené služby](/partner-center/shared-services) ani k předplatnému zákazníka. Pokud chcete povolit export nákladových dat, doporučujeme v partnerském tenantovi nastavit nezávislé předplatné s průběžnými platbami, které bude hostovat exportovaná nákladová data. Účet úložiště pro export se vytvoří v úložišti objektů služby Azure Storage hostovaném v předplatném s průběžnými platbami. Na základě rozsahu, ve kterém partner vytvoří export, se související data pravidelně automaticky exportují do účtu úložiště.

Uživatelé s přístupem Azure RBAC k předplatnému také můžou nákladová data exportovat do úložiště objektů blob v Azure hostovaného v některém předplatném zákaznického tenanta.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Vytvoření exportu v partnerském nebo zákaznickém tenantovi

Na webu Azure Portal se přihlaste k partnerskému nebo zákaznickému tenantovi a vyberte **Správa nákladů a fakturace**. Vyberte příslušný rozsah, například fakturační účet smlouvy s partnerem Microsoftu, a vyberte **Analýza nákladů**. Po načtení stránky vyberte **Export**. V části Naplánovat export vyberte **Zobrazit všechny exporty**.

![Výběr možností Export a Zobrazit všechny exporty](./media/get-started-partners/export01.png)

Dále vyberte **Přidat**, zadejte název a vyberte typ exportu. Vyberte kartu **Úložiště** a zadejte požadované informace.

![Přidání nového exportu a výběr karty Úložiště](./media/get-started-partners/export02.png)

Jakmile v partnerském tenantovi vytvoříte export, vyberte v něm předplatné s průběžnými platbami. Toto předplatné použijte k vytvoření účtu služby Azure Storage.

U uživatelů Azure RBAC v zákaznickém tenantovi vyberte předplatné v zákaznickém tenantovi. Předplatné použijte k vytvoření účtu Azure Storage.

Zkontrolujte obsah a vyberte **Vytvořit**, abyste mohli naplánovat export.

Pokud chcete ověřit data, která jsou v seznamu exportu, vyberte název účtu úložiště. Na stránce účtu úložiště vyberte **Kontejnery** a vyberte kontejner. Přejděte k odpovídající složce a vyberte soubor CSV. Vyberte **Stáhnout**, abyste získali soubor CSV, a pak tento soubor otevřete. Exportovaná data připomínají nákladová data a ta se zase podobají podrobnostem o použití na webu Azure Portal.

![Příklad exportovaných dat](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Rozhraní REST API služby Cost Management

Partneři a zákazníci můžou v rozhraních API služby Cost Management, popsaných v následujících oddílech, provádět běžné úkoly.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Rozhraní API služby Azure Cost Management – přímí a nepřímí poskytovatelé

Partneři, kteří mají ve svém tenantovi přístup k rozsahům fakturace, můžou k zobrazení fakturovaných nákladů použít následující rozhraní API.

Pokud mají partneři přístup k předplatnému, můžou volat rozhraní API v rozsahu předplatného bez ohledu na nákladovou zásadu. Ostatní uživatelé s přístupem k předplatnému, třeba zákazníci nebo prodejci, můžou rozhraní API volat, jen pokud partner povolí nákladovou zásadu zákaznickému tenantovi.


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

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Získání seznamu faktur v časovém období

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Volání rozhraní API vrátí pole faktur s prvky podobnými následujícímu kódu JSON.

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

Použijte předchozí vrácenou hodnotu ID pole a nahraďte jí v následujícím příkladu rozsah dotazu na podrobnosti o používání.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Příklad vrátí záznamy o použití přidružené ke konkrétní faktuře.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Získání zásady pro zákazníky kvůli zobrazení nákladů

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Nastavení zásady pro zákazníky kvůli zobrazení nákladů

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Získání informací o využití služby Azure k fakturačnímu účtu

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Stažení informací o využití služby Azure zákazníkem

Následující volání příkazem get je asynchronní operace.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Volání identifikátoru URI `Location` vráceného v odpovědi na kontrolu stavu operace. Pokud je stav operace *dokončeno*, obsahuje vlastnost `downloadUrl` odkaz, který můžete použít ke stažení vygenerované sestavy.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Získání nebo stažení ceníku používaných služeb Azure

Nejprve použijte následující příspěvek.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Pak volejte hodnotu vlastnosti asynchronní operace. Příklad:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Předchozí volání příkazem get vrátí odkaz na stažení ceníku.


#### <a name="to-get-aggregated-costs"></a>Získání agregovaných nákladů

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Vytvoření rozpočtu partnera

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Vytvoření rozpočtu zákazníka

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Odstranění rozpočtu

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Další kroky
- [Zahájení analýzy nákladů](quick-acm-cost-analysis.md) ve službě Cost Management
- [Vytvoření a správa rozpočtů](tutorial-acm-create-budgets.md) ve službě Cost Management
