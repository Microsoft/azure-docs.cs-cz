---
title: Architektura zotavení po havárii technologie Hyper-V v Azure Site Recovery
description: Tento článek poskytuje přehled komponent a architektury používaných při nasazení zotavení po havárii pro místní virtuální počítače Hyper-V (bez VMM) do Azure pomocí služby Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 73771a70bfe14e46fb08db75fd4baa522caaadb8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579604"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii z Hyper-V do Azure


Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů Hyper-V mezi místními hostiteli Hyper-V a Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

Hostitele Hyper-V můžete volitelně spravovat v privátních cloudech System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Komponenty architektury – Hyper-V bez nástroje VMM

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro replikaci Hyper-V do Azure, když nejsou hostitelé Hyper-V spravováni nástrojem VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních úloh virtuálních počítačů se ukládají v účtu úložiště. Virtuální počítače Azure se vytvářejí s daty replikovaných úloh, když dojde k převzetí služeb při selhání z vaší místní lokality.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Hyper-V** | Během nasazení Site Recovery shromažďujete hostitele a clustery Hyper-V do lokalit technologie Hyper-V. Na každého samostatného hostitele Hyper-V nebo na každém uzlu clusteru Hyper-V nainstalujete poskytovatele Azure Site Recovery a agenta Recovery Services. | Zprostředkovatel orchestruje replikaci pomocí služby Site Recovery přes internet. Agent Recovery Services se stará o replikaci dat.<br/><br/> Komunikace z poskytovatele i agenta je zabezpečená a šifrovaná. Šifrují se rovněž replikovaná data v úložišti Azure.
**Virtuální počítače Hyper-V** | Jeden nebo více virtuálních počítačů běžících na technologii Hyper-V. | Na virtuálních počítačích není nutné explicitně instalovat žádné požadavky.


**Architektura Hyper-V do Azure (bez VMM)**

