---
title: Řešení problémů replikace pro virtuální počítač VMware a replikací fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek poskytuje, řešení potíží pro běžné potíže s replikací při replikaci virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: f305f552d576f58914bc33351331f1da3c68bc23
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951644"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení problémů replikace pro virtuální počítače VMware a fyzické servery

Pokud chráníte virtuální počítače VMware nebo fyzických serverů požíváním Azure Site Recovery, může se zobrazit zpráva konkrétní chyba. Tento článek popisuje některé běžné problémy se můžou vyskytnout při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Potíže s úvodní replikací.

V mnoha případech jsou selhání počáteční replikace, na které jsme na webu podpory kvůli problémům s připojením mezi zdrojovém serveru proces nebo proces serveru do Azure. Většině případů můžete tyto potíže řešit pomocí následujících kroků uvedených dole.

### <a name="verify-the-source-machine"></a>Ověřte, zdrojový počítač
* Z příkazového řádku počítače zdrojového serveru použijte službu Telnet příkaz ping procesový Server s port https (standardně 9443), jak vidíte níže, jestli jsou všechny problémy se síťovým připojením nebo brány firewall portu blokující problémy.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Použít službu Telnet, nepoužívejte příkazem PING otestovat připojení.  Pokud není nainstalovaný protokol Telnet, postupujte podle seznamu kroků [zde](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Pokud se nelze připojit, povolit příchozí port 9443 na Procesovém serveru a zkontrolujte, pokud problém pořád ukončen. Byl někdy, ve kterém byl procesový server za hraniční síti, která je příčinou tohoto problému.

* Zkontrolujte, že stav služby `InMage Scout VX Agent – Sentinel/OutpostStart` Pokud není spuštěný a zkontrolujte Pokud problém stále přetrvává.   

## <a name="verify-the-process-server"></a>Ověřte, že procesový server

* **Zaškrtněte, pokud procesový server je aktivně doručením (push) dat do Azure**

Z počítač procesového serveru otevřete Správce úloh (stisknutím klávesy Ctrl-Shift-Esc). Přejděte na kartu výkonu a klikněte na odkaz Monitor otevřít zdroj. Z Resource Manageru, přejděte na kartu síť. Zaškrtněte, pokud cbengine.exe v "Procesy s aktivitou sítě" aktivně odesílá velké objemy dat (v MB).

![Povolení replikace](./media/vmware-azure-troubleshoot-replication/cbengine.png)

V opačném případě postupujte podle kroků uvedených dole:

* **Zkontrolujte, jestli je procesový server moct připojit objektů Blob v Azure**: vyberte a zkontrolujte cbengine.exe zobrazíte "připojení TCP' podívejte se, pokud je připojení z procesového serveru do služby Azure Storage blob URL.

![Povolení replikace](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Pokud není pak přejděte do ovládacích panelů > služeb, zkontrolujte, jestli jsou tyto služby do provozu:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(I opětovné) Pokud problém stále přetrvává spusťte žádné služby, která není spuštěná a kontrola.

* **Zkontrolujte, jestli je procesový server moct připojit k veřejné IP adrese Azure pomocí portu 443**

Otevřete nejnovější CBEngineCurr.errlog z `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` a vyhledejte: 443 a připojení pokus se nezdařil.

![Povolení replikace](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Pokud dojde k problémům, z příkazového řádku procesový Server, použijte telnet příkaz ping vaše veřejné IP adrese Azure (v maskována nad image) součástí CBEngineCurr.currLog přes port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Pokud se nemůžete připojit, potom zkontrolujte, jestli problém s přístupem k je kvůli bráně firewall nebo Proxy, jak je popsáno v dalším kroku.


* **Zaškrtněte, pokud není IP adresa brány firewall založeným na Procesovém serveru blokuje přístup**: Pokud používáte pravidla brány firewall založená na adresu IP serveru, pak si stáhnout kompletní seznam Microsoft Azure Datacenter rozsahy IP adres z [tady](https://www.microsoft.com/download/details.aspx?id=41653) a přidat je do konfigurace brány firewall k zajištění, že byla povolena komunikace s Azure (a port HTTPS (443)).  Povolte rozsahy IP adres pro oblast Azure svého předplatného a pro oblast Západní USA (používá se pro řízení přístupu a správu identit).

* **Zaškrtněte, pokud není brány firewall založeným na adresu URL na Procesovém serveru blokuje přístup**: Pokud používáte pravidla brány firewall na základě adresy URL na serveru, zkontrolujte následující adresy URL se přidají do konfigurace brány firewall.

  `*.accesscontrol.windows.net:` Používá se k řízení přístupu a správě identit.

  `*.backup.windowsazure.com:` Používá se k orchestraci a přenosu dat replikace.

  `*.blob.core.windows.net:` Používá se pro přístup k účtu úložiště, který ukládá replikovaná data

  `*.hypervrecoverymanager.windowsazure.com:` Používá se pro orchestraci a operace správy replikací.

  `time.nist.gov` a `time.windows.com`: používá se ke kontrole časové synchronizace mezi systémovým a globálním časem.

Adresy URL pro **Cloud Azure Government**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Zaškrtněte, pokud nejsou nastavení serveru Proxy na Procesovém serveru blokuje přístup**.  Pokud používáte Proxy Server, zajistěte, aby že serverem DNS překládá název proxy serveru.
Chcete-li zkontrolovat, co jste zadali v době instalace konfiguračního serveru. Přejděte ke klíči registru

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Teď zkontrolujte, že stejné nastavení se používá pomocí agenta Azure Site Recovery k odesílání dat.
Zálohování Microsoft Azure Search

Otevřete ho a klikněte na akci > změnit vlastnosti. Na kartě Konfigurace proxy serveru měli byste vidět adresu proxy serveru, která by měla být stejná, jak je znázorněno v nastavení registru. Pokud ne, změňte ji na stejné adrese.


* **Zaškrtněte, pokud není omezen omezení šířky pásma na Procesovém serveru**: zvětšit šířku pásma a zkontrolujte, jestli problém stále existuje.

## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc, odešle se dotaz a [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme k dispozici aktivní komunitě a jednu naši technici budou schopni pomoct.
