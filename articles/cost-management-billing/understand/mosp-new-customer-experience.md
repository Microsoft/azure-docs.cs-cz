---
title: Začínáme s aktualizovaným fakturačním účtem Azure
description: Začněte pracovat s aktualizovaným fakturačním účtem Azure a seznamte se se změnami v novém prostředí pro fakturaci a správu nákladů.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 7bec455b804d1f4b13ab7e13677092077214a121
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965846"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Začínáme s aktualizovaným fakturačním účtem Azure

Správa nákladů a faktur je jednou z klíčových součástí cloudového prostředí. Pomůže vám mít útraty v cloudu pod kontrolou a porozumět jí. Pro usnadnění správy nákladů a faktur teď Microsoft aktualizuje váš fakturační účet Azure, aby zahrnoval vylepšené funkce pro správu nákladů a fakturaci. Tento článek popisuje aktualizace vašeho fakturačního účtu a provede vás novými funkcemi.

> [!IMPORTANT]
> Váš účet se aktualizuje, když od Microsoftu obdržíte e-mail s upozorněním na aktualizace vašeho účtu. Toto oznámení se pošle 60 dní před tím, než se váš účet aktualizuje.

## <a name="more-flexibility-with-your-new-billing-account"></a>Více flexibility s novým fakturačním účtem

Následující diagram porovnává starý a nový fakturační účet:

