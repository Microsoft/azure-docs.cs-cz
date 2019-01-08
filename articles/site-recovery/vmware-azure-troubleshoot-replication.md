---
title: Řešení problémů s replikací pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro běžné potíže s replikací během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063674"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení problémů replikace pro virtuální počítače VMware a fyzické servery

Pokud chráníte virtuální počítače VMware nebo fyzické servery s využitím Azure Site Recovery, může se zobrazit zpráva konkrétní chyba. Tento článek popisuje některé běžné problémy se můžou vyskytnout při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Potíže s úvodní replikací

Počáteční replikace selhání často jsou způsobeny problémy s připojením mezi zdrojovým serverem a že procesový server nebo mezi procesovým serverem a Azure. Ve většině případů při řešení těchto problémů podle postupu uvedeného v následujících částech.

### <a name="check-the-source-machine"></a>Zkontrolujte zdrojový počítač

Seznam ukazuje takto můžete zkontrolovat zdrojový počítač:

*  Na příkazovém řádku na zdrojovém serveru použijte příkaz ping na procesovém serveru přes port HTTPS (výchozí port HTTPS je 9443) spuštěním následujícího příkazu Telnet. Příkaz zkontroluje problémy se síťovým připojením a problémy tento blok port brány firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Použijte službu Telnet k testování připojení. Nepoužívejte `ping`. Pokud není nainstalovaný protokol Telnet, proveďte kroky uvedené v [nainstalovat klient služby Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Pokud se nemůžete připojit k procesového serveru, povolte příchozí port 9443 na procesovém serveru. Například může být nutné k povolení příchozího portu 9443 na procesovém serveru, pokud síť obsahuje hraniční síti a monitorována podsíť. Potom zkontrolujte, jestli problém přetrvává.

*  Zkontrolujte stav **InMage Scout VX Agent – Sentinel/OutpostStart** služby. Pokud služba není spuštěná, spusťte službu a potom zkontrolujte, zda problém přetrvává.   

### <a name="check-the-process-server"></a>Zkontrolujte, že procesový server

Seznam ukazuje takto můžete zkontrolovat procesový server:

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

*  Pokud dva virtuální počítače v rámci vCenter mají stejnou instanci UUID, prvního virtuálního počítače zjištěny konfigurační server se zobrazí na webu Azure Portal. Pokud chcete tento problém vyřešit, ujistěte se, že žádné dva virtuální počítače mají stejnou instanci UUID.
*  Ujistěte se, že jste přidali přihlašovací údaje k vCenter správná při nastavování konfiguračního serveru pomocí šablony OVF nebo jednotný instalační program. Ověření přihlašovacích údajů, které jste přidali během instalace, najdete v článku [upravit přihlašovací údaje pro automatické zjišťování](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
*  Pokud není k dispozici pro přístup k serveru vCenter oprávnění požadovaná oprávnění, může dojít k selhání se zjistit virtuální počítače. Ujistěte se, že oprávnění popsaná v [Příprava účtu pro automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) jsou přidány do uživatelský účet vCenter.
*  Pokud virtuální počítač je už chránit pomocí Site Recovery, virtuální počítač není dostupný pro vybrané pro ochranu na portálu. Ujistěte se, že virtuální počítač, který hledáte, na portálu ještě nebyla zapnuta libovolným uživatelem nebo v jiném předplatném.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>Chráněné virtuální počítače nejsou dostupné na portálu

Virtuální počítače, které se replikují v rámci obnovení lokality nejsou k dispozici na webu Azure Portal, pokud existují duplicitní položky v systému. Zjistěte, jak odstranit zastaralé položky a vyřešte problém, naleznete v tématu [Azure Site Recovery VMware do Azure: Návod k vyčištění duplicitní nebo zastaralý položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, zveřejněte svůj dotaz v [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme k dispozici aktivní komunitě a jedním z našich techniků, kteří vám můžou pomoct.
