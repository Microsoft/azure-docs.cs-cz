---
title: Připojení virtuálních počítačů centra Azure Stack ke službě Azure Sentinel | Microsoft Docs
description: V tomto článku se dozvíte, jak zřídit rozšíření Azure Monitor, Update a Configuration Management pro správu konfigurace na virtuálních počítačích centra Azure Stack a začít je monitorovat pomocí funkce Sentinel Azure.
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
ms.openlocfilehash: 120f160e3c7bc7a9a0e81586ea4b97ee827dd33e
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807424"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Připojení virtuálních počítačů služby Azure Stack hub k Azure Sentinel

Pomocí služby Azure Sentinel můžete monitorovat virtuální počítače běžící na Azure a centra Azure Stack na jednom místě. Pokud chcete počítače na Azure Stack do služby Azure Sentinel, musíte nejdřív přidat rozšíření virtuálního počítače do stávajících virtuálních počítačů Azure Stack hub. 

Po připojení Azure Stack hub vyberte z Galerie řídicích panelů, které na základě vašich dat surfují přehledy. Tyto řídicí panely je možné snadno přizpůsobit podle svých potřeb.

## <a name="add-the-virtual-machine-extension"></a>Přidat rozšíření virtuálního počítače 

Přidejte rozšíření **Azure monitor, Update a Správa konfigurace** virtuálního počítače do virtuálních počítačů spuštěných v centru Azure Stack. 

1. Na nové kartě prohlížeče se přihlaste k [portálu Azure Stack hub](/azure-stack/user/azure-stack-use-portal#access-the-portal).

1. Přejít na stránku **virtuální počítače** , vyberte virtuální počítač, který chcete chránit pomocí služby Azure Sentinel. Informace o tom, jak vytvořit virtuální počítač v centru Azure Stack, najdete v tématu Vytvoření virtuálního počítače [s Windows serverem pomocí portálu centra Azure Stack](/azure-stack/user/azure-stack-quick-windows-portal) nebo [Vytvoření virtuálního počítače se systémem Linux pomocí portálu služby Azure Stack hub](/azure-stack/user/azure-stack-quick-linux-portal).

1. Vyberte **Rozšíření**. Zobrazí se seznam rozšíření virtuálních počítačů nainstalovaných na tomto virtuálním počítači.

1. Klikněte na kartu **Přidat** . Otevře se okno nabídky **nový prostředek** a zobrazí se seznam dostupných rozšíření virtuálních počítačů. 

1. Vyberte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** a klikněte na **vytvořit**. Otevře se okno **instalovat konfiguraci rozšíření** .

   ![Nastavení správy Azure Monitor, aktualizací a konfigurací](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Pokud nevidíte rozšíření pro **správu Azure monitor, aktualizace a konfigurace** uvedená na webu Marketplace, obraťte se na svého operátora centra Azure Stack, aby ho bylo možné zpřístupnit.

1. V nabídce Sentinel Azure vyberte **Nastavení pracovního prostoru** a pak zkopírujte  **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)**. 

1. V okně Azure Stacka **Instalace centra instalace** je vložte do označených polí a klikněte na tlačítko **OK**.

1. Po dokončení instalace rozšíření se jeho stav zobrazí jako **úspěšné zřízení**. Může trvat až jednu hodinu, než se virtuální počítač objeví na portálu Sentinel Azure.

Další informace o instalaci a konfiguraci agenta pro systém Windows najdete v tématu [připojení počítačů se systémem Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Informace o řešení problémů agenta pro Linux najdete v tématu [řešení potíží s agentem Azure Log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Na portálu Sentinel Azure v Azure v části **Virtual Machines** máte přehled o všech virtuálních počítačích a počítačích spolu s jejich stavem. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete rozšíření odebrat z virtuálního počítače pomocí portálu Azure Stack hub.

Odebrání rozšíření:

1. Otevřete **portál centra Azure Stack**.

1. Na stránce přejít na **virtuální počítače** vyberte virtuální počítač, ze kterého chcete rozšíření odebrat.

1. Vyberte **rozšíření**, vyberte rozšíření **Microsoft. EnterpriseCloud. Monitoring**.

1. Klikněte na **odinstalovat** a potvrďte výběr.

## <a name="next-steps"></a>Další kroky

Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- Streamování dat z [běžných zařízení formátu událostí](connect-common-event-format.md) do Azure Sentinel.
