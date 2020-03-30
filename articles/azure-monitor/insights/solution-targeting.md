---
title: Řešení monitorování cílení ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Řešení pro monitorování cílení umožňují omezit řešení monitorování na konkrétní sadu agentů.  Tento článek popisuje, jak vytvořit konfiguraci oboru a použít ji na řešení.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663228"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Řešení monitorování cílení ve službě Azure Monitor (Preview)
Když do předplatného přidáte řešení monitorování, ve výchozím nastavení se automaticky nasadí na všechny agenty Windows a Linux připojených k pracovnímu prostoru Log Analytics.  Můžete chtít spravovat náklady a omezit množství dat shromážděných pro řešení tím, že je omezíte na určitou sadu agentů.  Tento článek popisuje, jak používat **cílení na řešení,** což je funkce, která umožňuje použít obor pro vaše řešení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Jak se zaměřit na řešení
Existují tři kroky k cílení řešení, jak je popsáno v následujících částech. 


### <a name="1-create-a-computer-group"></a>1. Vytvoření počítačové skupiny
Počítače, které chcete zahrnout do oboru, určíte vytvořením [skupiny počítačů](../platform/computer-groups.md) v Azure Monitoru.  Skupina počítačů může být založena na dotazu protokolu nebo může být importována z jiných zdrojů, jako jsou skupiny Služby Active Directory nebo Služby WSUS. Jak [je popsáno níže](#solutions-and-agents-that-cant-be-targeted), do oboru budou zahrnuty pouze počítače, které jsou přímo připojené k Azure Monitoru.

Jakmile budete mít skupinu počítačů vytvořenou ve vašem pracovním prostoru, zahrnete ji do konfigurace oboru, kterou lze použít pro jedno nebo více řešení.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Vytvoření konfigurace oboru
 Konfigurace oboru zahrnuje **jednu** nebo více skupin počítačů a lze ji použít pro jedno nebo více řešení. 
 
 Vytvořte konfiguraci oboru pomocí následujícího procesu.  

 1. Na webu Azure Portal přejděte do **pracovních prostorů Log Analytics** a vyberte svůj pracovní prostor.
 2. Ve vlastnostech pracovního prostoru v části **Zdroje dat pracovního prostoru** vyberte Konfigurace **oboru**.
 3. Chcete-li vytvořit novou konfiguraci oboru, klepněte na tlačítko **Přidat.**
 4. Zadejte **název** konfigurace oboru.
 5. Klepněte na **tlačítko Vybrat skupiny počítačů**.
 6. Vyberte skupinu počítačů, kterou jste vytvořili, a volitelně všechny ostatní skupiny, které chcete přidat do konfigurace.  Klepněte na **tlačítko Vybrat**.  
 6. Chcete-li vytvořit konfiguraci oboru, klepněte na tlačítko **OK.** 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Použijte konfiguraci oboru na řešení.
Jakmile máte konfiguraci oboru, můžete ji použít na jedno nebo více řešení.  Všimněte si, že zatímco konfiguraci jednoho oboru lze použít s více řešeními, každé řešení může používat pouze jednu konfiguraci oboru.

Použijte konfiguraci oboru pomocí následujícího procesu.  

 1. Na webu Azure Portal přejděte do **pracovních prostorů Log Analytics** a vyberte svůj pracovní prostor.
 2. Ve vlastnostech pracovního prostoru vyberte **řešení**.
 3. Klikněte na řešení, které chcete obor.
 4. Ve vlastnostech řešení v části **Zdroje dat pracovního prostoru** vyberte Cílení na **řešení**.  Pokud možnost není k dispozici, [nelze na toto řešení cílit](#solutions-and-agents-that-cant-be-targeted).
 5. Klepněte na tlačítko **Přidat konfiguraci oboru**.  Pokud již máte konfiguraci použitou pro toto řešení, pak tato možnost nebude k dispozici.  Před přidáním další konfigurace je nutné odebrat existující konfiguraci.
 6. Klikněte na konfiguraci oboru, kterou jste vytvořili.
 7. Sledujte **stav** konfigurace a ujistěte se, že se zobrazí **Úspěšné**.  Pokud stav označuje chybu, klepněte na elipsu napravo od konfigurace a vyberte **Upravit konfiguraci oboru,** abyste udělali změny.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Řešení a agenti, na která nelze cílit
Níže jsou uvedena kritéria pro agenty a řešení, která nelze použít s cílením na řešení.

- Cílení na řešení se vztahuje pouze na řešení, která se nasazují na agenty.
- Cílení na řešení se vztahuje pouze na řešení poskytovaná společností Microsoft.  Nevztahuje se na řešení [vytvořená vy, která jste vytvořili sami nebo partneři](solutions-creating.md).
- Můžete odfiltrovat jenom agenty, kteří se připojují přímo k Azure Monitoru.  Řešení se automaticky nasadí do všech agentů, kteří jsou součástí připojené skupiny správy nástroje Operations Manager bez ohledu na to, zda jsou zahrnuta v konfiguraci oboru.

### <a name="exceptions"></a>Výjimky
Cílení na řešení nelze použít s následujícími řešeními, i když odpovídají uvedeným kritériím.

- Hodnocení stavu agenta

## <a name="next-steps"></a>Další kroky
- Další informace o řešeních monitorování, včetně řešení, která jsou k dispozici pro instalaci ve vašem prostředí, najdete v [části Přidání řešení monitorování Azure Log Analytics do pracovního prostoru](solutions.md).
- Další informace o vytváření skupin počítačů ve [skupinách počítačů v dotazech protokolu Azure Monitor](../platform/computer-groups.md).
