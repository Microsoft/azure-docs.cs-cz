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
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726821"
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

## <a name="next-steps"></a>Další kroky

- Další informace o [cenách](ea-pricing-overview.md)
- Přečtěte si [Nejčastější dotazy týkající se cost management + fakturace](../cost-management-billing-faq.yml) , kde najdete seznam otázek a odpovědí týkajících se služeb Azure Marketplace a předplacení Azure EA.