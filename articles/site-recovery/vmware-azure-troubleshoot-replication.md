---
title: Řešení problémů s replikací pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro běžné potíže s replikací během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/7/2019
ms.author: mayg
ms.openlocfilehash: 71c07d93d75ee372a50ec4ff5fc81e92926d329b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964769"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení problémů replikace pro virtuální počítače VMware a fyzické servery

Pokud chráníte virtuální počítače VMware nebo fyzické servery s využitím Azure Site Recovery, může se zobrazit zpráva konkrétní chyba. Tento článek popisuje některé běžné problémy se můžou vyskytnout při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitorování stavu procesového serveru, abyste předešli problémům s replikací

Doporučuje se pro monitorování stavu procesu serveru (PS) na portálu chcete zajistit, že replikace probíhá pro související zdrojové počítače. V trezoru, přejděte na Správa > infrastruktura Site Recovery > konfigurační servery. V okně konfiguračního serveru klikněte na tlačítko na Procesovém serveru podle přidružené servery. Proces serveru otevře se okno se statistikami jeho stavu. Můžete sledovat využití procesoru, využití paměti, stav služeb PS vyžadované pro replikaci, datum vypršení platnosti certifikátu a dostupné volné místo. Stav všech statistiky by měl být zelená. 

**Doporučuje se mít k dispozici paměť a využití procesoru v části 70 % a volné místo nad 25 %**. Volné místo odkazuje na místo na disku mezipaměti Procesovém serveru, který se používá k ukládání dat replikace ze zdrojového počítače před nahráním do Azure. Pokud se omezuje na méně než 20 %, replikace se omezí pro všechny přidružené zdrojové počítače. Postupujte podle [kapacity pokyny](./site-recovery-plan-capacity-vmware.md#capacity-considerations) pochopit požadované konfigurace pro replikaci zdrojového počítače.

Ujistěte se, že na počítači PS běží následující služby. Spusťte nebo restartujte jakoukoliv službu, která není spuštěná.

**Integrovaný procesový Server**

* cxprocessserver
* InMage PushInstall
* Služba nahrávání protokolu (LogUpload)
* InMage Scout Application Service
* Agent Microsoft Azure Recovery Services (obengine)
* Nástroje InMage Scout VX Agent – Sentinel/Outpost (svagents)
* tmansvc
* Světě služby publikování na webu (W3SVC)
* MySQL
* Služba Microsoft Azure Site Recovery (dra)

**Horizontální navýšení kapacity procesového serveru**

* cxprocessserver
* InMage PushInstall
* Služba nahrávání protokolu (LogUpload)
* InMage Scout Application Service
* Agent Microsoft Azure Recovery Services (obengine)
* Nástroje InMage Scout VX Agent – Sentinel/Outpost (svagents)
* tmansvc

**Procesový Server v Azure pro navrácení služeb po obnovení**

* cxprocessserver
* InMage PushInstall
* Služba nahrávání protokolu (LogUpload)

Ujistěte se, zda programu všechny služby nastaven na **automaticky nebo automaticky (zpožděné spuštění)**. Služba Microsoft Azure Recovery Services Agent (obengine) nemusí mít jeho programu nastavit jak je uvedeno výše.

## <a name="initial-replication-issues"></a>Potíže s úvodní replikací

Počáteční replikace selhání často jsou způsobeny problémy s připojením mezi zdrojovým serverem a že procesový server nebo mezi procesovým serverem a Azure. Ve většině případů při řešení těchto problémů podle postupu uvedeného v následujících částech.

### <a name="check-the-source-machine"></a>Zkontrolujte zdrojový počítač

Seznam ukazuje takto můžete zkontrolovat zdrojový počítač:

*  Na příkazovém řádku na zdrojovém serveru použijte příkaz ping na procesovém serveru přes HTTPS port spuštěním následujícího příkazu Telnet. Protokol HTTPS Port 9443 je jako výchozí procesový Server pro odesílání a příjem provozu replikace. Můžete upravit tento port v době registrace. Následující příkaz ověří pro problémy se síťovým připojením a problémy tento blok port brány firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Použijte službu Telnet k testování připojení. Nepoužívejte `ping`. Pokud není nainstalovaný protokol Telnet, proveďte kroky uvedené v [nainstalovat klient služby Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Pokud telnet je mohli úspěšně připojit k portu PS, by se zobrazí prázdnou obrazovku.

   Pokud se nemůžete připojit k procesového serveru, povolte příchozí port 9443 na procesovém serveru. Například může být nutné k povolení příchozího portu 9443 na procesovém serveru, pokud síť obsahuje hraniční síti a monitorována podsíť. Potom zkontrolujte, jestli problém přetrvává.

*  Pokud telnet je úspěšné a ještě zdrojového počítače hlásí, že PS není dostupný, otevřete webový prohlížeč na zdrojovém počítači a zkontrolujte, jestli je adresa https://<PS_IP>:<PS_Data_Port>/ dostupný.

    Chyba certifikátu HTTPS je očekáván dosažení této adresy. Ignoruje se chyba certifikátu a pokračování by měly končit 400 – Chybný požadavek, což znamená, že server požadavek nelze zpracovat, prohlížeče a, že standardní připojení HTTPS k serveru funguje dobře a kvalitní.

    Pokud tato akce nepomůže, podrobnosti o chybě v prohlížeči bude poskytovat pokyny. Pro třeba pokud ověřování proxy serveru je nesprávná, proxy server vrátí 407 – proxy serveru ověřování vyžaduje spolu s požadované akce v chybové zprávě. 

*  Zkontrolujte, že tyto protokoly na zdrojovém virtuálním počítači pro chyby související se sítí chyby nahrávání:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server"></a>Zkontrolujte, že procesový server

Seznam ukazuje takto můžete zkontrolovat procesový server:

> [!NOTE]
> Procesový Server musí mít statickou adresu IPv4 a nemůže na něm konfigurována překladu adres IP.

* **Zkontrolujte připojení mezi zdrojové počítače a procesového serveru**
1. V případě, je možné k Telnetu ze zdrojového počítače a zatím není PS ze zdroje dostupný, zkontrolujte připojení k začátku do konce se cxprocessserver ze zdrojového virtuálního počítače spuštěním nástroje cxpsclient na zdrojovém virtuálním počítači:

       <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

    V protokolech generovaných na PS v následujících adresářích podrobnosti o příslušné chyby:

       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
       and
       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
2. V případě, že neexistuje žádný prezenční signál z PS, zkontrolujte následující protokoly na PS:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

*  **Zkontrolujte, zda procesový server je aktivně doručením (push) dat do Azure**.

   1. Na procesovém serveru otevřete Správce úloh (stisknutím klávesy Ctrl + Shift + Esc).
   2. Vyberte **výkonu** kartu a potom vyberte **Monitor otevřít zdroj** odkaz. 
   3. Na **sledování prostředků** stránky, vyberte **sítě** kartu. V části **procesů pomocí síťové aktivity**, zkontrolujte, zda **cbengine.exe** aktivně odesílá velké objemy dat.

        ![Snímek obrazovky zobrazující svazky v rámci procesů pomocí síťové aktivity](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Pokud cbengine.exe neodesílá velké objemy dat, proveďte kroky v následujících částech.

*  **Zkontrolujte, zda procesový server můžete připojit k úložišti objektů Blob v Azure**.

   Vyberte **cbengine.exe**. V části **připojení TCP**, zkontrolujte, zda je připojení z procesového serveru na adresu URL blogu o Azure storage.

   ![Snímek obrazovky zobrazující spojení mezi cbengine.exe a adresu URL úložiště objektů Blob v Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Pokud není k dispozici připojení z procesového serveru na adresu URL blogu o Azure storage, v Ovládacích panelech vyberte **služby**. Zkontrolujte, zda jsou spuštěné následující služby:

   *  cxprocessserver
   *  Nástroje InMage Scout VX Agent – Sentinel/Outpost
   *  Agent Microsoft Azure Recovery Services
   *  Služba Microsoft Azure Site Recovery
   *  tmansvc

   Spusťte nebo restartujte jakoukoliv službu, která není spuštěná. Zkontrolujte, zda problém přetrvává.

*  **Zkontrolujte, zda procesový server může připojit k Azure veřejnou IP adresu pomocí portu 443**.

   V %programfiles%\Microsoft Azure Recovery Services Agent\Temp otevřete nejnovější CBEngineCurr.errlog soubor. V souboru vyhledejte **443** nebo řetězce **pokus o připojení se nezdařilo**.

   ![Snímek obrazovky zobrazující chybu protokoly ve složce Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Pokud se zobrazí problémy, na příkazovém řádku na procesovém serveru použijte službu Telnet příkaz ping Azure veřejné IP adresy (IP adresa je maskované na předchozím obrázku). Azure veřejné IP adresy najdete v souboru CBEngineCurr.currLog pomocí portu 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Pokud se nemůžete připojit, zkontrolujte, zda problém s přístupem k je z důvodu nastavení brány firewall nebo proxy serveru, jak je popsáno v dalším kroku.

*  **Zkontrolujte, zda brány firewall protokolu IP adres založené na procesovém serveru blokuje přístup**.

   Pokud použijete pravidla brány firewall založená na adresu IP serveru, stáhněte si úplný seznam [rozsahy IP adres datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Přidejte rozsahy IP adres pro konfiguraci brány firewall tak, aby Ujistěte se, že brána firewall umožňuje komunikaci do Azure (a výchozí port HTTPS 443). Povolte rozsahy IP adres pro oblast Azure svého předplatného a oblasti Azure USA – Západ, který se (používané pro řízení přístupu a identit správě).

*  **Zkontrolujte, zda bránu firewall založenou na adresu URL na procesovém serveru blokuje přístup**.

   Pokud používáte pravidlo brány firewall na základě adresy URL na serveru, přidejte adresy URL uvedené v následující tabulce ke konfiguraci brány firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Zkontrolujte, zda nastavení serveru proxy na procesovém serveru blokují přístup**.

   Pokud používáte proxy server, ujistěte se, že DNS server se přeložit název proxy serveru. Pokud chcete zjistit hodnotu, která jste zadali při nastavení konfiguračního serveru, přejděte ke klíči registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure lokality Recovery\ProxySettings**.

   Dále se ujistěte, že stejné nastavení bude používat agent Azure Site Recovery k odesílání dat: 
      
   1. Vyhledejte **služby Microsoft Azure Backup**. 
   2. Otevřít **Microsoft Azure Backup**a pak vyberte **akce** > **změnit vlastnosti**. 
   3. Na **konfiguraci proxy serveru** kartu, měli byste vidět adresa proxy serveru. Adresa proxy serveru musí být stejná jako adresa proxy serveru, který je zobrazen v nastavení registru. Pokud ne, změňte ho na stejnou adresu.

*  **Zkontrolujte, zda omezení šířky pásma je omezená na procesovém serveru**.

   Zvětšit šířku pásma a potom zkontrolujte, jestli problém přetrvává.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Zdrojový počítač není uvedená na webu Azure Portal

Při pokusu o vyberte zdrojový počítač k replikaci pomocí Site Recovery počítač nemusí být k dispozici pro jednu z následujících důvodů:

* **Dva virtuální počítače se stejnou instancí identifikátoru UUID**: Pokud dva virtuální počítače v rámci vCenter mají stejnou instanci UUID, prvního virtuálního počítače zjištěny konfigurační server se zobrazí na webu Azure Portal. Pokud chcete tento problém vyřešit, ujistěte se, že žádné dva virtuální počítače mají stejnou instanci UUID. Tento postup je běžný v případech, kde zálohování virtuálního počítače se stane aktivní a se přihlásí do našich záznamů zjišťování vidět. Odkazovat na [Azure Site Recovery VMware do Azure: Návod k vyčištění duplicitní nebo zastaralý položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) vyřešit.
* **Přihlašovací údaje uživatele nesprávné vCenter**: Ujistěte se, že jste přidali přihlašovací údaje k vCenter správná při nastavování konfiguračního serveru pomocí šablony OVF nebo jednotný instalační program. Ověření přihlašovacích údajů, které jste přidali během instalace, najdete v článku [upravit přihlašovací údaje pro automatické zjišťování](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Nedostatečná oprávnění vCenter**: Pokud není k dispozici pro přístup k serveru vCenter oprávnění požadovaná oprávnění, může dojít k selhání se zjistit virtuální počítače. Ujistěte se, že oprávnění popsaná v [Příprava účtu pro automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) jsou přidány do uživatelský účet vCenter.
* **Servery pro správu Azure Site Recovery**: Pokud virtuální počítač slouží jako server pro správu v rámci jednoho nebo více z následujících rolí – konfigurace serveru /scale-out procesový server nebo hlavní cílový server, nebudou moct vybrat virtuální počítač z portálu. Servery pro správu nelze replikovat.
* **Již chráněny/převzetí služeb při selhání prostřednictvím služby Azure Site Recovery**: Pokud virtuální počítač je už chráněná nebo převzetí služeb při selhání prostřednictvím služby Site Recovery, virtuální počítač není dostupný pro vybrané pro ochranu na portálu. Ujistěte se, že virtuální počítač, který hledáte, na portálu ještě nebyla zapnuta libovolným uživatelem nebo v jiném předplatném.
* **vCenter Nepřipojeno**: Zkontrolujte, jestli je vCenter v připojeném stavu. Abyste se přesvědčili, přejděte do trezoru služby Recovery Services > infrastruktura Site Recovery > konfigurační servery > klikněte na příslušný konfigurační server > Otevře se okno na vaše právo s podrobnostmi o přidružené servery. Zkontrolujte, jestli je vCenter připojený. Pokud ve stavu "Nepřipojeno", vyřešte problém a pak [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom virtuální počítač objeví na portálu.
* **Vypnout napájení ESXi**: Pokud hostitele ESXi, ve kterém se nachází virtuální počítač je ve vypnutém stavu, pak virtuální počítač neuvedete nebo nebude možné vybrat na webu Azure portal. Zapnout napájení hostitele ESXi [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom virtuální počítač objeví na portálu.
* **Čeká se na restartování**: Pokud čeká na restartování virtuálního počítače se pak nebudete moci vybrat počítač na webu Azure portal. Nezapomeňte dokončit aktivity čekající restartování [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom virtuální počítač objeví na portálu.
* **Nebyl nalezen IP**: Pokud virtuální počítač nemá platnou IP adresu s ním spojená, pak nebudete moci vybrat počítač na webu Azure portal. Nezapomeňte přiřadit platnou IP adresu pro virtuální počítač [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom virtuální počítač objeví na portálu.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Chráněné virtuální počítače jsou zobrazena šedě out na portálu

Virtuální počítače, které se replikují v rámci obnovení lokality nejsou k dispozici na webu Azure Portal, pokud existují duplicitní položky v systému. Zjistěte, jak odstranit zastaralé položky a tento problém vyřešit, najdete v tématu [Azure Site Recovery VMware do Azure: Návod k vyčištění duplicitní nebo zastaralý položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, zveřejněte svůj dotaz v [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme k dispozici aktivní komunitě a jedním z našich techniků, kteří vám můžou pomoct.
