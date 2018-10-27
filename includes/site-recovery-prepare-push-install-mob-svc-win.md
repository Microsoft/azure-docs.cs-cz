---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165940"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Příprava nabízené instalace na počítači Windows

1. Zkontrolujte, zda je síťové připojení mezi Windows počítačem a procesovým serverem.
1. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet by měl mít oprávnění správce, místní nebo doménový. Pouze pro nabízenou instalaci a aktualizací agenta, použijte tento účet.

   > [!NOTE]
   > Pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele v místním počítači. Chcete-li zakázat vzdálené řízení přístupu uživatele, pod klíčem registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System přidat novou hodnotu DWORD: **LocalAccountTokenFilterPolicy**. Nastavte hodnotu na **1**. K provedení této úlohy z příkazového řádku, spusťte následující příkaz:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. V bráně Windows Firewall v počítači, který chcete chránit, vyberte **povolit aplikaci nebo funkci průchod bránou Firewall**. Povolit **sdílení souborů a tiskáren** a **Windows Management Instrumentation (WMI)**. Pro počítače, které patří do nějaké domény můžete nakonfigurovat nastavení brány firewall pomocí objektu zásad skupiny (GPO).

   ![Nastavení brány firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Postupujte následovně:

    a. Přihlaste se ke konfiguračnímu serveru.

    b. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\home\svsystems\bin.

    c. Na **spravovat účty** kartu, vyberte možnost **přidat účet**.

    d. Přidejte účet, který jste vytvořili.

    e. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.
