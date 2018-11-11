---
title: Instalace služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat agenta služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 14be544c53bf3393466cfa33b2ad815f07d0005d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007412"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>Instalace služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzické servery

Při nastavení zotavení po havárii pro virtuální počítače VMware a fyzických serverů požíváním [Azure Site Recovery](site-recovery-overview.md), můžete nainstalovat [služby Site Recovery Mobility](vmware-physical-mobility-service-overview.md) na každém místních virtuálních počítačů VMware a fyzických serverů.  Služba Mobility zaznamenává datové zápisy na počítači a předává je na procesní server Site Recovery.

## <a name="install-on-windows-machine"></a>Nainstalujte na počítači s Windows

Na každém počítači s Windows, kterou chcete chránit, postupujte takto:

1. Zkontrolujte, zda je síťové připojení mezi počítačem a procesovým serverem. Pokud jste nenastavili samostatný procesový server, pak ve výchozím nastavení běží na konfiguračním serveru.
1. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet by měl mít oprávnění správce, místní nebo doménový. Pouze pro nabízenou instalaci a aktualizací agenta, použijte tento účet.
2. Pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele v místním počítači následujícím způsobem:
    - V klíči registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System přidáte novou hodnotu DWORD: **LocalAccountTokenFilterPolicy**. Nastavte hodnotu na **1**.
    -  Chcete-li to provést z příkazového řádku, spusťte následující příkaz:  
   "REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. V bráně Windows Firewall na počítači, který chcete chránit, vyberte **povolit aplikaci nebo funkci průchod bránou Firewall**. Povolit **sdílení souborů a tiskáren** a **Windows Management Instrumentation (WMI)**. Pro počítače, které patří do nějaké domény můžete nakonfigurovat nastavení brány firewall pomocí objektu zásad skupiny (GPO).

   ![Nastavení brány firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Chcete-li to provést, přihlaste se ke konfiguračnímu serveru.
5. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\home\svsystems\bin.
6. Na **spravovat účty** kartu, vyberte možnost **přidat účet**.
7. Přidejte účet, který jste vytvořili.
8. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.

## <a name="install-on-linux-machine"></a>Nainstalujte na počítač s Linuxem

Na každém počítači s Linuxem, který chcete chránit postupujte takto:

1. Zkontrolujte, zda je síťové připojení mezi počítačem s Linuxem a procesovým serverem.
2. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet musí být na zdrojovém serveru s Linuxem uživatelem **root**. Použijte tento účet pouze pro nabízenou instalaci a aktualizace.
3. Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
4. Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
5. Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
4. SFTP subsystému a ověřování heslem v souboru sshd_config povolte. Chcete-li to provést, přihlaste se jako **kořenové**.
5. V **/etc/ssh/sshd_config** souboru, vyhledejte řádek, který začíná **PasswordAuthentication**.
6. Zrušte na řádku komentář a změňte hodnotu na **Ano**.
7. Vyhledejte řádek, který začíná **subsystému**, a zrušte komentář na řádku.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Restartujte službu **sshd**.
9. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Chcete-li to provést, přihlaste se ke konfiguračnímu serveru.
10. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\home\svsystems\bin.
11. Na **spravovat účty** kartu, vyberte možnost **přidat účet**.
12. Přidejte účet, který jste vytvořili.
13. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.

## <a name="next-steps"></a>Další postup

Po instalaci služby Mobility na webu Azure Portal, vyberte **+ replikovat** chcete začít chránit tyto virtuální počítače. Další informace o povolení replikace pro [VMware VMs(vmware-azure-enable-replication.md) a [fyzických serverů](physical-azure-disaster-recovery.md#enable-replication).


