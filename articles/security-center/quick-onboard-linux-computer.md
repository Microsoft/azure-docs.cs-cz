---
title: Rychlý start Azure Security Center – Připojení počítačů s Linuxem ke službě Security Center | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak připojit počítače s Linuxem ke službě Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: rkarlin
ms.openlocfilehash: 48a04bbe77a6fe836c282556709e13bb9cd9a337
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848184"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Rychlý start: Připojení počítačů s Linuxem ke službě Azure Security Center
Po připojení předplatných Azure můžete zřízením agenta pro Linux povolit službu Security Center pro prostředky Linuxu spuštěné mimo Azure, například v místním prostředí nebo jiných cloudech.

V tomto rychlém startu se dozvíte, jak nainstalovat agenta pro Linux na počítač s Linuxem.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Než začnete s tímto rychlým startem, musíte mít službu Security Center na cenové úrovni Standard. Pokyny k upgradu najdete v tématu [Připojení předplatného Azure ke službě Security Center úrovně Standard](security-center-get-started.md). Můžete zkusit Security Center úrovně Standard zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Přidání nového počítače s Linuxem

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

 ![Přehled služby Security Center][2]

3. V hlavní nabídce služby Security Center vyberte **Začínáme**.
4. Vyberte kartu **Začínáme**. ![Začínáme][3]

5. V části **Přidat počítače umístěné mimo Azure** klikněte na **Konfigurovat**. Zobrazí se seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento nebo jiný pracovní prostor, který chcete použít.

    ![Přidání počítače umístěného mimo Azure](./media/quick-onboard-linux-computer/non-azure.png)

6.  Na stránce **Přímý agent** v části **STÁHNOUT A PŘOPOJIT AGENTA PRO LINUX** výběrem tlačítka pro **kopírování** zkopírujte příkaz *wget*.

7.  Otevřete Poznámkový blok a vložte do něj tento příkaz. Uložte tento soubor do umístění přístupného z vašeho počítače s Linuxem.

## <a name="install-the-agent"></a>Instalace agenta

1.  Na počítači s Linuxem otevřete dříve uložený soubor. Vyberte a zkopírujte celý obsah, otevřete konzolu terminálu a příkaz do ní vložte.
2.  Po dokončení instalace můžete instalaci agenta *omsagent* ověřit spuštěním příkazu *pgrep*. Příkaz vrátí ID procesu (PID) *omsagent*, jak je znázorněno níže:

  ![Instalace agenta][5]

Protokoly agenta služby Security Center pro Linux se nacházejí v umístění: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Protokoly agenta][6]

Po určité době, může to trvat až 30 minut, se nový počítač s Linuxem zobrazí ve službě Security Center.

Teď můžete na jednom místě monitorovat své virtuální počítače Azure i počítače umístěné mimo Azure. V části **Compute** najdete přehled všech virtuálních počítačů a počítačů i s doporučeními. Každý sloupec představuje jednu sadu doporučení. Barva doporučení představuje aktuální stav zabezpečení virtuálního počítače nebo počítače. Security Center navíc zobrazí všechny detekce pro tyto počítače ve výstrahách upozornění.

  ![Okno Compute][7] V okně **Compute** se zobrazují dva typy ikon:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Počítač umístěný mimo Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už ho nepotřebujete, můžete agenta z počítače s Linuxem odebrat.

Odebrání agenta:

1. Stáhněte na počítač [univerzální skript](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) agenta pro Linux.
2. Spusťte na počítači soubor .sh sady s argumentem *--purge*. Tím se kompletně odebere agent a jeho konfigurace.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zřídili agenta na počítači s Linuxem. Další informace o tom, jak používat Security Center, najdete v následném kurzu o konfiguraci zásad zabezpečení a o vyhodnocení zabezpečení vašich prostředků.

> [!div class="nextstepaction"]
> [Kurz: Definování a vyhodnocení zásad zabezpečení](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
