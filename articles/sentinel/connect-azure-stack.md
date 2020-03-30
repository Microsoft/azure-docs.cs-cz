---
title: Napalubí virtuální počítače Azure Stack do Azure Sentinelu | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak zřídit rozšíření virtuálního počítače Azure Monitor, Aktualizace a správa konfigurace na virtuálních počítačích Azure Stack a začít je sledovat pomocí Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588514"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Připojení virtuálních počítačů Azure Stack k Azure Sentinelu




S Azure Sentinelem můžete monitorovat svoje virtuální počítače spuštěné v Azure a Azure Stacku na jednom místě. Chcete-li napalubě počítače Azure Stack do Azure Sentinelu, musíte nejprve přidat rozšíření virtuálního počítače do vašich stávajících virtuálních počítačů Azure Stack. 

Po připojení počítačů Azure Stack si vyberte z galerie řídicích panelů, které na základě vašich dat poskytují přehledy. Tyto řídicí panely lze snadno přizpůsobit vašim potřebám.



## <a name="add-the-virtual-machine-extension"></a>Přidání rozšíření virtuálního počítače 

Přidejte rozšíření virtuálního počítače **Azure Monitor, Update a Configuration Management** do virtuálních počítačů spuštěných ve vašem Azure Stacku. 

1. Na nové kartě prohlížeče se přihlaste na [portál Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Přejděte na stránku **Virtuální počítače** a vyberte virtuální počítač, který chcete chránit pomocí Azure Sentinelu. Informace o tom, jak vytvořit virtuální počítač v Azure Stacku, najdete [v tématu Vytvoření virtuálního počítače se serverem Windows pomocí portálu Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo Vytvoření [virtuálního počítače s linuxovým serverem pomocí portálu Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Vyberte **Extensions** (Rozšíření). Zobrazí se seznam rozšíření virtuálních strojů nainstalovaných v tomto virtuálním počítači.
4. Klikněte na kartu **Přidat.** Otevře se okno nabídky **Nový prostředek** a zobrazí seznam dostupných rozšíření virtuálních strojů. 
5. Vyberte rozšíření **Azure Monitor, Update a Configuration Management** a klikněte na **Vytvořit**. Otevře se okno konfigurace **rozšíření Instalace.**

   ![Nastavení azure monitoru, aktualizace a správy konfigurace](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Pokud na vašem webu Marketplace nevidíte rozšíření **Azure Monitor, Update and Configuration Management,** obraťte se na operátora Azure Stack, aby ho zpřístupnili.

6. V nabídce Azure Sentinel vyberte **nastavení pracovního prostoru** následované **klávesou Upřesnit**a zkopírujte **ID pracovního prostoru** a klíč **pracovního prostoru (primární klíč).** 
1. V okně **rozšíření Instalace** zásobníku Azure je vložte do označených polí a klikněte na **OK**.
1. Po dokončení instalace rozšíření se jeho stav zobrazí jako **zřizování proběhlo úspěšně**. Může trvat až jednu hodinu, než se virtuální počítač zobrazí na portálu Azure Sentinel.

Další informace o instalaci a konfiguraci agenta systému Windows naleznete v tématu [Připojení počítačů se systémem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Řešení potíží s agenty v Linuxu najdete [v tématu Poradce při potížích s agentem Azure Log Analytics Linux .](../azure-monitor/platform/agent-linux-troubleshoot.md)

Na portálu Azure Sentinel v Azure v části **Virtuální počítače**máte přehled o všech virtuálních počítačích a počítačích spolu s jejich stavem. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Když už nepotřebujete, můžete odebrat rozšíření z virtuálního počítače prostřednictvím portálu Azure Stack.

Odebrání rozšíření:

1. Otevřete **portál zásobníku Azure**.
2. Přejděte na stránku **Virtuální počítače,** vyberte virtuální počítač, ze kterého chcete rozšíření odebrat.
3. Vyberte **Rozšíření**, vyberte rozšíření **Microsoft.EnterpriseCloud.Monitoring**.
4. Klikněte na **Odinstalovat**a potvrďte výběr.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Streamujte data ze [zařízení Common Error Format](connect-common-event-format.md) do Azure Sentinelu.
