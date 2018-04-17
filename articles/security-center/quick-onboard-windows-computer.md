---
title: Rychlý start Azure Security Center – Připojení počítačů s Windows ke službě Security Center | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak zřídit agenta Microsoft Monitoring Agent na počítači s Windows.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: b872153278810ce92f19c7c71fe473a2b77def35
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Rychlý start: Připojení počítačů s Windows ke službě Azure Security Center
Po připojení předplatných Azure můžete zřízením agenta Microsoft Monitoring Agent povolit službu Security Center pro prostředky spuštěné mimo Azure, například v místním prostředí nebo jiných cloudech.

V tomto rychlém startu se dozvíte, jak nainstalovat agenta Microsoft Monitoring Agent na počítač s Windows.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Než začnete s tímto rychlým startem, musíte mít službu Security Center na cenové úrovni Standard. Pokyny k upgradu najdete v tématu [Připojení předplatného Azure ke službě Security Center úrovně Standard](security-center-get-started.md). Službu Security Center úrovně Standard si můžete zdarma vyzkoušet na 60 dní.

## <a name="add-new-windows-computer"></a>Přidání nového počítače s Windows

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

 ![Přehled služby Security Center][2]

3. V hlavní nabídce služby Security Center vyberte **Připojení k rozšířenému zabezpečení**.
4. Vyberte možnost **Chcete přidat počítače umístěné mimo Azure**.

   ![Připojení k rozšířenému zabezpečení][3]

5. V části **Přidat počítače umístěné mimo Azure** se zobrazí seznam vašich pracovních prostorů Log Analytics. Seznam obsahuje i výchozí pracovní prostor, který pro vás vytvořila služba Security Center při povolení automatického zřizování, pokud existuje. Vyberte tento pracovní prostor nebo jiný pracovní prostor, který chcete použít.

    ![Přidání počítače umístěného mimo Azure][4]

  Otevře se okno **Přímý agent** s odkazem na stažení agenta pro Windows a klíči pro ID vašeho pracovního prostoru, které použijete při konfiguraci agenta.

6.  Vyberte odkaz **Stáhnout agenta pro Windows** odpovídající typu procesoru vašeho počítače a stáhněte instalační soubor.

7.  Napravo od **ID pracovního prostoru** vyberte ikonu kopírování a vložte ID do Poznámkového bloku.

8.  Napravo od **Primárního klíče** vyberte ikonu kopírování a vložte klíč do Poznámkového bloku.

## <a name="install-the-agent"></a>Instalace agenta
Teď je potřeba stažený soubor nainstalovat na cílový počítač.

1. Zkopírujte soubor do cílového počítače a spusťte instalaci.
2. Na **úvodní** stránce vyberte **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak vyberte **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak vyberte **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak vyberte **Další**.
6. Na stránce **Azure Log Analytics** vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (Primární klíč)**, které jste zkopírovali do Poznámkového bloku v rámci předchozího postupu.
7. Pokud se má počítač hlásit do pracovního prostoru Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, vyberte **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.
8. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, vyberte **Další**.

  ![Instalace agenta][5]

9. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak vyberte **Nainstalovat**.
10. Na stránce **Konfigurace byla úspěšně dokončena** vyberte **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Tam můžete zkontrolovat svou konfiguraci a ověřit připojení agenta.

Další informace o instalaci a konfiguraci agenta najdete v tématu popisujícím [připojení počítačů s Windows](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup-wizard).

Teď můžete na jednom místě monitorovat své virtuální počítače Azure i počítače umístěné mimo Azure. V části **Compute** najdete přehled všech virtuálních počítačů a počítačů i s doporučeními. Každý sloupec představuje jednu sadu doporučení. Barva doporučení představuje aktuální stav zabezpečení virtuálního počítače nebo počítače. Security Center navíc zobrazí všechny detekce pro tyto počítače ve výstrahách upozornění.

  ![Okno Compute][6]

V okně **Compute** se zobrazují dva typy ikon:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Počítač umístěný mimo Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuální počítač Azure

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už ho nepotřebujete, můžete agenta z počítače s Windows odebrat.

Odebrání agenta:

1. Otevřete **Ovládací panely**.
2. Otevřete **Programy a funkce**.
3. V části **Programy a funkce** vyberte **Microsoft Monitoring Agent** a klikněte na **Odinstalovat**.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zřídili agenta Microsoft Monitoring Agent na počítači s Windows. Další informace o tom, jak používat Security Center, najdete v následném kurzu o konfiguraci zásad zabezpečení a o vyhodnocení zabezpečení vašich prostředků.

> [!div class="nextstepaction"]
> [Kurz: Definování a vyhodnocení zásad zabezpečení](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
