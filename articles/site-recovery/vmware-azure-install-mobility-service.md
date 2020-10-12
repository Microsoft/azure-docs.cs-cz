---
title: Příprava zdrojových počítačů na instalaci služby mobility přes nabízenou instalaci pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure | Microsoft Docs
description: Naučte se, jak připravit server pro instalaci agenta mobility pomocí nabízené instalace pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524993"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Příprava zdrojového počítače na nabízenou instalaci agenta mobility

Při nastavování zotavení po havárii pro virtuální počítače VMware a fyzické servery pomocí [Azure Site Recovery](site-recovery-overview.md)nainstalujete [službu Site Recovery mobility](vmware-physical-mobility-service-overview.md) na každý místní virtuální počítač VMware a fyzický server.  Služba mobility zachycuje zápisy dat na počítači a předá je na Site Recovery procesový Server.

## <a name="install-on-windows-machine"></a>Nainstalovat do počítače se systémem Windows

Na každém počítači s Windows, který chcete chránit, udělejte toto:

1. Ujistěte se, že mezi počítačem a procesovým serverem existuje síťové připojení. Pokud jste nenastavili samostatný procesový Server, pak ve výchozím nastavení běží na konfiguračním serveru.
1. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet by měl mít práva správce, a to buď místní, nebo doména. Tento účet používejte pouze pro nabízenou instalaci a pro aktualizace agenta.
2. Pokud nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele následujícím způsobem:
    - V části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System klíč registru přidejte novou hodnotu DWORD: **LocalAccountTokenFilterPolicy**. Nastavte hodnotu na **1**.
    -  Pokud to chcete provést na příkazovém řádku, spusťte následující příkaz:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. V bráně Windows Firewall na počítači, který chcete chránit, vyberte možnost **povolení aplikace nebo funkce přes bránu firewall**. Povolte **sdílení souborů a tiskáren** a **rozhraní WMI (Windows Management Instrumentation) (WMI)**. Pro počítače, které patří do domény, můžete nakonfigurovat nastavení brány firewall pomocí objektu Zásady skupiny (GPO).

   ![Nastavení brány firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Uděláte to tak, že se přihlásíte ke konfiguračnímu serveru.
5. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce%ProgramData%\ASR\home\svsystems\bin.
6. Na kartě **Spravovat účty** vyberte **Přidat účet**.
7. Přidejte účet, který jste vytvořili.
8. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.

## <a name="install-on-linux-machine"></a>Nainstalovat do počítače se systémem Linux

V každém počítači se systémem Linux, který chcete chránit, udělejte toto:

1. Zajistěte, aby bylo síťové připojení mezi počítačem a procesovým serverem se systémem Linux.
2. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet musí být na zdrojovém serveru s Linuxem uživatelem **root**. Tento účet používejte pouze pro nabízenou instalaci a pro aktualizace.
3. Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
4. Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
5. Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
4. Povolit podsystém SFTP a ověřování hesla v souboru sshd_config. Uděláte to tak, že se přihlásíte jako **kořen**.
5. V souboru **sshd_config/etc/ssh/** vyhledejte řádek, který začíná na **PasswordAuthentication**.
6. Odkomentujte řádek a změňte hodnotu na **Ano**.
7. Vyhledejte řádek, který začíná na **subsystému**, a odkomentujte řádek.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Restartujte službu **sshd**.
9. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Uděláte to tak, že se přihlásíte ke konfiguračnímu serveru.
10. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce%ProgramData%\home\svsystems\bin.
11. Na kartě **Spravovat účty** vyberte **Přidat účet**.
12. Přidejte účet, který jste vytvořili.
13. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.
1. Další krok pro aktualizaci nebo ochranu SUSE Linux Enterprise Server 11 nebo RHEL 5 nebo CentOS 5 nebo Debian 7 počítačů. [Ujistěte se, že je na konfiguračním serveru k dispozici nejnovější verze](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Antivirová ochrana na replikovaných počítačích

Pokud mají počítače, které chcete replikovat, spuštěné aktivní antivirový software, ujistěte se, že jste vyloučili instalační složku služby mobility z hlediska antivirové operace (*C:\ProgramData\ASR\agent*). Tím se zajistí, že replikace bude fungovat podle očekávání.

## <a name="next-steps"></a>Další kroky

Po instalaci služby mobility můžete v Azure Portal vybrat **+ replikovat** a začít chránit tyto virtuální počítače. Přečtěte si další informace o povolení replikace pro [virtuální počítače VMware](vmware-azure-enable-replication.md) a [fyzické servery](physical-azure-disaster-recovery.md#enable-replication).


