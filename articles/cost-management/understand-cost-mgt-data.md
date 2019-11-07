---
title: Pochopení Azure Cost Management dat | Microsoft Docs
description: Tento článek vám pomůže lépe porozumět datům, která jsou součástí Azure Cost Management a jak často je zpracovává, shromažďuje, zobrazuje a uzavřel.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721364"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek vám pomůže lépe porozumět nákladům a datům o využití Azure, které jsou součástí Azure Cost Management. Vysvětluje, jak často se data zpracovávají, shromažďují, zobrazují a uzavřela. Účtují se za využití Azure za měsíc. I když fakturační cykly jsou měsíční období, počáteční a koncové datum cyklu se liší podle typu předplatného. Jak často Cost Management přijímat data o využití se liší v závislosti na různých faktorech. Tyto faktory zahrnují, jak dlouho trvá zpracování dat a jak často služby Azure emitují využití do fakturačního systému.

Cost Management zahrnuje veškeré využití a nákupy, včetně rezervací a nabídek třetích stran pro účty smlouva Enterprise (EA). Účty zákaznické smlouvy Microsoftu a jednotlivé předplatné s tarify průběžných plateb zahrnují jenom využití ze služeb Azure a Marketplace. Není zahrnutá podpora a další náklady. Náklady jsou odhadované, dokud se nevygeneruje faktura a nebere v kreditech žádný faktor.

## <a name="supported-microsoft-azure-offers"></a>Podporované nabídky Microsoft Azure

