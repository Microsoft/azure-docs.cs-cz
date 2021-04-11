---
title: Začínáme s aktualizovaným fakturačním účtem Azure
description: Začněte pracovat s aktualizovaným fakturačním účtem Azure a seznamte se se změnami v novém prostředí pro fakturaci a správu nákladů.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: 4f7179a5ad35b4d3ca9a92119fb7b492e2aff779
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122504"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Začínáme s aktualizovaným fakturačním účtem Azure

Správa nákladů a faktur je jednou z klíčových součástí cloudového prostředí. Pomůže vám mít útraty v cloudu pod kontrolou a porozumět jí. Pro usnadnění správy nákladů a faktur teď Microsoft aktualizuje váš fakturační účet Azure, aby zahrnoval vylepšené funkce pro správu nákladů a fakturaci. Tento článek popisuje aktualizace vašeho fakturačního účtu a provede vás novými funkcemi.

> [!IMPORTANT]
> Váš účet se aktualizuje, když od Microsoftu obdržíte e-mail s upozorněním na aktualizace vašeho účtu. Toto oznámení se pošle 60 dní před tím, než se váš účet aktualizuje.

## <a name="more-flexibility-with-your-new-billing-account"></a>Více flexibility s novým fakturačním účtem

Následující diagram porovnává starý a nový fakturační účet:

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="Diagram znázorňující porovnání hierarchie fakturace ve starém a novém účtu." border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

Váš fakturační účet obsahuje jeden nebo několik fakturačních profilů, pomocí nichž můžete spravovat faktury a způsoby platby. Každý fakturační profil obsahuje nejméně jeden oddíl faktury, který vám umožňuje organizovat náklady na faktuře pro tento profil.

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="Diagram znázorňující novou fakturační hierarchii" border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

