---
title: Příprava zdrojových počítačů k instalaci služby Mobility service prostřednictvím push instalace pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak připravit server na instalaci agenta mobility prostřednictvím nabízené instalace pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186456"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Připravte zdrojový stroj pro push instalaci agenta mobility

Když nastavíte zotavení po havárii pro virtuální počítače VMware a fyzické servery pomocí [Azure Site Recovery](site-recovery-overview.md), nainstalujete [službu Mobility site recovery do](vmware-physical-mobility-service-overview.md) každého místního virtuálního počítače VMware a fyzického serveru.  Služba Mobility zachytí zápisy dat v počítači a předá je na server procesu obnovení webu.

## <a name="install-on-windows-machine"></a>Instalace na počítači s Windows

Na každém počítači se systémem Windows, který chcete chránit, postupujte takto:

1. Ujistěte se, že je síťové připojení mezi počítačem a procesovým serverem. Pokud jste nenastavili samostatný procesní server, pak je ve výchozím nastavení spuštěn na konfiguračním serveru.
1. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet by měl mít práva správce, místní nebo doménová. Tento účet používejte pouze pro nabízenou instalaci a pro aktualizace agenta.
2. Pokud nepoužíváte účet domény, zakažte řízení vzdáleného přístupu uživatelů v místním počítači následujícím způsobem:
    - V části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key přidejte nový dword: **LocalAccountTokenFilterPolicy**. Nastavte hodnotu na **1**.
    -  Chcete-li to provést na příkazovém řádku, spusťte následující příkaz:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. V bráně Windows Firewall na počítači, který chcete chránit, vyberte **Povolit aplikaci nebo funkci prostřednictvím brány firewall**. Povolte **sdílení souborů a tiskáren** a **službu WMI (WMI).** U počítačů, které patří do domény, můžete nakonfigurovat nastavení brány firewall pomocí objektu zásad skupiny (GPO).

   ![Nastavení brány firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Chcete-li to provést, přihlaste se k konfiguračnímu serveru.
5. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\ASR\home\svsystems\bin.
6. Na kartě **Spravovat účty** vyberte **Přidat účet**.
7. Přidejte účet, který jste vytvořili.
8. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.

## <a name="install-on-linux-machine"></a>Instalace na počítači s Linuxem

Na každém počítači s Linuxem, který chcete chránit, postupujte takto:

1. Ujistěte se, že existuje síťové připojení mezi počítačem S Linuxem a procesním serverem.
2. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet musí být na zdrojovém serveru s Linuxem uživatelem **root**. Tento účet používejte pouze pro nabízenou instalaci a pro aktualizace.
3. Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
4. Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
5. Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
4. Povolte ověřování podsystému SFTP a hesla v souboru sshd_config. Chcete-li to provést, přihlaste se jako **root**.
5. V souboru **/etc/ssh/sshd_config** najděte řádek začínající **ověřováním hesla**.
6. Odkomentujte řádek a změňte hodnotu na **ano**.
7. Najděte řádek, který začíná **subsystémem**, a odkomentujte řádek.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Restartujte službu **sshd**.
9. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Chcete-li to provést, přihlaste se k konfiguračnímu serveru.
10. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\home\svsystems\bin.
11. Na kartě **Spravovat účty** vyberte **Přidat účet**.
12. Přidejte účet, který jste vytvořili.
13. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus na replikovaných počítačích

Pokud jsou počítače, které chcete replikovat, spuštěn aktivní antivirový software, vyloučíte instalační složku služby Mobility z antivirových operací (*C:\ProgramData\ASR\agent*). Tím je zajištěno, že replikace funguje podle očekávání.

## <a name="next-steps"></a>Další kroky

Po instalaci služby mobility vyberte na webu Azure Portal **možnost + Replikovat** a začněte tyto virtuální počítače chránit. Další informace o povolení replikace pro [virtuální zařízení VMware](vmware-azure-enable-replication.md) a [fyzické servery](physical-azure-disaster-recovery.md#enable-replication).


