---
title: Začínáme s fakturačním účtem pro smlouvu se zákazníkem Microsoftu – Azure
description: Seznamte se se svým fakturačním účtem Smlouvy se zákazníkem Microsoftu, včetně fakturačních profilů a způsobů platby faktur.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 632fac953a54e661164152cda79082aed2c0bc06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690472"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu

Fakturační účet se vytváří při registraci k Azure. Slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Je to například tehdy, když se zaregistrujete v Azure pro práci na svých osobních projektech, ale současně máte k Azure přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet.

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Váš fakturační účet

Váš fakturační účet pro smlouvu se zákazníkem Microsoftu obsahuje jeden nebo několik fakturačních profilů, pomocí nichž můžete spravovat faktury a způsoby platby. Každý fakturační profil obsahuje nejméně jeden oddíl faktury, který vám umožňuje organizovat náklady na faktuře pro tento profil.

Následující diagram ukazuje vztah mezi fakturačním účtem, fakturačními profily a oddíly faktury.

![Diagram znázorňující hierarchii fakturace smlouvy se zákazníkem Microsoftu](./media/mca-overview/mca-billing-hierarchy.png)

Role ve fakturačním účtu mají nejvyšší úroveň oprávnění. Ve výchozím nastavení získává přístup k tomuto účtu jen uživatel, který se zaregistroval v Azure. Role by se měly přidělovat uživatelům, kteří potřebují zobrazovat faktury a sledovat náklady pro celou organizaci, jako jsou manažeři financí nebo IT. Podrobnosti najdete v části [Role a úlohy související s fakturačním účtem](../manage/understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Fakturační profily

Fakturační profil slouží ke správě faktur a způsobů platby. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Ta obsahuje příslušné poplatky za všechna předplatná Azure a další nákupy z předchozího měsíce.

Fakturační profil se pro váš fakturační účet vytváří automaticky. Ve výchozím nastavení obsahuje jeden oddíl faktury. Můžete ale vytvářet další oddíly, které vám umožní snadno sledovat a organizovat náklady podle vašich potřeb, například pro jednotlivé projekty, oddělení nebo vývojová prostředí. Tyto oddíly se zobrazí na faktuře pro fakturační profil a budou odrážet využití jednotlivých předplatných a nákupy, které jste k nim přiřadili.

K rolím ve fakturačních profilech se vážou oprávnění pro zobrazování a správu faktur a způsobů platby. Tyto role můžete přidělit uživatelům, kteří hradí faktury, například členům účetního týmu ve vaší organizaci. Podrobnosti najdete v části [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Měsíční faktura pro každý fakturační profil

Na začátku kalendářního měsíce se pro každý fakturační profil vygeneruje měsíční faktura. Tato faktura obsahuje všechny poplatky za předchozí měsíc.

Zobrazit fakturu, stáhnout dokumenty nebo změnit nastavení tak, aby se vám další faktury zasílaly e-mailem, je možné webu Azure Portal. Podrobnosti najdete v tématu [Stahování faktur pro smlouvu se zákazníkem Microsoftu](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Způsoby platby faktur

Každý fakturační profil má vlastní způsoby plateb, pomocí kterých se hradí příslušné faktury. Podporují se následující způsoby:

| Typ             | Definice  |
|------------------|-------------|
|Kredity Azure    |  Kredity se automaticky použijí pro poplatky, které je možné jejich prostřednictvím pokrýt. Snižuje se tak částka, kterou bude potřeba zaplatit. Podrobnosti najdete v tématu věnovaném [sledování zůstatku kreditů Azure pro fakturační profil](../manage/mca-check-azure-credits-balance.md). |
|Šek nebo bezhotovostní převod | Pokud je na vašem účtu schválená tato možnost, můžete splatnou částku faktury uhradit pomocí šeku nebo převodu. Podrobnosti k platbě jsou uvedené na faktuře. |
|Platební karta | Uživatelé, kteří se zaregistrují v Azure prostřednictvím webu Azure, mohou platit kreditní kartou. |

### <a name="apply-policies-to-control-purchases"></a>Použití zásad pro řízení nákladů

Zásady slouží k řízení nákupů na Azure Marketplace a rezervací Azure prostřednictvím fakturačního profilu. Pomocí těchto zásad můžete nákupy rezervací Azure a produktů Marketplace zakázat. Jakmile je použijete, nebude možné k těmto nákupům používat předplatná účtovaná danému fakturačnímu profilu.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Určování cen a smlouvy o úrovni služeb pro předplatná pomocí plánů Azure

Plány Azure stanovují ceny a smlouvy o úrovni služeb pro předplatná Azure. Při vytvoření fakturačního profilu se povolí automaticky. Využívat je mohou všechny oddíly faktury, které jsou přidružené k fakturačnímu profilu. Uživatelé, kteří mají přístup k oddílu faktury, vytváří pomocí plánů předplatná Azure. Ve fakturačních účtech pro smlouvu se zákazníkem Microsoftu se podporují následující plány Azure:

| Plánování             | Definice  |
|------------------|-------------|
|Plán Microsoft Azure   | Umožňuje uživatelům vytvářet předplatná, ve kterých lze spouštět jakékoli úlohy.  |
|Plán Microsoft Azure pro vývoj a testování | Umožňuje předplatitelům sady Visual Studio vytvářet předplatná omezená jen na úlohy vývoje a testování. Výhodou těchto předplatných jsou nižší sazby a přístup k exkluzivním imagím virtuálních počítačů na webu Azure Portal. |

## <a name="invoice-sections"></a>Oddíly faktury

Oddíly faktury slouží k organizaci nákladů na faktuře. Můžete je vytvářet například tehdy, když potřebujete jednu fakturu pro celou organizaci, na které mají být náklady uspořádané podle oddělení, týmu nebo projektu. V tomto scénáři tak máte jeden fakturační profil, v němž vytváříte oddíl pro každé oddělení, tým a projekt.

Po vytvoření oddílu faktury můžete ostatním uživatelům udělit oprávnění k vytváření předplatných Azure účtovaných do daného oddílu. V něm se pak budou zobrazovat všechny poplatky za využití a nákupy v rámci daných předplatných.

Pro role v oddílu faktury jsou k dispozici oprávnění, pomocí kterých můžete určovat, kdo smí předplatná Azure vytvářet. Role přiřazujete uživatelům, kteří nastavují prostředí Azure pro týmy ve vaší organizaci, například technickým vedoucím nebo architektům. Podrobnosti najdete v části [Role a úlohy související s oddíly faktury](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

Informace o fakturačním účtu najdete v následujících článcích:

- [Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure](../manage/understand-mca-roles.md)
- [Vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu](../manage/create-subscription.md)
- [Uspořádání nákladů na faktuře pomocí oddílů](../manage/mca-section-invoice.md)
