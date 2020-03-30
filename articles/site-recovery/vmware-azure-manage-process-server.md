---
title: Správa procesního serveru pro virtuální počítače VMware/zotavení po havárii fyzického serveru v azure site recovery
description: Tento článek popisuje správu procesu serveru pro zotavení po havárii virtuálních počítačích VMware nebo fyzických serverů pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257248"
---
# <a name="manage-process-servers"></a>Správa procesních serverů

Tento článek popisuje běžné úkoly pro správu serveru obnovení webu.

Procesní server se používá k přijímání, optimalizaci a odesílání dat replikace do Azure. Provádí také nabízenou instalaci služby Mobility na virtuálních počítačích VMware a fyzických serverech, které chcete replikovat, a provádí automatické zjišťování místních počítačů. Pro replikaci místních virtuálních počítačů VMware nebo fyzických serverů do Azure se procesní server ve výchozím nastavení instaluje v počítači konfiguračního serveru. 

- Pro velká nasazení můžete potřebovat další místní procesní servery pro škálování kapacity.
- Pro navrácení služeb po službou po službě 14: Z Azure do místního je nutné nastavit dočasný procesní server v Azure. Tento virtuální virtuální ms můžete odstranit po dokončení navrácení služeb po selhání. 

Přečtěte si další informace o procesním serveru.


## <a name="upgrade-a-process-server"></a>Upgrade procesního serveru

Když nasadíte procesní server místně nebo jako virtuální počítač Azure pro navrácení služeb po službě po službě 14, nainstaluje se nejnovější verze procesního serveru. Týmy site recovery pravidelně vydávají opravy a vylepšení a doporučujeme udržovat procesní servery aktuální. Procesový server můžete upgradovat následujícím způsobem:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Přesunutí virtuálních měn pro vyvážení zatížení procesního serveru

Vyvažte zatížení přesunutím virtuálních zařízení mezi dvěma procesními servery takto:

1. V trezoru klikněte v části **Spravovat** **infrastrukturu obnovení lokality**. V části **Pro fyzické počítače VMware & klikněte**na **položku Konfigurační servery**.
2. Klikněte na konfigurační server, na kterém jsou zaregistrovány procesní servery.
3. Klikněte na procesní server, pro který chcete vyvážit provoz zatížení.

    ![Načíst rovnováhu](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klepněte na **položku Vyrovnávání zatížení**, vyberte cílový procesový server, na který chcete počítače přesunout. Pak klikněte na **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klepněte na **tlačítko Vybrat počítače**a zvolte počítače, které chcete přesunout z aktuálního na cílový procesový server. Podrobnosti o průměrné změně dat se zobrazí u každého virtuálního počítače. Pak klikněte na **OK**. 
3. V trezoru sledujte průběh úlohy v části**Úlohy obnovení webu** **monitorování** > .

Bude trvat přibližně 15 minut, než se změny projeví na portálu. Chcete-li provést rychlejší efekt, [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Přepnutí celéúlohy na jiný procesový server

Přesuňte celé úlohy zpracované procesovým serverem na jiný procesní server následujícím způsobem:

1. V trezoru klikněte v části **Spravovat** **infrastrukturu obnovení lokality**. V části **Pro fyzické počítače VMware & klikněte**na **položku Konfigurační servery**.
2. Klikněte na konfigurační server, na kterém jsou zaregistrovány procesní servery.
3. Klikněte na procesní server, ze kterého chcete přepnout pracovní vytížení.
4. Klikněte na **Přepnout**, vyberte cílový procesový server, na který chcete přesunout pracovní vytížení. Pak klikněte na **OK**

    ![Přepínač](media/vmware-azure-manage-process-server/Switch.PNG)

5. V trezoru sledujte průběh úlohy v části**Úlohy obnovení webu** **monitorování** > .

Bude trvat přibližně 15 minut, než se změny projeví na portálu. Chcete-li provést rychlejší efekt, [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registrace hlavního cílového serveru

Hlavní cílový server je umístěn na konfiguračním serveru a na horizontálních navýšeních kapacity. Musí být zaregistrován na konfiguračním serveru. V případě, že dojde k chybě v této registraci, může to mít vliv na stav chráněných položek. Chcete-li zaregistrovat hlavní cílový server s konfiguračním serverem, přihlaste se na konkrétní konfigurační server/horizontální navýšení kapacity procesu serveru, na kterém je požadována registrace. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**a na příkazovém řádku správce spusťte následující.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Opětovná registrace procesního serveru

Znovu zaregistrujte procesní server spuštěný místně nebo na virtuálním počítači Azure s konfiguračním serverem následujícím způsobem:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po uložení nastavení postupujte takto:

1. Na procesním serveru otevřete příkazový řádek správce.
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**a spusťte příkaz:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Změna nastavení proxy serveru pro místní procesový server

Pokud místní procesní server používá proxy server pro připojení k Azure, můžete upravit nastavení proxy takto:

1. Přihlaste se k počítači procesního serveru. 
2. Otevřete příkazové okno Prostředí PowerShell pro správu a spusťte následující příkaz:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**a spusťte tento příkaz:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Odebrání procesního serveru

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Vyloučení složek z antivirového softwaru

Pokud je antivirový software spuštěn na horizontálním navýšení kapacity procesního serveru (nebo hlavního cílového serveru), vylučte z antivirových operací následující složky:


- C:\Program Files\Agent služby Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Obnovení webu Microsoft Azure
- Zpracovat instalační adresář serveru. Příklad: C:\Program Files (x86)\Microsoft Azure Site Recovery