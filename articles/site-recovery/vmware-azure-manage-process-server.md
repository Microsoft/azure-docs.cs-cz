---
title: Spravovat procesový server pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje spravovat procesový server nastavit pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 05e77821ffe913a8c6669d58a3145635d230ff2b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846993"
---
# <a name="manage-process-servers"></a>Správa procesních serverů

Procesový server použít při replikaci virtuálních počítačů VMware nebo fyzických serverů do Azure je ve výchozím nastavení nainstalován na počítači místní konfigurační server. Existuje několik instancí, ve kterých budete muset nastavit samostatný procesový server:

- Pro velká nasazení můžete potřebovat další místní Procesové servery pro horizontální kapacity.
- Pro navrácení služeb po obnovení, budete potřebovat dočasný procesní server v Azure. Po dokončení navrácení služeb po obnovení můžete odstranit tento virtuální počítač. 

Tento článek shrnuje obvyklé úlohy správy těchto dalších procesových serverů.

## <a name="upgrade-a-process-server"></a>Upgrade procesového serveru

Upgradujte procesový server spuštěn v místním prostředí nebo v Azure (pro účely navrácení služeb po obnovení), následujícím způsobem:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Obvykle když použijete k vytvoření procesového serveru v Azure pro účely navrácení služeb po obnovení Image z Galerie Azure, běží nejnovější dostupnou verzi. Týmy, které Site Recovery vydání opravy a vylepšení v pravidelných intervalech, a doporučujeme, abyste že informujte procesových serverů.

## <a name="balance-the-load-on-process-server"></a>Vyrovnávání zatížení na procesovém serveru

K vyrovnávání zatížení mezi dvěma servery procesu

1. Přejděte do **trezor služby Recovery Services** > **spravovat** > **infrastruktura Site Recovery** > **pro VMware a fyzických počítačů** > **konfigurační servery**.
2. Klikněte na konfiguračním serveru, do kterého jsou registrované procesových serverů.
3. Seznam procesů serverů zaregistrovaných do konfigurační servery jsou k dispozici na stránce.
4. Klikněte na procesovém serveru, na který chcete upravit zatížení.

    ![Nástroje DSM](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Můžete buď používat **nástroj pro vyrovnávání zatížení** nebo **přepínač** možnosti, jak je popsáno níže, podle požadavku.

### <a name="load-balance"></a>Nástroj pro vyrovnávání zatížení

Díky této možnosti můžete vybrat jeden nebo více virtuálních počítačů a můžete přenést na jiný procesový server.

1. Klikněte na **Vyrovnávání zatížení**, vyberte cílový procesový server z rozevíracího seznamu. Klikněte na tlačítko **OK**.

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klikněte na **vyberte počítače, které**, vyberte virtuální počítače, které chcete přesunout z aktuálního procesu serveru na cílový procesový server. Pro každý virtuální počítač se zobrazí podrobnosti průměr dat změny.
3. Klikněte na **OK**. Sledovat průběh úlohy v části **trezor služby Recovery Services** > **monitorování** > **úlohy Site Recovery**.
4. Bude trvat 15 minut, než se změny tak, aby odrážely po úspěšném dokončení této operace nebo [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) pro přímý vliv.

### <a name="switch"></a>Přepínač

Pomocí této možnosti se celé zatížení chráněný v rámci procesu serveru přesune na jiný procesový server.

1. Klikněte na **přepínač**, vyberte cílový procesový server, klikněte na tlačítko **OK**.

    ![Přepínač](media/vmware-azure-manage-process-server/Switch.PNG)

2. Sledovat průběh úlohy v části **trezor služby Recovery Services** > **monitorování** > **úlohy Site Recovery**.
3. Bude trvat 15 minut, než se změny tak, aby odrážely po úspěšném dokončení této operace nebo [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) pro přímý vliv.

## <a name="reregister-a-process-server"></a>Opětovná registrace procesového serveru

Pokud je potřeba znovu zaregistrujte procesový server spuštěné místně nebo v Azure, s konfiguračním serverem, udělejte toto:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po uložení nastavení, postupujte takto:

1. Na procesovém serveru otevřete příkazový řádek správce.
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**, a spusťte příkaz:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Upravit nastavení proxy serveru pro místní procesový server

Pokud na procesovém serveru používá proxy server pro připojení k Site Recovery v Azure, pomocí tohoto postupu, pokud je potřeba upravit stávající nastavení proxy serveru.

1. Přihlaste se do počítače serveru procesu. 
2. Otevřete okno příkazového řádku Powershellu pro správce a spusťte následující příkaz:
  ```powershell
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**, a spusťte následující příkaz:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Odebrat procesového serveru

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Správa antivirový software na procesových serverech.

Pokud je na samostatný procesový server nebo hlavní cílový server aktivní antivirový software, vylučte z operace antivirový program následující složky:


- Agent C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Proces serveru instalační adresář, například: C:\Program Files (x86) \Microsoft Azure Site Recovery

