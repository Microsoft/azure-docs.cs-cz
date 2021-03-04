---
title: Zakázat monitorování v nástroji VM Insights
description: Tento článek popisuje, jak zastavit monitorování virtuálních počítačů ve službě VM Insights.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7eca08abf1ef3bed1aa7fdd806853b94d5615854
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717056"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Zakázání monitorování virtuálních počítačů ve službě VM Insights

Jakmile povolíte monitorování virtuálních počítačů, můžete se později rozhodnout zakázat monitorování v nástroji VM Insights. Tento článek popisuje, jak zakázat monitorování pro jeden nebo více virtuálních počítačů.  

V současné době nepodporuje služba VM Insights selektivní zakázání monitorování virtuálních počítačů. Váš pracovní prostor Log Analytics může podporovat přehledy virtuálních počítačů a další řešení. Může také shromažďovat další data monitorování. Pokud vaše Log Analytics pracovní prostor poskytuje tyto služby, je potřeba pochopit účinek a metody zakázání monitorování před tím, než začnete.

Funkce Cloud Insights využívá k zajištění svého prostředí tyto komponenty:

* Log Analytics pracovní prostor, který ukládá data monitorování z virtuálních počítačů a jiných zdrojů.
* Kolekce čítačů výkonu nakonfigurovaných v pracovním prostoru. Kolekce aktualizuje konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `VMInsights`, což je monitorovací řešení nakonfigurované v pracovním prostoru. Toto řešení aktualizuje konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `MicrosoftMonitoringAgent` a `DependencyAgent` , což jsou rozšíření virtuálních počítačů Azure. Tato rozšíření shromažďují a odesílají data do pracovního prostoru.

Při přípravě na zakázání monitorování virtuálních počítačů mějte na paměti tyto skutečnosti:

* Pokud jste vyhodnotili s jedním virtuálním počítačem a použili jste přednastavené výchozí Log Analytics pracovní prostor, můžete monitorování zakázat odinstalováním agenta závislostí z virtuálního počítače a odpojením agenta Log Analytics z tohoto pracovního prostoru. Tento přístup je vhodný, pokud máte v úmyslu použít virtuální počítač pro jiné účely a později se rozhodnout znovu připojit k jinému pracovnímu prostoru.
* Pokud jste vybrali stávající pracovní prostor Log Analytics, který podporuje jiná řešení monitorování a shromažďování dat z jiných zdrojů, můžete součásti řešení odebrat z pracovního prostoru bez přerušení nebo ovlivnění pracovního prostoru.  

>[!NOTE]
> Po odebrání součástí řešení z pracovního prostoru můžete dál zobrazovat data o výkonu a mapování pro virtuální počítače Azure. Data se nakonec přestanou zobrazovat v zobrazeních **výkon** a **Mapa** . Možnost **Povolit** bude dostupná z vybraného virtuálního počítače Azure, takže v budoucnu můžete monitorování znovu povolit.  

## <a name="remove-vm-insights-completely"></a>Úplné odebrání přehledů z virtuálního počítače

Pokud stále potřebujete Log Analytics pracovní prostor, postupujte podle těchto kroků a úplně odeberte přehledy o virtuálních počítačích. Řešení odeberete `VMInsights` z pracovního prostoru.  

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste zvolili, když jste povolili službu VM Insights.
4. Na levé straně vyberte **řešení**.  
5. V seznamu řešení vyberte **VMInsights (název pracovního prostoru)**. Na stránce **Přehled** pro řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Zakázat monitorování a zachovat pracovní prostor  

Pokud váš pracovní prostor Log Analytics stále potřebuje podporovat monitorování z jiných zdrojů, postupujte podle těchto kroků a zakažte monitorování na virtuálním počítači, který jste použili k vyhodnocení přehledů virtuálních počítačů. Pro virtuální počítače Azure odeberete rozšíření virtuálního počítače agenta závislosti a rozšíření virtuálního počítače Log Analytics agenta pro Windows nebo Linux přímo z virtuálního počítače. 

>[!NOTE]
>Agenta Log Analytics neodstraňujte, pokud: 
>
> * Azure Automation spravuje virtuální počítač pro orchestraci procesů nebo ke správě konfigurace nebo aktualizací. 
> * Azure Security Center spravuje virtuální počítač z důvodu zabezpečení a detekce hrozeb. 
>
> Pokud odeberete agenta Log Analytics, zabráníte tím, aby tyto služby a řešení aktivně spravovaly váš virtuální počítač. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. V Azure Portal vyberte **Virtual Machines**. 
3. V seznamu vyberte virtuální počítač. 
4. Na levé straně vyberte **rozšíření**. Na stránce **rozšíření** vyberte **DependencyAgent**.
5. Na stránce vlastnosti rozšíření vyberte **odinstalovat**.
6. Na stránce **rozšíření** vyberte **MicrosoftMonitoringAgent**. Na stránce vlastnosti rozšíření vyberte **odinstalovat**.  
