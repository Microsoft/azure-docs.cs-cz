---
title: Cílení řešení monitorování v Azure Monitor | Microsoft Docs
description: Cílení řešení monitorování vám umožní omezit řešení monitorování na konkrétní sadu agentů.  Tento článek popisuje, jak vytvořit konfiguraci oboru a použít ji pro řešení.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 338dc22c7a4f9fbca9f007ae76c092a3fe5f6762
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587240"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Cílení řešení monitorování v Azure Monitor (Preview)
Když přidáte řešení monitorování do předplatného, automaticky se nasadí ve výchozím nastavení na všechny agenty systému Windows a Linux připojené k vašemu pracovnímu prostoru Log Analytics.  Můžete chtít spravovat náklady a omezit množství shromažďovaných dat pro řešení tím, že je omezíte na konkrétní sadu agentů.  Tento článek popisuje, jak používat **cílení na řešení** , což je funkce, která umožňuje použít obor pro vaše řešení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Jak cílit na řešení
Existují tři kroky pro cílení řešení, jak je popsáno v následujících částech. 


### <a name="1-create-a-computer-group"></a>1. Vytvoření skupiny počítačů
Počítače, které chcete zahrnout do oboru, určíte tak, že v Azure Monitor vytvoříte [skupinu počítačů](../logs/computer-groups.md) .  Skupina počítačů může být založená na dotazu protokolu nebo naimportovaná z jiných zdrojů, jako je například služba Active Directory nebo skupiny WSUS. Jak je [popsáno níže](#solutions-and-agents-that-cant-be-targeted), do oboru budou zahrnuty pouze počítače, které jsou přímo připojeny k Azure monitor.

Až budete mít v pracovním prostoru vytvořenou skupinu počítačů, zahrnete ji do konfigurace oboru, kterou je možné použít pro jedno nebo více řešení.
 
 
### <a name="2-create-a-scope-configuration"></a>2. vytvoření konfigurace oboru
 **Konfigurace oboru** zahrnuje jednu nebo více skupin počítačů a lze ji použít pro jedno nebo více řešení. 
 
 Pomocí následujícího procesu vytvořte konfiguraci oboru.  

 1. V Azure Portal přejděte na **Log Analytics pracovní prostory** a vyberte svůj pracovní prostor.
 2. Ve vlastnostech pracovního prostoru v části **zdroje dat pracovního prostoru** vyberte **Konfigurace oboru**.
 3. Kliknutím na **Přidat** vytvořte novou konfiguraci oboru.
 4. Zadejte **název** konfigurace oboru.
 5. Klikněte na **Vybrat skupiny počítačů**.
 6. Vyberte skupinu počítačů, kterou jste vytvořili, a volitelně jakékoli jiné skupiny, které chcete přidat do konfigurace.  Klikněte na **Vybrat**.  
 6. Kliknutím na tlačítko **OK** vytvořte konfiguraci oboru. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. použijte konfiguraci oboru na řešení.
Jakmile budete mít konfiguraci oboru, můžete ji použít pro jedno nebo více řešení.  Všimněte si, že zatímco jedna konfigurace oboru se dá použít s více řešeními, může každé řešení použít jenom jednu konfiguraci oboru.

Použijte konfiguraci oboru pomocí následujícího procesu.  

 1. V Azure Portal přejděte na **Log Analytics pracovní prostory** a vyberte svůj pracovní prostor.
 2. V okně vlastnosti pracovního prostoru vyberte **řešení**.
 3. Klikněte na řešení, které chcete nastavit jako obor.
 4. Ve vlastnostech řešení v části **zdroje dat pracovního prostoru** vyberte **cílení řešení**.  Pokud možnost není k dispozici, [nelze toto řešení cílit](#solutions-and-agents-that-cant-be-targeted).
 5. Klikněte na **Přidat konfiguraci oboru**.  Pokud již máte pro toto řešení nainstalovanou konfiguraci, tato možnost nebude k dispozici.  Před přidáním jiného je nutné odebrat existující konfiguraci.
 6. Klikněte na konfiguraci oboru, kterou jste vytvořili.
 7. Sledujte **stav** konfigurace, abyste se ujistili, že se zobrazila **úspěšná**.  Pokud stav indikuje chybu, klikněte na elipsu napravo od konfigurace a vyberte **Upravit konfiguraci oboru** , aby se změny provedly.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Řešení a agenti, kteří se nedají cílit
Níže jsou uvedena kritéria pro agenty a řešení, která se nedají použít při cílení na řešení.

- Cílení na řešení platí jenom pro řešení, která se nasazují na agenty.
- Cílení na řešení platí jenom pro řešení poskytovaná Microsoftem.  Nevztahuje se na řešení [vytvořená sami nebo partnery](./solutions.md).
- Můžete odfiltrovat jenom agenty, kteří se připojují přímo k Azure Monitor.  Řešení se automaticky nasadí na všechny agenty, kteří jsou součástí propojené skupiny pro správu Operations Manager bez ohledu na to, jestli jsou zahrnuté v konfiguraci oboru.

### <a name="exceptions"></a>Výjimky
Cílení na řešení nelze použít s těmito řešeními, i když splňují zadaná kritéria.

- Posouzení Agent Health

## <a name="next-steps"></a>Další kroky
- Další informace o řešeních monitorování, včetně řešení dostupných pro instalaci ve vašem prostředí, najdete v části [Přidání řešení Azure Log Analytics monitorování do vašeho pracovního prostoru](solutions.md).
- Přečtěte si další informace o vytváření skupin počítačů [ve skupinách počítačů v Azure Monitorch dotazech protokolu](../logs/computer-groups.md).
