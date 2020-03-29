---
title: Upgrade agenta služby Microsoft Azure Recovery Services (MARS)
description: Přečtěte si, jak upgradovat agenta služby Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672937"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Upgrade agenta služby Microsoft Azure Recovery Services (MARS)

V tomto článku se dozvíte, jak:

* Identifikace serverů s dřívějšími verzemi agenta MARS
* Aktualizace instalací MARS na těchto serverech

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identifikace serverů s dřívějšími verzemi agenta MARS

Pro instalace agenta Azure Backup a zálohovacího serveru Azure:

1. Přejděte do trezoru služby Recovery Services, kde jste zaregistrovali servery, které jsou potenciálně zálohovány staršími verzemi agenta. Reprezentativní seznam trezorů se staršími agenty Azure Backup najdete ve výstrahách Azure Backup Update z Azure.
1. V části **Nastavení** na levé straně v trezoru služby Recovery Services vyberte v části **Správa** možnost **Infrastruktura zálohování.**
1. Pokud chcete zjistit agenty zálohování Azure nainstalované v rámci instalací serveru Azure Backup, přejděte na **servery pro správu zálohování** v části **Servery pro správu**. Zobrazí se seznam serverů, které mají instalace serveru Azure Backup, spolu s číslem verze přidruženého agenta Azure Backup.

    ![Seznam agentů MARS nainstalovaných v rámci instalací serveru Azure Backup](./media/upgrade-mars-agent/backup-management-servers.png)

1. Chcete-li zkontrolovat verze agenta pro instalace agentů Microsoft Azure Recovery Services (MARS) nebo agenta Azure Backup, přejděte na **chráněné servery** v části **Servery pro správu**. Pak vyberte **agenta Azure Backup** v části Typ správy zálohování. To bude seznam serverů, které mají instalace agenta Azure Backup spolu s číslem verze pro instalaci.

    ![Seznam serverů s nainstalovaným agentem MARS](./media/upgrade-mars-agent/protected-servers.png)

1. Seřaďte sloupec verze agenta zálohování Azure kliknutím na sloupec **Verze agenta agenta AGENTA** MARS nebo sloupec **Verze agenta zálohování Azure** pro instalace zálohovacího serveru Azure.

1. V předchozím kroku získáte seznam serverů s agenty Azure Backup, kteří mají verze nižší než 2.0.9083.0 nebo verze agenta uvedené jako prázdné. Jedná se o servery, kde je třeba aktualizovat agenty Azure Backup.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Aktualizace instalace agenta MARS na serveru

Jakmile určíte servery, které potřebují aktualizaci agenta Azure Backup, proveďte následující kroky pro každý identifikovaný server (pomocí zálohovacího serveru Azure nebo agenta MARS). Než provedete následující kroky, [stáhněte si nejnovější verzi agenta zálohování Azure.](https://aka.ms/azurebackup_agent)

1. Klikněte na řádek, který má agenta Azure Backup nižší než 2.0.9083.0 nebo prázdné. Tím se otevře obrazovka s podrobnostmi serveru.

    ![Chráněné servery s zastaralými verzemi agenta](./media/upgrade-mars-agent/old-agent-version.png)

    ![Zálohovací servery Azure s zastaralými verzemi agenta](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Kliknutím na **tlačítko Připojit** obdržíte soubor připojení ke vzdálené ploše a připojíte se k serveru nebo se přímo připojíte k serveru prostřednictvím připojení Kejkoutní ploše na serveru.

    ![Připojení k serveru prostřednictvím připojení ke vzdálené ploše](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Pokud uvedený server neexistuje nebo byl vyřazen z provozu, můžete ignorovat zbývající kroky níže a přeskočit na další server.

1. Zadejte své přihlašovací údaje pro správu a přihlaste se.

1. Pokud má server nebo proxy server omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na serveru nebo proxy serveru je nakonfigurováno tak, aby umožňovalo adresu URL odpovídající cloudu Azure, který používáte:

    Azure Cloud | zprostředkovatele identity
    -- | ---
    Azure Cloud (veřejný) |   `https://login.windows.net`
    Azure China 21Vianet Cloud   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Německý cloud Azure  |  `https://login.microsoftonline.de`

1. Zkopírujte instalační program aktualizace agenta zálohování Azure na server.

    ![Kopírování instalačního programu aktualizace agenta zálohování Azure na server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Spusťte instalační program. Otevře se Průvodce upgradem agenta služby Microsoft Azure Recovery Services.

    ![Průvodce upgradem agenta služby Microsoft Azure Recovery Services](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Klikněte na **Další**.

1. Klikněte na tlačítko **Upgradovat**.

    ![Instalace agenta služby Microsoft Azure Recovery Services](./media/upgrade-mars-agent/upgrade-installation.png)

1. Obrazovka s konečným potvrzením označuje, že agent azure backup byl úspěšně aktualizován.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Pro zákazníky správce dat system center (SC DPM)

Pokud jste nainstalovali agenty zálohování Azure na serverech System Center Data Protection Manager (SC DPM), musíte podle následujících kroků zjistit, jestli vaše servery DPM potřebují aktualizaci agenta zálohování Azure:

1. Přihlaste se k serveru SC DPM jako správce.
2. Otevřete konzolu DPM.
3. Klikněte na **Management** v levém dolním navigačním panelu.
4. V rámci informací, které se zobrazí v levém navigačním panelu, vyhledejte informace o verzi agenta Azure Backup.
5. Pokud je verze nižší než 2.0.9083.0, stáhněte si nejnovější instalační program agenta zálohování Azure a spusťte instalační program na serveru DPM a aktualizujte agenta Azure Backup.

Opakujte výše uvedené kroky pro všechny servery DPM ve vašem prostředí.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [zálohovat počítače s Windows pomocí agenta Azure Backup MARS](backup-windows-with-mars-agent.md)
