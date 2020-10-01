---
title: Připojení počítačů mimo Azure k Azure Security Center
description: Přečtěte si, jak připojit počítače mimo Azure k Security Center
author: memildin
ms.author: memildin
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: df780e4e55bb5c119320d4b33502d50a95da1eaf
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612213"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Připojení počítačů mimo Azure k Security Center

Security Center může monitorovat stav zabezpečení počítačů mimo Azure, tyto prostředky je však nejprve potřeba připojit. 

Počítače mimo Azure můžete přidat některým z následujících způsobů:

- Používání Azure ARC (**doporučeno**)
- Ze stránek Security Center v Azure Portal (**Začínáme** a **inventář**)

Každá z nich je popsána níže.

## <a name="add-non-azure-machines-with-azure-arc"></a>Přidání počítačů mimo Azure pomocí ARC Azure

Používání ARC Azure je preferovaným způsobem, jak přidat počítače mimo Azure do Azure Security Center.

Počítač s povoleným ARC Azure se stal zdrojem Azure a zobrazí se v Security Center s doporučeními, jako jsou vaše jiné prostředky Azure. 

Kromě toho Azure ARC poskytuje rozšířené možnosti, jako je možnost povolit zásady na počítači, nasadit agenta Log Analytics jako rozšíření, zjednodušit nasazení s ostatními službami Azure a další. Přehled výhod najdete v tématu [podporované scénáře](../azure-arc/servers/overview.md#supported-scenarios).

**Nasazení ARC Azure:**

- Pro jeden počítač postupujte podle pokynů v části [rychlý Start: připojení hybridního počítače se servery s podporou ARC Azure](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Pokud chcete nasadit Azure ARC ve velkém měřítku, přečtěte si téma [připojení hybridních počítačů ke škálování Azure](../azure-arc/servers/onboard-service-principal.md) .

Přečtěte si další informace o [ARC Azure](../azure-arc/servers/overview.md).

> [!TIP]
> Pokud se připojujete k AWS počítačům Security Center, konektor pro AWS transparentně zpracovává nasazení ARC Azure za vás. Další informace najdete v informacích [o připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md).

## <a name="add-non-azure-machines-from-security-centers-portal-pages"></a>Přidání počítačů mimo Azure ze stránek portálu Security Center

1. V nabídce Security Center otevřete stránku **Začínáme** .
1. Vyberte kartu **Začínáme**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Karta Začínáme na stránce Začínáme" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Níže **přidejte servery, které nejsou na Azure**, a vyberte **Konfigurovat** .

    > [!TIP]
    > Můžete taky otevřít přidat počítače **na stránce** **Přidat servery, které nepatří do Azure** .
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Karta Začínáme na stránce Začínáme":::

    Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento nebo jiný pracovní prostor, který chcete použít.

    Můžete přidat počítače do existujícího pracovního prostoru nebo vytvořit nový pracovní prostor. 

1. Chcete-li vytvořit nový pracovní prostor, vyberte možnost  **vytvořit nový pracovní prostor**.

1. V seznamu pracovních prostorů vyberte možnost **Přidat servery** pro příslušný pracovní prostor.
    Zobrazí se stránka **Správa agentů** .

    Odsud v závislosti na typu počítačů, které se chystáte zaregistrování, vyberte příslušný postup:

    - [Připojení virtuálních počítačů s Azure Stack](#onboard-your-azure-stack-vms)
    - [Připojení počítačů se systémem Linux](#onboard-your-linux-machines)
    - [Zprovoznění počítačů s Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Připojení virtuálních počítačů s Azure Stack
Chcete-li přidat Azure Stack virtuálních počítačů, potřebujete informace na stránce **Správa agentů** a na virtuálních počítačích, které jsou spuštěny v Azure Stack, nakonfigurujte rozšíření **Azure monitor, aktualizace a Správa konfigurace** .
1. Na stránce **Správa agentů** zkopírujte **ID pracovního prostoru** a **primární klíč** do poznámkového bloku.
1. Přihlaste se k portálu **Azure Stack** a otevřete stránku **virtuální počítače** .
1. Vyberte virtuální počítač, který chcete chránit pomocí Security Center.
    >[!TIP]
    > Informace o tom, jak vytvořit virtuální počítač na Azure Stack, najdete v [tomto rychlém startu pro virtuální počítače s Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) nebo v [tomto rychlém startu pro virtuální počítače](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)se systémem Linux.
1. Vyberte **Rozšíření**. Zobrazí se seznam rozšíření virtuálních počítačů nainstalovaných na tomto virtuálním počítači.
1. Vyberte kartu **Přidat** . V nabídce **nový prostředek** se zobrazí seznam dostupných rozšíření virtuálních počítačů.
1. Vyberte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** a vyberte **vytvořit**. Otevře se stránka **instalace konfigurace rozšíření** .
    >[!NOTE]
    > Pokud nevidíte rozšíření **pro správu Azure monitor, aktualizace a konfigurace** uvedená na webu Marketplace, obraťte se na svého operátora Azure Stack a zpřístupněte ho.
1. Na stránce **instalace konfigurace rozšíření** vložte **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali do poznámkového bloku v předchozím kroku.
1. Po dokončení konfigurace vyberte **OK**. Stav rozšíření se zobrazí jako **úspěšné zřizování**. Může trvat až jednu hodinu, než se virtuální počítač objeví v Security Center.


### <a name="onboard-your-linux-machines"></a>Připojení počítačů se systémem Linux
Pokud chcete přidat počítače se systémem Linux, budete potřebovat příkaz WGET ze stránky **Správa agentů** .
1. Na stránce **Správa agentů** zkopírujte příkaz **WGET** do poznámkového bloku. Uložte tento soubor do umístění přístupného z vašeho počítače s Linuxem.
1. V počítači se systémem Linux otevřete soubor pomocí příkazu WGET. Vyberte celý obsah a zkopírujte ho a vložte do konzoly terminálu.
1. Po dokončení instalace můžete ověřit, že je *omsagent* nainstalovaný, spuštěním příkazu *pgrep* . Příkaz vrátí *omsagent* PID.
    Protokoly pro agenta najdete na adrese: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* může trvat až 30 minut, než se nový počítač se systémem Linux objeví v Security Center.


### <a name="onboard-your-windows-machines"></a>Zprovoznění počítačů s Windows
Chcete-li přidat počítače se systémem Windows, potřebujete informace na stránce **Správa agentů** a stáhnout příslušný soubor agenta (32/64 bitů).
1. Vyberte odkaz **Stáhnout agenta pro Windows** odpovídající typu procesoru vašeho počítače a stáhněte instalační soubor.
1. Na stránce **Správa agentů** zkopírujte **ID pracovního prostoru** a **primární klíč** do poznámkového bloku.
1. Zkopírujte stažený instalační soubor do cílového počítače a spusťte ho.
1. Postupujte podle pokynů Průvodce instalací**Next**(Next **, souhlasím**, **Next**, **Next**).
    1. Na stránce **Azure Log Analytics** vložte **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali do poznámkového bloku.
    1. Pokud by měl počítač nahlásit do pracovního prostoru Log Analytics v cloudu Azure Government, vyberte v rozevíracím seznamu **cloud Azure** možnost **státní správa Azure USA** .
    1. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, vyberte **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.
    1. Po zadání všech nastavení konfigurace vyberte **Další**.
    1. Na stránce **připraveno k instalaci** zkontrolujte nastavení, které se má použít, a vyberte **nainstalovat**.
    1. Na stránce **Konfigurace byla úspěšně dokončena** vyberte **Dokončit**.

Po dokončení se **agent Log Analytics** zobrazí v **Ovládacích panelech**. Tam můžete zkontrolovat svou konfiguraci a ověřit připojení agenta.

Další informace o instalaci a konfiguraci agenta najdete v tématu [připojení počítačů s Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Ověřoval
Blahopřejeme! Teď můžete na jednom místě zobrazit počítače s Azure a mimo Azure společně. Otevřete [stránku inventarizace assetů](asset-inventory.md) a vyfiltrujte příslušné typy prostředků. Tyto ikony rozlišují tyto typy:

  ![Ikona ASC pro počítač mimo Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Počítač mimo Azure

  ![Ikona ASC pro počítač Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure

  ![Ikona ASC pro počítač ARC Azure](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Počítač s podporou ARC Azure

## <a name="next-steps"></a>Další kroky

Na této stránce jste si ukázali, jak přidat počítače mimo Azure do Azure Security Center. Chcete-li monitorovat svůj stav, použijte nástroje inventáře, jak je vysvětleno na následující stránce:

- [Prozkoumejte a spravujte svoje prostředky pomocí inventáře prostředků a nástrojů pro správu.](asset-inventory.md)