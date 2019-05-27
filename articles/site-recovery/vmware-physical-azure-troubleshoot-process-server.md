---
title: Řešení potíží s Azure Site Recovery procesový server
description: Tento článek popisuje postup řešení potíží s Azure Site Recovery procesový server
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969366"
---
# <a name="troubleshoot-the-process-server"></a>Řešení potíží s procesového serveru

[Site Recovery](site-recovery-overview.md) procesový server se používá při nastavování zotavení po havárii do Azure pro místní virtuální počítače VMware a fyzické servery. Tento článek popisuje postup řešení potíží s procesový server, včetně replikace a připojením.

[Další informace](vmware-physical-azure-config-process-server-overview.md) o procesovém serveru.

## <a name="before-you-start"></a>Než začnete

Než začnete, řešení potíží:

1. Ujistěte se, že rozumíte jak [monitorovat procesových serverů](vmware-physical-azure-monitor-process-server.md).
2. Kontrola osvědčených postupů níže.
3. Ujistěte se, že dodržujete [důležité informace o kapacitě](site-recovery-plan-capacity-vmware.md#capacity-considerations)a použijte pokyny k nastavení velikosti pro [konfigurační server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) nebo [samostatné procesových serverů](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Osvědčené postupy pro nasazení procesového serveru

Pro optimální výkon procesových serverů jsme jste shrnout počet obecné osvědčené postupy.

**Osvědčený postup** | **Podrobnosti**
--- |---
**Použití** | Ujistěte se, že konfigurační server nebo samostatný procesový server používají pouze pro zamýšlený účel. Není cokoli, spusťte na počítači.
**IP adresa** | Ujistěte se, že procesový server nemá statickou IPv4 adresu a nemá nakonfigurované překladu adres.
**Využití paměti a procesoru ovládacího prvku** |Zachovejte využití procesoru a paměti, v části 70 %.
**Zkontrolujte volné místo** | Volné místo odkazuje na místo na disku mezipaměti na procesovém serveru. Replikační data uložená v mezipaměti před nahráním do služby Azure.<br/><br/> Zachovejte volné místo nad 25 %. Pokud klesne pod 20 %, replikace se omezí pro replikované počítače, které jsou spojeny s procesovým serverem.

## <a name="check-process-server-health"></a>Zkontrolujte stav zpracování procesu serveru

Prvním krokem při řešení potíží je kontrolovat stav a stav procesového serveru. Chcete-li to provést, zkontrolujte všechny výstrahy, zkontrolujte, zda jsou spuštěny požadované služby a ověřte, zda je prezenční signál z procesového serveru. Tyto kroky jsou shrnuty v následující obrázek, za nímž následuje postupy, které vám pomohou provést kroky.

![Řešení potíží s stav zpracování procesu serveru](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1: Řešení potíží s procesu výstrahy týkající se stavu serveru

Procesový server generuje počet výstrah stavu služby. Tyto výstrahy a doporučené akce, jsou shrnuty v následující tabulce.

**Typ výstrahy** | **Chyba** | **Řešení potíží**
--- | --- | --- 
![V pořádku][green] | Žádný  | Procesový server je v pořádku a připojené.
![Upozornění][yellow] | Nejsou zadané služby spuštěné. | 1. Zkontrolujte, jestli jsou spuštěné služby.<br/> 2. Pokud služby synchronizace jsou spuštěné podle očekávání, postupujte podle pokynů níže [řešení potíží s připojením a replikace](#check-connectivity-and-replication).
![Upozornění][yellow]  | Využití procesoru > 80 % po dobu posledních 15 minut. | 1. Nepřidávejte nové počítače.<br/>2. Zkontrolujte, že počet virtuálních počítačů pomocí procesového serveru odpovídá [definované mezní hodnoty](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte zřízení [další procesový server](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).
![Kritická][red] |  Využití procesoru > 95 % po dobu posledních 15 minut. | 1. Nepřidávejte nové počítače.<br/>2. Zkontrolujte, že počet virtuálních počítačů pomocí procesového serveru odpovídá [definované mezní hodnoty](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte zřízení [další procesový server](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).<br/> 4. Pokud se problém nevyřeší, spusťte [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) pro replikaci VMware/fyzické prostředky serveru.
![Upozornění][yellow] | Využití paměti > 80 % po dobu posledních 15 minut. |  1. Nepřidávejte nové počítače.<br/>2. Zkontrolujte, že počet virtuálních počítačů pomocí procesového serveru odpovídá [definované mezní hodnoty](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte zřízení [další procesový server](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle pokynů spojené s upozorněním.<br/> 4. Pokud se problém opakuje, postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).
![Kritická][red] | Využití paměti > 95 % po dobu posledních 15 minut. | 1. Nepřidávejte nových počítačů a vzhledem k tomu, nastavení [další procesový server](vmware-azure-set-up-process-server-scale.md).<br/> 2. Postupujte podle pokynů spojené s upozorněním.<br/> 3. 4. Pokud problém přetrvává, postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).<br/> 4. Pokud se problém nevyřeší, spusťte [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) pro potíže s replikací VMware/fyzické prostředky serveru.
![Upozornění][yellow] | Mezipaměť složky volné místo < 30 % po dobu posledních 15 minut. | 1. Nemáte přidávání nových počítačů a zvažte zřízení [další procesový server](vmware-azure-set-up-process-server-scale.md).<br/>2. Zkontrolujte, že počet virtuálních počítačů pomocí procesového serveru odpovídá [pokyny](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).
![Kritická][red] |  < 25 % volného místa pro posledních 15 minut | 1. Postupujte podle pokynů, které jsou přidružené k upozornění pro tento problém.<br/> 2. 3. Postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).<br/> 3. Pokud se problém nevyřeší, spusťte [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) pro replikaci VMware/fyzické prostředky serveru.
![Kritická][red] | Žádný prezenční signál z procesového serveru pro 15 minut nebo déle. Služba tmansvs nekomunikuje s konfiguračním serverem. | (1) zkontrolujte, že procesový server je zprovozněný.<br/> 2. Zkontrolujte, že na procesovém serveru běží tmassvc.<br/> 3. Postupujte podle pokynů níže a [řešení potíží s připojením a replikace](#check-connectivity-and-replication).


![klíč tabulky](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2: Kontrola služby procesového serveru

Služby, které by měl být spuštěn na procesovém serveru jsou shrnuty v následující tabulce. Existují mírné rozdíly ve službách, v závislosti na způsobu nasazení procesového serveru. 

Pro všechny služby s výjimkou agenta Microsoft Azure Recovery Services (obengine), zkontrolujte, programu je nastavená na **automatické** nebo **automaticky (zpožděné spuštění)**.
 
**Nasazení** | **Spuštění služby**
--- | ---
**Procesový server na konfiguračním serveru** | Server; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Služba nahrávání protokolu (LogUpload); InMage Scout Application Service; Agent Microsoft Azure Recovery Services (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; Světě služby publikování na webu (W3SVC); MySQL; Služba Microsoft Azure Site Recovery (dra)
**Procesového serveru běžícího jako samostatný server** | Server; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Služba nahrávání protokolu (LogUpload); InMage Scout Application Service; Agent Microsoft Azure Recovery Services (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Procesový server, které jsou nasazené v Azure pro navrácení služeb po obnovení** | Server; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Služba nahrávání protokolu (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3: Kontrola prezenčního signálu proces serveru

Pokud neexistuje žádný prezenční signál z procesového serveru (kód chyby: 806), postupujte takto:

1. Ověřte, že procesový server virtuálního počítače je v provozu.
2. Chyby v těchto protokolech.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Zkontrolujte připojení a replikace

 Selhání počáteční a průběžné replikace je často způsobeno problémy s připojením mezi zdrojové počítače a že procesový server, nebo mezi procesovým serverem a Azure. Tyto kroky jsou shrnuty v následující obrázek, za nímž následuje postupy, které vám pomohou provést kroky.

![Řešení potíží s připojením a replikace](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4: Zkontrolujte synchronizaci času na zdrojovém počítači

Ujistěte se, že systémové datum a čas pro replikované počítače se synchronizuje. [Další informace](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5: Zkontrolujte antivirový software na zdrojovém počítači

Zkontrolujte, že žádný antivirový software na replikované počítači blokuje Site Recovery. Pokud je potřeba vyloučit Site Recovery z antivirové programy, zkontrolujte [v tomto článku](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6: Zkontrolujte připojení ze zdrojového počítače


1. Nainstalujte [klient služby Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na zdrojovém počítači, pokud je potřeba. Nepoužívejte příkaz Ping.
2. Ze zdrojového počítače odešlete zprávu ping procesový server na port HTTPS s Telnet. Ve výchozím nastavení je 9443 port HTTPS pro přenosy replikace.

    `telnet <process server IP address> <port>`

3. Ověřte, zda je připojení úspěšné.


**Připojení** | **Podrobnosti** | **Akce**
--- | --- | ---
**Úspěšné** | Telnet zobrazí prázdnou obrazovku a že procesový server je dostupný. | Žádné další požadované akce.
**Neúspěšné** | Nemůžete se připojit | Ujistěte se, že je povolený příchozí port 9443 na procesovém serveru. Pokud například máte hraniční síti nebo monitorovaná podsíť. Zkontrolujte připojení znovu.
**Částečně úspěšné** | Můžete se připojit, ale zdrojový počítač hlásí, že procesový server není dostupný. | Pokračujte s dalším postupem řešení potíží.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7: Řešení potíží s nedostupný procesový server

Pokud procesový server není dostupný ze zdrojového počítače, zobrazí se chyba 78186. Pokud nejsou uvedeny, tento problém povede k oběma konzistentní a body konzistentní při selhání obnovení nejsou generovány podle očekávání.

Řešení potíží s kontrolou, jestli může zdrojový počítač přístup k IP adrese procesového serveru a spusťte nástroj cxpsclient na zdrojovém počítači, zkontrolujte připojení k začátku do konce.


### <a name="check-the-ip-connection-on-the-process-server"></a>Zkontrolujte připojení IP na procesovém serveru

Pokud telnet je úspěšné, ale zdrojový počítač hlásí, že procesový server není dostupný, zkontrolujte, zda můžete přístup k IP adrese procesového serveru.

1. Ve webovém prohlížeči zkuste kontaktovat https://<PS_IP>:<PS_Data_Port>/ IP adresu.
2. Pokud se tato kontrola zobrazí chybu certifikátu HTTPS, který je normální. Pokud můžete chybu ignorovat, měli byste vidět 400 - Chybný požadavek. To znamená, že server nemůže požadavek vyřídit. prohlížeč a že je v pořádku standardní připojení HTTPS.
3. Pokud tato kontrola nebude fungovat, poznamenejte si prohlížeč chybová zpráva. 407 chyby se například jednat o problém se ověření proxy serverem.

### <a name="check-the-connection-with-cxpsclient"></a>Zkontrolujte připojení s cxpsclient

Kromě toho můžete spustit nástroj cxpsclient ke kontrole připojení začátku do konce.

1. Spusťte nástroj následujícím způsobem:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na procesovém serveru najdete v protokolech generovaných v těchto složek:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Zkontrolujte, že chyby (Chyba 78028) nahrávání protokolů zdrojového virtuálního počítače pro

Problém s blokované ze zdrojového počítače ke službě procesu nahrávání dat může vést k obou body obnovení konzistentní při selhání a konzistentní vzhledem k tomu vygenerovat. 

1. Řešení potíží s nahrávání selhání sítě, můžete vyhledat chyby v tomto protokolu:

    C:\Program soubory (x86) \Microsoft Azure Site Recovery\agent\svagents*.log 

2. Použití rest postupy v tomto článku vám mohou pomoci při řešení problémů nahrát data.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8: Zkontrolujte, zda je na procesovém serveru předání dat

Zkontrolujte, zda procesový server je aktivně doručením (push) dat do Azure.

  1. Na procesovém serveru otevřete Správce úloh (stisknutím klávesy Ctrl + Shift + Esc).
  2. Vyberte **výkonu** kartu > **Monitor otevřít zdroj**.
  3. V **sledování prostředků** stránky, vyberte **sítě** kartu. V části **procesů pomocí síťové aktivity**, zkontrolujte, zda cbengine.exe aktivně odesílá velké vNotolume data.

       ![Svazky procesů pomocí síťové aktivity](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Pokud cbengine.exe neodesílá velké objemy dat, proveďte kroky v následujících částech.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9: Zkontrolujte připojení k procesu serveru do Azure blob storage

1. Sledování prostředků, vyberte **cbengine.exe**.
2. V části **připojení TCP**, zkontrolujte, zda je připojení z procesového serveru do služby Azure storage.

  ![Připojení mezi cbengine.exe a adresu URL úložiště objektů Blob v Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Kontrola služby

Pokud není k dispozici připojení z procesového serveru na adresu URL úložiště objektů blob v Azure, zkontrolujte, zda jsou spuštěny služby.

1. V Ovládacích panelech vyberte **služby**.
2. Ověřte, že jsou spuštěné následující služby:

    - cxprocessserver
    - Nástroje InMage Scout VX Agent – Sentinel/Outpost
    - Agent Microsoft Azure Recovery Services
    - Služba Microsoft Azure Site Recovery
    - tmansvc

3. Spusťte nebo restartujte jakoukoliv službu, která není spuštěná.
4. Ověřte, že procesový server je připojený a dostupný. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10: Zkontrolujte připojení k procesu serveru na Azure veřejnou IP adresu

1. Na procesovém serveru v **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, otevřete nejnovější CBEngineCurr.errlog soubor.
2. V souboru vyhledejte **443**, nebo pro řetězec **pokus o připojení se nezdařilo**.

  ![Protokoly chyb ve složce Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Pokud se zobrazí problémy, nacházejí v souboru CBEngineCurr.currLog Azure veřejné IP adresy pomocí portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na příkazovém řádku na procesovém serveru použijte příkaz ping Azure veřejné IP adresy Telnet.
6. Pokud se nemůžete připojit, použijte následující postup.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11: Zkontrolujte nastavení brány firewall serveru procesu. 

Zkontrolujte, zda brány firewall protokolu IP adres založené na procesovém serveru blokuje přístup.

1. Pro pravidla brány firewall založená na adresu IP:

    (a) stáhněte si úplný seznam [rozsahy IP adres datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    (b) přidejte rozsahy IP adres do vaší konfigurace brány firewall, ujistěte se, že brána firewall umožňuje komunikaci do Azure (a výchozí port HTTPS 443).

    c) povolit rozsahy IP adres pro oblast Azure svého předplatného a pro oblasti Azure USA – Západ, který se (používané pro řízení přístupu a identit správě).

2. Pro brány firewall založené na adresu URL přidejte adresy URL uvedené v následující tabulce ke konfiguraci brány firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12: Ověřte nastavení proxy serveru procesu 

1. Pokud používáte proxy server, ujistěte se, že DNS server se přeložit název proxy serveru. Zkontrolujte hodnotu, kterou jste zadali, když nastavíte konfigurační server v klíči registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure lokality Recovery\ProxySettings**.
2. Ujistěte se, že agent Azure Site Recovery k odesílání dat používají stejné nastavení.

    (a) Hledat **služby Microsoft Azure Backup**.

    (b) otevřete **Microsoft Azure Backup**a vyberte **akce** > **změnit vlastnosti**.

    c) na **konfiguraci proxy serveru** kartu, je adresa proxy musí být stejná jako adresa proxy serveru, který je zobrazen v nastavení registru. Pokud ne, změňte ho na stejnou adresu.

## <a name="step-13-check-bandwidth"></a>Krok 13: Zkontrolujte šířky pásma

Zvětšit šířku pásma mezi procesovým serverem a Azure a potom zkontrolujte, jestli problém přetrvává.


## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, zveřejněte svůj dotaz v [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png