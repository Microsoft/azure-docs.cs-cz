---
title: Řešení potíží s replikaci pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro běžné potíže s replikací během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790652"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení problémů replikace pro virtuální počítače VMware a fyzické servery

Pokud chráníte virtuální počítače VMware nebo fyzických serverů požíváním Azure Site Recovery, může se zobrazit zpráva konkrétní chyba. Tento článek popisuje některé běžné problémy se můžou vyskytnout při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Azure Site Recovery](site-recovery-overview.md).


## <a name="initial-replication-issues"></a>Potíže s úvodní replikací

V mnoha případech jsou selhání počáteční replikace, na které jsme na webu podpory kvůli problémům s připojením mezi zdrojovém serveru proces nebo proces serveru do Azure. Většině případů můžete tyto potíže řešit pomocí následujících kroků uvedených dole.

### <a name="verify-the-source-machine"></a>Ověřte, zdrojový počítač
* Z příkazového řádku počítače zdrojového serveru použijte službu Telnet příkaz ping procesový Server s port https (standardně 9443), jak vidíte níže, jestli jsou všechny problémy se síťovým připojením nebo brány firewall portu blokující problémy.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Použít službu Telnet, nepoužívejte příkazem PING otestovat připojení.  Pokud není nainstalovaný protokol Telnet, postupujte podle seznamu kroků [zde](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Pokud se nelze připojit, povolit příchozí port 9443 na Procesovém serveru a zkontrolujte, pokud problém pořád ukončen. Byl někdy, ve kterém byl procesový server za hraniční síti, která je příčinou tohoto problému.

* Zkontrolujte, že stav služby `InMage Scout VX Agent – Sentinel/OutpostStart` Pokud není spuštěný a zkontrolujte Pokud problém stále přetrvává.   

### <a name="verify-the-process-server"></a>Ověřte, že procesový server

* **Zaškrtněte, pokud procesový server je aktivně doručením (push) dat do Azure**

Z počítač procesového serveru otevřete Správce úloh (stisknutím klávesy Ctrl-Shift-Esc). Přejděte na kartu výkonu a klikněte na odkaz Monitor otevřít zdroj. Z Resource Manageru, přejděte na kartu síť. Zaškrtněte, pokud cbengine.exe v "Procesy s aktivitou sítě" aktivně odesílá velké objemy dat (v MB).

![Povolení replikace](./media/vmware-azure-troubleshoot-replication/cbengine.png)

V opačném případě postupujte podle kroků uvedených dole:

* **Zkontrolujte, jestli je procesový server moct připojit objektů Blob v Azure**: Vyberte a zkontrolujte cbengine.exe zobrazíte "připojení TCP' podívejte se, pokud je připojení z procesového serveru do služby Azure Storage blob URL.

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

Otevřete nejnovější CBEngineCurr.errlog z `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` a vyhledat: připojení k 443 a pokus se nezdařil.

![Povolení replikace](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Pokud dojde k problémům, z příkazového řádku procesový Server, použijte telnet příkaz ping vaše veřejné IP adrese Azure (v maskována nad image) součástí CBEngineCurr.currLog přes port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Pokud se nemůžete připojit, potom zkontrolujte, jestli problém s přístupem k je kvůli bráně firewall nebo Proxy, jak je popsáno v dalším kroku.


* **Zaškrtněte, pokud není IP adresa brány firewall založeným na Procesovém serveru blokuje přístup**: Pokud používáte pravidla brány firewall založená na adresu IP serveru, pak si stáhnout kompletní seznam Microsoft Azure Datacenter rozsahy IP adres z [tady](https://www.microsoft.com/download/details.aspx?id=41653) a přidat je do konfigurace brány firewall k zajištění, že byla povolena komunikace s Azure (a port HTTPS (443)).  Povolte rozsahy IP adres pro oblast Azure svého předplatného a pro oblast Západní USA (používá se pro řízení přístupu a správu identit).

* **Zaškrtněte, pokud není brány firewall založeným na adresu URL na Procesovém serveru blokuje přístup**:  Pokud použijete pravidla brány firewall na základě adresy URL na serveru, ujistěte se, že následující adresy URL se přidají do konfigurace brány firewall.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Zaškrtněte, pokud nejsou nastavení serveru Proxy na Procesovém serveru blokuje přístup**.  Pokud používáte Proxy Server, zajistěte, aby že serverem DNS překládá název proxy serveru.
Chcete-li zkontrolovat, co jste zadali v době instalace konfiguračního serveru. Přejděte ke klíči registru

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Teď zkontrolujte, že stejné nastavení se používá pomocí agenta Azure Site Recovery k odesílání dat.
Zálohování Microsoft Azure Search

Otevřete ho a klikněte na akci > změnit vlastnosti. Na kartě Konfigurace proxy serveru měli byste vidět adresu proxy serveru, která by měla být stejná, jak je znázorněno v nastavení registru. Pokud ne, změňte ji na stejné adrese.


* **Zaškrtněte, pokud není omezen omezení šířky pásma na Procesovém serveru**:  Zvětšit šířku pásma a zkontrolujte, jestli problém stále existuje.

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>Zdrojový počítač, který se má chránit pomocí Site Recovery není uvedený na portálu Azure portal

Při pokusu o zvolte zdrojového počítače na povolení replikace prostřednictvím služby Azure Site Recovery se na počítači možná není k dispozici vám pokračujte z následujících důvodů

* Pokud jsou dva virtuální počítače v rámci serveru vCenter s stejnou instanci UUID, pak první virtuální počítač zjištěných konfiguračního serveru se zobrazí na portálu. Pokud chcete vyřešit, zajistěte, aby žádné dva virtuální počítače měly stejnou instanci UUID.
* Ujistěte se, že jste přidali přihlašovací údaje k vCenter správná během sada konfigurace pomocí šablony OVF a sjednocené sady. K ověření přihlašovacích údajů přidaných, najdete pokyny, které sdílí [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* Pokud je k dispozici pro přístup k serveru vCenter oprávnění nemají dostatečná oprávnění, může vést k selhání v zjišťování virtuálních počítačů. Zkontrolujte oprávnění k dispozici [tady](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) jsou přidány do uživatelský účet vCenter.
* Pokud virtuální počítač je už chránit pomocí Site Recovery, pak nebude k dispozici pro ochranu. Ujistěte se, že virtuální počítač, který hledáte, na portálu ještě nebyla zapnuta libovolným uživatelem nebo v jiných předplatných.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Chráněné virtuální počítače jsou zobrazena šedě out na portálu

Virtuální počítače, které se replikují v rámci obnovení lokality mají šedou barvu pokud existuje duplicitní položky v systému. Přečtěte si pokyny uvedené [tady](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) odstranit zastaralé položky a vyřešte problém.

## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc, odešle se dotaz a [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme k dispozici aktivní komunitě a jednu naši technici budou schopni pomoct.
