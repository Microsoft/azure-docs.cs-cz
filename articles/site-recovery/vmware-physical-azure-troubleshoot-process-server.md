---
title: Řešení potíží s Azure Site Recovery procesový Server
description: Tento článek popisuje, jak řešit problémy s Azure Site Recovery procesový Server.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ad1bec66edaa3fcc6049f4911684f6e6d6c3e366
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369399"
---
# <a name="troubleshoot-the-process-server"></a>Řešení potíží s procesovým serverem

[Site Recovery](site-recovery-overview.md) procesový Server se používá při nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware a fyzické servery. Tento článek popisuje, jak řešit problémy s procesovým serverem, včetně potíží s replikací a připojením.

[Přečtěte si další informace](vmware-physical-azure-config-process-server-overview.md) o procesovém serveru.

## <a name="before-you-start"></a>Než začnete

Než začnete řešit potíže:

1. Ujistěte se, že rozumíte tomu, jak [sledovat procesové servery](vmware-physical-azure-monitor-process-server.md).
2. Přečtěte si následující doporučené postupy.
3. Ujistěte se, že dodržujete [požadavky na kapacitu](site-recovery-plan-capacity-vmware.md#capacity-considerations), a použijte pokyny pro změnu velikosti pro [konfigurační server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) nebo [samostatné procesové servery](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Osvědčené postupy pro nasazení procesového serveru

Pro optimální výkon procesových serverů jsme shromáždili řadu obecných osvědčených postupů.

**Osvědčené postupy** | **Podrobnosti**
--- |---
**Použití** | Ujistěte se, že se konfigurační server/samostatný procesový Server používá pouze k zamýšlenému účelu. Nespouštějte na počítači nic jiného.
**IP adresa** | Ujistěte se, že procesový Server má statickou adresu IPv4 a nemá nakonfigurován překlad adres (NAT).
**Řízení paměti a využití CPU** |Ponechte využití CPU a paměti pod 70%.
**Zajistěte volné místo** | Volné místo označuje místo na disku mezipaměti na procesovém serveru. Data replikace se ukládají do mezipaměti, než se nahrají do Azure.<br/><br/> Nechte volné místo nad 25%. Pokud překročíte 20%, replikace se omezí pro replikované počítače, které jsou přidružené k procesu serveru.

## <a name="check-process-server-health"></a>Ověřit stav procesového serveru

Prvním krokem při řešení potíží je kontrolu stavu a stavu procesového serveru. Provedete to tak, že zkontrolujete všechny výstrahy, zkontrolujete, že jsou spuštěné požadované služby, a ověříte, že z procesového serveru existuje prezenční signál. Tyto kroky jsou shrnuté na následujícím obrázku a následují postupy, které vám pomohou postupovat podle pokynů.

![Řešení potíží s stavem procesového serveru](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1: řešení potíží s upozorněními na stav procesového serveru

Procesový Server vygeneruje množství upozornění na stav. Tyto výstrahy a doporučené akce jsou shrnuty v následující tabulce.

**Typ upozornění** | **Chyba** | **Řešení potíží**
--- | --- | --- 
![V pořádku][green] | Žádné  | Procesový Server je připojený a v pořádku.
![Upozornění][yellow] | Zadané služby nejsou spuštěny. | 1. Ověřte, zda jsou služby spuštěny.<br/> 2. Pokud služby běží podle očekávání, postupujte podle pokynů níže a odstraňte potíže s [připojením a replikací](#check-connectivity-and-replication).
![Upozornění][yellow]  | Využití CPU > 80% za posledních 15 minut. | 1. nepřidávejte nové počítače.<br/>2. Ověřte, že počet virtuálních počítačů, které používají procesový Server, se zarovnají k [definovaným limitům](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesového serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. [při řešení problémů s připojením a replikací](#check-connectivity-and-replication)postupujte podle následujících pokynů.
![Kritické][red] |  Využití CPU > 95% za posledních 15 minut. | 1. nepřidávejte nové počítače.<br/>2. Ověřte, že počet virtuálních počítačů, které používají procesový Server, se zarovnají k [definovaným limitům](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesového serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. [při řešení problémů s připojením a replikací](#check-connectivity-and-replication)postupujte podle následujících pokynů.<br/> 4. Pokud potíže potrvají, spusťte [Plánovač nasazení](./site-recovery-deployment-planner.md) pro replikaci VMware/fyzického serveru.
![Upozornění][yellow] | Využití paměti > 80% za posledních 15 minut. |  1. nepřidávejte nové počítače.<br/>2. Ověřte, že počet virtuálních počítačů, které používají procesový Server, se zarovnají k [definovaným limitům](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zvažte nastavení [dalšího procesového serveru](vmware-azure-set-up-process-server-scale.md).<br/>3. postupujte podle pokynů souvisejících s upozorněním.<br/> 4. Pokud potíže potrvají, postupujte podle pokynů níže, abyste vyřešili [problémy s připojením a replikací](#check-connectivity-and-replication).
![Kritické][red] | Využití paměti > 95% za posledních 15 minut. | 1. nepřidávejte nové počítače a zvažte nastavení [dalšího procesového serveru](vmware-azure-set-up-process-server-scale.md).<br/> 2. postupujte podle pokynů souvisejících s upozorněním.<br/> 3.4. Pokud se problém opakuje, postupujte podle pokynů níže, abyste vyřešili [problémy s připojením a replikací](#check-connectivity-and-replication).<br/> 4. Pokud potíže potrvají, spusťte [Plánovač nasazení](./site-recovery-deployment-planner.md) pro potíže s replikací VMware/fyzického serveru.
![Upozornění][yellow] | Místo pro složku mezipaměti < 30% za posledních 15 minut. | 1. nepřidávejte nové počítače a zvažte nastavení [dalšího procesového serveru](vmware-azure-set-up-process-server-scale.md).<br/>2. Ověřte, že počet virtuálních počítačů, které používají procesový Server, jsou zarovnané na [pokyny](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. [při řešení problémů s připojením a replikací](#check-connectivity-and-replication)postupujte podle následujících pokynů.
![Kritické][red] |  Volné místo < 25% za posledních 15 minut | 1. postupujte podle pokynů souvisejících s upozorněním pro tento problém.<br/> 2.3. [Při řešení potíží s připojením a replikací](#check-connectivity-and-replication)postupujte podle následujících pokynů.<br/> 3. Pokud potíže potrvají, spusťte [Plánovač nasazení](./site-recovery-deployment-planner.md) pro replikaci VMware/fyzického serveru.
![Kritické][red] | Žádný prezenční signál od procesového serveru po dobu 15 minut nebo déle. Služba tmansvs nekomunikuje s konfiguračním serverem. | 1) ověřte, že je procesový Server v provozu.<br/> 2. Ověřte, že je na procesovém serveru spuštěný tmassvc.<br/> 3. [při řešení problémů s připojením a replikací](#check-connectivity-and-replication)postupujte podle následujících pokynů.


![Klíč tabulky](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2: Projděte si služby procesového serveru

Služby, které by měly být spuštěny na procesovém serveru, jsou shrnuty v následující tabulce. V závislosti na tom, jak je procesový Server nasazený, existují mírné rozdíly v rámci služeb. 

U všech služeb s výjimkou agenta Microsoft Azure Recovery Services (obengine) ověřte, zda je StartType nastaven na hodnotu **automaticky** nebo **automaticky (zpožděné spuštění)**.
 
**Nasazení** | **Běžící služby**
--- | ---
**Procesový Server na konfiguračním serveru** | ProcessServer; ProcessServerMonitor; cxprocessserver PushInstall InMage; Služba LogUpload (log upload Service); InMage Scout Application Service; Agent Microsoft Azure Recovery Services (obengine); InMage Scout VX agent – Sentinel/subpost (svagents); tmansvc W3SVC (World World Web Publishing Service); MySQL Služba Microsoft Azure Site Recovery (DRA)
**Procesový Server spuštěný jako samostatný server** | ProcessServer; ProcessServerMonitor; cxprocessserver PushInstall InMage; Služba LogUpload (log upload Service); InMage Scout Application Service; Agent Microsoft Azure Recovery Services (obengine); InMage Scout VX agent – Sentinel/subpost (svagents); tmansvc.
**Procesový Server nasazený v Azure pro navrácení služeb po obnovení** | ProcessServer; ProcessServerMonitor; cxprocessserver PushInstall InMage; Služba nahrání protokolu (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3: ověření prezenčního signálu procesového serveru

Pokud z procesového serveru není žádný prezenční signál (kód chyby 806), udělejte toto:

1. Ověřte, že je virtuální počítač procesového serveru v provozu.
2. V těchto protokolech vyhledejte chyby.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log c\programdata\asr\home\svsystems\ monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Ověření připojení a replikace

 Úvodní a průběžné selhání replikace často způsobují problémy s připojením mezi zdrojovými počítači a procesovým serverem, nebo mezi procesovým serverem a Azure. Tyto kroky jsou shrnuté na následujícím obrázku a následují postupy, které vám pomohou postupovat podle pokynů.

![Vývojový diagram znázorňuje kroky pro řešení potíží s připojením a replikací.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4: ověření času synchronizace na zdrojovém počítači

Ujistěte se, že je systémové datum a čas pro replikovaný počítač synchronizovaný. [Další informace](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5: Ověřte antivirový software na zdrojovém počítači

Ověřte, že Site Recovery žádný antivirový software na replikovaném počítači neblokuje. Pokud potřebujete vyloučit Site Recovery z antivirových programů, přečtěte si [Tento článek](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6: ověření připojení ze zdrojového počítače


1. V případě potřeby nainstalujte [klienta služby Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) na zdrojový počítač. Nepoužívejte Příkazy k zadání příkazů.
2. Na zdrojovém počítači otestujte procesový Server na portu HTTPS pomocí protokolu Telnet. Standardně 9443 je port HTTPS pro provoz replikace.

    `telnet <process server IP address> <port>`

3. Ověřte, zda připojení proběhlo úspěšně.


**Připojení** | **Podrobnosti** | **Akce**
--- | --- | ---
**Úspěšnou** | Telnet zobrazí prázdnou obrazovku a procesový Server je dostupný. | Není vyžadována žádná další akce.
**Neúspěšné** | Nemůžete se připojit | Ujistěte se, že na procesovém serveru je povolený příchozí port 9443. Například pokud máte hraniční síť nebo monitorovaná podsíť. Znovu ověřte připojení.
**Částečně úspěšné** | Můžete se připojit, ale zdrojový počítač hlásí, že procesový Server není dostupný. | Pokračujte dalším postupem řešení potíží.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7: řešení potíží s nedosažitelným procesovým serverem

Pokud procesový Server není dostupný ze zdrojového počítače, zobrazí se chyba 78186. Pokud tento problém neřešíte, bude to mít za následek to, že body obnovení konzistentní vzhledem k aplikacím i chybové havárie se negenerují podle očekávání.

Odstraňte potíže tím, že zkontrolujete, jestli má zdrojový počítač přístup k IP adrese procesového serveru, a spusťte na zdrojovém počítači nástroj cxpsclient, abyste zkontrolovali koncové připojení.


### <a name="check-the-ip-connection-on-the-process-server"></a>Ověřte připojení IP na procesovém serveru.

Pokud je protokol Telnet úspěšný, ale zdrojový počítač hlásí, že procesový Server není dostupný, ověřte, zda se můžete připojit k IP adrese procesového serveru.

1. Ve webovém prohlížeči se pokuste získat přístup k IP adrese https://<PS_IP>: <PS_Data_Port>/.
2. Pokud tato kontrolní zpráva zobrazuje chybu certifikátu HTTPS, je to normální. Pokud chybu ignorujete, měla by se zobrazit zpráva 400 – Chybný požadavek. To znamená, že server nemůže obsluhovat požadavek prohlížeče a že standardní připojení HTTPS je přesné.
3. Pokud tato kontrolu nefunguje, poznamenejte si chybovou zprávu prohlížeče. Například Chyba 407 znamená problém s ověřováním proxy serveru.

### <a name="check-the-connection-with-cxpsclient"></a>Ověřit připojení pomocí cxpsclient

Kromě toho můžete spustit nástroj cxpsclient a podívat se na koncové připojení.

1. Spusťte nástroj následujícím způsobem:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na procesovém serveru ověřte generované protokoly v těchto složkách:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Ověřte protokoly zdrojového virtuálního počítače pro selhání nahrávání (chyba 78028)

Problém s odesíláním dat blokovaným ze zdrojových počítačů do procesové služby může vést k tomu, že se negenerují body obnovení konzistentní vzhledem k havárii i konzistentní vzhledem k aplikacím. 

1. Pokud chcete řešit problémy se selháním síťového nahrávání, můžete najít chyby v tomto protokolu:

    C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents *. log 

2. Pomocí zbývajících postupů v tomto článku můžete vyřešit problémy s nahráváním dat.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8: ověření, zda procesový server přenáší data

Ověřte, jestli procesový Server aktivně donáší data do Azure.

  1. Na procesovém serveru otevřete Správce úloh (stiskněte kombinaci kláves CTRL + SHIFT + ESC).
  2. Vyberte kartu **výkon** > **otevřít sledování prostředků**.
  3. Na stránce **Sledování prostředků** vyberte kartu **síť** . V části **procesy s aktivitou sítě**ověřte, zda cbengine.exe aktivně odesílá velké množství dat.

       ![Snímek obrazovky s velkým počtem svazků v procesech se síťovou aktivitou](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Pokud cbengine.exe neposílá velký objem dat, proveďte kroky v následujících částech.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9: ověření připojení procesového serveru ke službě Azure Blob Storage

1. V Sledování prostředků vyberte **cbengine.exe**.
2. V části **připojení TCP**zkontrolujte, jestli je mezi procesovým serverem dosažitelný přístup k úložišti Azure.

  ![Snímek obrazovky s připojením mezi cbengine.exe a adresou URL úložiště objektů BLOB v Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Kontrolovat služby

Pokud se z procesového serveru nemůžete připojit k adrese URL služby Azure Blob Storage, podívejte se, jestli jsou spuštěné služby.

1. V Ovládacích panelech vyberte **služby**.
2. Ověřte, zda jsou spuštěny následující služby:

    - cxprocessserver
    - InMage Scout VX agent – Sentinel/vystavení
    - Agent Microsoft Azure Recovery Services
    - Služba Microsoft Azure Site Recovery
    - tmansvc

3. Spusťte nebo restartujte jakoukoli službu, která není spuštěná.
4. Ověřte, že je procesový server připojený a dostupný. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10: ověření připojení procesového serveru k veřejné IP adrese Azure

1. Na procesovém serveru v **%ProgramFiles%\Microsoft Azure Recovery Services Agent\Temp**otevřete nejnovější soubor CBEngineCurr. errlog.
2. V souboru vyhledejte **443**nebo **se pokus o připojení k řetězci nezdařil**.

  ![Protokoly chyb v dočasné složce](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Pokud se zobrazí problémy, v souboru CBEngineCurr. currLog s použitím portu 443 se nachází veřejná IP adresa Azure:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na příkazovém řádku na procesovém serveru použijte k otestování vaší veřejné IP adresy Azure službu Telnet.
6. Pokud se nemůžete připojit, postupujte podle následujícího postupu.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11: Ověřte nastavení brány firewall procesového serveru. 

Ověřte, jestli brána firewall na procesovém serveru blokuje přístup na základě IP adresy.

1. Pro pravidla brány firewall založená na IP adresách:

    a) Stáhněte úplný seznam [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653).

    b) přidejte rozsahy IP adres do konfigurace brány firewall, abyste zajistili, že brána firewall umožňuje komunikaci do Azure (a k výchozímu portu HTTPS, 443).

    c) Povolte rozsahy IP adres pro oblast Azure svého předplatného a pro oblast Azure Západní USA (slouží k řízení přístupu a správě identit).

2. Pro brány firewall založené na adrese URL přidejte do konfigurace brány URL uvedené v následující tabulce.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12: ověření nastavení serveru proxy procesového serveru 

1. Pokud používáte proxy server, ujistěte se, že server DNS přeloží název proxy server. Ověřte hodnotu, kterou jste zadali při nastavování konfiguračního serveru v klíči registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Zajistěte, aby se stejná nastavení používala agent Azure Site Recovery k odesílání dat.

    a) vyhledejte **Microsoft Azure Backup**.

    b) otevřete **Microsoft Azure Backup**a vyberte **Akce**  >  **vlastnosti změny**.

    c) na kartě **konfigurace proxy** serveru musí být adresa proxy shodná s adresou proxy serveru, která je uvedena v nastavení registru. Pokud ne, změňte ji na stejnou adresu.

## <a name="step-13-check-bandwidth"></a>Krok 13: ověření šířky pásma

Zvyšte šířku pásma mezi procesovým serverem a Azure a potom zkontrolujte, jestli k problému stále dochází.


## <a name="next-steps"></a>Další kroky

Pokud potřebujete další nápovědu, vystavte svůj dotaz na [stránce s dotazem Microsoft Q&Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png