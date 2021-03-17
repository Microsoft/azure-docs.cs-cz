---
title: Azure Marketplace
description: Popisuje, jakým způsobem mohou zákazníci EA využívat Azure Marketplace.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 0f2d3c830f27eec9f521e6f79ac8dce3bce818e9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442560"
---
# <a name="azure-marketplace"></a>Azure Marketplace

Tento článek vysvětluje, jak partneři a zákazníci EA mohou zobrazovat poplatky za Marketplace a povolit nákupy na Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace pro zákazníky EA

Přímí zákazníci můžou poplatky za Azure Marketplace zobrazit na portálu Azure Enterprise Portal. Nákupy na Azure Marketplace a související spotřeba se účtují mimo zálohu na Azure, čtvrtletně nebo měsíčně, a to zpětně.

Nepřímí zákazníci najdou svá předplatná Azure Marketplace na stránce **Manage Subscriptions** (Správa předplatných) na portálu Azure Enterprise Portal, ale ceny budou skryté. Pokud zákazníci chtějí získat informace o poplatcích za Azure Marketplace, měli by se obrátit na svého poskytovatele LSP (Licensing Solutions Provider).

Nové měsíčně či ročně se opakující nákupy na Azure Marketplace se plně účtují během období, kdy se položky z Azure Marketplace kupují. Platnost těchto položek se v následujícím období automaticky prodlouží ke stejnému dni, kdy došlo k původnímu nákupu.

Stávající měsíční poplatky se budou i nadále obnovovat k prvnímu dni každého kalendářního měsíce. Roční poplatky se obnoví k datu výročí nákupu.