Role ve fakturačním účtu mají nejvyšší úroveň oprávnění. Tyto role by se měly přiřazovat uživatelům, kteří potřebují zobrazovat faktury a sledovat náklady pro celý účet, jako jsou manažeři financí nebo IT v organizaci nebo jednotlivec, který si účet zaregistroval. Podrobnosti najdete v části [Role a úlohy související s fakturačním účtem](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Po aktualizaci účtu se uživateli, který má ve starém fakturačním účtu roli správce účtu, pro nový účet poskytne role vlastníka.

## <a name="billing-profiles"></a>Fakturační profily

Fakturační profil slouží ke správě faktur a způsobů platby. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Faktura obsahuje příslušné poplatky z předchozího měsíce za všechna předplatná přidružená k fakturačnímu účtu.

Po aktualizaci účtu se pro každé předplatné automaticky vytvoří fakturační profil. Poplatky za předplatné se naúčtují příslušnému fakturačnímu profilu a zobrazují se na jeho faktuře.

K rolím ve fakturačních profilech se vážou oprávnění pro zobrazování a správu faktur a způsobů platby. Tyto role by se měly přiřazovat uživatelům, kteří hradí faktury, například členům účetního týmu ve vaší organizaci. Podrobnosti najdete v části [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Po aktualizaci účtu se pro každé předplatné, pro které jste ostatním poskytli oprávnění [zobrazovat faktury](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice), uživatelům, kteří mají roli vlastníka, přispěvatele, čtenáře nebo čtenáře fakturace Azure, poskytne pro příslušný fakturační profil role čtenáře.

## <a name="invoice-sections"></a>Oddíly faktury

Oddíly faktury slouží k uspořádání nákladů na faktuře. Můžete je vytvářet například tehdy, když potřebujete jednu fakturu, na které mají být náklady uspořádané podle oddělení, týmu nebo projektu. V tomto scénáři tak máte jeden fakturační profil, v němž vytváříte oddíl pro každé oddělení, tým a projekt.

Po aktualizaci účtu se vytvoří oddíl faktury pro každý fakturační profil a v oddílu faktury se přiřadí příslušné předplatné. Když přidáte další předplatná, můžete vytvořit další oddíly a přiřadit předplatná k oddílům faktury. Tyto oddíly se zobrazí na faktuře pro fakturační profil a budou odrážet využití jednotlivých předplatných, které jste k nim přiřadili.

Pro role v oddílu faktury jsou k dispozici oprávnění, pomocí kterých můžete určovat, kdo smí předplatná Azure vytvářet. Tyto role by se měly přiřazovat uživatelům, kteří nastavují prostředí Azure pro týmy ve vaší organizaci, například technickým vedoucím nebo architektům. Podrobnosti najdete v části [Role a úlohy související s oddíly faktury](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Vylepšené funkce ve vašem novém prostředí

Vaše nové prostředí zahrnuje následující možnosti fakturace a správy nákladů, které usnadňují správu faktur a nákladů:

#### <a name="invoice-management"></a>Správa faktur

**Předvídatelnější měsíční fakturační období:** V novém účtu začíná fakturační období prvním dnem v měsíci a končí posledním dnem v měsíci, a to bez ohledu na to, kdy jste se k použití Azure zaregistrovali. Faktura se vygeneruje na začátku každého měsíce a bude obsahovat všechny poplatky za předchozí měsíc.

**Získejte jednu měsíční fakturu pro více předplatných** – ve vašem stávajícím účtu získáte fakturu pro každé předplatné Azure. Po aktualizaci vašeho účtu se zachová stávající chování, ale budete mít flexibilitu při konsolidaci poplatků za Vaše předplatná na jediné faktuře. Po aktualizaci účtu postupujte podle následujících kroků a Konsolidujte své poplatky na jednu fakturu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte **Cost Management a fakturace**.  
   ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. Na levé straně obrazovky vyberte **předplatná Azure** . 
4. V tabulce jsou uvedena předplatná Azure, za která platíte. Ve sloupci profil fakturace byste našli Fakturační profil, který se fakturuje v rámci předplatného. Poplatky za předplatné se zobrazí na faktuře pro fakturační profil. Pokud chcete konsolidovat poplatky za všechna Vaše předplatná na jediné faktuře, musíte propojit všechna Vaše předplatná s jedním fakturačním profilem.  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Snímek obrazovky, který zobrazuje seznam předplatných Azure." lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. Vyberte fakturační profil, který chcete použít. 
6. Vyberte předplatné, které není propojené s profilem fakturace, který jste zvolili v kroku 5. Klikněte na tři tečky u předplatného. Vyberte **Změnit oddíl faktury**.  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="Snímek obrazovky, který ukazuje, kde najít možnost změny části faktury" lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. Vyberte profil fakturace, který jste zvolili v kroku #5.  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="Snímek obrazovky, který ukazuje, jak změnit oddíl faktury" lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. Vyberte **změnit**.
9. Opakujte kroky 6-8 pro všechny ostatní odběry. 

**Jedna měsíční faktura pro předplatná Azure, plány podpory a produkty z Azure Marketplace:** Dostanete jednu měsíční fakturu pro všechny poplatky, včetně poplatků za využití předplatných Azure, za plány podpory a za nákupy na Azure Marketplace.

**Seskupení nákladů na faktuře podle vašich potřeb:** Náklady na faktuře můžete seskupit na základě vašich potřeb – podle oddělení, projektů nebo týmů.

**Nastavení volitelného čísla nákupní objednávky na faktuře:** Pokud chcete fakturu přidružit k vašim interním finančním systémům, nastavte číslo nákupní objednávky. Na webu Azure Portal můžete toto číslo spravovat a také kdykoli upravit.

#### <a name="payment-management"></a>Správa plateb

**Okamžitá platba faktur pomocí platební karty:** Už není potřeba čekat na naúčtování automatické platby na kartě. Platební kartu můžete na webu Azure Portal použít k uhrazení splatné faktury nebo faktury po platnosti.

**Sledování všech plateb vztahujících se k účtu:** Projděte si na webu Azure Portal všechny platby naúčtované pro váš účet, včetně použitého způsobu platby, zaplacené částky a data úhrady.

#### <a name="cost-management"></a>Správa nákladů

**Naplánování měsíčních exportů údajů o využití do účtu úložiště:** Denně, týdně nebo měsíčně je možné automaticky publikovat data o nákladech a využití do účtu úložiště.

#### <a name="account-and-subscription-management"></a>Správa účtů a předplatných

**Přiřazení několika správců pro provádění fakturačních operací:** Oprávnění k fakturaci můžete přiřadit několika uživatelům, kteří budou spravovat fakturaci pro váš účet. Poskytnutím možnosti čtení, zápisu nebo obou těchto možností dalším uživatelům získáte potřebnou flexibilitu.

**Vytváření dalších předplatných přímo na webu Azure Portal:** Na webu Azure Portal můžete předplatná vytvářet jedním výběrem.

#### <a name="api-support"></a>Podpora rozhraní API

**Provádění operací správy nákladů a fakturace prostřednictvím rozhraní API, sady SDK a PowerShellu:** Rozhraní API pro spotřebu, fakturaci a správu nákladů můžete využít k přetáhnutí fakturačních dat a údajů o nákladech do nástrojů pro analýzu dat, kterým dáváte přednost.

**Provádění všech operací s předplatnými prostřednictvím rozhraní API, sady SDK a PowerShellu:** Rozhraní API předplatných Azure můžete využít k automatizaci správy předplatných Azure, včetně vytvoření, přejmenování a zrušení předplatného.

## <a name="get-prepared-for-your-new-experience"></a>Zajištění připravenosti na nové prostředí

V rámci přípravy na nové prostředí doporučujeme následující:

**Měsíční fakturační období a jiné datum faktury**

V novém prostředí se vaše faktura vygeneruje přibližně devátého dne v měsíci a bude obsahovat všechny poplatky za předchozí měsíc. Toto datum se může lišit od data, kdy se faktury generovaly ve starém účtu. Pokud faktury sdílíte s ostatními, upozorněte je na změnu data.


**Faktury za první měsíc po migraci**

Den, kdy se Váš účet aktualizuje, vaše stávající neúčtované poplatky se dokončují a faktury za tyto poplatky obdržíte na den, kdy obvykle obdržíte faktury. Například Jan má dvě předplatná Azure – Azure sub 01 s fakturačním cyklem od pátého dne v měsíci do čtvrtého dne následujícího měsíce a Azure sub 02 s fakturačním cyklem z desátého dne v měsíci do devátého dne v příštím měsíci. Jan získá faktury pro předplatné Azure obvykle na pátý měsíc. Když se teď v poslední době aktualizuje účet Jan, účtují se poplatky za Azure sub 01 od března 5. dubna a účtují se za Azure sub 02 od 10. března do 9. dubna. Jan obdrží dvě faktury, jednu pro každou proceduru v dubnu 5. Po aktualizaci účtu bude fakturační cyklus Jan na základě kalendářního měsíce a bude pokrývat všechny poplatky vzniklé od začátku kalendářního měsíce až do konce tohoto kalendářního měsíce.  Faktury za předchozí kalendářní měsíc jsou k dispozici na 9. roku v měsíci. Takže v příkladu výše obdrží Jan od 5. dubna až do 30. května další fakturu. 


**Nová rozhraní API pro správu nákladů a fakturaci**

Pokud k dotazování nebo aktualizaci fakturačních dat nebo údajů o využití používáte rozhraní API pro fakturaci nebo Cost Management, musíte použít nová rozhraní API. Následující tabulka uvádí rozhraní API, která nefungují s novým fakturačním účtem, a změny, které musíte v novém fakturačním účtu udělat.

|Rozhraní API | Změny  |
|---------|---------|
|[Fakturační účty – seznam](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | V rozhraní API pro fakturační účty – seznam má starý fakturační účet jako typ smlouvy nastavený **MicrosoftOnlineServiceProgram**, v novém fakturačním účtu bude nastavený typ smlouvy **MicrosoftCustomerAgreement**. Pokud využíváte závislost na typu smlouvy, proveďte příslušnou aktualizaci. |
|[Faktury – seznam podle předplatného pro fakturaci](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Toto rozhraní API vrátí jenom faktury, které se vygenerovaly před aktualizací vašeho účtu. Pokud chcete získat faktury vygenerované v novém fakturačním účtu, musíte použít rozhraní API pro [faktury seznam podle fakturačního účtu](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |


## <a name="cost-management-updates-after-account-update"></a>Aktualizace Cost Managementu po aktualizaci účtu

Aktualizovaný fakturační účet Azure pro Smlouvu se zákazníkem poskytuje přístup k novému a rozšířenému prostředí Cost Managementu na webu Azure Portal, které pro účet s průběžnými platbami nebylo k dispozici.

### <a name="new-capabilities"></a>Nové funkce

Pro fakturační účet Azure jsou k dispozici následující nové funkce.

#### <a name="new-billing-scopes"></a>Nové rozsahy fakturace

V rámci aktualizovaného účtu máte ve službě Cost Management + Billing k dispozici nové rozsahy. Kromě pomoci s hierarchickým uspořádáním a fakturací představují také způsob, jak zobrazit kombinované poplatky z několika základních předplatných. Další informace o rozsazích fakturace najdete v tématu [Rozsahy smluv se zákazníkem Microsoftu](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Můžete také využít rozhraní API služby Cost Management a získat tak kombinované zobrazení nákladů ve větším rozsahu. Všechna rozhraní API služby Cost Management, která využívají rozsah předplatného, jsou stále k dispozici s některými drobnými změnami ve schématu. Další informace o těchto rozhraních API najdete v tématu věnovaném [rozhraním API služby Azure Cost Management](/rest/api/cost-management/) a [rozhraním API služby Azure Consumption](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Alokace nákladů

S aktualizovaným účtem můžete pomocí funkcí pro alokaci nákladů distribuovat náklady ze sdílených služeb v rámci vaší organizace. Další informace o alokaci nákladů najdete v tématu [Vytváření a správa pravidel alokace nákladů na Azure](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Konektor služby Azure Cost Management pro Power BI Desktop vám pomůže vytvářet vlastní sestavy a vizualizace využití a výdajů za Azure. K údajům o nákladech a využití máte přístup po připojení k aktualizovanému účtu. Další informace o konektoru služby Azure Cost Management pro Power BI Desktop najdete v tématu [Vytvoření vizuálů a sestav s konektorem Azure Cost Management v aplikaci Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Aktualizované možnosti

Pro fakturační účet Azure jsou k dispozici následující aktualizované funkce.

#### <a name="cost-analysis"></a>Analýza nákladů

Můžete dál zobrazovat a sledovat náklady za jednotlivé měsíce a nově můžete v rámci analýzy nákladů zobrazit náklady na rezervace a náklady na nákup na webu Marketplace.

Při použití aktualizovaného účtu dostanete jednu fakturu se všemi poplatky za Azure. Máte teď také zjednodušené zobrazení měsíčního kalendáře, které nahrazuje dříve používané zobrazení fakturačních období.

Pokud například vaše fakturační období pro původní účet bylo od 24. listopadu do 23. prosince, po upgradu se přejde na období od 1. listopadu do 30. listopadu, od 1. prosince do 31. prosince atd.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Snímek obrazovky znázorňující porovnání starých a nových fakturačních období" lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Rozpočty

Nově můžete vytvářet rozpočty pro fakturační účet, které vám umožní sledovat náklady v rámci předplatných. Máte také možnost udržovat si prostřednictvím rozpočtů přehled o poplatcích za nákupy. Další informace o rozpočtech najdete v tématu [Vytváření a správa rozpočtů Azure](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>exporty

Nový fakturační účet nabízí vylepšené funkce exportu. Můžete například vytvořit exporty pro skutečné náklady, které zahrnují nákupy nebo amortizované náklady (náklady na nákup rezervací rozprostřené v rámci nákupního období). Můžete také vytvořit export pro fakturační účet a získat tak údaje o využití a poplatcích napříč všemi předplatnými ve fakturačním účtu. Další informace o exportu najdete v tématu [Vytvoření a správa exportovaných dat](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Exporty vytvořené před aktualizací účtu pomocí **měsíčního exportu nákladů za poslední měsíc** budou obsahovat data za poslední kalendářní měsíc, nikoli za poslední fakturační období.

Například pro fakturační období od 23. prosince do 22. ledna budou ve vyexportovaném souboru CSV k dispozici údaje o nákladech a využití za tuto dobu. Po dokončení aktualizace bude export obsahovat data za kalendářní měsíc. Například od 1. ledna do 31. ledna atd.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Snímky obrazovky znázorňující porovnání starých a nových podrobností o exportu" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Další informace

Následující oddíly obsahují další informace týkající se nového prostředí.

**Žádné výpadky služeb:** Služby Azure ve vašem předplatném poběží i nadále bez přerušení. Jediná aktualizace, ke které dojde, je aktualizace fakturačního prostředí. Stávající prostředky, skupiny prostředků ani skupiny pro správu to neovlivní.

**Žádné změny prostředků Azure:** Tato aktualizace neovlivňuje přístup k prostředkům Azure, které byly nastavené pomocí řízení přístupu na základě role v Azure (Azure RBAC).

**Dostupnost dřívějších faktur v novém prostředí:** Faktury vygenerované před aktualizací vašeho účtu budou dál dostupné na webu Azure Portal.

**Faktury za aktualizaci účtu uprostřed měsíce:** Pokud se váš účet aktualizuje uprostřed měsíce, dostanete jednu fakturu na poplatky nashromážděné do dne, kdy se váš účet aktualizoval. Další fakturu dostanete za zbytek měsíce. Představte si například, že váš účet má jedno předplatné a aktualizuje se 15. září. Dostanete jednu fakturu na poplatky naúčtované do 15. září. Další fakturu dostanete za období od 15. do 30. září. Od října budete dostávat jednu fakturu měsíčně.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

Další informace o fakturačním účtu najdete v následujících článcích.

- [Principy rolí pro správu pro nový fakturační účet](../manage/understand-mca-roles.md)
- [Vytvoření dalšího předplatného Azure pro nový fakturační účet](../manage/create-subscription.md)
- [Uspořádání nákladů na faktuře pomocí oddílů](../manage/mca-section-invoice.md)