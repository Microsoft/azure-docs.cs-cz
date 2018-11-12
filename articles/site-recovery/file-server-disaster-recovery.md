---
title: Ochrana souborového serveru pomocí Azure Site Recovery
description: Tento článek popisuje, jak zajistit ochranu souborového serveru pomocí Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 0eed8179396c80e4ea26ff90b53324b9a2891df0
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214898"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Ochrana souborového serveru pomocí Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure. Zotavení po havárii zahrnuje replikaci, převzetí služeb při selhání a zotavení různých úloh.

Tento článek popisuje, jak zajistit ochranu souborového serveru pomocí Site Recovery, a obsahuje další doporučení vhodná pro různá prostředí. 

- [Replikace počítačů souborového serveru Azure IaaS](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikace místního souborového serveru pomocí Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura souborového serveru
Cílem otevřeného a distribuovaného systému sdílení souborů je poskytovat prostředí, ve kterém může skupina uživatelů v různých zeměpisných umístěních efektivně spolupracovat při práci na souborech se zárukou vynucování jejich požadavků na integritu. Typický ekosystém místního souborového serveru, který podporuje velký počet souběžných uživatelů a položek obsahu využívá pro plánování replikace a omezování šířky pásma replikaci systému souborů DFS (Distributed File System). 

Replikace systému souborů DFS využívá kompresní algoritmus známý jako RDC (Remote Differential Compression), který umožňuje efektivní aktualizaci souborů přes síť s omezenou šířkou pásma. Tento algoritmus detekuje vkládání, odebírání a změny uspořádání dat v souborech. Replikace systému souborů DFS může replikovat pouze změněné bloky aktualizovaných souborů. Existují také prostředí souborových serverů, ve kterých se v době mimo špičku provádějí denní zálohy, které vyhovují potřebám zotavení po havárii. Replikace systému souborů DFS není implementovaná.

Následující diagram znázorňuje prostředí souborového serveru s implementovanou replikací systému souborů DFS.
                
![Architektura replikace systému souborů DFS](media/site-recovery-file-server/dfsr-architecture.JPG)

V předchozím diagramu se na replikaci souborů napříč skupinou replikace aktivně podílí několik souborových serverů označovaných jako členové. Obsah v replikované složce je dostupný pro všechny klienty, kteří odesílají požadavky do jakéhokoli člena, a to i v případě, že některý ze členů přejde do offline režimu.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Doporučení k zotavení po havárii pro souborové servery

* **Replikace souborového serveru pomocí Site Recovery:** Souborové servery je možné replikovat do Azure pomocí Site Recovery. V případě nedostupnosti jednoho nebo několika místních souborových serverů je možné aktivovat virtuální počítače pro obnovení v Azure. Tyto virtuální počítače pak můžou obsluhovat požadavky klientů v místním prostředí za předpokladu, že existuje připojení VPN typu Site-to-Site a v Azure je nakonfigurovaná služba Active Directory. Tuto metodu můžete využít v případě prostředí s nakonfigurovanou replikací systému souborů DFS nebo jednoduchého prostředí souborového serveru bez replikace systému souborů DFS. 

* **Rozšíření replikace systému souborů DFS na virtuální počítač Azure IaaS:** V clusterovaném prostředí souborového serveru s implementovanou replikací systému souborů DFS můžete rozšířit místní replikaci systému souborů DFS do Azure. Roli souborového serveru pak může zastávat virtuální počítač Azure. 

    * Jakmile se vyřeší závislosti v podobě připojení VPN typu Site-to-Site a služby Active Directory a povolí se replikace systému souborů DFS, v případě nedostupnosti jednoho nebo několika místních souborových serverů se klienti budou moct připojit k virtuálnímu počítači Azure, který jejich požadavky zpracuje.

    * Tento přístup můžete využít v případě, že vaše virtuální počítače obsahují konfigurace, které Site Recovery nepodporuje. Příkladem je sdílený disk clusteru, který se občas běžně používá v prostředích souborových serverů. Replikace systému souborů DFS funguje dobře také v prostředích s nízkou šířkou pásma se středním podílem výpovědí. Je potřeba zvážit dodatečné náklady na neustálý provoz virtuálního počítače Azure. 

* **Replikace souborů pomocí Synchronizace souborů Azure:** Pokud se chystáte využít cloud nebo již používáte virtuální počítač Azure, můžete použít Synchronizaci souborů Azure. Synchronizace souborů Azure nabízí synchronizaci plně spravovaných sdílených složek v cloudu, které jsou přístupné přes standardní protokol [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB). Sdílené složky Azure je pak možné připojit současně do cloudových i místních nasazení systémů Windows, Linux a macOS. 

Následující diagram vám pomůže určit, jakou strategii použít pro vaše prostředí souborového serveru.

![Rozhodovací strom](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktory, které je potřeba vzít v úvahu při zvažování zotavení po havárii do Azure

|Prostředí  |Doporučení  |Body ke zvážení |
|---------|---------|---------|
|Prostředí souborového serveru s replikací systému souborů DFS nebo bez|   [Replikace pomocí Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery nepodporuje clustery sdílených disků ani úložiště připojené k síti (NAS). Pokud se ve vašem prostředí používají tyto konfigurace, využijte podle potřeby některý z ostatních přístupů. <br> Site Recovery nepodporuje protokol SMB 3.0. Replikovaný virtuální počítač začlení změny pouze tehdy, když se provedené změny souborů aktualizují v původním umístění souborů.
|Prostředí souborového serveru s replikací systému souborů DFS     |  [Rozšíření replikace systému souborů DFS na virtuální počítač Azure IaaS](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      Replikace systému souborů DFS dobře funguje v prostředích s extrémně omezenou šířkou pásma. Tento přístup vyžaduje virtuální počítač Azure, který je neustále spuštěný. Při plánování budete muset vzít v úvahu náklady na tento virtuální počítač.         |
|Virtuální počítač Azure IaaS     |     [Synchronizace souborů](#use-azure-file-sync-service-to-replicate-your-files)   |     Pokud ve scénáři zotavení po havárii využíváte Synchronizaci souborů, během převzetí služeb při selhání musíte ručně zajistit, aby byly sdílené složky transparentním způsobem přístupné pro klientský počítač. Synchronizace souborů vyžaduje, aby na klientském počítači byl otevřený port 445.     |


### <a name="site-recovery-support"></a>Podpora Site Recovery
Vzhledem k tomu, že je replikace Site Recovery nezávislá na aplikaci, očekává se, že tato doporučení budou platit v následujících scénářích.
| Zdroj    |Do sekundární lokality    |Do Azure
|---------|---------|---------|
|Azure| -|Ano|
|Hyper-V|   Ano |Ano
|VMware |Ano|   Ano
|Fyzický server|   Ano |Ano
 

> [!IMPORTANT]
> Než budete pokračovat v některém z následujících tří přístupů, nezapomeňte se postarat o tyto závislosti.

**Připojení typu Site-to-Site:** Je potřeba navázat přímé připojení mezi místní lokalitou a sítí Azure, aby byla možná komunikace mezi servery. Použijte zabezpečené připojení VPN typu Site-to-Site k virtuální síti Azure, která slouží jako lokalita pro zotavení po havárii. Další informace najdete v tématu [Navázání připojení VPN typu Site-to-Site mezi místní lokalitou a virtuální sítí Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory:** Replikace systému souborů DFS závisí na službě Active Directory. To znamená, že se doménová struktura Active Directory s místními řadiči domény rozšíří do lokality pro zotavení po havárii v Azure. Pokud je potřeba udělit přístup nebo ověřit přístup uživatelů, musíte tyto kroky provést i v případě, že nevyužíváte replikaci systému souborů DFS. Další informace najdete v tématu [Rozšíření místní služby Active Directory do Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Doporučení k zotavení po havárii pro virtuální počítače Azure IaaS

Pokud konfigurujete a spravujete zotavení po havárii souborových serverů hostovaných na virtuálních počítačích Azure IaaS, máte na výběr ze dvou možností podle toho, jestli chcete provést přesun do služby [Soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Použití Synchronizace souborů Azure](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Použití Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Replikace souborů hostovaných na virtuálním počítači IaaS pomocí Synchronizace souborů

Službu Soubory Azure je možné použít k úplnému nahrazení nebo doplnění tradičních místních souborových serverů nebo zařízení NAS. Sdílené složky Azure je také možné pomocí Synchronizace souborů replikovat na místní nebo cloudové servery Windows. Tím se zajistí výkonné a distribuované ukládání dat do mezipaměti v místě, kde se tato data používají. Následující kroky popisují doporučení k zotavení po havárii pro virtuální počítače Azure, které provádějí stejné funkce jako tradiční souborové servery:
* Ochrana počítačů pomocí Site Recovery. Postupujte podle kroků v tématu [Replikace virtuálního počítače Azure do jiné oblasti Azure](azure-to-azure-quickstart.md).
* Replikace souborů z virtuálního počítače, který pro cloud funguje jako souborový server, pomocí Synchronizace souborů.
* Pomocí funkce [plánu obnovení](site-recovery-create-recovery-plans.md) v Site Recovery můžete přidat skripty pro [připojení sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a získat tak přístup ke sdílené složce na svém virtuálním počítači.

Následující kroky stručně popisují, jak používat Synchronizaci souborů:

1. [Vytvořte účet úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste pro své účty úložiště zvolili geograficky redundantní úložiště jen pro čtení, v případě havárie získáte ke svým datům přístup pro čtení ze sekundární oblasti. Další informace najdete v tématu [Strategie zotavení sdílených složek Azure po havárii](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvořte sdílenou složku](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. Na svém souborovém serveru Azure [spusťte Synchronizaci souborů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. Vytvořte skupinu synchronizace. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat alespoň jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí obsahovat také jeden koncový bod serveru, který představuje cestu na serveru Windows.
5. Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a místním serverem.
6. V případě havárie v místním prostředí proveďte převzetí služeb při selhání s využitím [plánu obnovení](site-recovery-create-recovery-plans.md). Přidejte skript pro [připojení sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a získejte ke sdílené složce přístup na svém virtuálním počítači.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikace virtuálního počítače souborového serveru IaaS pomocí Site Recovery

Pokud k virtuálnímu počítači souborového serveru IaaS přistupují místní klienti, proveďte následující kroky. Jinak přeskočte ke kroku 3.

1. Navažte připojení VPN typu Site-to-Site mezi místní lokalitou a sítí Azure.
2. Rozšiřte místní službu Active Directory.
3. Pro počítač souborového serveru IaaS [nastavte zotavení po havárii](azure-to-azure-tutorial-enable-replication.md) do sekundární oblasti.


Další informace o zotavení po havárii do sekundární oblasti najdete v [tomto článku](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikace místního souborového serveru pomocí Site Recovery

Následující kroky popisují replikaci virtuálního počítače VMware. Pokyny k replikaci virtuálního počítače Hyper-V najdete v [tomto kurzu](tutorial-hyper-v-to-azure.md).

1. [Připravte prostředky Azure](tutorial-prepare-azure.md) na replikaci místních počítačů.
2. Navažte připojení VPN typu Site-to-Site mezi místní lokalitou a sítí Azure. 
3. Rozšiřte místní službu Active Directory.
4. [Připravte místní servery VMware](tutorial-prepare-on-premises-vmware.md).
5. Pro místní virtuální počítače [nastavte zotavení po havárii](tutorial-vmware-to-azure.md) do Azure.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozšíření replikace systému souborů DFS na virtuální počítač Azure IaaS

1. Navažte připojení VPN typu Site-to-Site mezi místní lokalitou a sítí Azure. 
2. Rozšiřte místní službu Active Directory.
3. Ve virtuální síti Azure [vytvořte a zřiďte virtuální počítač souborového serveru](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
Ujistěte se, že se virtuální počítač přidá do stejné virtuální sítě Azure, která má křížové připojení s místním prostředím. 
4. Na Windows Serveru nainstalujte a nakonfigurujte [replikaci systému souborů DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx).
5. [Implementujte obor názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Díky implementaci oboru názvů DFS je možné provést převzetí služeb při selhání sdílených složek z produkčních lokalit do lokalit pro zotavení po havárii prostřednictvím aktualizace cílů složek v oboru názvů DFS. Po replikaci těchto změn oboru názvů DFS přes Active Directory se uživatelé transparentně připojí k odpovídajícím cílům složek.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Replikace místních souborů pomocí Synchronizace souborů
Pomocí Synchronizace souborů můžete replikovat soubory do cloudu. V případě havárie a nedostupnosti místního souborového serveru můžete připojit požadovaná umístění souborů z cloudu a pokračovat v obsluze požadavků z klientských počítačů.
Integrace Synchronizace souborů se Site Recovery:

* Zajistěte ochranu počítačů souborového serveru pomocí Site Recovery. Postupujte podle kroků v [tomto kurzu](tutorial-vmware-to-azure.md).
* Použijte Synchronizaci souborů k replikaci souborů z počítače, který pro cloud funguje jako souborový server.
* Pomocí funkce plánu obnovení v Site Recovery přidejte skripty pro připojení sdílené složky Azure na virtuálním počítači v Azure, u kterého došlo k převzetí služeb při selhání.

Podle následujícího postupu použijte Synchronizaci souborů:

1. [Vytvořte účet úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste pro své účty úložiště zvolili geograficky redundantní úložiště jen pro čtení (doporučeno), v případě havárie máte ke svým datům přístup pro čtení ze sekundární oblasti. Další informace najdete v tématu [Strategie zotavení sdílených složek Azure po havárii](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvořte sdílenou složku](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. Na místní souborový server [nasaďte Synchronizaci souborů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. Vytvořte skupinu synchronizace. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat alespoň jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí obsahovat také jeden koncový bod serveru, který představuje cestu na místním serveru Windows.
5. Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a místním serverem.
6. V případě havárie v místním prostředí proveďte převzetí služeb při selhání s využitím [plánu obnovení](site-recovery-create-recovery-plans.md). Přidejte skript pro připojení sdílené složky Azure a získejte ke sdílené složce přístup na svém virtuálním počítači.

> [!NOTE]
> Ujistěte se, že je otevřený port 445. Služba Soubory Azure využívá protokol SMB. Protokol SMB komunikuje přes port TCP 445. Zkontrolujte, jestli vaše brána firewall neblokuje na klientském počítači port TCP 445.


## <a name="do-a-test-failover"></a>Provedení testovacího převzetí služeb při selhání

1. Přejděte na web Azure Portal a vyberte váš trezor služby Recovery Services.
2. Vyberte plán obnovení vytvořený pro prostředí souborového serveru.
3. Vyberte **Testovací převzetí služeb při selhání**.
4. Vyberte bod obnovení a virtuální síť Azure a spusťte proces testovacího převzetí služeb při selhání.
5. Po zprovoznění sekundárního prostředí můžete provést ověřování.
6. Po dokončení ověřování vyberte v plánu obnovení **Vyčistit testovací převzetí služeb při selhání** a prostředí testovacího převzetí služeb při selhání se vyčistí.

Další informace o tom, jak provést testovací převzetí služeb při selhání, najdete v tématu [Testovací převzetí služeb při selhání v Site Recovery](site-recovery-test-failover-to-azure.md).

Pokyny k provedení testovacího převzetí služeb při selhání pro Active Directory a DNS najdete v tématu [Důležité informace o testovacím převzetí služeb při selhání pro Active Directory a DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Provedení převzetí služeb při selhání

1. Přejděte na web Azure Portal a vyberte váš trezor služby Recovery Services.
2. Vyberte plán obnovení vytvořený pro prostředí souborového serveru.
3. Vyberte **Převzetí služeb při selhání**.
4. Vyberte bod obnovení a spusťte proces převzetí služeb při selhání.

Další informace o tom, jak provést převzetí služeb při selhání, najdete v tématu [Převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).