Některé služby prodejců třetích stran dostupné na Azure Marketplace teď spotřebovávají zůstatek peněžního zálohu na Azure v rámci vaší smlouvy Enterprise (EA). Dříve se tyto služby účtovaly mimo zálohu na Azure EA a fakturovaly se samostatně. Použití zálohy na Azure EA pro tyto služby na Azure Marketplace pomáhá zákazníkům zjednodušit nákup a správu plateb. Úplný seznam služeb, které teď využívají zálohu na Azure, najdete na v [aktualizaci k 6. březnu 2018 na webu Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partneři

Partneři LSP můžou stáhnout ceník Azure Marketplace ze stránky ceníku na portálu Azure Enterprise Portal. V pravém horním rohu vyberte odkaz **Marketplace Price list** (Ceník Marketplace). Ceník Azure Marketplace zobrazuje všechny dostupné služby a jejich ceny.

Stažení ceníku:

1. Na portálu Azure Enterprise Portal přejděte na **Reports** (Sestavy)  >  **Price Sheet** (Ceník).
1. V pravém horním rohu najdete pod svým uživatelským jménem odkaz na ceník Azure Marketplace.
1. Klikněte na odkaz pravým tlačítkem a vyberte **Save Target As** (Uložit cíl jako).
1. V okně **Save** (Uložit) změňte název dokumentu na `AzureMarketplacePricelist.zip`. Tím se soubor .xlsx změní na soubor .zip.
1. Po dokončení stahování budete mít soubor zip s ceníky pro jednotlivé země.
1. Partneři LSP by ke zjištění cen pro konkrétní zemi měli použít soubor pro konkrétní zemi. Partneři LSP můžou použít kartu **Notifications** (Oznámení) k tomu, aby se dozvěděli o skladových položkách, které jsou nové nebo vyřazené.
1. Změny cen se objevují zřídka. Partneři LSP obdrží e-mailová oznámení o zvýšení ceny a devizových změnách 30 dní předem.
1. Partneři LSP obdrží jednu fakturu za každou registraci, každého nezávislého výrobce softwaru a každé čtvrtletí.

### <a name="enabling-azure-marketplace-purchases"></a>Povolení nákupů na Azure Marketplace

Podnikoví správci můžou zakázat nebo povolit nákupy na Azure Marketplace pro všechna předplatná Azure v rámci dané registrace. Pokud podnikový správce zakáže nákupy a nějaká předplatná Azure již obsahují předplatná Azure Marketplace, tato předplatná Azure Marketplace se nezruší, ani to na ně nebude mít žádný vliv.

Přestože zákazníci můžou převést svá přímá předplatná Azure na smlouvu Azure EA tím, že je na portálu Azure Enterprise Portal přidruží ke své registraci, podřízená předplatná se tím automaticky nepřevedou.

Povolení nákupů na Azure Marketplace:

1. Přihlaste se k portálu Azure Enterprise Portal jako podnikový správce.
1. Přejděte do části **Správa**.
1. V části **Enrollment Detail** (Podrobnosti o registraci) vyberte ikonu tužky vedle řádkové položky **Azure Marketplace**.
1. Podle potřeby přepněte přepínač **Enabled/Disabled** (Povoleno/zakázáno) nebo Free **BYOL SKUs Only** (Jen skladové položky Free nebo BYOL).
1. Vyberte **Uložit**.

> [!NOTE]
> Použitím možnosti Jen skladové položky Free nebo BYOL (používání vlastní licence) se omezí nákupy a pořizování skladových položek Azure Marketplace pouze na skladové položky BYOL a Free.

### <a name="services-billed-hourly-for-azure-ea"></a>Služby účtované po hodinách v Azure EA

Následující služby se ve smlouvě Enterprise účtují po hodinách namísto měsíčních sazeb v MOSP:

- Application Delivery Network
- Web Application Firewall (Brána firewall webových aplikací)

### <a name="azure-remoteapp"></a>Azure RemoteApp

Pokud máte smlouvu Enterprise, platíte za službu Azure RemoteApp na základě vaší cenové úrovně smlouvy Enterprise. Neúčtují se žádné další poplatky. Standardní cena zahrnuje počátečních 40 hodin. Neomezená cena pokrývá počátečních 80 hodin. RemoteApp přestane generovat data o využití po uplynutí 80 hodin.

## <a name="azure-marketplace-faq"></a>Nejčastější dotazy k Azure Marketplace

Tato část vysvětluje, jak se záloha na Azure může použít na některé služby prodejců třetích stran na webu Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Co se u služeb Azure Marketplace a zálohy na Azure se smlouvou EA změnilo?

Od 1. března 2018 využívají některé služby prodejců třetích stran zálohu na Azure se smlouvou EA. S výjimkou rezervovaných instancí virtuálního počítače Azure byly služby dříve fakturovány mimo zálohu na Azure se smlouvou EA a účtovaly se samostatně.

Rozšířili jsme možnosti použití zálohy na Azure tak, aby zahrnovaly některé služby publikované na Azure Marketplace třetími stranami, které si naši zákazníci kupují nejčastěji. Použití zálohy na Azure se smlouvou EA pro tyto služby na Azure Marketplace pomáhá zjednodušit nákup a správu plateb.

### <a name="why-did-we-make-this-change"></a>Proč jsme tuto změnu provedli?

Zákazníci neustále hledají další způsoby, jak využít zálohu na Azure hrazenou předem. Zákazníci tuto změnu často vyžadovali a měla vliv na velkou část zákazníků Azure Marketplace.

### <a name="how-do-you-benefit"></a>Jaké přináší výhody?

Fakturace se zjednoduší a budete mít možnost zálohu na Azure se smlouvou EA lépe využít. Vzhledem k tomu, že tyto služby jsou zahrnuté do zálohy na Azure, bude vaše záloha na Azure se smlouvou EA o to cennější.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Které služby Azure Marketplace používají zálohu na Azure se smlouvou EA a jak to zjistím?

Když si koupíte službu, která používá zálohu na Azure, Azure Marketplace zobrazí právní omezení. Podporují se některé služby publikované společnostmi Red Hat, SUSE, Autodesk a Oracle. Podobně pojmenované služby publikované jinými stranami se v současné době od zálohy na Azure neodečtou. Úplný seznam najdete na konci těchto nejčastějších dotazů.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Co když zálohu na Azure se smlouvou EA vyčerpám?

Pokud využijete celou svoji zálohu na Azure a služby využíváte nadlimitně, poplatky související s těmito službami se zobrazí na další faktuře za nadlimitní využití společně se všemi dalšími službami založenými na spotřebě. Před změnou, ke které došlo 1. března 2018, se tyto poplatky fakturovaly s ostatními službami Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Proč zálohu na Azure se smlouvou EA nevyužívají všechny služby Azure Marketplace?

Často pracujeme na tom, abychom zákazníkům v souvislosti se zálohou na Azure se smlouvou EA poskytovali co nejlepší služby. Tato změna se týkala velkého množství zákazníků a významné části celkové útraty na Azure Marketplace. V budoucnu se možná přidají další služby.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jaký to bude mít vliv na nepřímé registrace a partnery?

Na naše zákazníky ani partnery s nepřímou registrací to nebude mít žádný vliv. Na tyto služby se vztahují stejné možnosti přirážky partnera jako na ostatní služby založené na spotřebě. Jedinou změnou je, že se poplatky objevují na jiné faktuře a že platba poplatků pochází ze záloh na Azure se smlouvou EA příslušného zákazníka.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Existuje seznam služeb Azure Marketplace, které využívají zálohu na Azure se smlouvou EA?

Pro konkrétní nabídky Azure Marketplace je možné využít prostředky zálohy na Azure. Úplný seznam produktů zapojených do tohoto programu najdete v tématu týkajícím se [služeb třetích stran využívajících zálohu na Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).


## <a name="next-steps"></a>Další kroky

- Další informace o [cenách](ea-pricing-overview.md)