![Diagram znázorňující, že místní lokalita Hyper-V do architektury Azure bez VMM](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Komponenty architektury – technologie Hyper-V s nástrojem VMM

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro replikaci Hyper-V do Azure, když jsou hostitelé Hyper-V spravováni v cloudech VMM.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních úloh virtuálních počítačů se ukládají v účtu úložiště. Když dojde k převzetí služeb při selhání z místního webu, vytvoří se virtuální počítače Azure s replikovanými daty.<br/><br/> Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Server VMM** | Server VMM obsahuje jeden nebo více cloudů s hostiteli Hyper-V. | Na server VMM nainstalujete poskytovatele Site Recovery, pro orchestraci replikace pomocí Site Recovery a registraci serveru v trezoru služby Recovery Services.
**Hostitel Hyper-V** | Jeden nebo několik hostitelů/clusterů Hyper-V spravovaných nástrojem VMM. |  Agenta Recovery Services nainstalujete na každého hostitele nebo uzel clusteru Hyper-V.
**Virtuální počítače Hyper-V** | Jeden nebo několik virtuálních počítačů spuštěných na hostitelském serveru Hyper-V. | Na virtuálních počítačích není výslovně potřeba nic instalovat.
**Sítě** | Logické sítě a sítě virtuálních počítačů nastavené na serveru VMM. Síť virtuálních počítačů by měla být propojená s logickou sítí, která je přidružená ke cloudu. | Sítě virtuálních počítačů jsou namapované na virtuální sítě Azure. Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, přidají se do sítě Azure, která je namapovaná na síť virtuálních počítačů.

**Architektura Hyper-V do Azure (s VMM)**

![Diagram znázorňující, že místní lokalita Hyper-V do architektury Azure pomocí nástroje VMM.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Nastavení odchozího připojení k síti

Aby mohla Site Recovery fungovat podle očekávání, musíte upravit odchozí síťové připojení, aby bylo možné prostředí replikovat.

> [!NOTE]
> Site Recovery nepodporuje připojení k síti pomocí ověřovacího proxy serveru.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |


## <a name="replication-process"></a>Proces replikace

![Diagram znázorňující proces replikace Hyper-V do Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikace a proces obnovení**


### <a name="enable-protection"></a>Povolení ochrany

1. Po povolení ochrany pro virtuální počítače Hyper-V (na webu Azure Portal nebo místně) se spustí **Povolení ochrany**.
2. Úloha zkontroluje, zda počítač splňuje požadavky, a potom vyvolá metodu [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice), která nastaví replikaci s nastavením, které jste nakonfigurovali.
3. Úloha spustí počáteční replikaci vyvoláním metody [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice), která zahájí úplnou replikaci virtuálního počítače a odešle virtuální disky virtuálního počítače do Azure.
4. Úlohu můžete sledovat na kartě **úlohy** .      ![Snímek obrazovky se seznamem úloh na kartě úlohy. ](media/hyper-v-azure-architecture/image1.png) ![Snímek obrazovky zapnout ochranu s dalšími podrobnostmi](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Počáteční replikace dat

1. Při spuštění počáteční replikace se pořídí snímek [virtuálního počítače Hyper-V](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) .
2. Virtuální pevné disky virtuálního počítače se replikují jednou po jednom, dokud se všechny nezkopírují do Azure. Tato situace může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířce pásma sítě. [Přečtěte si, jak](https://support.microsoft.com/kb/3056159) zvýšit šířku pásma sítě.
3. Pokud dojde ke změnám na disku v době, kdy probíhá počáteční replikace, sledování replikace repliky technologie Hyper-V sleduje změny jako protokoly replikace technologie Hyper-V (. hrl). Tyto soubory protokolu jsou umístěné ve stejné složce jako disky. Každý disk má přidružený soubor. hrl, který se odesílá do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


### <a name="finalize-protection-process"></a>Finalizace procesu ochrany

1. Po dokončení počáteční replikace se spustí **ochrana finalizace úlohy virtuálního počítače** . Nakonfiguruje síť a další nastavení po replikaci tak, aby byl virtuální počítač chráněný.
2. V této fázi můžete zkontrolovat nastavení virtuálního počítače, abyste se ujistili, že je připravený na převzetí služeb při selhání. Pro virtuální počítač můžete spustit postupné procházení zotavení po havárii (testovací převzetí služeb při selhání) a zkontrolovat, jestli převezme služby při selhání podle očekávání. 


## <a name="delta-replication"></a>Rozdílová replikace

1. Po počáteční replikaci se v souladu se zásadami replikace spustí rozdílová replikace.
2. Sledovací modul replikace replik technologie Hyper-V sleduje změny virtuálního pevného disku jako souborů. HRL. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
3. Protokol se pošle do účtu úložiště zákazníka. Při přenosu protokolu do Azure jsou změny v primárním disku sledovány v jiném souboru protokolu ve stejné složce.
4. Během počáteční a rozdílové replikace můžete virtuální počítač monitorovat v Azure Portal.

### <a name="resynchronization-process"></a>Proces opakované synchronizace

1. Pokud rozdílová replikace selže a úplná replikace by byla náročná, pokud jde o šířku pásma nebo čas, pak se virtuální počítač označí pro resynchronizaci.
    - Pokud například soubory .hrl dosáhnou 50 % velikosti disku, pak se u virtuálního počítače stanoví nutnost resynchronizace.
    -  Ve výchozím nastavení je automatické spuštění resynchronizace naplánováno mimo kancelářskou dobu.
1.  Opětovná synchronizace odesílá jenom rozdílová data.
    - Minimalizuje množství dat odesílaných výpočetními součty zdrojového a cílového virtuálního počítače.
    - Používá algoritmus bloků s pevným blokem, ve kterém jsou zdrojové a cílové soubory rozdělené do pevných bloků dat.
    - Vygenerují se kontrolní součty pro jednotlivé bloky dat. Tyto jsou porovnány s cílem určit, které bloky ze zdroje je nutné použít na cíl.
2. Po dokončení resynchronizace by měla pokračovat normální rozdílová replikace.
3. Pokud nechcete čekat na výchozí opětovnou synchronizaci mimo hodiny, můžete virtuální počítač znovu synchronizovat ručně. Například pokud dojde k výpadku. Provedete to tak, že v Azure Portal > znovu **synchronizujete** virtuální počítač.

    ![Snímek obrazovky znázorňující možnost opětovné synchronizace](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Opakovat proces

Pokud dojde k chybě replikace, je předdefinován opakovaný pokus. Opakování je klasifikované tak, jak je popsáno v tabulce.

**Kategorie** | **Podrobnosti**
--- | ---
**Neopravitelné chyby** | Pokus se nebude opakovat. Stav virtuálního počítače bude **Kritický** a bude nutný zásah správce.<br/><br/> Mezi příklady těchto chyb patří poškozený řetězec VHD, neplatný stav pro virtuální počítač repliky, chyby ověřování v síti, chyby autorizace a Chyby nenalezení virtuálních počítačů (pro samostatné servery Hyper-V.
**Opravitelné chyby** | Pokusy se budou opakovat v každém intervalu replikace a pomocí exponenciální regrese se bude od počátku prvního pokusu zvětšovat interval opakování o 1, 2, 4, 8 a 10 minut. Pokud chyba přetrvává, bude se pokus opakovat každých 30 minut. Mezi tyto příklady patří chyby sítě, chybové zprávy s nízkým diskem a nedostatečné paměťové podmínky.



## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

1. Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat. Pokud vaše primární lokalita není dostupná, spusťte neplánované převzetí služeb při selhání.
2. Můžete převzít službu při selhání jednoho počítače nebo vytvořit plány zotavení a orchestrovat převzetí služeb při selhání více počítačů.
3. Spouštíte převzetí služeb při selhání. Po dokončení první fáze převzetí služeb při selhání byste měli být schopni zobrazit vytvořené virtuální počítače repliky v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba.
4. Pak potvrdíte převzetí služeb při selhání, abyste mohli začít přistupovat ke úlohám z repliky virtuálního počítače Azure.

Po opětovném zprovoznění místní infrastruktury můžete provést navrácení služeb po obnovení. Navrácení služeb po obnovení proběhne ve třech fázích:

1. Vykonání plánovaného převzetí služeb při selhání z Azure do místní lokality:
    - **Minimalizovat prostoje**: Pokud použijete tuto možnost Site Recovery před převzetí služeb při selhání synchronizuje data. Kontroluje změněné bloky dat a stáhne je do místní lokality, zatímco virtuální počítač Azure běží a minimalizuje výpadky. Když ručně určíte, že převzetí služeb při selhání by mělo být dokončené, virtuální počítač Azure se vypne a všechny poslední změny rozdílu se zkopírují a spustí se převzetí služeb při selhání.
    - **Úplné stažení**: při převzetí služeb při selhání je tato data možností synchronizovaná. Tato možnost stáhne celý disk. Je rychlejší, protože nejsou vypočítány žádné kontrolní součty, ale existuje více výpadků. Tuto možnost použijte, pokud jste už nějakou dobu spustili repliky virtuálních počítačů Azure, nebo pokud se místní virtuální počítač odstranil.
    - **Vytvořit virtuální počítač**: můžete vybrat, že se navrácení služeb po obnovení do stejného virtuálního počítače nebo na jiný virtuální počítač. Můžete určit, že Site Recovery vytvořit virtuální počítač, pokud ještě neexistuje.

2. Po dokončení počáteční synchronizace můžete vybrat, aby se převzetí služeb při selhání dokončilo. Po dokončení se můžete přihlásit k místnímu virtuálnímu počítači a ověřit, jestli všechno funguje podle očekávání. V Azure Portal vidíte, že se virtuální počítače Azure zastavily.
3.  Pak potvrdíte převzetí služeb při selhání, které se dokončí, a znovu otevřete přístup k zatížení z místního virtuálního počítače.
4. Po úspěšném dokončení úloh se povolí zpětná replikace, aby se místní virtuální počítače znovu replikují do Azure.



## <a name="next-steps"></a>Další kroky


V [tomto kurzu](tutorial-prepare-azure.md) se naučíte používat replikaci Hyper-V do Azure.