Následující informace zobrazují aktuálně podporované [Microsoft Azure nabídky](https://azure.microsoft.com/support/legal/offer-details/) v Azure cost management. Nabídka Azure je typ předplatného Azure, které máte. Data jsou k dispozici v Cost Management od **data k dispozici** . Pokud se změní předplatné, nebudou k dispozici náklady ještě před datem změny nabídky.

| **Kategorie**  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** | **Data dostupná z** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR – 0148P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR – 0017P | Květen 2014<sup>1</sup> |
| **Smlouva se zákazníkem Microsoftu** | [Plán Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Není dostupné. | Březen 2019<sup>3</sup> |
| **Smlouva se zákazníkem Microsoftu** | [Microsoft Azure plán pro vývoj a testování](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Není dostupné. | Březen 2019<sup>3</sup> |
| **Smlouva o zákaznících Microsoftu podporovaná partnery** | Plán Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 a CSPDEVTEST_2018-05-01<br><br>ID kvóty se znovu použije pro zákaznickou smlouvu Microsoftu a starší verze CSP. V současné době se podporují jenom předplatná Zákaznická smlouva od Microsoftu. | Není dostupné. | Říjen 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR – 0062P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR – 0003P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR – 0023P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR – 0025P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR – 0044P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Systém Azure v rámci licenčního programu Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR – 0111P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR – 0029P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR – 0059P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR – 0060P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR – 0063P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR – 0064P | 2\. října 2018<sup>2</sup> |

_<sup>**1**</sup> pro data před 1. května 2014 navštivte [portál Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> pro data do 2. října 2018 navštivte [centrum účtů Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> smlouvy o zákaznících Microsoftu zahájené v březnu 2019 a neobsahují historická data před tímto bodem._

_<sup>**4**</sup> historická data pro předplatná založená na kreditech a platbách předem nemusí odpovídat vaší faktuře. Zobrazit [historická data nemusí odpovídat](#historical-data-might-not-match-invoice) níže uvedené faktuře._

Následující nabídky ještě nejsou podporované:

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure Německo** | [Azure Německo – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR – 0145P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Azure Germany v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Průběžné platby**                 | Azure for Students Starter | DreamSpark_2015 – 02 – 01 | MS-AZR – 0144P |
| **Průběžné platby** | [Azure pro studenty](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018 – 01 – 01 | MS-AZR – 0170P |
| **Průběžné platby**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016 – 01 – 01 | MS-AZR – 0036P |
| **Plány podpory** | Standard Support                    | Default_2014-09-01 | MS-AZR – 0041P |
| **Plány podpory** | Podpora Professional Direct         | Default_2014-09-01 | MS-AZR – 0042P |
| **Plány podpory** | Podpora pro vývojáře                   | Default_2014-09-01 | MS-AZR – 0043P |
| **Plány podpory** | Plán podpory pro Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government pro – Direct support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Určení typu nabídky
Pokud nevidíte data pro předplatné a chcete zjistit, jestli vaše předplatné spadá do podporovaných nabídek, můžete ověřit, jestli je vaše předplatné podporované. Pokud chcete ověřit, jestli je předplatné Azure podporované, přihlaste se k [Azure Portal](https://portal.azure.com). Pak v levém podokně nabídky vyberte **všechny služby** . V seznamu služeb vyberte **předplatná**. V nabídce seznam předplatných klikněte na odběr, který chcete ověřit. Vaše předplatné se zobrazí na kartě Přehled, kde můžete zobrazit **nabídku** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad karty Přehled předplatného zobrazující nabídku a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady zahrnuté v Cost Management

V následujících tabulkách jsou uvedena data, která jsou součástí Cost Management. Všechny náklady jsou odhadované až do vygenerování faktury. Uvedené náklady nezahrnují bezplatné a předem placené kredity.

**Náklady a data o využití**

| **Obsaženy** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>5</sup>        | Poplatky za podporu – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Marketplace – využití nabídky<sup>6</sup> | Daně – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Nákupy na Marketplace<sup>6</sup>      | Kredity – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Nákupy na rezervované<sup>7</sup>      |  |
| Amortizace nákupních rezervací<sup>7</sup>      |  |

_<sup>**5**</sup> využití služeb Azure je založené na rezervacích a sjednaných cenách._

_<sup>**6**</sup> nákupů na tržišti není v tuto chvíli k dispozici pro nabídky s průběžnými platbami, MSDN a Visual Studio._

_<sup>**7**</sup> nákupy rezervací jsou v tuto chvíli dostupné jenom pro účty smlouva Enterprise (EA)._

**Mezipaměť**

| **Obsaženy** | **Nezahrnuto** |
| --- | --- |
| Značky prostředků<sup>8</sup> | Značky skupiny prostředků |

_<sup>**8**</sup> značek prostředků se používá, protože použití je emitováno z každé služby a není dostupné zpětně k historickému využití._

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace dat o hodnocení využití

Data o nákladech a využití jsou dostupná ve službě Cost Management + fakturace v Azure Portal a v [podpůrných rozhraních API](index.yml). Při kontrole nákladů mějte na paměti následující body:

- Odhadované poplatky za aktuální fakturační období se aktualizují po dobu šesti dní za den.
- Odhadované poplatky za aktuální fakturační období se můžou změnit, protože se vám bude účtovat víc využívání.
- Každá aktualizace je kumulativní a obsahuje všechny položky řádku a informace z předchozí aktualizace.
- Azure dokončí nebo _uzavře_ aktuální fakturační období až do 72 hodin (tři kalendářní dny) po skončení fakturačního období.

Následující příklady znázorňují způsob, jakým mohou být fakturační období ukončeny.

Předplatná smlouva Enterprise (EA) – Pokud se fakturační měsíc končí 31. března, odhadované poplatky se aktualizují až 72 hodin později. V tomto příkladu podle půlnoci (UTC) 4. dubna.

Předplatná s průběžnými platbami – Pokud se fakturační měsíc ukončí 15. května, odhadované poplatky se můžou aktualizovat až 72 hodin později. V tomto příkladu může půlnoc (UTC) 19.

### <a name="rerated-data"></a>Přehodnocená data

Bez ohledu na to, jestli k načtení dat používáte [rozhraní api Cost Management](index.yml)Power BI nebo Azure Portal, můžete očekávat, že se aktuální poplatky za fakturační období mají přepočítat a následně se změnit, dokud se faktura nezavře.

## <a name="cost-management-data-fields"></a>Cost Management datová pole

V souborech podrobností o využití a Cost Management rozhraní API se nacházejí následující datová pole. Pro následující Tučná pole můžou partneři pomocí funkce filtrovat a seskupit podle funkcí analýzy nákladů analyzovat náklady podle několika polí. Tučná pole se vztahují jenom na zákaznické smlouvy Microsoftu, které partneři podporuje.

| **Název pole** | **Popis** |
| --- | --- |
| invoiceId | ID faktury zobrazené na faktuře pro konkrétní transakci |
| previousInvoiceID | Odkaz na původní fakturu je refundace (záporné náklady). Vyplněno pouze v případě, že existuje refundace. |
| billingAccountName | Název fakturačního účtu, který představuje partnera. Účtují se všechny náklady na zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. |
| billingAccountID | Identifikátor fakturačního účtu, který představuje partnera. |
| billingProfileID | Identifikátor pro fakturační profil, který seskupuje náklady napříč fakturami v jedné fakturační měně mezi zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. |
| billingProfileName | Název fakturačního profilu, který seskupuje náklady napříč fakturami v jedné fakturační měně mezi zákazníky, kteří se připojili k smlouvě o zákaznících Microsoftu a zákazníkům CSP, kteří učinili nárok na nákupy jako SaaS, Azure Marketplace a rezervace. |
| invoiceSectionName | Název projektu, který se účtuje na faktuře. Neplatí pro smlouvy o zákaznících Microsoftu, které jsou zaregistrované partnery. |
| invoiceSectionID | Identifikátor projektu, který se účtuje na faktuře Neplatí pro smlouvy o zákaznících Microsoftu, které jsou zaregistrované partnery. |
| **CustomerTenantID** | Identifikátor tenanta Azure Active Directory předplatného zákazníka&#39;s. |
| **CustomerName** | Název tenanta Azure Active Directory pro předplatné zákazníka&#39;s. |
| **CustomerTenantDomainName** | Název domény pro klienta Azure Active Directory předplatného zákazníka&#39;s. |
| **PartnerTenantID** | Identifikátor pro tenanta Azure Active Directory&#39;partnera s. |
| **PartnerName** | Název partnerského Azure Active Directory tenanta. |
| **ResellerMPNID** | MPNID pro prodejce, který je přidružený k předplatnému. |
| costCenter | Nákladové středisko přidružené k předplatnému. |
| billingPeriodStartDate | Počáteční datum fakturačního období, jak je znázorněno na faktuře. |
| billingPeriodEndDate | Datum ukončení fakturačního období, jak je znázorněno na faktuře. |
| servicePeriodStartDate | Počáteční datum období hodnocení, kdy se vyhodnotilo využití služby za poplatky. Ceny služeb Azure se určují pro období hodnocení. |
| servicePeriodEndDate | Koncové datum období, kdy bylo využití služby vyhodnoceno za poplatek. Ceny služeb Azure se určují na základě období hodnocení. |
| date | Pro data o spotřebě Azure zobrazuje datum využití jako hodnocené. V případě rezervované instance se zobrazí datum nákupu. Pro periodické poplatky a jednorázové poplatky, jako je například Marketplace a podpora, se zobrazuje datum nákupu. |
| productID | Identifikátor produktu, který má za sebou účtováné poplatky podle využití nebo nákupu. Je to zřetězený klíč productID a SKuID, jak je znázorněno v partnerském centru. |
| product | Název produktu, který účtuje náklady podle spotřeby nebo nákupu, jak je vidět na faktuře. |
| serviceFamily | Zobrazuje rodinu služeb pro zakoupené nebo účtované produkty. Například úložiště nebo výpočetní prostředky. |
| productOrderID | Identifikátor prostředku nebo plánu Azure, ke kterému předplatné patří Například plán Azure. |
| productOrderName | Název plánu Azure, ke kterému předplatné patří Například plán Azure. |
| consumedService | Spotřebované služby (starší taxonomie), které se používají ve starších podrobnostech použití EA. |
| meterID | Měřený identifikátor pro měřenou spotřebu. |
| meterName | Určuje název měřiče pro měřenou spotřebu. |
| meterCategory | Identifikuje službu nejvyšší úrovně pro použití. |
| meterSubCategory | Definuje typ nebo podkategorii služby Azure, které můžou mít vliv na tuto sazbu. |
| meterRegion | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra. |
| subscription ID | Jedinečný identifikátor vygenerovaný společností Microsoft pro předplatné Azure. |
| subscriptionName | Název předplatného Azure. |
| Doba účinnosti | Zobrazí období platnosti nabídky. Například rezervované instance zobrazují 12 měsíců z ročního období rezervované instance. Pro jednorázové nákupy nebo opakované nákupy se termín zobrazuje jeden měsíc pro SaaS, Azure Marketplace a podporu. Nedá se použít pro spotřebu v Azure. |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ vydavatele, který identifikuje vydavatele jako první stranu, prodejce třetích stran nebo agenturu třetích stran. |
| PartNumber | Číslo součásti pro nevyužité rezervované instance a Azure Marketplace služby. |
| publisherName | Jméno vydavatele služby, včetně vydavatelů společnosti Microsoft nebo třetích stran. |
| reservationId | Identifikátor pro nákup rezervované instance |
| reservationName | Název rezervované instance. |
| reservationOrderId | ČísloObjednávky pro rezervovanou instanci. |
| frequency | Četnost platby pro rezervovanou instanci. |
| resourceGroup | Název skupiny prostředků Azure, která se používá pro správu prostředků životního cyklu. |
| instanceID (nebo) ResourceID | Identifikátor instance prostředku |
| resourceLocation | Název umístění prostředku. |
| Umístění | Normalizované umístění prostředku. |
| effectivePrice | Efektivní Jednotková cena služby v cenové měně. Jedinečné pro produkt, rodinu služeb, měřič a nabídku. Používá se s cenami v ceníku pro fakturační účet. V případě, že jsou k dispozici vrstvené ceny nebo zahrnuté množství, zobrazuje se za ni smíšená cena za spotřebu. |
| Množství | Nakoupené nebo spotřebované měřené množství. Množství měřiče použitého během fakturačního období. |
| unitOfMeasure | Určuje jednotku, ve které se služba účtuje. Například GB a hodiny. |
| pricingCurrency | Měna, která definuje jednotkovou cenu |
| billingCurrency | Měna definující účtované náklady |
| chargeType | Definuje typ poplatků, který představuje náklady v Azure Cost Management jako nákup a refundace. |
| costinBillingCurrency | ExtendedCost nebo promísící náklady před zdaněním v účtované měně. |
| costinPricingCurrency | ExtendedCost nebo smíšené náklady před zdaněním v cenové měně za účelem korelace s cenami. |
| **costinUSD** | Odhadované ExtendedCost nebo smíšené náklady před zdaněním v USD. |
| **paygCostInBillingCurrency** | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby ve fakturační měně. K dispozici pouze v oborech RBAC. |
| **paygCostInUSD** | Zobrazuje náklady, pokud jsou ceny v maloobchodních cenách. Zobrazuje ceny za průběžné platby v USD. K dispozici pouze v oborech RBAC. |
| exchangeRate | Směnný kurz, který se používá k převodu z cenové měny na fakturační měnu |
| exchangeRateDate | Datum směnného kurzu, který&#39;se používá k převodu z cenové měny na fakturační měnu. |
| isAzureCreditEligible | Uvádí, zda náklady mají nárok na platby prostřednictvím kreditů Azure. |
| serviceInfo1 | Starší verze pole, která zachycuje volitelná metadata specifická pro službu. |
| serviceInfo2 | Starší verze pole, která zachycuje volitelná metadata specifická pro službu. |
| additionalInfo | Metadata konkrétních služeb. Například typ image u virtuálního počítače. |
| značek | Značka, kterou přiřadíte k měřiči. Pomocí značek můžete seskupovat záznamy fakturace. Pomocí značek můžete například distribuovat náklady podle oddělení, které používá měřič. |
| **partnerEarnedCreditRate** | Pokud je na základě přístupu k partnerovi pro správu partnerského serveru k dispozici kredity, je sazba uplatněna. |
| **partnerEarnedCreditApplied** | Označuje, zda byl použit partnerský kredit. |


## <a name="usage-data-update-frequency-varies"></a>Frekvence aktualizace dat využití se liší.

Dostupnost dat o využití v Cost Management závisí na několika faktorech, včetně:

- Jak často služby Azure (například úložiště, výpočetní prostředky, CDN a SQL) generují využití.
- Čas potřebný ke zpracování dat o využití prostřednictvím modulu hodnocení a kanálů pro správu nákladů.

Některé služby emitují využití častěji než jiné. Můžete tak zobrazit data v Cost Management pro některé služby dřív než jiné služby, které generují data méně často. K zobrazení v Cost Management obvykle trvá použití služeb 8-24 hodin. Mějte na paměti, že data pro otevřený měsíc se budou aktualizovat, protože budete mít větší využití, protože aktualizace jsou kumulativní.

## <a name="historical-data-might-not-match-invoice"></a>Historická data se nemusí shodovat s fakturou.

Historická data pro nabídky na základě kreditu a platby se nemusí shodovat s vaší fakturou. Některé nabídky Azure s průběžnými platbami podle aktuálního využití, MSDN a Visual Studio můžou mít kredity Azure a na fakturu se vztahují rozšířené platby. Historická data uvedená v Cost Management však vycházejí jenom z odhadovaných poplatků za využití. Cost Management historická data nezahrnují platby a kredity. V důsledku toho historická data zobrazená pro následující nabídky nemusí přesně odpovídat vaší faktuře.

- Azure for Students (MS-AZR-0170P)
- Systém Azure v rámci licenčního programu Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezplatná zkušební verze (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Viz také

- Pokud jste ještě nedokončili první rychlý Start pro Cost Management, přečtěte si ho v části [zahájení analýzy nákladů](quick-acm-cost-analysis.md).
