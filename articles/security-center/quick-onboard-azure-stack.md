---
title: Azure Security Center rychlý start – připojení virtuálních počítačů Azure Stack vám Security Center | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak zřídit Azure Monitor, aktualizace a správa konfigurace rozšíření virtuálních počítačů na virtuálních počítačích Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
editor: ''
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 7a630acee079301b95e7e05f5c5333dd116abb68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706410"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Rychlý start:  Připojení virtuálních počítačů Azure Stack vám Security Center
Jakmile připojíte svým předplatným Azure, můžete povolit Security Center k ochraně vašich virtuálních počítačů spuštěných ve službě Azure Stack tak, že přidáte **Azure Monitor, aktualizace a správa konfigurace** rozšíření virtuálního počítače z Tržiště Azure Stack.

V tomto rychlém startu se dozvíte, jak přidat **Azure Monitor, aktualizace a správa konfigurace** rozšíření virtuálního počítače na virtuálním počítači (se systémy Linux a Windows jsou podporované) spuštěná ve službě Azure Stack.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Musíte mít předplatné Azure na Security Center úrovně Standard před zahájením tohoto rychlého startu. Pokyny k upgradu najdete v tématu [Připojení předplatného Azure ke službě Security Center úrovně Standard](security-center-get-started.md). Můžete se pokusit Security Center úrovně Standard zdarma po dobu 30 dnů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Vyberte pracovní prostor ve službě Azure Security Center

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**. 

   ![Přehled služby Security Center][2]

3. V hlavní nabídce služby Security Center vyberte **Začínáme**.
4. Vyberte kartu **Začínáme**.

   ![Začínáme][3]

5. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento pracovní prostor nebo jiný pracovní prostor, který chcete virtuální počítač Azure Stack, sestavy zabezpečení dat s.

    ![Přidání počítače umístěného mimo Azure](./media/quick-onboard-windows-computer/non-azure.png)

   **Přímý Agent** okně se otevře s odkazem na stažení agenta a klíči pro ID vašeho pracovního prostoru, které použijete při konfiguraci agenta.

   >[!NOTE]
   > NENÍ potřeba stáhnout agenta ručně. Agent se nainstaluje jako rozšíření virtuálního počítače v následujících krocích.

6. Napravo od **ID pracovního prostoru** vyberte ikonu kopírování a vložte ID do Poznámkového bloku.

7. Napravo od **Primárního klíče** vyberte ikonu kopírování a vložte klíč do Poznámkového bloku.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Přidání rozšíření virtuálního počítače do vašich stávajících virtuálních počítačích Azure Stack
Teď musíte přidat **Azure Monitor, aktualizace a správa konfigurace** rozšíření virtuálního počítače pro virtuální počítače běžící v Azure stacku.

1. Na nové kartě prohlížeče, přihlaste se do vaší **Azure Stack** portálu.
2. Přejděte **virtuálních počítačů** stránky, vyberte virtuální počítač, který chcete chránit pomocí služby Security Center. Informace o tom, jak vytvořit virtuální počítač ve službě Azure Stack najdete v tématu [v tomto rychlém startu pro virtuální počítače s Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo [v tomto rychlém startu pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Vyberte **Extensions** (Rozšíření). Seznam rozšíření virtuálních počítačů, které jsou nainstalované na tomto virtuálním počítači se zobrazí.
4. Klikněte na tlačítko **přidat** kartu. **Nový prostředek** okně s nabídkou otevře a zobrazí seznam rozšíření k dispozici virtuálních počítačů. 
5. Vyberte **Azure Monitor, aktualizace a správa konfigurace** rozšíření a klikněte na tlačítko **vytvořit**. **Nainstalovat rozšíření** otevře se konfigurační okno.

>[!NOTE]
> Pokud se nezobrazí **Azure Monitor, aktualizace a správa konfigurace** rozšíření do seznamu marketplace, kontaktujte prosím vašeho operátory Azure stacku a zpřístupnit ji.

6. Na **nainstalovat rozšíření** okno konfigurace, Vložit **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali do poznámkového bloku v předchozím postupu.
7.  Až skončíte, poskytující nezbytných nastavení konfigurace, klikněte na tlačítko **OK**.
8. Po dokončení instalace rozšíření, jeho stav se zobrazí jako **Zřizování proběhlo úspěšně**. Může trvat až jednu hodinu pro virtuální počítač se zobrazí na portálu Security Center.

Další informace o instalaci a konfiguraci agenta pro Windows najdete v tématu [počítače připojit Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Pro Linux najdete v řešení potíží se agent, [řešení potíží s agentem Azure Log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Teď můžete na jednom místě monitorovat své virtuální počítače Azure i počítače umístěné mimo Azure. V zabezpečení Center portálu v Azure, v části **Compute**, budete mít přehled o všech virtuálních počítačů a počítačů spolu s jejich doporučení. Security Center navíc zobrazí všechny detekce pro tyto počítače ve výstrahách upozornění.

  ![Okno Compute][6]

V okně **Compute** se zobrazují dva typy ikon:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Počítač umístěný mimo Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure (virtuální počítače Azure Stack se zobrazí v této skupině)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete odebrat rozšíření z virtuálního počítače prostřednictvím portálu Azure Stack.

Odebrat rozšíření:

1. Otevřít **portálu Azure Stack**.
2. Přejděte na **virtuálních počítačů** stránky, vyberte virtuální počítač, ze kterého chcete odebrat rozšíření.
3. Vyberte **rozšíření**, vyberte požadované rozšíření **Microsoft.EnterpriseCloud.Monitoring**.
4. Klikněte na **odinstalovat**a potvrďte výběr kliknutím **Ano**.

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zřídili Azure Monitor, aktualizace a správa konfigurace rozšíření na virtuální počítač spuštěný ve službě Azure Stack. Další informace o tom, jak používat Security Center, najdete v následném kurzu o konfiguraci zásad zabezpečení a o vyhodnocení zabezpečení vašich prostředků.

> [!div class="nextstepaction"]
> [Kurz: Definování a vyhodnocení zásad zabezpečení](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
