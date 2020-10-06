---
title: Přehled rozhraní API služby Azure Consumption
description: Přečtěte si o rozhraní API služby Azure Consumption, které poskytuje programový přístup k informacím o nákladech a využití pro vaše prostředky Azure.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 07595eed70850ce5270a261d588a33b68ff964fe
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371928"
---
# <a name="azure-consumption-api-overview"></a>Přehled rozhraní API služby Azure Consumption

Rozhraní API služby Azure Consumption poskytuje programový přístup k informacím o nákladech a využití pro vaše prostředky Azure. Tato rozhraní API aktuálně podporují jenom smlouvy Enterprise a předplatná Web Direct (s několika výjimkami). Rozhraní API se průběžně aktualizují, aby podporovala i jiné typy předplatných Azure.

Rozhraní API služby Azure Consumption poskytuje přístup pro:
- Podnikoví a weboví přímí zákazníci
    - Podrobnosti využití
    - Poplatky za marketplace
    - Doporučení pro rezervace
    - Podrobnosti rezervace
    - Přehledy rezervace
- Jenom pro podnikové zákazníky
    - Ceník
    - Rozpočty
    - Zůstatky

## <a name="usage-details-api"></a>Rozhraní API Podrobnosti využití

Rozhraní API Podrobnosti využití slouží k získání informací o poplatcích za všechny prostředky Azure od první strany a jejich využití. Informace mají podobu podrobných záznamů o využití, které se v současné době vystavují jednou denně pro každý měřič a prostředek. Tyto informace se dají využít k přiřazování nákladů napříč všemi prostředky nebo ke zkoumání nákladů/využití u konkrétních prostředků.

Rozhraní API nabízí tyto funkce:

-   **Data spotřeby na úrovni měřiče** – můžete zobrazit data včetně nákladů na využití, měřiče, který poplatek vystavil, a prostředků Azure, kterých se poplatek týká. Všechny záznamy údajů o využití se mapují na denní interval.
-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Filtrování** – sadu výsledků v rozhraní API můžete pomocí následujících filtrů omezit a získat menší sadu záznamů s údaji o využití:
    - Konec využívání / začátek využívání
    - Skupina prostředků
    - Název prostředku
-   **Agregace dat** – pomocí protokolu OData můžete používat výrazy k agregaci údajů o využití podle značek nebo vlastností filtru.
-   **Využití pro různé typy nabídek** – podrobné informace o využití jsou aktuálně dostupné zákazníkům programů Enterprise a Web Direct.

