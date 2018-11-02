---
title: Cílení na řešení pro správu v Azure | Dokumentace Microsoftu
description: Cílení na řešení pro správu lze omezit řešení pro správu pro konkrétní sadu agentů.  Tento článek popisuje, jak vytvořit konfiguraci oboru a použijte ji pro řešení.
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
ms.openlocfilehash: 1aaa753c91a324621dc66fab23e5fed3a9d11d01
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912761"
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Cílení na řešení pro správu v Azure (Preview)
Když přidáte řešení pro správu k vašemu předplatnému, se automaticky nasadí ve výchozím nastavení pro všechny agenty Windows a Linuxem, připojený k pracovnímu prostoru Log Analytics.  Můžete chtít spravovat náklady a omezit objem dat shromážděných pro řešení podle omezení na konkrétní sadu agentů.  Tento článek popisuje způsob použití **cílení řešení** což je funkce, která vám umožní použít obor do vašich řešení.

## <a name="how-to-target-a-solution"></a>Jak se zaměřit na řešení
Existují tři kroky pro cílení na řešení, jak je popsáno v následujících částech. 


### <a name="1-create-a-computer-group"></a>1. Vytvořit skupinu počítačů
Zadejte počítače, které chcete zahrnout do oboru tak, že vytvoříte [skupinu počítačů](../log-analytics/log-analytics-computer-groups.md) v Log Analytics.  Skupina počítačů můžete podle hledání v protokolu nebo importovat z jiných zdrojů, jako jsou skupiny služby Active Directory nebo služby WSUS. Jako [popisovaném](#solutions-and-agents-that-cant-be-targeted), zahrne jenom počítače, které jsou připojeny přímo k Log Analytics v oboru.

Jakmile máte skupinu počítačů, které jsou vytvořené v pracovním prostoru, budete jej zahrnout v konfiguraci oboru, který lze použít na jeden nebo více řešení.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Vytvořit konfiguraci oboru
 A **konfiguraci oboru** obsahuje jeden nebo více skupin počítačů které můžou uplatnit na jeden nebo více řešení. 
 
 Vytvořte konfiguraci oboru podle následujícího postupu.  

 1. Na webu Azure Portal, přejděte na **Log Analytics** a vyberte svůj pracovní prostor.
 2. Ve vlastnostech pro pracovní prostor v rámci **zdroje dat pracovního prostoru** vyberte **konfigurace oboru**.
 3. Klikněte na tlačítko **přidat** vytvořit novou konfiguraci oboru.
 4. Zadejte **název** pro konfiguraci oboru.
 5. Klikněte na tlačítko **vyberte skupiny počítačů**.
 6. Vyberte skupiny počítačů, kterou jste vytvořili a volitelně případné další skupiny přidejte do konfigurace.  Klikněte na **Vybrat**.  
 6. Klikněte na tlačítko **OK** se vytvořit konfiguraci oboru. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Použijte konfiguraci oboru řešení.
Jakmile se konfigurace oboru, můžete ji použít na jeden nebo více řešení.  Všimněte si, že i když jeden obor konfigurace můžete použít pomocí více řešení, každé řešení lze použít pouze jednu konfiguraci oboru.

Použijte konfiguraci oboru podle následujícího postupu.  

 1. Na webu Azure Portal, přejděte na **Log Analytics** a vyberte svůj pracovní prostor.
 2. V okně vlastností pracovního prostoru vyberte **řešení**.
 3. Klikněte na řešení, které chcete do oboru.
 4. Ve vlastnostech řešení ve složce **zdroje dat pracovního prostoru** vyberte **cílení řešení**.  Pokud možnost není k dispozici pak [nemůžou být cílem tohoto řešení](#solutions-and-agents-that-cant-be-targeted).
 5. Klikněte na tlačítko **Přidat konfiguraci oboru**.  Pokud už máte konfigurace použitý pro toto řešení bude tato možnost není k dispozici.  Je třeba odebrat existující konfiguraci před přidáním jiný.
 6. Klikněte na tlačítko pro konfiguraci oboru, který jste vytvořili.
 7. Podívejte **stav** o konfiguraci a ujistěte se, že zobrazuje **proběhlo úspěšně**.  Pokud stav označuje chybu, pak klikněte na tlačítko se třemi tečkami napravo od požadované konfigurace, vyberte **upravit konfiguraci oboru** provádět změny.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Řešení a agenty, kteří nemůžou být cílem
Toto jsou kritéria pro agenty a řešení, které nemohou být součástí cílení řešení.

- Cílení na řešení platí jenom pro řešení, které nasazujete agenty.
- Cílení na řešení platí jenom pro řešení poskytovaná microsoftem.  Nevztahuje se na řešení [vytvořili sami nebo partnery](monitoring-solutions-creating.md).
- Můžete filtrovat pouze agenty, které se připojují přímo ke službě Log Analytics.  Řešení se automaticky nasadí do všech agentů, které jsou součástí připojené skupiny pro správu nástroje Operations Manager, jestli jsou zahrnuté v konfiguraci oboru.

### <a name="exceptions"></a>Výjimky
Cílení na řešení nelze použít s následující řešení i v případě, že se vešly uvedená kritéria.

- Hodnocení stavu agenta

## <a name="next-steps"></a>Další postup
- Další informace o řešení pro správu řešení, které je možné nainstalovat ve vašem prostředí na včetně [řešení pro správu přidat Azure Log Analytics pro váš pracovní prostor](monitoring-solutions.md).
- Další informace o vytváření skupin počítačů na [prohledávání protokolů skupiny počítačů v Log Analytics](../log-analytics/log-analytics-computer-groups.md).
