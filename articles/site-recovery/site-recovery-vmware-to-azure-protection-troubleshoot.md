---
title: "Řešení potíží s ochrany selhání VMware nebo fyzický do Azure | Microsoft Docs"
description: "Tento článek popisuje běžné chyby replikace počítače VMware a řešení potíží s nimi"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: asgang
ms.openlocfilehash: 9e0c602646009b20c8d4f8a29d55b7f44a089131
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Řešení problémů s replikací místní VMware nebo fyzický server
Při ochraně virtuálních počítačů VMware nebo fyzických serverů pomocí Azure Site Recovery, může se zobrazit konkrétní chybová zpráva. Tento článek podrobně popisuje některé z nejběžnějších chybových zpráv došlo, společně s řešení potíží s kroky k jejich řešení.


## <a name="initial-replication-is-stuck-at-0"></a>Počáteční replikace se zasekla v umístění % 0
Většina selhání je počáteční replikace, které jsme na podporu jsou způsobeny problémy s připojením mezi zdrojový server proces serveru nebo proces serveru do Azure.
Pro většině případů můžete řešení těchto problémů pomocí následujících kroků uvedených níže.

###<a name="check-the-following-on-source-machine"></a>Zkontrolujte následující na ZDROJOVÉM počítači
* Z příkazového řádku počítače zdrojového serveru použijte Telnet na příkaz ping procesový Server s port https (standardně 9443), jak vidíte níže, pokud jsou k dispozici žádné problémy se síťovým připojením nebo blokující problémy port brány firewall.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Pomocí služby Telnet, nepoužívejte příkaz PING k testování připojení.  Pokud Telnet není nainstalovaná, postupujte podle kroků seznamu [sem](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Pokud se nelze připojit, povolit příchozí port 9443 na Procesovém serveru a zkontrolujte, pokud tento problém pořád ukončí. Byl některých případech, kdy byl procesový server za hraniční sítě, který byl příčinou tohoto problému.

* Zkontrolujte stav služby `InMage Scout VX Agent – Sentinel/OutpostStart` Pokud není spuštěná a kontrola Pokud problém přetrvává.   

###<a name="check-the-following-on-process-server"></a>Zkontrolujte následující na PROCESOVÉM serveru

* **Zkontrolujte, pokud procesový server není aktivně předání dat do Azure**

Z počítače procesový Server otevřete Správce úloh (stiskněte kombinaci kláves Ctrl-Shift-Esc). Přejděte na kartu výkonu a klikněte na odkaz sledování otevřete prostředků. Ze Správce prostředků, přejděte na kartu síť. Zkontrolujte, pokud cbengine.exe v "Procesy s síťové aktivity" aktivně odeslání velkého objemu dat (v MB).

![Povolení replikace](./media/site-recovery-protection-common-errors/cbengine.png)

Pokud ne, postupujte podle následujících kroků:

* **Zkontrolujte, jestli je procesový server připojit objektů Blob v Azure**: vyberte a zkontrolujte cbengine.exe zobrazení TCP připojení chcete zobrazit, pokud je k dispozici připojení z procesového serveru do adresy URL objektu blob úložiště Azure.

![Povolení replikace](./media/site-recovery-protection-common-errors/rmonitor.png)

Pokud není pak přejděte do ovládacích panelů > služeb, zkontrolujte, zda tyto služby jsou spuštěná:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re) Pokud problém přetrvává, spusťte žádné služby, která není spuštěna a zkontrolujte.

* **Zkontrolujte, jestli je možné se připojit k Azure veřejnou IP adresu přes port 443 procesového serveru**

Otevřete nejnovější CBEngineCurr.errlog z `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` a vyhledejte řetězec: 443 a připojení pokus se nezdařil.

![Povolení replikace](./media/site-recovery-protection-common-errors/logdetails1.png)

Pokud se problémy objevily, z příkazového řádku procesový Server, použijte telnet na příkaz ping vaší Azure veřejné IP adresy (maskovat ve výše image) v CBEngineCurr.currLog přes port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Pokud se nemůžete připojit, potom zkontrolujte, jestli se problém přístup je z důvodu brány firewall nebo Proxy, jak je popsáno v dalším kroku.


* **Zkontrolujte, pokud IP adresa brána firewall na procesový server není přístup blokován**: Pokud používáte pravidla brány firewall založená na adresu IP na serveru, pak stáhnout úplný seznam Microsoft Azure Datacenter rozsahy IP adres z [sem](https://www.microsoft.com/download/details.aspx?id=41653) a přidat je do vaší konfiguraci brány firewall a ujistěte se, že umožňují komunikaci s Azure (a port HTTPS (443)).  Povolte rozsahy IP adres pro oblast Azure svého předplatného a pro oblast Západní USA (používá se pro řízení přístupu a správu identit).

* **Zkontrolujte, pokud adresa URL brána firewall na procesový server není přístup blokován**: Pokud používáte pravidla brány firewall založená na adresu URL na serveru, zkontrolujte následující adresy URL se přidají do konfigurace brány firewall.

  `*.accesscontrol.windows.net:` Používá se k řízení přístupu a správě identit.

  `*.backup.windowsazure.com:` Používá se k orchestraci a přenosu dat replikace.

  `*.blob.core.windows.net:` Slouží pro přístup k účtu úložiště, že úložiště replikovaná data

  `*.hypervrecoverymanager.windowsazure.com:` Používá se pro orchestraci a operace správy replikací.

  `time.nist.gov` a `time.windows.com`: používá k ověření synchronizaci času mezi systémem a globálním časem.

Adresy URL pro **cloudu Azure Government**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Zkontrolujte, pokud nastavení proxy serveru na Procesovém serveru neblokují přístup**.  Pokud používáte Proxy Server, zajistěte, aby že název proxy serveru je řešení serverem DNS.
Chcete-li zkontrolovat, co jste zadali v době instalace konfigurační Server. Přejděte ke klíči registru

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Teď zajistíte, že stejné nastavení jsou používány agentem Azure Site Recovery k odesílání dat.
Zálohování Microsoft Azure Search

![Povolení replikace](./media/site-recovery-protection-common-errors/mab.png)

Otevřete ho a klikněte na akci > změnit vlastnosti. Karta Konfigurace proxy serveru měli byste vidět adresu proxy serveru, která by měla být stejná, jak je uvedeno nastavení registru. V opačném případě změňte se stejnou adresou.

![Povolení replikace](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Zkontrolujte, pokud omezení šířky pásma není omezen na Procesovém serveru**: zvětšit šířku pásma a zkontrolujte, zda stále existuje problém.

##<a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc, následně je publikovat dotazu [fórum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme aktivní komunitě a jeden z našich technici bude moct vám pomůže.
