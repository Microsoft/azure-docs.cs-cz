---
title: Upgrade agenta Microsoft Azure Recovery Services (MARS)
description: Přečtěte si, jak upgradovat agenta Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 79abc877f102d83cdb3c72a571f2203984eb54e4
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890187"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Upgrade agenta Microsoft Azure Recovery Services (MARS)

V tomto článku se dozvíte, jak:

* Identifikujte servery s dřívějšími verzemi agenta MARS.
* Aktualizace instalací MARS na těchto serverech

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identifikujte servery s dřívějšími verzemi agenta MARS.

Pro instalace agenta Azure Backup a Azure Backup serveru:

1. Přejděte do trezoru Recovery Services, kde jste zaregistrovali servery, které jsou potenciálně zálohované staršími verzemi agenta. Seznam trezorů se staršími agenty Azure Backup najdete v části Azure Backup aktualizace výstrah z Azure.
1. V části **Nastavení** na levé straně trezoru Recovery Services vyberte možnost **infrastruktura zálohování** v části **Spravovat** .
1. Pokud chcete zjistit, jaké agenty služby Azure Backup jsou nainstalované jako součást instalací serveru Azure Backup, přečtěte si část **zálohování serverů pro správu** v části **servery pro správu**. Zobrazí se seznam serverů, které mají instalaci serveru Azure Backup, spolu s číslem verze pro přidruženého agenta Azure Backup.

    ![Seznam agentů MARS nainstalovaných jako součást instalací serveru Azure Backup](./media/upgrade-mars-agent/backup-management-servers.png)

1. Chcete-li ověřit, zda jsou verze agenta pro instalaci agenta Microsoft Azure Recovery Services (MARS) nebo agenta Azure Backup, v části **servery pro správu**klikněte na **chráněné servery** . Pak v části typ správy zálohování vyberte **agent Azure Backup** . Zobrazí se seznam serverů, které mají instalace agenta Azure Backup spolu s číslem verze instalace.

    ![Seznam serverů s nainstalovaným agentem MARS](./media/upgrade-mars-agent/protected-servers.png)

1. Výběrem sloupce **verze** agenta pro instalace agenta Mars nebo sloupce **verze Azure Backup agenta** pro instalace Azure Backup serveru seřaďte sloupec verze agenta Azure Backup.

1. Předchozí krok vám poskytne seznam serverů s agenty Azure Backup, které mají verze nižší než 2.0.9083.0 nebo verze agenta uvedené jako prázdné. Jedná se o servery, na kterých je potřeba aktualizovat agenty Azure Backup.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Aktualizace instalace agenta MARS na serveru

Jakmile identifikujete servery, které vyžadují aktualizaci agenta Azure Backup, proveďte následující kroky pro každý identifikovaný Server (pomocí Azure Backup serveru nebo agenta MARS). Než budete postupovat podle následujících kroků, [Stáhněte si nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent) .

1. Vyberte řádek, který má Azure Backup agenta nižší než 2.0.9083.0 nebo prázdný. Otevře se obrazovka s podrobnostmi serveru.

    ![Chráněné servery s neaktuálními verzemi agentů](./media/upgrade-mars-agent/old-agent-version.png)

    ![Servery Azure Backup s zastaralými verzemi agentů](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Vyberte **připojit** , pokud chcete získat soubor připojení ke vzdálené ploše pro připojení k serveru nebo se přímo připojit k serveru přes připojení ke vzdálené ploše na serveru.

    ![Připojení k serveru přes připojení ke vzdálené ploše](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Pokud uvedený server neexistuje nebo byl vyřazen z provozu, můžete zbývající kroky ignorovat a přeskočit na další server.

1. Zadejte přihlašovací údaje pro správu a přihlaste se.

1. Pokud má server proxy serveru nebo serveru omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na serveru nebo proxy serveru umožňují, aby byla adresa URL vhodná pro cloud Azure, který používáte:

    Cloud Azure | URL
    --- | ---
    Cloud Azure (veřejný) |   `https://login.windows.net`
    Cloud pro Azure Čína 21Vianet   | `https://login.chinacloudapi.cn`
    Cloud Azure pro státní správu USA |   `https://login.microsoftonline.us`
    Cloud pro Azure němčina  |  `https://login.microsoftonline.de`

1. Zkopírujte instalační program aktualizace agenta Azure Backup na server.

    ![Kopírování instalačního programu agenta Azure Backup na server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Spusťte instalační program. Spustí se Průvodce upgradem agenta Microsoft Azure Recovery Services.

    ![Průvodce upgradem agenta Microsoft Azure Recovery Services](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Vyberte **Další**.

1. Vyberte možnost **upgradovat**.

    ![Instalace agenta Microsoft Azure Recovery Services](./media/upgrade-mars-agent/upgrade-installation.png)

1. Na obrazovce poslední potvrzení se zobrazí oznámení o úspěšné aktualizaci agenta Azure Backup.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Pro zákazníky nástroje System Center Data Protection Manager (SC DPM)

Pokud jste nainstalovali agenty Azure Backup na servery nástroje System Center Data Protection Manager (SC DPM), musíte podle následujících pokynů zjistit, jestli vaše servery DPM potřebují aktualizaci agenta Azure Backup:

1. Přihlaste se k serveru SC DPM jako správce.
2. Otevřete konzolu DPM.
3. V levém dolním navigačním panelu konzoly vyberte **Správa** .
4. V informacích, které se zobrazí na levém navigačním panelu, vyhledejte informace o verzi agenta Azure Backup.
5. Pokud je verze nižší než 2.0.9083.0, Stáhněte si nejnovější instalační program agenta Azure Backup a spusťte instalační program na serveru DPM a aktualizujte agenta Azure Backup.

Opakujte výše uvedené kroky pro všechny servery DPM ve vašem prostředí.

## <a name="next-steps"></a>Další kroky

Informace o [zálohování počítačů s Windows pomocí agenta Azure Backup Mars](backup-windows-with-mars-agent.md)
