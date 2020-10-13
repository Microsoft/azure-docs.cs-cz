---
title: Správa procesového serveru pro zotavení po havárii virtuálních počítačů VMware nebo fyzického serveru v Azure Site Recovery
description: Tento článek popisuje, jak spravovat procesový Server pro zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361380"
---
# <a name="manage-process-servers"></a>Správa procesních serverů

Tento článek popisuje běžné úlohy pro správu Site Recovery procesového serveru.

Procesový server slouží k přijímání, optimalizaci a posílání dat replikace do Azure. Provádí taky nabízenou instalaci služby mobility na virtuální počítače VMware a fyzické servery, které chcete replikovat, a provádí automatické zjišťování místních počítačů. Pro replikaci místních virtuálních počítačů VMware nebo fyzických serverů do Azure se procesový Server nainstaluje ve výchozím nastavení na počítači konfiguračního serveru. 

- U rozsáhlých nasazení budete možná potřebovat další místní procesní servery pro škálování kapacity.
- Pro navrácení služeb po obnovení z Azure do místního prostředí musíte v Azure nastavit dočasný procesový Server. Tento virtuální počítač můžete odstranit, když se provede navrácení služeb po obnovení. 

Přečtěte si další informace o procesovém serveru.


## <a name="upgrade-a-process-server"></a>Upgrade procesového serveru

Když nasazujete procesový Server v místním prostředí nebo jako virtuální počítač Azure pro navrácení služeb po obnovení, je nainstalovaná nejnovější verze procesového serveru. Site Recovery Teams vydává opravy a vylepšení pravidelně a doporučujeme udržovat procesové servery v aktuálním stavu. Procesový Server můžete upgradovat následujícím způsobem:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Přesunutí virtuálních počítačů za účelem vyrovnávání zatížení procesového serveru

Vyvážení zátěže přesunutím virtuálních počítačů mezi dvěma procesových serverů následujícím způsobem:

1. V trezoru v části **Spravovat** klikněte na **Site Recovery infrastruktura**. V části **pro fyzické počítače VMware &** klikněte na **konfigurační servery**.
2. Klikněte na konfigurační server, se kterým jsou zaregistrované procesní servery.
3. Klikněte na procesový Server, pro který chcete vyrovnávat zatížení provozu.

    ![Snímek obrazovky ukazuje procesový Server, pro který můžete vyrovnávat zatížení provozu.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klikněte na **Vyrovnávání zatížení**, vyberte cílový procesový Server, na který chcete přesunout počítače. Pak klikněte na **OK** .

    ![Snímek obrazovky zobrazuje podokno vyrovnávání zatížení s vybraným cílovým procesovým serverem.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klikněte na **Vybrat počítače**a vyberte počítače, které chcete přesunout z aktuálního na cílový procesový Server. Pro každý virtuální počítač se zobrazí podrobnosti o průměrné změně dat. Pak klikněte na **OK**. 
3. V trezoru Sledujte průběh úlohy pod **sledováním**  >  **Site Recovery úloh**.

Změny se projeví na portálu tím, že bude trvat přibližně 15 minut. V případě rychlejšího efektu [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Přepnout celou úlohu na jiný procesový Server

Přesuňte celé zatížení zpracovávané procesovým serverem na jiný procesový Server následujícím způsobem:

1. V trezoru v části **Spravovat** klikněte na **Site Recovery infrastruktura**. V části **pro fyzické počítače VMware &** klikněte na **konfigurační servery**.
2. Klikněte na konfigurační server, se kterým jsou zaregistrované procesní servery.
3. Klikněte na procesový Server, ze kterého chcete úlohu přepnout.
4. Klikněte na **přepínač**, vyberte cílový procesový Server, na který chcete přesunout úlohu. Pak klikněte na **OK** .

    ![Snímek obrazovky se zobrazí v podokně vybrat cílový proces serveru.](media/vmware-azure-manage-process-server/Switch.PNG)

5. V trezoru Sledujte průběh úlohy pod **sledováním**  >  **Site Recovery úloh**.

Změny se projeví na portálu tím, že bude trvat přibližně 15 minut. V případě rychlejšího efektu [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registrace hlavního cílového serveru

Hlavní cílový server se nachází na konfiguračním serveru a procesových serverech se škálováním na více instancí. Musí být zaregistrovaný na konfiguračním serveru. V případě, že v této registraci dojde k selhání, může to mít vliv na stav chráněných položek. Pokud chcete zaregistrovat hlavní cílový server s konfiguračním serverem, přihlaste se ke konkrétnímu konfiguračnímu serveru nebo procesu serveru se škálováním na více instancí, na kterém je registrace nutná. Přejděte do složky **%ProgramData%\ASR\Agent**a spusťte následující příkaz na příkazovém řádku správce.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Opětovná registrace procesového serveru

Znovu zaregistrujte procesový Server, na kterém běží místně, nebo na virtuálním počítači Azure pomocí konfiguračního serveru následujícím způsobem:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po uložení nastavení proveďte následující kroky:

1. Na procesovém serveru otevřete příkazový řádek správce.
2. Přejděte do složky **%ProgramData%\ASR\Agent**a spusťte příkaz:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Úprava nastavení proxy serveru pro místní procesový Server

Pokud místní procesový Server používá proxy server pro připojení k Azure, můžete nastavení proxy upravit následujícím způsobem:

1. Přihlaste se k počítači procesového serveru. 
2. Otevřete okno příkazového řádku PowerShellu pro správu a spusťte následující příkaz:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Přejděte do složky **%ProgramData%\ASR\Agent**a spusťte tento příkaz:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Odebrání procesového serveru

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Vyloučení složek ze antivirového softwaru

Pokud je antivirový software spuštěný na procesovém serveru se škálováním na více instancí (nebo na hlavním cílovém serveru), vylučte z antivirové operace následující složky:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Instalační adresář procesového serveru. Například: C:\Program Files (x86) \Microsoft Azure Site Recovery