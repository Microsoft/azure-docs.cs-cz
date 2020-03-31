---
title: Zakázání monitorování ve službě Azure Monitor pro virtuální počítače
description: Tento článek popisuje, jak zastavit monitorování virtuálních počítačů v Azure Monitor u virtuálních počítačů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480517"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Zakázání monitorování virtuálních počítačů ve službě Azure Monitor pro virtuální počítače

Po povolení monitorování vašich virtuálních počítačů (virtuálnípočítače), můžete později zvolit zakázat monitorování ve službě Azure Monitor pro virtuální počítače. Tento článek ukazuje, jak zakázat monitorování pro jeden nebo více virtuálních stránek.  

V současné době Azure Monitor pro virtuální počítače nepodporuje selektivní zakázání monitorování virtuálních her. Pracovní prostor Log Analytics může podporovat Azure Monitor pro virtuální počítače a další řešení. Může také shromažďovat další data monitorování. Pokud váš pracovní prostor Log Analytics poskytuje tyto služby, musíte pochopit efekt a metody zakázání monitorování před zahájením.

Azure Monitor pro virtuální počítače spoléhá na následující součásti, které poskytují své zkušenosti:

* Pracovní prostor Log Analytics, který ukládá data monitorování z virtuálních aplikací a dalších zdrojů.
* Kolekce čítačů výkonu nakonfigurovaných v pracovním prostoru. Kolekce aktualizuje konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `VMInsights`, což je monitorovací řešení nakonfigurované v pracovním prostoru. Toto řešení aktualizuje konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `MicrosoftMonitoringAgent`a `DependencyAgent`, které jsou rozšíření virtuálních počítačích Azure. Tato rozšíření shromažďovat a odesílat data do pracovního prostoru.

Při přípravě na zakázání monitorování virtuálních počítačů mějte na paměti tyto důležité informace:

* Pokud jste vyhodnotili pomocí jednoho virtuálního počítače a použili předvolený výchozí pracovní prostor Log Analytics, můžete zakázat monitorování odinstalováním agenta závislostí z virtuálního počítači a odpojením agenta Analýzy protokolů od tohoto pracovního prostoru. Tento přístup je vhodný, pokud máte v úmyslu použít virtuální hod pro jiné účely a později se rozhodnete znovu připojit k jinému pracovnímu prostoru.
* Pokud jste vybrali již existující pracovní prostor Log Analytics, který podporuje jiná řešení monitorování a shromažďování dat z jiných zdrojů, můžete odebrat součásti řešení z pracovního prostoru bez přerušení nebo ovlivnění pracovního prostoru.  

>[!NOTE]
> Po odebrání součástí řešení z pracovního prostoru můžete nadále zobrazit data o výkonu a mapování pro virtuální počítače Azure. Data se nakonec přestanou zobrazovat v **zobrazeních Výkon** a **Mapa.** Možnost **Povolit** bude dostupná z vybraného virtuálního počítače Azure, takže v budoucnu můžete monitorování znovu povolit.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Úplně odebrat Azure Monitor pro virtuální počítače

Pokud stále potřebujete pracovní prostor Log Analytics, postupujte takto a úplně odeberte Azure Monitor pro virtuální počítače. `VMInsights` Odeberete řešení z pracovního prostoru.  

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Při psaní seznam filtruje návrhy na základě vašeho vstupu. Vyberte **Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste zvolili, když jste povolili Azure Monitor pro virtuální počítače.
4. Vlevo vyberte **Řešení**.  
5. V seznamu řešení vyberte **VMInsights (název pracovního prostoru).** Na stránce **Přehled** řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **možnost Ano**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Zakázání monitorování a zachování pracovního prostoru  

Pokud váš pracovní prostor Log Analytics stále potřebuje podporovat monitorování z jiných zdrojů, postupujte podle následujících kroků zakázat monitorování na virtuálním počítači, který jste použili k vyhodnocení Azure Monitor pro virtuální počítače. Pro virtuální počítače Azure odeberete rozšíření virtuálního počítače agenta závislostí a rozšíření virtuálního počítače agenta Log Analytics pro Windows nebo Linux přímo z virtuálního počítače. 

>[!NOTE]
>Neodstraňujte agenta Analýzy protokolů, pokud: 
>
> * Azure Automation spravuje virtuální počítač k orchestraci procesů nebo ke správě konfigurace nebo aktualizací. 
> * Azure Security Center spravuje virtuální počítač pro zabezpečení a detekci hrozeb. 
>
> Pokud odeberete agenta Log Analytics, zabráníte těmto službám a řešením v proaktivní správě virtuálního počítače. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Na webu Azure Portal vyberte **Virtuální počítače**. 
3. V seznamu vyberte virtuální počítač. 
4. Vlevo vyberte **Rozšíření**. Na stránce **Rozšíření** vyberte **DependencyAgent**.
5. Na stránce vlastností rozšíření vyberte **Odinstalovat**.
6. Na stránce **Rozšíření** vyberte **MicrosoftMonitoringAgent**. Na stránce vlastností rozšíření vyberte **Odinstalovat**.  
