---
title: Cílení na řešení pro správu v Azure | Microsoft Docs
description: Cílení na řešení pro správu můžete omezit na konkrétní sadu agentů řešení pro správu.  Tento článek popisuje, jak vytvořit konfiguraci oboru a použít ji k řešení.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 65585e6c09def23101d9735c8b9c719d213938ac
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887843"
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Cílení na řešení pro správu v Azure (Preview)
Když přidáte k předplatnému řešení pro správu, je automaticky nasadí ve výchozím nastavení všechny agenty systému Windows a Linux připojené k pracovní prostor analýzy protokolů.  Můžete spravovat vaše náklady a omezit množství dat vybraných pro řešení omezením na konkrétní sadu agenty.  Tento článek popisuje způsob použití **cílení na řešení** což je funkce, která umožňuje použít obor pro vaše řešení.

## <a name="how-to-target-a-solution"></a>Postupy pro řešení
Existují tři kroky na cílení na řešení, jak je popsáno v následujících částech. 


### <a name="1-create-a-computer-group"></a>1. Vytvořit skupinu počítačů
Zadejte počítače, pro které chcete zahrnout do oboru tak, že vytvoříte [skupinu počítačů](../log-analytics/log-analytics-computer-groups.md) v analýzy protokolů.  Skupina počítačů můžete podle protokolu hledání nebo importovat z jiných zdrojů, například skupin služby Active Directory nebo služby WSUS. Jako [popsané níže](#solutions-and-agents-that-cant-be-targeted), budou zahrnuty pouze počítače, které jsou přímo připojené k analýze protokolů v oboru.

Jakmile budete mít skupina počítačů v pracovním prostoru, pak budete její zahrnutí do konfigurace oboru, který lze použít na jeden nebo více řešení.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Vytvoření konfigurace oboru
 A **konfigurace oboru** obsahuje jeden nebo více skupin počítačů a dají se použít na jeden nebo více řešení. 
 
 Vytvořte obor konfiguraci podle následujícího postupu.  

 1. Na portálu Azure přejděte do **analýzy protokolů** a vyberte pracovní prostor.
 2. Ve vlastnostech prostoru v **zdroje dat pracovního prostoru** vyberte **konfigurace oboru**.
 3. Klikněte na tlačítko **přidat** vytvořit novou konfiguraci oboru.
 4. Zadejte **název** pro konfiguraci rozsahu.
 5. Klikněte na tlačítko **vyberte skupiny počítačů**.
 6. Vyberte skupiny počítačů, který jste vytvořili a volitelně případné další skupiny pro přidání do konfigurace.  Klikněte na **Vybrat**.  
 6. Klikněte na tlačítko **OK** pro vytvoření konfigurace oboru. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Použijete konfiguraci oboru na řešení.
Jakmile je konfigurace oboru, můžete ji použít na jeden nebo více řešení.  Všimněte si, že při konfiguraci jeden obor lze použít s více řešení, každé řešení můžete použít pouze jednu konfiguraci oboru.

Použití konfigurace oboru podle následujícího postupu.  

 1. Na portálu Azure přejděte do **analýzy protokolů** a vyberte pracovní prostor.
 2. Ve vlastnostech pracovním prostoru vyberte **řešení**.
 3. Klikněte na řešení, které chcete obor.
 4. Ve vlastnostech pro řešení v části **zdroje dat pracovního prostoru** vyberte **cílení na řešení**.  Pokud není k dispozici možnost pak [toto řešení nemůžou být cílem](#solutions-and-agents-that-cant-be-targeted).
 5. Klikněte na tlačítko **konfigurace oboru přidat**.  Pokud již máte konfigurace použít k tomuto řešení Tato možnost nebude k dispozici.  Je nutné odebrat existující konfigurace před přidáním jiný.
 6. Klikněte na konfigurace oboru, který jste vytvořili.
 7. Sledování **stav** konfigurace zajistit, že se zobrazí **úspěšné**.  Pokud stav označuje chybu, pak klikněte na tlačítko se třemi tečkami napravo od konfigurace a vyberte **konfiguraci úpravy rozsahu** provádět změny.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Řešení a agenty, kteří nemůžou být cílem
Toto jsou kritéria pro agenty a řešení, které nelze použít s cílem řešení.

- Cílení na řešení se vztahuje pouze na řešení, které nasazujete agenty.
- Cílení na řešení platí jenom pro řešení od společnosti Microsoft.  Nevztahuje se na řešení [vytvořené sami nebo partnery](monitoring-solutions-creating.md).
- Můžete filtrovat pouze na agenty, které se připojují přímo k Log Analytics.  Řešení automaticky nasadí na všechny agenty, které jsou součástí připojené skupiny pro správu nástroje Operations Manager, zda jsou zahrnuté v konfiguraci oboru.

### <a name="exceptions"></a>Výjimky
Cílení na řešení nelze použít s následující řešení i když se vešly uvedená kritéria.

- Vyhodnocení stavu agenta

## <a name="next-steps"></a>Další postup
- Další informace o řešení pro správu včetně řešení, které jsou k dispozici pro instalaci ve vašem prostředí v [přidat Azure Log Analytics řešení pro správu do pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
- Další informace o vytváření skupin počítačů v [skupiny počítačů v analýzy protokolů protokolu hledání](../log-analytics/log-analytics-computer-groups.md).