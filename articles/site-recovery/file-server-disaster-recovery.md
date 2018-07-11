---
title: Chránit souborový server s využitím Azure Site Recovery
description: Tento článek popisuje, jak chránit souborový server s využitím Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 0b6d5dccbce30c55e259e4bb3f8ae4194a02b646
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916879"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Chránit souborový server s využitím Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a Azure virtual machines (VM). Zotavení po havárii zahrnuje replikaci, převzetí služeb při selhání a obnovení různé úlohy.

Tento článek popisuje, jak chránit souborový server s využitím Site Recovery a poskytuje další doporučení podle různých prostředí. 

- [Replikovat počítače Azure IaaS souborového serveru](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Pomocí Site Recovery replikovat serveru v místním souboru](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura serveru
Cílem otevřít distribuované sdílení souborů systému je a poskytuje prostředí, kde můžete spolupracovat skupinu uživatelů, geograficky distribuované efektivně pracovat na souborech a zaručit, že se vynucují požadavky na jejich integritu. Typický místní soubor ekosystémem partnerů pro server podporující velký počet souběžných uživatelů a velký počet položek obsahu distribuované systému službu replikace souborů (DFSR) používá replikace plánování a omezování šířky pásma. 

Služby DFSR používá algoritmus komprese označovaný jako Remote Differential Compression (RDC), který slouží k efektivnímu aktualizaci souborů přes síť omezenou šířkou pásma. Zjistí vkládání, odebrání a změnu uspořádání dat v souborech. Služby DFSR je povoleno Replikovat pouze bloky změněné souborů, když jsou aktualizovány soubory. Existují také soubor serverových prostředích, kde denní zálohy jsou prováděny v mimo špičku časování, které slouží různým podle potřeb po havárii. Služby DFSR není implementována.

Následující diagram znázorňuje soubor prostředí serveru pomocí služby DFSR implementována.
                
![Architektura služby DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

Na předchozím obrázku několik souborových serverů nazývají členy aktivně účastnit replikaci souborů mezi replikační skupiny. Obsah v replikované složce jsou k dispozici pro všechny klienty, kteří odesílají požadavky na některý z členů, i v případě, že člen přejde do režimu offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Doporučení pro zotavení po havárii pro souborové servery

* **Pomocí Site Recovery replikovat souborový server**: souborové servery je možné replikovat do Azure pomocí Site Recovery. Když budou pro jeden nebo více místních souborových serverů, můžete se aktivují obnovení virtuálních počítačů v Azure. Virtuální počítače můžete pak obsluhovat požadavky od klientů, místní, není k dispozici je připojení VPN typu site-to-site a Active Directory je nakonfigurovaná v Azure. Tuto metodu v případě prostředí nakonfigurovaný DFSR nebo prostředí serveru jednoduchého souboru můžete použít s žádné DFSR. 

* **Rozšiřte DFSR na Virtuálním počítači Azure IaaS**: V prostředí clusterového souborového serveru s DFSR implementované, můžete rozšířit místní DFSR do Azure. Virtuální počítač Azure je pak povoleno provádět roli souborového serveru. 

    * Poté, co jsou zpracovány závislosti připojení VPN typu site-to-site a služby Active Directory a DFSR se používají, když budou pro jeden nebo více místních souborových serverů, klienti mohou připojit k virtuálnímu počítači Azure, která obsluhuje požadavky.

    * Tento přístup můžete použít, pokud konfigurace, které služba Site Recovery nepodporuje virtuální počítače. Příkladem je sdíleném disku clusteru, který se někdy běžně používá v souboru serverových prostředích. Služby DFSR také dobře funguje v prostředí s malou šířkou pásma s střední výpovědí. Je potřeba zvážit další náklady na Virtuálním počítači Azure s a pořád spuštěný. 

* **Pomocí Azure File Sync replikovat svoje soubory**: Pokud máte v plánu používat cloud nebo už používáte virtuální počítač Azure, můžete použít Azure File Sync. Azure File Sync nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardních průmyslových synchronizace [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokol (SMB). Sdílené složky Azure může pak být připojit současně do cloudových i místních nasazení systémů Windows, Linux a macOS. 

Následující diagram vám pomůže určit, jaká strategie pro vaše serverové prostředí souboru.

![Rozhodovací strom](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktory, které je třeba zvážit ve vašich rozhodnutí o zotavení po havárii do Azure

|Prostředí  |Doporučení  |Body ke zvážení |
|---------|---------|---------|
|Soubor serveru prostředí s nebo bez něj DFSR|   [Pomocí služby Site Recovery pro replikaci](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery nepodporuje sdílené diskové clustery nebo úložiště připojené k síti (NAS). Pokud vaše prostředí používá tyto konfigurace, používejte některou z jiné postupy, podle potřeby. <br> Site Recovery nepodporuje protokol SMB 3.0. Replikovaný virtuální počítač obsahuje změny, pouze v případě, že změny provedené v souborech se aktualizují v původním umístění souborů.
|Soubor prostředí serveru pomocí služby DFSR     |  [Rozšiřte DFSR na virtuálním počítači Azure IaaS](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      Služby DFSR dobře funguje v prostředí s velmi crunched šířky pásma. Tento přístup vyžaduje virtuální počítač Azure, který je v provozu a pořád spuštěný. Budete muset počítat náklady na virtuální počítač při plánování.         |
|Virtuální počítač Azure IaaS     |     [File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     Pokud používáte File Sync ve scénáři zotavení po havárii, při převzetí služeb při selhání je nutné provést ručně prováděné akce, abyste měli jistotu, že sdílené složky jsou přístupné do klientského počítače transparentním způsobem. File Sync vyžaduje port 445 z klientského počítače otevřen.     |


### <a name="site-recovery-support"></a>Site Recovery podporu
Protože replikace Site Recovery je nezávislý na aplikace, se očekává tato doporučení platí pro následující scénáře.
| Zdroj    |Do sekundární lokality    |Do Azure
|---------|---------|---------|
|Azure| -|Ano|
|Hyper-V|   Ano |Ano
|VMware |Ano|   Ano
|Fyzický server|   Ano |Ano
 

> [!IMPORTANT]
> Než budete pokračovat s žádným z následujících tří postupů, ujistěte se, že se tyto závislosti se postaral o.

**Připojení Site-to-site**: přímé připojení mezi místní lokalitou a síť Azure musí vytvořit umožňujícím komunikaci mezi servery. Používejte zabezpečené připojení VPN typu site-to-site k virtuální síti Azure, který se používá jako lokalitu pro zotavení po havárii. Další informace najdete v tématu [vytvořit připojení site-to-site VPN mezi místní lokalitou a Azure virtual network,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Služby Active Directory**: DFSR závisí na službě Active Directory. To znamená, že doménová struktura služby Active Directory s řadiče místní domény rozšířeno na webu pro zotavení po havárii v Azure. I v případě, že nepoužíváte DFSR, pokud odpovídající uživatelé muset být udělen přístup nebo ověření pro přístup, je nutné provést tyto kroky. Další informace najdete v tématu [rozšířit místní služby Active Directory do Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Doporučení pro zotavení po havárii pro virtuální počítače Azure IaaS

Pokud konfigurujete a spravovat zotavení po havárii souborových serverů, které jsou hostované ve virtuálních počítačích Azure IaaS, můžete zvolit mezi dvěma možnostmi, v závislosti na tom, jestli chcete přesunout do [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Používání File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Pomocí služby Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Používání File Sync replikovat soubory, které jsou hostované na virtuálním počítači IaaS

Službu Soubory Azure je možné použít k úplnému nahrazení nebo doplnění tradičních místních souborových serverů nebo zařízení NAS. Sdílené složky Azure je také replikovat pomocí služby File Sync na servery Windows, ať už místní nebo v cloudu, výkonu a distribuované ukládání do mezipaměti dat, ve kterém se používá. Následující kroky popisují doporučení pro zotavení po havárii pro virtuální počítače Azure, které provádějí stejné funkce jako tradiční souborové servery:
* Ochrana počítačů pomocí Site Recovery. Postupujte podle kroků v [replikace virtuálního počítače Azure do jiné oblasti Azure](azure-to-azure-quickstart.md).
* Používání File Sync replikovat soubory z virtuálního počítače, který funguje jako souborový server do cloudu.
* Pomocí Site Recovery [plánu obnovení](site-recovery-create-recovery-plans.md) funkce přidat skripty, které [připojení sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a přístup do sdílené složky na virtuálním počítači.

Následující kroky stručně popisují způsob použití File Sync:

1. [Vytvoření účtu úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste zvolili geograficky redundantní úložiště jen pro čtení pro účty úložiště, získáte přístup pro čtení k datům ze sekundární oblasti v případě havárie. Další informace najdete v tématu [strategie zotavení po havárii sdílené složky Azure file](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvoření sdílené složky](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Začaly synchronizovat soubory](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) na vašem serveru Azure file.
4. Vytvořte skupinu synchronizace. Koncové body v rámci skupiny synchronizace se udržovat synchronizované mezi sebou. Skupina synchronizace musí obsahovat aspoň jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí obsahovat také jeden koncový bod serveru, který představuje cestu na serveru Windows.
5. Soubory jsou teď udržovat synchronizované sdílené složky Azure a místním serverem.
6. V případě havárie v místním prostředí, proveďte převzetí služeb při selhání pomocí [plánu obnovení](site-recovery-create-recovery-plans.md). Přidat skript, který chcete [připojení sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a přístup do sdílené složky na virtuálním počítači.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Pomocí Site Recovery replikovat virtuální počítač IaaS soubor serveru

Pokud máte klienty na místě, které přistupují k virtuálním počítači IaaS souborového serveru, proveďte všechny následující kroky. V opačném případě přejděte ke kroku 3.

1. Vytvořit připojení VPN site-to-site mezi místní lokalitou a síť Azure.
2. Rozšiřte místní služby Active Directory.
3. [Nastavení zotavení po havárii](azure-to-azure-tutorial-enable-replication.md) pro počítači IaaS souborového serveru do sekundární oblasti.


Další informace o zotavení po havárii do sekundární oblasti, naleznete v tématu [v tomto článku](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Pomocí Site Recovery replikovat serveru v místním souboru

Následující kroky popisují replikace pro virtuální počítač VMware. Pokyny k replikaci virtuálních počítačů Hyper-V, najdete v článku [v tomto kurzu](tutorial-hyper-v-to-azure.md).

1. [Příprava prostředků Azure](tutorial-prepare-azure.md) pro replikaci místních počítačů.
2. Vytvořit připojení VPN site-to-site mezi místní lokalitou a síť Azure. 
3. Rozšiřte místní služby Active Directory.
4. [Příprava místních serverů VMware](tutorial-prepare-on-premises-vmware.md).
5. [Nastavení zotavení po havárii](tutorial-vmware-to-azure.md) do Azure pro místní virtuální počítače.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozšiřte DFSR na virtuálním počítači Azure IaaS

1. Vytvořit připojení VPN site-to-site mezi místní lokalitou a síť Azure. 
2. Rozšiřte místní služby Active Directory.
3. [Vytvoření a zřízení souborového serveru virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) ve službě Azure virtual network.
Ujistěte se, že je virtuální počítač přidat do stejné virtuální síť Azure, která má víc připojení se v místním prostředí. 
4. Instalace a [konfigurace DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) ve Windows serveru.
5. [Implementovat obor názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. S oborem názvů DFS implementované lze provést převzetí služeb při selhání sdílené složky v produkčním prostředí na servery pro zotavení po havárii prostřednictvím aktualizace cíle složky oboru názvů DFS. Po těchto DFS replikaci změn oboru názvů přes službu Active Directory, jsou uživatelé připojeni k cílům příslušné složky transparentně.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Pomocí File Sync replikovat místních souborů
Synchronizace souborů můžete použít k replikaci souborů do cloudu. V případě havárie a doba nedostupnosti s místními souborového serveru se můžete připojit umístění požadovaného souboru z cloudu a dál obsluhovat žádosti z klientských počítačů.
File Sync integrovat se Site Recovery:

* Ochrana počítače souborového serveru s využitím Site Recovery. Postupujte podle kroků v [v tomto kurzu](tutorial-vmware-to-azure.md).
* Používání File Sync replikovat soubory z počítače, který slouží jako souborový server do cloudu.
* Přidat skripty pro připojení sdílené složky Azure v převzetím služeb při selhání souborového serveru virtuálního počítače v Azure pomocí funkce plánu obnovení ve službě Site Recovery.

Použijte následující postup použijte File Sync:

1. [Vytvoření účtu úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste zvolili (doporučeno) pro vaše účty storage geograficky redundantní úložiště jen pro čtení, máte přístup pro čtení k datům ze sekundární oblasti v případě havárie. Další informace najdete v tématu [strategie zotavení po havárii sdílené složky Azure file](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvoření sdílené složky](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Nasazení služby File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) v s místními souborového serveru.
4. Vytvořte skupinu synchronizace. Koncové body v rámci skupiny synchronizace se udržovat synchronizované mezi sebou. Skupina synchronizace musí obsahovat aspoň jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí obsahovat také jeden koncový bod serveru, který představuje cestu na místní Windows server.
5. Soubory jsou teď udržovat synchronizované sdílené složky Azure a místním serverem.
6. V případě havárie v místním prostředí, proveďte převzetí služeb při selhání pomocí [plánu obnovení](site-recovery-create-recovery-plans.md). Přidáte skript pro připojení sdílené složky Azure a přístup do sdílené složky na virtuálním počítači.

> [!NOTE]
> Ujistěte se, že je otevřený port 445. Služba soubory Azure používá protokol SMB. Protokol SMB komunikuje přes TCP port 445. Zaškrtněte, pokud chcete zobrazit, pokud brána firewall neblokuje port TCP 445 z klientského počítače.


## <a name="do-a-test-failover"></a>Provést testovací převzetí služeb

1. Přejděte na webu Azure portal a vyberte váš trezor služby Recovery Services.
2. Vyberte plán obnovení pro prostředí serveru soubor vytvořen.
3. Vyberte **testovací převzetí služeb při selhání**.
4. Vyberte bod obnovení a Azure virtual network se spustit proces testovacího převzetí služeb při selhání.
5. Když sekundární prostředí se nahoru, provedete vaše ověření.
6. Po dokončení ověření vyberte **vyčištění testovacího převzetí služeb při selhání** v plánu obnovení a převzetí služeb při selhání testovací prostředí vyčištěno.

Další informace o tom, jak provést testovací převzetí služeb najdete v tématu [testovací převzetí služeb při selhání Site Recovery](site-recovery-test-failover-to-azure.md).

Pokyny k provádění testovacího převzetí služeb při selhání pro Active Directory a DNS, najdete v části [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory a DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Služeb při selhání

1. Přejděte na web Azure Portal a vyberte svůj trezor služby Recovery Services.
2. Vyberte plán obnovení pro prostředí serveru soubor vytvořen.
3. Vyberte **převzetí služeb při selhání**.
4. Vyberte bod obnovení, zahájíte proces převzetí služeb při selhání.

Další informace o tom, jak provést převzetí služeb, naleznete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).
