---
title: Připojit data protokolu syslog do Azure Sentinel Preview | Microsoft Docs
description: Přečtěte si, jak připojit data protokolu syslog ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679210"
---
# <a name="connect-your-external-solution-using-syslog"></a>Připojení externího řešení pomocí protokolu syslog

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete připojit libovolné místní zařízení, které podporuje syslog, do Azure Sentinel. K tomu je potřeba použít agenta založeného na počítači se systémem Linux mezi zařízením a službou Sentinel Azure. Pokud je váš počítač se systémem Linux v Azure, můžete streamovat protokoly z vašeho zařízení nebo aplikace do vyhrazeného pracovního prostoru vytvořeného v Azure a připojit ho. Pokud Váš počítač se systémem Linux není v Azure, můžete streamovat protokoly ze zařízení na vyhrazený místní virtuální počítač nebo počítač, na který instalujete agenta pro Linux. 

> [!NOTE]
> Pokud vaše zařízení podporuje syslog CEF, připojení je více dokončeno a měli byste zvolit tuto možnost a postupovat podle pokynů v tématu [připojení dat z CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to funguje

Syslog je protokol protokolování událostí, které jsou společné pro Linux. Aplikace odešle zprávy, které mohou být uložené na místním počítači nebo doručí do kolekcí Syslog. Pokud je nainstalován agent Log Analytics pro Linux, nakonfiguruje místní démon syslog, aby předal zprávy agentovi. Agent potom zprávu pošle Azure Monitor, kde se vytvoří odpovídající záznam.

Další informace najdete v tématu [zdroje dat syslog v Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> Agent může shromažďovat protokoly z více zdrojů, ale musí být nainstalovaný na vyhrazeném proxy počítači.

## <a name="connect-your-syslog-appliance"></a>Připojení zařízení syslog

1. Na portálu Sentinel Azure vyberte **datové konektory** a v tabulce klikněte na řádek **syslog** a v podokně syslog napravo klikněte na **otevřít stránku konektoru**.
2. Pokud je váš počítač se systémem Linux v rámci Azure, vyberte **Stáhnout a nainstalovat agenta na virtuálním počítači Azure Linux**. V okně virtuální počítače vyberte počítače, na které chcete agenta nainstalovat, a klikněte na **připojit** v horní části.
1. Pokud počítač se systémem Linux v rámci Azure není, vyberte **Stáhnout a nainstalovat agenta na počítač se systémem Linux mimo Azure**. V okně **Direct agent** zkopírujte příkaz v části stáhnout a začlenit **agenta pro Linux** a spusťte ho na svém počítači. 
   > [!NOTE]
   > Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. 

1. V části **Konfigurovat protokoly, které se mají připojit** v okně Nastavení konektoru syslog, postupujte podle pokynů:
    1. Kliknutím na odkaz **Otevřete konfiguraci rozšířeného nastavení pracovního prostoru**. 
    1. Vyberte **data**a potom **syslog**.
    1. Potom v tabulce nastavte, která zařízení mají shromažďovat zprávy syslog. Měli byste buď přidat nebo vybrat zařízení, která vaše zařízení syslog zahrnuje do hlaviček protokolu. Tuto konfiguraci můžete zobrazit v zařízení syslog ve složce syslog-d ve složce:/etc/rsyslog.d/Security-config-omsagent.conf a v r-syslogu v/etc/syslog-ng/Security-config-omsagent.conf. 
       > [!NOTE]
       > Pokud zaškrtnete políčko, které se má u **počítačů použít**, bude tato konfigurace platit pro všechny počítače se systémem Linux připojené k tomuto pracovnímu prostoru. Tuto konfiguraci můžete zobrazit v počítači syslog pod 
1. Kliknutím **sem otevřete okno Konfigurace**.
1. Vyberte **data** a pak **syslog**.
   - Ujistěte se, že všechny zařízení, které odesíláte pomocí protokolu syslog, jsou v tabulce. Pro každé zařízení budete monitorovat, nastavit závažnost. Klikněte na tlačítko **Použít**.
1. V počítači syslog se ujistěte, že posíláte tato zařízení. 

1. Chcete-li použít příslušné schéma v Log Analytics pro protokoly syslog, vyhledejte protokol **SYSLOG**.
1. Pomocí funkce Kusto popsané v tématu [použití funkcí v Azure Monitorch dotazech protokolu](../azure-monitor/log-query/functions.md) můžete analyzovat zprávy syslog a pak je uložit jako novou funkci Log Analytics a pak funkci použít jako nový datový typ.




## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit místní zařízení syslog ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
