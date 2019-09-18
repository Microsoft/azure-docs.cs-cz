---
title: Připojení dat Azure Information Protection k Azure Sentinel Preview | Microsoft Docs
description: Naučte se připojit Azure Information Protection data v Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067744"
---
# <a name="connect-data-from-azure-information-protection"></a>Připojení dat z Azure Information Protection

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí konfigurace konektoru Azure Information Protection data můžete streamovat informace o protokolování z [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) do Azure Sentinel. Azure Information Protection vám pomůže řídit a zabezpečovat citlivá data, ať už jsou uložená v cloudu nebo místně.

Pokud je [centrální vytváření sestav pro Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) už nakonfigurované tak, aby se informace protokolování z této služby ukládaly do stejného pracovního prostoru Log Analytics, jako jste teď vybrali pro Azure Sentinel, můžete přeskočit konfiguraci Tento datový konektor. Informace o protokolování z Azure Information Protection jsou již k dispozici pro službu Azure Sentinel.

Pokud se však protokolování informací z Azure Information Protection chystá k jinému pracovnímu prostoru Log Analytics, než který jste právě vybrali pro Azure Sentinel, proveďte jednu z následujících akcí:

- Změňte pracovní prostor vybraný v Azure Sentinel.

- Změňte pracovní prostor pro Azure Information Protection, který můžete provést konfigurací tohoto datového konektoru.
    
    Když změníte pracovní prostor, budou se teď v pracovním prostoru, který používáte pro Azure Sentinel, ukládat data nových sestav pro Azure Information Protection a historická data nejsou dostupná pro Azure Sentinel. Pokud je navíc nakonfigurovaný předchozí pracovní prostor pro vlastní dotazy, výstrahy nebo rozhraní REST API, je potřeba je překonfigurovat pro pracovní prostor Azure Sentinel, pokud ho budete chtít používat pro Azure Information Protection. Pro klienty a služby, které používají Azure Information Protection, není potřeba žádná další konfigurace.

## <a name="prerequisites"></a>Požadavky

- Jedna z následujících rolí správce Azure AD pro vašeho tenanta: Azure Information Protection správce, správce zabezpečení nebo globální správce.
    
    > [!NOTE]
    > Roli správce Azure Information Protection nemůžete použít, pokud je váš tenant na [jednotné platformě pro označování](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).

- Oprávnění ke čtení a zápisu do pracovního prostoru Log Analytics, který používáte pro Sentinel a Azure Information Protection.

- Do Azure Portal byla přidána Azure Information Protection. Pokud potřebujete s tímto krokem pomáhat, přečtěte si téma [přidání Azure Information Protection do Azure Portal](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Připojení k Azure Information Protection

Následující pokyny použijte, pokud jste nenakonfigurovali Log Analytics pracovní prostor pro Azure Information Protection, nebo potřebujete změnit pracovní prostor, ve kterém jsou uloženy informace o protokolování Azure Information Protection. 

1. V Azure Sentinel vyberte **datové konektory**a pak **Azure Information Protection**.

2. V okně **Azure Information Protection** vyberte **otevřít stránku konektoru**.

3. V dalším okně vyberte v části **Konfigurace** možnost **Azure Information Protection** , abyste přešli na **Azure Information Protection Analytics**.

4. V seznamu dostupných pracovních prostorů vyberte pracovní prostor, který aktuálně používáte pro službu Azure Sentinel. Pokud vyberete jiný pracovní prostor, data sestav z Azure Information Protection nebudou k dispozici pro Azure Sentinel.

5. Po výběru pracovního prostoru vyberte **OK** a **stav** konektoru by se teď měl změnit na **připojeno**.

6. Data sestav z Azure Information Protection jsou uložena v tabulce **InformationProtectionLogs_CL** v rámci vybraného pracovního prostoru. 
    
    Pokud chcete pro tato data sestav použít příslušné schéma v Azure Monitor, vyhledejte **InformationProtectionEvents**. Informace o těchto funkcích událostí najdete v části [Popis schématu pro funkce událostí](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) v dokumentaci k Azure Information Protection.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Information Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
