---
title: "Správa procesu serveru v Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje správu serveru procesu nastavení pro virtuální počítač VMware a replikaci fyzický server v Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: 
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>Spravovat servery procesu

Ve výchozím nastavení je procesový server používají při replikaci virtuálních počítačů VMware nebo fyzických serverů do Azure nainstalována na místní počítač konfigurace serveru. Existuje několik instancí, ve kterých budete muset nastavit samostatný procesový server:

- Pro nasazení ve velkých organizacích může být nutné další místní servery proces škálování kapacity.
- Navrácení služeb po obnovení, budete potřebovat dočasný procesní server nastavit v Azure. Tento virtuální počítač můžete odstranit, pokud se provádí navrácení služeb po obnovení. 

Tento článek obsahuje souhrn typické úlohy správy pro tyto servery další proces.

## <a name="upgrade-a-process-server"></a>Upgrade serveru pro proces

Upgrade serveru proces spuštěn místně nebo v Azure (pro účely navrácení služeb po obnovení), následujícím způsobem:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Obvykle když použijete Image Galerie Azure k vytvoření procesový server v Azure pro účely navrácení služeb po obnovení, je spuštěna na nejnovější dostupnou verzi. Site Recovery týmy vydání opravy a vylepšení v pravidelných intervalech, a doporučujeme že zachovat aktuální proces servery.



## <a name="reregister-a-process-server"></a>Opětovná registrace procesového serveru

Pokud potřebujete znovu zaregistrujte procesový server běžících místně nebo v Azure, s konfiguračním serverem, postupujte takto:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po uložení nastavení, postupujte takto:

1. Na procesní server otevřete příkazový řádek správce.
2. Přejděte do složky **%PROGRAMDATA%\ASR\Agent**, a spusťte příkaz:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Upravte nastavení proxy serveru pro místní server procesu

Pokud procesový server používá proxy server pro připojení k Site Recovery v Azure, použijte tento postup, pokud se budete muset upravit stávající nastavení proxy serveru.

1. Přihlaste se k počítači serveru procesu. 
2. Otevřete okno příkazového prostředí PowerShell pro správce a spusťte následující příkaz:
  ```
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


## <a name="remove-a-process-server"></a>Odebrání serveru procesu

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


