---
title: Připojení Azure Information Protection k Azure Sentinelu
description: Zjistěte, jak propojit data Azure Information Protection v Azure Sentinelu.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588548"
---
# <a name="connect-data-from-azure-information-protection"></a>Připojení dat z Azure Information Protection

> [!IMPORTANT]
> Datový konektor Azure Information Protection v Azure Sentinelu je momentálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Informace o protokolování z Azure Information Protection do Azure [Sentinelu](https://azure.microsoft.com/services/information-protection/) můžete streamovat na konfiguraci datového konektoru Azure Information Protection. Azure Information Protection vám pomůže řídit a zabezpečit vaše citlivá data, ať už jsou uložená v cloudu nebo místně.

Pokud centrální [vytváření sestav pro Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) je již nakonfigurované tak, aby protokolování informací z této služby je uloženve stejném pracovním prostoru Log Analytics, jak jste aktuálně vybrali pro Azure Sentinel, můžete přeskočit konfiguraci tohoto datového konektoru. Informace o protokolování z Azure Information Protection je už k dispozici Azure Sentinel.

Pokud však protokolování informací z Azure Information Protection bude v jiném pracovním prostoru Log Analytics, než který jste aktuálně vybrali pro Azure Sentinel, udělejte jednu z těchto věcí:

- Změňte pracovní prostor vybraný v Azure Sentinelu.

- Změňte pracovní prostor pro Azure Information Protection, což můžete udělat konfigurací tohoto datového konektoru.
    
    Pokud změníte pracovní prostor, nová data sestav pro Azure Information Protection se teď uloží do pracovního prostoru, který používáte pro Azure Sentinel, a historická data nebudou pro Azure Sentinel dostupná. Kromě toho pokud předchozí pracovní prostor je nakonfigurovaný pro vlastní dotazy, výstrahy nebo REST API, musí být překonfigurovánpro pracovní prostor Azure Sentinel, pokud chcete pokračovat v jejich používání pro Azure Information Protection. Pro klienty a služby, které používají Azure Information Protection, není potřeba žádná změna konfigurace.

## <a name="prerequisites"></a>Požadavky

- Jedna z následujících rolí správce Azure AD pro vašeho tenanta: 
    - Správce azure information protection
    - Správce zabezpečení
    - Správce dodržování předpisů
    - Správce dat dodržování předpisů
    - Globální správce
    
    > [!NOTE]
    > Roli správce azure information protection nelze použít, pokud je váš tenant na [platformě jednotného označování](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Tyto role správce jsou vyžadovány jenom pro konfiguraci konektoru Azure Information Protection a nejsou povinné, když je Azure Sentinel připojený k Azure Information Protection.

- Oprávnění ke čtení a zápisu do pracovního prostoru Log Analytics, který používáte pro Azure Sentinel a Azure Information Protection.

- Azure Information Protection byla přidána na portál Azure. Pokud potřebujete pomoc s tímto krokem, najdete [v tématu Přidání Ochrany informací Azure na portál Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Připojení k Azure Information Protection

Pokud jste nenakonfigurovali pracovní prostor Analýzy protokolů pro ochranu informací Azure, použijte následující pokyny nebo potřebujete změnit pracovní prostor, ve které jsou uloženy informace o protokolování služby Azure Information Protection.

1. V Azure Sentinelu vyberte **datové konektory**a pak **Azure Information Protection (Preview)**.

2. Vyberte **Otevřít stránku konektoru**.

3. V okně **Konfigurovat analýzu (preview)** vyberte pracovní prostor, který aktuálně používáte pro Azure Sentinel. Pokud vyberete jiný pracovní prostor, data sestav z Azure Information Protection nebudou pro Azure Sentinel dostupná.

4. Po výběru pracovního prostoru vyberte **ok** a **stav** spojnice by se měl nyní změnit na **Připojeno**.

5. Data sestav z Azure Information Protection se ukládají v **tabulce InformationProtectionLogs_CL** ve vybraném pracovním prostoru. 
    
    Chcete-li pro tato data vytváření sestav použít příslušné schéma v Azure Monitoru, vyhledejte **položku InformationProtectionEvents**. Informace o těchto funkcích událostí najdete v [části Odkaz na přátelské schéma pro funkce událostí](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) v dokumentaci k Azure Information Protection.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Azure Information Protection k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
