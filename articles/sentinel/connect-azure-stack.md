---
title: Připojení virtuálních počítačů s Azure Stack do Azure Sentinel | Microsoft Docs
description: V tomto článku se dozvíte, jak zřídit rozšíření Azure Monitor, Update a Configuration Management pro správu konfigurace na Azure Stack virtuálních počítačích a začít je monitorovat pomocí Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601161"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Připojení virtuálních počítačů s Azure Stack k Azure Sentinel

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Pomocí služby Azure Sentinel můžete monitorovat virtuální počítače běžící v Azure a Azure Stack na jednom místě. Pokud chcete počítače na Azure Stack do služby Azure Sentinel, musíte nejdřív přidat rozšíření virtuálního počítače do stávajících virtuálních počítačů s Azure Stack. 

Po připojení Azure Stack počítače si můžete vybrat z Galerie řídicích panelů, které na základě vašich dat surfují přehledy. Tyto řídicí panely je možné snadno přizpůsobit podle svých potřeb.



## <a name="add-the-virtual-machine-extension"></a>Přidat rozšíření virtuálního počítače 

Do virtuálních počítačů spuštěných v Azure Stack přidejte rozšíření **Azure monitor, Update a Configuration Management** . 

1. Na nové kartě prohlížeče se přihlaste k [portálu Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Přejít na stránku **virtuální počítače** , vyberte virtuální počítač, který chcete chránit pomocí služby Azure Sentinel. Informace o tom, jak vytvořit virtuální počítač na Azure Stack, najdete v tématu Vytvoření virtuálního počítače [s Windows serverem pomocí portálu Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo [Vytvoření virtuálního počítače se systémem Linux pomocí portálu Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Vyberte **Extensions** (Rozšíření). Zobrazí se seznam rozšíření virtuálních počítačů nainstalovaných na tomto virtuálním počítači.
4. Klikněte na kartu **Přidat** . Otevře se okno nabídky **nový prostředek** a zobrazí se seznam dostupných rozšíření virtuálních počítačů. 
5. Vyberte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** a klikněte na **vytvořit**. Otevře se okno **instalovat konfiguraci rozšíření** .

   ![Nastavení správy Azure Monitor, aktualizací a konfigurací](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Pokud nevidíte rozšíření pro **správu Azure monitor, aktualizace a konfigurace** uvedená na webu Marketplace, obraťte se na svého operátora Azure Stack a zpřístupněte ho.

6. V nabídce Sentinel Azure vyberte **Nastavení pracovního prostoru** a pak zkopírujte **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** . 
1. V okně Azure Stack **nainstalovat rozšíření** je vložte do označených polí a klikněte na tlačítko **OK**.
1. Po dokončení instalace rozšíření se jeho stav zobrazí jako **úspěšné zřízení**. Může trvat až jednu hodinu, než se virtuální počítač objeví na portálu Sentinel Azure.

Další informace o instalaci a konfiguraci agenta pro systém Windows najdete v tématu [připojení počítačů se systémem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Informace o řešení problémů agenta pro Linux najdete v tématu [řešení potíží s agentem Azure Log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Na portálu Sentinel Azure v Azure v části **Virtual Machines**máte přehled o všech virtuálních počítačích a počítačích spolu s jejich stavem. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete rozšíření odebrat z virtuálního počítače pomocí portálu Azure Stack.

Odebrání rozšíření:

1. Otevřete **portál Azure Stack**.
2. Na stránce přejít na **virtuální počítače** vyberte virtuální počítač, ze kterého chcete rozšíření odebrat.
3. Vyberte **rozšíření**, vyberte rozšíření **Microsoft. EnterpriseCloud. Monitoring**.
4. Klikněte na **odinstalovat**a potvrďte výběr.

## <a name="next-steps"></a>Další postup

Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- Streamuje data z [běžných chybových formátů](connect-common-event-format.md) do Azure Sentinel.
