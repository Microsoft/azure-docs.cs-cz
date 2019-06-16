---
title: Spravovat procesový server používá pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje spravovat procesový server nastavit pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925624"
---
# <a name="manage-process-servers"></a>Správa procesních serverů

Tento článek popisuje běžné úlohy správy procesový server Site Recovery.

Procesový server se používá pro příjem, optimalizovat a odesílání dat replikace do Azure. Také provádí nabízenou instalaci služby Mobility na virtuální počítače VMware a fyzických serverů, které chcete replikovat, a provádí automatické zjišťování místních počítačů. Pro replikaci místních virtuálních počítačů VMware nebo fyzických serverů do Azure, je na procesovém serveru nainstalované ve výchozím nastavení na počítači serveru configuration. 

- Pro velká nasazení můžete potřebovat další místní Procesové servery pro horizontální kapacity.
- Pro navrácení služeb po obnovení z Azure do místní musíte vytvořit dočasný procesní server v Azure. Po dokončení navrácení služeb po obnovení můžete odstranit tento virtuální počítač. 

Další informace o procesovém serveru.


## <a name="upgrade-a-process-server"></a>Upgrade procesového serveru

Při nasazení procesového serveru místní, nebo jako virtuální počítač Azure pro navrácení služeb po obnovení, je nainstalovaná nejnovější verze procesového serveru. Týmy, které Site Recovery vydání opravy a vylepšení v pravidelných intervalech, a doporučujeme, abyste že informujte procesových serverů. Procesový server můžete upgradovat následovně:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Přesunout virtuální počítače k vyrovnávání zatížení serveru procesu

Vyrovnávání zatížení díky přesunu virtuálních počítačů mezi dvěma procesových serverů, následujícím způsobem:

1. V trezoru v části **spravovat** klikněte na tlačítko **infrastruktura Site Recovery**. V části **pro VMware a fyzických počítačů**, klikněte na tlačítko **konfigurační servery**.
2. Klikněte na konfiguračním serveru, pomocí kterého jsou registrované procesových serverů.
3. Klikněte na procesovém serveru, pro které chcete vyrovnávat zatížení provozu.

    ![Nástroje DSM](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klikněte na tlačítko **Vyrovnávání zatížení**, vyberte cílový procesový server, na který chcete přesunout počítače. Pak klikněte na tlačítko **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klikněte na tlačítko **vyberte počítače, které**a vyberte počítače, které chcete přesunout z aktuální cílový procesový server. Pro každý virtuální počítač se zobrazí podrobnosti průměr dat změny. Pak klikněte na **OK**. 
3. V trezoru, sledovat průběh úlohy v části **monitorování** > **úlohy Site Recovery**.

To bude trvat přibližně 15 minut se změny projeví na portálu. Pro efekt rychlejší [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Přepnout na jiný procesový server celé zatížení

Přesuňte celé zatížení zpracovat procesový server na jiný procesový server, následujícím způsobem:

1. V trezoru v části **spravovat** klikněte na tlačítko **infrastruktura Site Recovery**. V části **pro VMware a fyzických počítačů**, klikněte na tlačítko **konfigurační servery**.
2. Klikněte na konfiguračním serveru, pomocí kterého jsou registrované procesových serverů.
3. Klikněte na procesovém serveru, ze kterého chcete přejít úlohu.
4. Klikněte na **přepínač**, vyberte cílový procesový server, na který chcete přesunout úlohy. Pak klikněte na tlačítko **OK**

    ![Přepínač](media/vmware-azure-manage-process-server/Switch.PNG)

5. V trezoru, sledovat průběh úlohy v části **monitorování** > **úlohy Site Recovery**.

To bude trvat přibližně 15 minut se změny projeví na portálu. Pro efekt rychlejší [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Opětovná registrace procesového serveru

Opětovná registrace procesového serveru spuštěné místně nebo na Virtuálním počítači Azure s konfiguračním serverem následujícím způsobem:

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

Pokud procesového serveru v místním používá proxy server pro připojení k Azure, můžete upravit následující nastavení proxy serveru:

1. Přihlaste se do počítače serveru procesu. 
2. Otevřete okno příkazového řádku Powershellu pro správce a spusťte následující příkaz:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**, a spusťte tento příkaz:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Odebrat procesového serveru

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Vyloučení složek z antivirového softwaru

Pokud antivirový software běží na horizontální navýšení kapacity procesového serveru (nebo hlavní cílový server), vylučte z operace antivirový program následující složky:


- Agent C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Instalační adresář serveru procesu. Příklad: C:\Program Files (x86)\Microsoft Azure Site Recovery