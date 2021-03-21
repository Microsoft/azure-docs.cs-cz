---
title: Připojit Azure Information Protection k Azure Sentinel
description: Konfigurace streamování informací z Azure Information Protection do Azure Sentinel konfigurací konektoru Azure Information Protection data Connector.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655982"
---
# <a name="connect-data-from-azure-information-protection"></a>Připojení dat z Azure Information Protection

> [!IMPORTANT]
> Konektor dat Azure Information Protection v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí konfigurace konektoru Azure Information Protection data můžete streamovat informace o protokolování z [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) do Azure Sentinel. Azure Information Protection vám pomůže řídit a zabezpečovat citlivá data, ať už jsou uložená v cloudu nebo místně.

Pokud je [centrální vytváření sestav pro Azure Information Protection](/azure/information-protection/reports-aip) už nakonfigurované tak, aby se informace protokolování z této služby ukládaly do stejného pracovního prostoru Log Analytics, jako jste teď vybrali pro Azure Sentinel, můžete přeskočit konfiguraci tohoto datového konektoru. Informace o protokolování z Azure Information Protection jsou již k dispozici pro službu Azure Sentinel.

Pokud se však protokolování informací z Azure Information Protection chystá k jinému pracovnímu prostoru Log Analytics, než který jste právě vybrali pro Azure Sentinel, proveďte jednu z následujících akcí:

- Změňte pracovní prostor vybraný v Azure Sentinel.

- Změňte pracovní prostor pro Azure Information Protection, který můžete provést konfigurací tohoto datového konektoru.
    
    Když změníte pracovní prostor, budou se teď v pracovním prostoru, který používáte pro Azure Sentinel, ukládat data nových sestav pro Azure Information Protection a historická data nejsou dostupná pro Azure Sentinel. Pokud je navíc nakonfigurovaný předchozí pracovní prostor pro vlastní dotazy, výstrahy nebo rozhraní REST API, je potřeba je překonfigurovat pro pracovní prostor Azure Sentinel, pokud ho budete chtít používat pro Azure Information Protection. Pro klienty a služby, které používají Azure Information Protection, není potřeba žádná další konfigurace.

## <a name="prerequisites"></a>Předpoklady

- Jedna z následujících rolí správce Azure AD pro vašeho tenanta: 
    - Správce Azure Information Protection
    - Správce zabezpečení
    - Správce dodržování předpisů
    - Správce dat dodržování předpisů
    - Globální správce
    
    > [!NOTE]
    > Roli správce Azure Information Protection nemůžete použít, pokud je váš tenant na [jednotné platformě pro označování](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Tyto role správců se vyžadují jenom při konfiguraci konektoru Azure Information Protection a nevyžadují se, když je Azure Sentinel připojený k Azure Information Protection.

- Oprávnění ke čtení a zápisu do pracovního prostoru Log Analytics, který používáte pro Azure Sentinel a Azure Information Protection.

- Do Azure Portal byla přidána Azure Information Protection. Pokud potřebujete s tímto krokem pomáhat, přečtěte si téma [přidání Azure Information Protection do Azure Portal](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Připojení k Azure Information Protection

Následující pokyny použijte, pokud jste nenakonfigurovali Log Analytics pracovní prostor pro Azure Information Protection, nebo potřebujete změnit pracovní prostor, ve kterém jsou uloženy informace o protokolování Azure Information Protection.

1. V Azure Sentinel vyberte **datové konektory**  >  **Azure Information Protection (Preview)**.

2. Vyberte **stránku otevřít konektor**.

3. V části **Konfigurace** vyberte **připojit Azure Information Protection protokoly**.

4. V okně **Konfigurace analýzy (Preview)** vyberte pracovní prostor, který aktuálně používáte pro službu Azure Sentinel. Pokud vyberete jiný pracovní prostor, data sestav z Azure Information Protection nejsou k dispozici pro službu Azure Sentinel.

5. Po výběru pracovního prostoru vyberte **OK**. **Stav** konektoru se změní na **připojeno**.

6. Data sestavy z Azure Information Protection jsou uložena v tabulce **InformationProtectionLogs_CL** ve vybraném pracovním prostoru. 
    
    Pokud chcete pro tato data sestav použít příslušné schéma v Azure Monitor, vyhledejte **InformationProtectionEvents**. Informace o těchto funkcích událostí najdete v části [Popis schématu pro funkce událostí](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) v dokumentaci k Azure Information Protection.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Information Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).