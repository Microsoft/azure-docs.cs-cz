---
title: Na palubě virtuálních počítačů Azure Stack do Azure Security Center
description: Tento rychlý start ukazuje, jak zřídit rozšíření virtuálního počítače Azure Monitor, Update and Configuration Management na virtuálních počítačích Azure Stack.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686521"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Úvodní příručka: Připojení virtuálních počítačů Azure Stack do Centra zabezpečení
Po připojení předplatného Azure můžete povolit Security Center k ochraně vašich virtuálních počítačů spuštěných na Azure Stack přidáním rozšíření **Azure Monitor, Update and Configuration Management** virtuálního počítače z webu Azure Stack Marketplace.

Tento rychlý start ukazuje, jak přidat rozšíření virtuálního počítače **Azure Monitor, Update and Configuration Management** na virtuální mši (linux a Windows jsou oba podporované) spuštěné ve službě Azure Stack.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Před spuštěním tohoto rychlého startu musíte mít předplatné Azure na úrovni Standard centra zabezpečení. Pokyny k upgradu najdete v tématu [Připojení předplatného Azure ke službě Security Center úrovně Standard](security-center-get-started.md). Úroveň Security Center Standard můžete vyzkoušet zdarma po dobu 30 dnů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Výběr pracovního prostoru v Azure Security Center

1. Přihlaste se k [portálu Azure](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**. 

   ![Přehled služby Security Center][2]

3. V hlavní nabídce služby Security Center vyberte **Začínáme**.
4. Vyberte kartu **Začínáme**.

   ![Začínáme][3]

5. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento pracovní prostor nebo jiný pracovní prostor, do kterého má virtuální počítač Azure Stack vykazovat data zabezpečení.

    ![Přidání počítače umístěného mimo Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Okno **Direct Agent** se otevře s odkazem pro stažení agenta a klíče pro ID pracovního prostoru pro použití při konfiguraci agenta.

   >[!NOTE]
   > Není nutné stáhnout agenta ručně. Agent se nainstaluje jako rozšíření virtuálního zařízení v následujících krocích.

6. Napravo od **ID pracovního prostoru** vyberte ikonu kopírování a vložte ID do Poznámkového bloku.

7. Napravo od **Primárního klíče** vyberte ikonu kopírování a vložte klíč do Poznámkového bloku.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Přidání rozšíření virtuálního počítače do stávajících virtuálních počítačů Azure Stack
Teď musíte přidat rozšíření virtuálního počítače **Azure Monitor, Update and Configuration Management** do virtuálních počítačů spuštěných ve vašem Azure Stacku.

1. Na nové kartě prohlížeče se přihlaste k **portálu Azure Stack.**
2. Přejděte na stránku **Virtuální počítače** a vyberte virtuální počítač, který chcete chránit pomocí Centra zabezpečení. Informace o tom, jak vytvořit virtuální počítač v Azure Stacku, najdete [v tomto rychlém startu pro virtuální počítače s Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo tento rychlý start pro virtuální počítače s [Linuxem](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Vyberte **Extensions** (Rozšíření). Zobrazí se seznam rozšíření virtuálních strojů nainstalovaných v tomto virtuálním počítači.
4. Klikněte na kartu **Přidat.** Otevře se okno nabídky **Nový prostředek** a zobrazí seznam dostupných rozšíření virtuálních strojů. 
5. Vyberte rozšíření **Azure Monitor, Update and Configuration Management** a klikněte na **Vytvořit**. Otevře se konfigurační okno **Prodlužování** rozšíření.

>[!NOTE]
> Pokud na vašem webu Marketplace nevidíte rozšíření **Azure Monitor, Update and Configuration Management,** obraťte se na operátora Azure Stack, aby ho zpřístupnili.

6. V okně **Konfigurace rozšíření Instalace** vložte **ID pracovního prostoru** a klíč **pracovního prostoru (primární klíč),** které jste zkopírovali do poznámkového bloku v předchozím postupu.
7. Po dokončení poskytování potřebných nastavení konfigurace klepněte na tlačítko **OK**.
8. Po dokončení instalace rozšíření, jeho stav se zobrazí jako **zřizování úspěšné**. Může trvat až jednu hodinu, než se virtuální počítač zobrazí na portálu Centra zabezpečení.

Další informace o instalaci a konfiguraci agenta systému Windows naleznete v tématu [Connect Windows Computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Řešení potíží s agenty v Linuxu najdete [v tématu Poradce při potížích s agentem Azure Log Analytics Linux .](../azure-monitor/platform/agent-linux-troubleshoot.md)

Teď můžete na jednom místě monitorovat své virtuální počítače Azure i počítače umístěné mimo Azure. Na portálu Security Center v Azure máte v části **Compute**přehled o všech virtuálních počítačích a počítačích spolu s jejich doporučeními. Security Center také zobrazí jakékoli zjišťování pro tyto počítače v výstrahách zabezpečení.

  ![Okno Compute][6]

V okně **Compute** se zobrazují dva typy ikon:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Počítač umístěný mimo Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure (virtuální počítače azure se zobrazí v této skupině)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Když už nepotřebujete, můžete odebrat rozšíření z virtuálního počítače prostřednictvím portálu Azure Stack.

Odebrání rozšíření:

1. Otevřete **portál zásobníku Azure**.
2. Přejděte na stránku **Virtuální počítače,** vyberte virtuální počítač, ze kterého chcete rozšíření odebrat.
3. Vyberte **Rozšíření**, vyberte rozšíření **Microsoft.EnterpriseCloud.Monitoring**.
4. Klikněte na **Odinstalovat**a potvrďte výběr klepnutím na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zřídit rozšíření Azure Monitor, aktualizace a správa konfigurace na virtuálním počítači spuštěném v Azure Stacku. Další informace o tom, jak používat Security Center, najdete v následném kurzu o konfiguraci zásad zabezpečení a o vyhodnocení zabezpečení vašich prostředků.

> [!div class="nextstepaction"]
> [Kurz: Definování a vyhodnocení zásad zabezpečení](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