![Diagram znázorňující porovnání hierarchie fakturace ve starém a novém účtu](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Váš fakturační účet obsahuje jeden nebo několik fakturačních profilů, pomocí nichž můžete spravovat faktury a způsoby platby. Každý fakturační profil obsahuje nejméně jeden oddíl faktury, který vám umožňuje organizovat náklady na faktuře pro tento profil.

![Diagram znázorňující novou fakturační hierarchii](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

Role ve fakturačním účtu mají nejvyšší úroveň oprávnění. Tyto role by se měly přiřazovat uživatelům, kteří potřebují zobrazovat faktury a sledovat náklady pro celý účet, jako jsou manažeři financí nebo IT v organizaci nebo jednotlivec, který si účet zaregistroval. Podrobnosti najdete v části [Role a úlohy související s fakturačním účtem](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Po aktualizaci účtu se uživateli, který má ve starém fakturačním účtu roli správce účtu, pro nový účet poskytne role vlastníka.

## <a name="billing-profiles"></a>Fakturační profily

Fakturační profil slouží ke správě faktur a způsobů platby. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Faktura obsahuje příslušné poplatky z předchozího měsíce za všechna předplatná přidružená k fakturačnímu účtu.

Po aktualizaci účtu se pro každé předplatné automaticky vytvoří fakturační profil. Poplatky za předplatné se naúčtují příslušnému fakturačnímu profilu a zobrazují se na jeho faktuře.

K rolím ve fakturačních profilech se vážou oprávnění pro zobrazování a správu faktur a způsobů platby. Tyto role by se měly přiřazovat uživatelům, kteří hradí faktury, například členům účetního týmu ve vaší organizaci. Podrobnosti najdete v části [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). 

Po aktualizaci účtu se pro každé předplatné, pro které jste ostatním poskytli oprávnění [zobrazovat faktury](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice), uživatelům, kteří mají roli vlastníka, přispěvatele, čtenáře nebo čtenáře fakturace Azure, poskytne pro příslušný fakturační profil role čtenáře.

## <a name="invoice-sections"></a>Oddíly faktury

Oddíly faktury slouží k uspořádání nákladů na faktuře. Můžete je vytvářet například tehdy, když potřebujete jednu fakturu, na které mají být náklady uspořádané podle oddělení, týmu nebo projektu. V tomto scénáři tak máte jeden fakturační profil, v němž vytváříte oddíl pro každé oddělení, tým a projekt.

Po aktualizaci účtu se vytvoří oddíl faktury pro každý fakturační profil a v oddílu faktury se přiřadí příslušné předplatné. Když přidáte další předplatná, můžete vytvořit další oddíly a přiřadit předplatná k oddílům faktury. Tyto oddíly se zobrazí na faktuře pro fakturační profil a budou odrážet využití jednotlivých předplatných, které jste k nim přiřadili.

Pro role v oddílu faktury jsou k dispozici oprávnění, pomocí kterých můžete určovat, kdo smí předplatná Azure vytvářet. Tyto role by se měly přiřazovat uživatelům, kteří nastavují prostředí Azure pro týmy ve vaší organizaci, například technickým vedoucím nebo architektům. Podrobnosti najdete v části [Role a úlohy související s oddíly faktury](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Vylepšené funkce ve vašem novém prostředí

Vaše nové prostředí zahrnuje následující možnosti fakturace a správy nákladů, které usnadňují správu faktur a nákladů:

#### <a name="invoice-management"></a>Správa faktur

**Předvídatelnější měsíční fakturační období:** V novém účtu začíná fakturační období prvním dnem v měsíci a končí posledním dnem v měsíci, a to bez ohledu na to, kdy jste se k použití Azure zaregistrovali. Faktura se vygeneruje na začátku každého měsíce a bude obsahovat všechny poplatky za předchozí měsíc.

**Jedna měsíční faktura pro několik předplatných:** Máte možnost dostávat měsíční fakturu pro každé předplatné, nebo jednu fakturu pro několik předplatných.

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

**Vytváření dalších předplatných přímo na webu Azure Portal:** Na webu Azure Portal můžete předplatná vytvářet jedním kliknutím.

#### <a name="api-support"></a>Podpora rozhraní API

**Provádění operací správy nákladů a fakturace prostřednictvím rozhraní API, sady SDK a PowerShellu:** Rozhraní API pro spotřebu, fakturaci a správu nákladů můžete využít k přetáhnutí fakturačních dat a údajů o nákladech do nástrojů pro analýzu dat, kterým dáváte přednost.

**Provádění všech operací s předplatnými prostřednictvím rozhraní API, sady SDK a PowerShellu:** Rozhraní API předplatných Azure můžete využít k automatizaci správy předplatných Azure, včetně vytvoření, přejmenování a zrušení předplatného.

## <a name="get-prepared-for-your-new-experience"></a>Zajištění připravenosti na nové prostředí

V rámci přípravy na nové prostředí doporučujeme následující:

**Měsíční fakturační období a jiné datum faktury**

V novém prostředí se vaše faktura vygeneruje přibližně devátého dne v měsíci a bude obsahovat všechny poplatky za předchozí měsíc. Toto datum se může lišit od data, kdy se faktury generovaly ve starém účtu. Pokud faktury sdílíte s ostatními, upozorněte je na změnu data.

**Nová rozhraní API pro správu nákladů a fakturaci**

Pokud k dotazování nebo aktualizaci fakturačních dat nebo údajů o využití používáte rozhraní API pro fakturaci nebo Cost Management, musíte použít nová rozhraní API. Následující tabulka uvádí rozhraní API, která nefungují s novým fakturačním účtem, a změny, které musíte v novém fakturačním účtu udělat.

|Rozhraní API | Změny  |
|---------|---------|
|[Fakturační účty – seznam](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | V rozhraní API pro fakturační účty – seznam má starý fakturační účet jako typ smlouvy nastavený **MicrosoftOnlineServiceProgram**, v novém fakturačním účtu bude nastavený typ smlouvy **MicrosoftCustomerAgreement**. Pokud využíváte závislost na typu smlouvy, proveďte prosím příslušnou aktualizaci. |
|[Faktury – seznam podle předplatného pro fakturaci](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Toto rozhraní API vrátí jenom faktury, které se vygenerovaly před aktualizací vašeho účtu. Pokud chcete získat faktury vygenerované v novém fakturačním účtu, musíte použít rozhraní API pro [faktury seznam podle fakturačního účtu](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |

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