Další informace najdete v technické specifikaci [rozhraní API Podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Rozhraní API Poplatky za Marketplace

Rozhraní API Poplatky za Marketplace slouží k získání informací o poplatcích a využití pro všechny prostředky z Marketplace (nabídky třetích stran v Azure). Tato data se dají využít k přiřazování nákladů napříč všemi prostředky z Marketplace nebo ke zkoumání nákladů/využití u konkrétních prostředků.

Rozhraní API nabízí tyto funkce:

-   **Data spotřeby na úrovni měřiče** – můžete zobrazit data včetně nákladů na využití Marketplace, měřiče, který poplatek vystavil, a prostředků, kterých se poplatek týká. Všechny záznamy údajů o využití se mapují na denní interval.
-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Filtrování** – sadu výsledků v rozhraní API můžete pomocí následujících filtrů omezit a získat menší sadu záznamů týkajících se Marketplace:
    - Začátek využívání / konec využívání
    - Skupina prostředků
    - Název prostředku
-   **Využití pro různé typy nabídek** – informace o Marketplace jsou aktuálně dostupné zákazníkům programů Enterprise a Web Direct.

Další informace najdete v technické specifikaci [rozhraní API Poplatky za Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Rozhraní API Zůstatky

Zákazníci se smlouvou Enterprise můžou pomocí rozhraní API Zůstatky získat měsíční přehled informací o zůstatcích, nových nákupech, poplatcích za službu Azure Marketplace, úpravách a poplatcích za nadlimitní využití. Tyto informace se můžou týkat aktuálního fakturačního období nebo libovolného minulého období. Podniky můžou tato data využít k porovnání s ručně vypočítanými souhrnnými poplatky. Toto rozhraní API neposkytuje informace o konkrétních prostředcích ani agregované zobrazení nákladů.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Jenom zákazníci se smlouvou Enterprise** – toto rozhraní API je dostupné jenom zákazníkům se smlouvou Enterprise.
    - K volání tohoto rozhraní API potřebují zákazníci oprávnění správce smlouvy Enterprise.

Další informace najdete v technické specifikaci [rozhraní API Zůstatky](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Rozhraní API Rozpočty

Zákazníci se smlouvou Enterprise můžou pomocí tohoto rozhraní API vytvářet rozpočty nákladů nebo využití pro prostředky, skupiny prostředků nebo měřiče účtování. Po zjištění těchto informací je možné nastavit výstrahy, které budou upozorňovat na překročení uživatelsky definovaných rozpočtových prahů.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Jenom zákazníci se smlouvou Enterprise** – toto rozhraní API je dostupné jenom zákazníkům se smlouvou Enterprise.
-   **Konfigurovatelná oznámení** – je možné určit uživatele, který dostane oznámení o překročení rozpočtu.
-   **Rozpočty na základě využití nebo nákladů** – rozpočet je možné vytvořit buď na základě spotřeby, nebo na základě nákladů, podle toho, co je v daném scénáři vhodnější.
-   **Filtrování** – rozpočet je možné filtrovat pomocí nastavitelných filtrů a zobrazit menší podmnožinu prostředků.
    - Skupina prostředků
    - Název prostředku
    - Měřič
-   **Konfigurovatelná období rozpočtu** – můžete určit, jak často se má rozpočet obnovovat a jak dlouho je rozpočet platný.

Další informace najdete v technické specifikaci [rozhraní API Rozpočty](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Rozhraní API Doporučení pro rezervace

Pomocí tohoto rozhraní API můžete získat doporučení pro nákup rezervovaných instancí virtuálních počítačů. Doporučení jsou sestavená tak, aby umožňovala zákazníkům analyzovat očekávané úspory nákladů a kupní ceny.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Filtrování** – výsledky doporučení můžete přizpůsobit pomocí následujících filtrů:
    - Rozsah
    - Období pro zpětné hodnocení
-   **Informace o rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně dostupné zákazníkům programů Enterprise a Web Direct.

Další informace najdete v technické specifikaci [rozhraní API Doporučení pro rezervace](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Rozhraní API Podrobnosti rezervace

Pomocí rozhraní API Podrobnosti rezervace můžete zobrazit informace o dříve zakoupených rezervacích virtuálních počítačů, například množství rezervované spotřeby v porovnání se skutečně využívaným množstvím. Data můžete zobrazit na úrovni konkrétních virtuálních počítačů.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Filtrování** – sadu výsledků v rozhraní API můžete pomocí následujících filtrů omezit a získat menší sadu rezervací:
    - Rozsah dat
-   **Informace o rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně dostupné zákazníkům programů Enterprise a Web Direct.

Další informace najdete v technické specifikaci [rozhraní API Podrobnosti rezervace](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Rozhraní API Přehledy rezervace

Pomocí tohoto rozhraní API můžete zobrazit souhrnné informace o dříve zakoupených rezervacích virtuálních počítačů, například množství rezervované spotřeby v porovnání se souhrnem skutečně využívaného množství.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Filtrování** – výsledky na úrovni denních podrobností můžete přizpůsobit pomocí následujícího filtru:
    - Datum využití
-   **Informace o rezervacích pro různé typy nabídek** – informace o rezervacích jsou aktuálně dostupné zákazníkům programů Enterprise a Web Direct.
-   **Denní nebo měsíční agregace** – volající můžou určit, jestli chtějí zobrazit souhrnná data rezervace za den, nebo za měsíc.

Další informace najdete v technické specifikaci [rozhraní API Přehledy rezervace](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Rozhraní API Ceník
Zákazníci se smlouvou Enterprise můžou pomocí tohoto rozhraní API načíst vlastní ceny pro všechny měřiče. Podniky můžou tyto údaje v kombinaci s informacemi o využití a informacemi o využití Marketplace použít k výpočtu nákladů na základě dat o využití a Marketplace.

Rozhraní API nabízí tyto funkce:

-   **Řízení přístupu na základě role v Azure (Azure RBAC)** – na webu [Azure Portal](https://portal.azure.com), v rozhraní [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) nebo v [rutinách Azure PowerShell](https://docs.microsoft.com/powershell/azure/) se dají nakonfigurovat zásady přístupu určující, kteří uživatelé nebo aplikace můžou získat přístup k údajům o využití pro dané předplatné. Volající musí k ověření používat standardní tokeny Azure Active Directory. Přidejte volajícího do role Čtenář fakturace, Čtenář, Vlastník nebo Přispěvatel, aby získal přístup k datům využití pro určité předplatné Azure.
-   **Jenom zákazníci se smlouvou Enterprise** – toto rozhraní API je dostupné jenom zákazníkům se smlouvou Enterprise. Zákazníkům Web Direct doporučujeme ke zjištění cen používat rozhraní API RateCard.

Další informace najdete v technické specifikaci [rozhraní API Ceník](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scénáře

Tady jsou některé ze scénářů, ve kterých je možné využít rozhraní API služby Consumption:

-   **Sesouhlasení faktur** – naúčtoval mi Microsoft správnou částku?  Jaká je výše mé faktury a můžu ji vypočítat vlastními silami?
-   **Přiřazení poplatků** – když teď znám výši své faktury, kdo v mé organizaci ji má zaplatit?
-   **Optimalizace nákladů** – znám výši fakturované částky… Jak můžu peníze, které utrácím za Azure, využít efektivněji?
-   **Sledování nákladů** – chci zjistit, kolik utrácím a jak moc v průběhu času využívám Azure. Jaké jsou trendy? Co můžu dělat lépe?
-   **Náklady na Azure v průběhu měsíce** – jaké jsou moje dosavadní náklady za aktuální měsíc? Musím ve své útratě nebo využití Azure dělat nějaké změny? V kterém období měsíce využívám Azure nejvíc?
-   **Nastavení výstrah** – chci si nastavit výstrahy týkající se spotřeby podle prostředků nebo částky na základě rozpočtu.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak prostřednictvím rozhraní API Azure pro fakturaci získat pomocí kódu programu přehled o využití Azure, najdete v tématu [Přehled rozhraní API Azure pro fakturaci](usage-rate-card-overview.md).
