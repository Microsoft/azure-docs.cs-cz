---
title: Připojení virtuálních počítačů Azure Stack k Azure Security Center
description: V tomto rychlém startu se dozvíte, jak zřídit rozšíření pro virtuální počítače s Azure Monitor, aktualizací a konfigurací na Azure Stack virtuálních počítačích.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686521"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Rychlý Start: připojení virtuálních počítačů s Azure Stack k Security Center
Po zaregistrování předplatného Azure můžete povolit Security Center k ochraně virtuálních počítačů běžících na Azure Stack přidáním rozšíření **Azure monitor, aktualizace a konfigurace** virtuálního počítače pro správu konfigurace z Azure Stack Marketplace.

V tomto rychlém startu se dozvíte, jak přidat rozšíření virtuálního počítače pro **Azure monitor, aktualizaci a správu konfigurace** na virtuálním počítači (Linux a Windows se podporují i v případě, že běží) v Azure Stack.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Než začnete s tímto rychlým startem, musíte mít předplatné Azure na úrovni Standard Security Center. Pokyny k upgradu najdete v tématu [Připojení předplatného Azure ke službě Security Center úrovně Standard](security-center-get-started.md). Po dobu 30 dnů můžete vyzkoušet Security Center úroveň Standard zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Vyberte svůj pracovní prostor v Azure Security Center

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**. 

   ![Přehled služby Security Center][2]

3. V hlavní nabídce služby Security Center vyberte **Začínáme**.
4. Vyberte kartu **Začínáme**.

   ![Začínáme][3]

5. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento pracovní prostor nebo jiný pracovní prostor, do kterého má Azure Stack virtuální počítač nahlásit data zabezpečení.

    ![Přidání počítače umístěného mimo Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Otevře se okno **přímý agent** s odkazem pro stažení agenta a klíčů pro ID vašeho pracovního prostoru, které chcete použít při konfiguraci agenta.

   >[!NOTE]
   > Nemusíte stahovat agenta ručně. Agent se v následujících krocích nainstaluje jako rozšíření virtuálního počítače.

6. Napravo od **ID pracovního prostoru** vyberte ikonu kopírování a vložte ID do Poznámkového bloku.

7. Napravo od **Primárního klíče** vyberte ikonu kopírování a vložte klíč do Poznámkového bloku.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Přidat rozšíření virtuálního počítače do stávajících virtuálních počítačů s Azure Stack
Nyní musíte přidat rozšíření **Azure monitor, Update a Configuration Management** pro virtuální počítače, které jsou spuštěny v Azure Stack.

1. Na nové kartě prohlížeče se přihlaste k portálu **Azure Stack** .
2. Přejít na stránku **virtuální počítače** , vyberte virtuální počítač, který chcete chránit pomocí Security Center. Informace o tom, jak vytvořit virtuální počítač na Azure Stack, najdete v [tomto rychlém startu pro virtuální počítače s Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo v [tomto rychlém startu pro virtuální počítače](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)se systémem Linux.
3. Vyberte **Extensions** (Rozšíření). Zobrazí se seznam rozšíření virtuálních počítačů nainstalovaných na tomto virtuálním počítači.
4. Klikněte na kartu **Přidat** . Otevře se okno nabídky **nový prostředek** a zobrazí se seznam dostupných rozšíření virtuálních počítačů. 
5. Vyberte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** a klikněte na **vytvořit**. Otevře se okno **instalovat konfiguraci rozšíření** .

>[!NOTE]
> Pokud nevidíte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** uvedená na webu Marketplace, obraťte se na svého operátora Azure Stack a zpřístupněte ho.

6. V okně **nainstalovat konfiguraci rozšíření** vložte do programu Poznámkový blok **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali do poznámkového bloku v předchozím postupu.
7. Až dokončíte nastavení potřebné konfigurace, klikněte na **OK**.
8. Po dokončení instalace rozšíření se jeho stav zobrazí jako **úspěšné zřizování**. Může trvat až jednu hodinu, než se virtuální počítač objeví na portálu Security Center.

Další informace o instalaci a konfiguraci agenta pro systém Windows najdete v tématu [připojení počítačů se systémem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Informace o řešení problémů agenta pro Linux najdete v tématu [řešení potíží s agentem Azure Log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Teď můžete na jednom místě monitorovat své virtuální počítače Azure i počítače umístěné mimo Azure. Na portálu Security Center v Azure v části **COMPUTE**máte přehled o všech virtuálních počítačích a počítačích spolu s jejich doporučeními. Security Center také doplní jakékoli zjišťování pro tyto počítače v výstrahách zabezpečení.

  ![Okno Compute][6]

V okně **Compute** se zobrazují dva typy ikon:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Počítač umístěný mimo Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure (Azure Stack virtuální počítače se budou zobrazovat v této skupině)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete rozšíření odebrat z virtuálního počítače pomocí portálu Azure Stack.

Odebrání rozšíření:

1. Otevřete **portál Azure Stack**.
2. Na stránce přejít na **virtuální počítače** vyberte virtuální počítač, ze kterého chcete rozšíření odebrat.
3. Vyberte **rozšíření**, vyberte rozšíření **Microsoft. EnterpriseCloud. Monitoring**.
4. Klikněte na **odinstalovat**a potvrďte výběr kliknutím na **Ano**.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zřídili rozšíření pro správu Azure Monitor, aktualizace a konfigurace na virtuálním počítači, na kterém běží Azure Stack. Další informace o tom, jak používat Security Center, najdete v následném kurzu o konfiguraci zásad zabezpečení a o vyhodnocení zabezpečení vašich prostředků.

> [!div class="nextstepaction"]
> [Kurz: Definování a vyhodnocení zásad zabezpečení](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
