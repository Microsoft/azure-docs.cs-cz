---
title: Spravovat procesový server pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje spravovat procesový server nastavit pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 8a9342a2354cd4c92fa0230965b4eef6284ee826
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209118"
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


