---
title: Poradce při potížích s procesem obnovení webu Azure server
description: Tento článek popisuje, jak řešit problémy se serverem procesu obnovení webu Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256858"
---
# <a name="troubleshoot-the-process-server"></a>Poradce při potížích s procesním serverem

Server [procesu obnovení lokality](site-recovery-overview.md) se používá při nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware a fyzické servery. Tento článek popisuje, jak řešit problémy s procesem serveru, včetně replikace a připojení problémy.

[Přečtěte si další informace](vmware-physical-azure-config-process-server-overview.md) o procesním serveru.

## <a name="before-you-start"></a>Než začnete

Než začnete s odstraňováním potíží:

1. Ujistěte se, že rozumíte tomu, jak [sledovat procesní servery](vmware-physical-azure-monitor-process-server.md).
2. Projděte si níže uvedené doporučené postupy.
3. Ujistěte se, že dodržujete [aspekty kapacity](site-recovery-plan-capacity-vmware.md#capacity-considerations)a používáte pokyny pro změnu velikosti pro [konfigurační server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) nebo [samostatné procesní servery](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Doporučené postupy pro nasazení procesního serveru

Pro optimální výkon procesních serverů jsme shrnuli řadu obecných doporučených postupů.

**Osvědčených** | **Podrobnosti**
--- |---
**Použití** | Ujistěte se, že konfigurační server/samostatný procesní server jsou používány pouze k určenému účelu. Nespouštějte nic jiného na stroji.
**IP adresa** | Ujistěte se, že procesní server má statickou adresu IPv4 a nemá nakonfigurovaný nakonfigurovaný naváděný adresu NAT.
**Řízení využití paměti/procesoru** |Udržujte využití procesoru a paměti pod 70 %.
**Zajistěte volný prostor** | Volné místo označuje místo na disku mezipaměti na procesním serveru. Data replikace se ukládají do mezipaměti před odesláním do Azure.<br/><br/> Udržujte volný prostor nad 25%. Pokud je nižší než 20 %, replikace je omezena pro replikované počítače, které jsou přidruženy k procesu serveru.

## <a name="check-process-server-health"></a>Kontrola stavu serveru procesu

Prvním krokem při řešení potíží je kontrola stavu a stavu procesního serveru. Chcete-li to provést, zkontrolujte všechny výstrahy, zkontrolujte, zda jsou spuštěny požadované služby, a ověřte, zda je prezenční signál z procesního serveru. Tyto kroky jsou shrnuty v následujícím obrázku, následovaný postupy, které vám pomohou provést kroky.

![Poradce při potížích se stavem procesního serveru](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1: Poradce při potížích se stavy serveru procesu

Procesový server generuje řadu výstrah stavu. Tyto výstrahy a doporučené akce jsou shrnuty v následující tabulce.

**Typ upozornění** | **Chyba** | **Řešení potíží**
--- | --- | --- 
![V pořádku][green] | Žádný  | Procesní server je připojen a v pořádku.
![Upozornění][yellow] | Zadané služby nejsou spuštěny. | 1. Zkontrolujte, zda jsou spuštěny služby.<br/> 2. Pokud jsou služby spuštěny podle očekávání, postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).
![Upozornění][yellow]  | Využití procesoru > 80 % za posledních 15 minut. | 1. Nepřidávejte nové stroje.<br/>2. Zkontrolujte, zda je počet virtuálních připojení používajících procesní server zarovnán s [definovanými omezeními,](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesního serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).
![Kritická][red] |  Využití procesoru > 95 % za posledních 15 minut. | 1. Nepřidávejte nové stroje.<br/>2. Zkontrolujte, zda je počet virtuálních připojení používajících procesní server zarovnán s [definovanými omezeními,](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesního serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).<br/> 4. Pokud problém přetrvává, spusťte [Plánovač nasazení](https://aka.ms/asr-v2a-deployment-planner) pro replikaci serveru VMware/fyzický server.
![Upozornění][yellow] | Využití paměti > 80% za posledních 15 minut. |  1. Nepřidávejte nové stroje.<br/>2. Zkontrolujte, zda je počet virtuálních připojení používajících procesní server zarovnán s [definovanými omezeními,](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesního serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. Postupujte podle pokynů spojených s varováním.<br/> 4. Pokud problém přetrvává, postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).
![Kritická][red] | Využití paměti > 95% za posledních 15 minut. | 1. Nepřidávejte nové stroje a zvažte zřízení [dalšího procesního serveru](vmware-azure-set-up-process-server-scale.md).<br/> 2. Postupujte podle pokynů spojených s varováním.<br/> 3. 4. Pokud problém přetrvává, postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).<br/> 4. Pokud problém přetrvává, spusťte [Plánovač nasazení](https://aka.ms/asr-v2a-deployment-planner) pro problémy s replikací serveru VMware/fyzické.
![Upozornění][yellow] | Volné místo ve složce mezipaměti < 30 % za posledních 15 minut. | 1. Nepřidávejte nové počítače a zvažte nastavení [dalšího procesního serveru](vmware-azure-set-up-process-server-scale.md).<br/>2. Zkontrolujte, zda je počet virtuálních připojení používajících procesní server v souladu s [pokyny](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).
![Kritická][red] |  Volný prostor < 25% za posledních 15 minut | 1. Postupujte podle pokynů spojených s upozorněním pro tento problém.<br/> 2. 3. Podle následujících pokynů [můžete řešit problémy s připojením a replikací](#check-connectivity-and-replication).<br/> 3. Pokud problém přetrvává, spusťte [Plánovač nasazení](https://aka.ms/asr-v2a-deployment-planner) pro replikaci serveru VMware/fyzický server.
![Kritická][red] | Žádný prezenční signál z procesního serveru po dobu 15 minut nebo déle. Služba tmansvs nekomunikuje s konfiguračním serverem. | 1) Zkontrolujte, zda je procesní server v provozu.<br/> 2. Zkontrolujte, zda je na procesním serveru spuštěn tmassvc.<br/> 3. Postupujte podle následujících pokynů k [řešení problémů s připojením a replikací](#check-connectivity-and-replication).


![Klíč tabulky](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2: Kontrola služeb procesního serveru

Služby, které by měly být spuštěny na procesovém serveru, jsou shrnuty v následující tabulce. Existují mírné rozdíly ve službách v závislosti na tom, jak je nasadit procesní server. 

U všech služeb kromě agenta služby Microsoft Azure Recovery Services (obengine) zkontrolujte, zda je typ StartType nastavený na **automatické** nebo **automatické (zpožděné spuštění).**
 
**Nasazení** | **Běžící služby**
--- | ---
**Procesní server na konfiguračním serveru** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Protokolovat upload služby (LogUpload); InMage Scout aplikační služba; Agent služby Microsoft Azure Recovery Services (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; služba publikování na webu (W3SVC); MySQL; Služba obnovení webu Microsoft Azure (dra)
**Procesní server spuštěný jako samostatný server** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Protokolovat upload služby (LogUpload); InMage Scout aplikační služba; Agent služby Microsoft Azure Recovery Services (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Procesní server nasazený v Azure pro navrácení služeb po službě 14.** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload service (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3: Kontrola prezenčního signálu procesního serveru

Pokud ze procesního serveru (kód chyby 806) nenateče žádný prezenční signál), postupujte takto:

1. Ověřte, zda je virtuální virtuální server procesu v provozu.
2. Zkontrolujte, zda tyto protokoly nejsou chyby.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Kontrola připojení a replikace

 Počáteční a probíhající selhání replikace jsou často způsobeny problémy s připojením mezi zdrojovými počítači a procesovým serverem nebo mezi procesním serverem a Azure. Tyto kroky jsou shrnuty v následujícím obrázku, následovaný postupy, které vám pomohou provést kroky.

![Poradce při potížích s připojením a replikací](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4: Ověření synchronizace času na zdrojovém počítači

Ujistěte se, že systémové datum a čas pro replikovaný počítač je synchronizován. [Další informace](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5: Kontrola antivirového softwaru na zdrojovém počítači

Zkontrolujte, zda v replikovaném počítači neblokuje žádný antivirový software. Pokud potřebujete vyloučit program Site Recovery z antivirových programů, přečtěte si [tento článek](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6: Kontrola připojení ze zdrojového počítače


1. V případě potřeby nainstalujte [klienta Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) do zdrojového počítače. Nepoužívejte Ping.
2. Ze zdrojového počítače příkazping procesového serveru na portu HTTPS s Telnet. Ve výchozím nastavení 9443 je port HTTPS pro replikační provoz.

    `telnet <process server IP address> <port>`

3. Ověřte, zda je připojení úspěšné.


**Připojení** | **Podrobnosti** | **Akce**
--- | --- | ---
**Úspěšné** | Telnet zobrazí prázdnou obrazovku a procesní server je dostupný. | Není nutná žádná další akce.
**Neúspěšné** | Nelze se připojit | Ujistěte se, že příchozí port 9443 je povolen na procesu serveru. Například pokud máte hraniční síť nebo stíněnou podsíť. Zkontrolujte připojení znovu.
**Částečně úspěšné** | Můžete se připojit, ale zdrojový počítač hlásí, že procesní server není dostupný. | Pokračujte dalším postupem řešení potíží.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7: Poradce při potížích s nedostupným procesním serverem

Pokud procesní server není dostupný ze zdrojového počítače, zobrazí se chyba 78186. Pokud není vyřešen, tento problém povede k tomu, že body obnovení konzistentní v aplikaci a konzistentní s chybami nebudou generovány podle očekávání.

Poradce při potížích kontrolou, zda zdrojový počítač může dosáhnout IP adresy procesního serveru a spustit nástroj cxpsclient ve zdrojovém počítači, chcete-li zkontrolovat připojení od konce do konce.


### <a name="check-the-ip-connection-on-the-process-server"></a>Kontrola připojení IP na procesním serveru

Pokud je telnet úspěšný, ale zdrojový počítač hlásí, že procesní server není dostupný, zkontrolujte, zda se můžete dostat k IP adrese procesního serveru.

1. Ve webovém prohlížeči se pokuste o přístup k IP adrese https://<PS_IP>:<PS_Data_Port>/.
2. Pokud tato kontrola zobrazí chybu certifikátu HTTPS, je to normální. Pokud chybu ignorujete, měli byste vidět 400 - Bad Request. To znamená, že server nemůže obsluhovat požadavek prohlížeče a že standardní připojení HTTPS je v pořádku.
3. Pokud tato kontrola nefunguje, poznamenejte si chybovou zprávu prohlížeče. Například chyba 407 bude znamenat problém s ověřováním proxy serveru.

### <a name="check-the-connection-with-cxpsclient"></a>Zkontrolujte připojení s cxpsclient

Kromě toho můžete spustit nástroj cxpsclient a zkontrolovat připojení od konce na konec.

1. Spusťte nástroj takto:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na procesním serveru zkontrolujte generované protokoly v těchto složkách:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Kontrola zdrojových protokolů virtuálních zařízení pro selhání nahrávání (chyba 78028)

Problém s nahráváním dat blokovaným ze zdrojových počítačů do procesní služby může vést k tomu, že nejsou generovány body obnovení konzistentní s havárií a konzistentní s aplikací. 

1. Chcete-li odstranit selhání nahrávání v síti, můžete hledat chyby v tomto protokolu:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Pomocí zbývajících postupů v tomto článku vám může pomoci vyřešit problémy s odesíláním dat.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8: Kontrola, zda procesový server tlačí data

Zkontrolujte, jestli procesní server aktivně vyřazuje data do Azure.

  1. Na procesním serveru otevřete Správce úloh (stiskněte Ctrl+Shift+Esc).
  2. Vyberte kartu **Výkon** > **Otevřít nástroj Sledování prostředků**.
  3. Na stránce **Sledování prostředků** vyberte kartu **Síť.** V části **Procesy s aktivitou sítě**zkontrolujte, zda program cbengine.exe aktivně odesílá velký objem dat.

       ![Svazky v rámci procesů se síťovou aktivitou](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Pokud soubor cbengine.exe neodesílá velký objem dat, proveďte kroky v následujících částech.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9: Kontrola připojení procesního serveru k úložišti objektů blob Azure

1. V programu Sledování prostředků vyberte **soubor cbengine.exe**.
2. V **části Připojení TCP**zkontrolujte, jestli je připojení z procesního serveru do úložiště Azure.

  ![Připojení mezi souborem cbengine.exe a adresou URL úložiště objektů blob Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Zkontrolovat služby

Pokud neexistuje žádné připojení z procesního serveru k adrese URL úložiště objektů blob Azure, zkontrolujte, že jsou spuštěné služby.

1. V Ovládacích panelech vyberte **Položku Služby**.
2. Ověřte, zda jsou spuštěny následující služby:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services Agent
    - Služba obnovení webu Microsoft Azure
    - tmansvc

3. Spusťte nebo restartujte jakoukoli službu, která není spuštěna.
4. Ověřte, zda je procesní server připojený a dostupný. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10: Kontrola připojení procesního serveru k veřejné IP adrese Azure

1. Na procesním serveru otevřete v **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**nejnovější soubor CBEngineCurr.errlog.
2. V souboru hledání **443**nebo pokus o připojení řetězce **se nezdařilo**.

  ![Protokoly chyb ve složce Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Pokud se zobrazí problémy, najdete svou veřejnou IP adresu Azure v souboru CBEngineCurr.currLog pomocí portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na příkazovém řádku na procesním serveru použijte program Telnet k příkazu ping na veřejnou IP adresu Azure.
6. Pokud se nemůžete připojit, postupujte podle následujícího postupu.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11: Zkontrolujte nastavení brány firewall serveru procesu. 

Zkontrolujte, zda brána firewall založená na adrese IP na procesním serveru blokuje přístup.

1. Pro pravidla brány firewall založené na adrese IP:

    a) Stáhněte si úplný seznam [rozsahů IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Přidejte rozsahy IP adres do konfigurace brány firewall, abyste zajistili, že brána firewall umožní komunikaci s Azure (a na výchozí port HTTPS 443).

    c) Povolit rozsahy IP adres pro oblast Azure vašeho předplatného a pro oblast Azure – usa (používá se pro řízení přístupu a správu identit).

2. U bran firewall založených na adrese URL přidejte adresy URL uvedené v následující tabulce do konfigurace brány firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12: Ověření nastavení proxy serveru procesu 

1. Používáte-li proxy server, zkontrolujte, zda je název serveru proxy server serverem DNS přeložen. Zkontrolujte hodnotu, kterou jste zadali při nastavování konfiguračního serveru v klíči registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Ujistěte se, že stejné nastavení používá agent obnovení webu Azure k odesílání dat.

    a) Vyhledejte **microsoft azure backup**.

    b) Otevřete **microsoft azure backup**a vyberte**vlastnosti změny** **akce** > .

    c) Na kartě **Konfigurace proxy serveru** by měla být adresa proxy stejná jako adresa proxy, která je zobrazena v nastavení registru. Pokud ne, změňte ji na stejnou adresu.

## <a name="step-13-check-bandwidth"></a>Krok 13: Kontrola šířky pásma

Zvyšte šířku pásma mezi procesním serverem a Azure a zkontrolujte, zda problém stále dochází.


## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc, zadejte svůj dotaz ve [fóru Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
