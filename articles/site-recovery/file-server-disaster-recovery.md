---
title: "Chránit souborový server s využitím Azure Site Recovery"
description: "Tento článek popisuje, jak chránit souborový server s využitím Azure Site Recovery"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: f53a8641a50a6c968a6ba7b841e0e8f938b5d9f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Chránit souborový server s využitím Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místní počítače a virtuální počítače Azure (VM). Zotavení po havárii zahrnuje replikace, převzetí služeb při selhání a obnovení úlohy.

Tento článek popisuje, jak chránit souborový server pomocí Site Recovery a umožňuje dalších doporučeních podle různých prostředích. 

- [Replikovat počítače Azure IaaS souborového serveru](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikovat pomocí Site Recovery pro místní souborový server](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura serveru souboru
Cílem otevřete distribuované sdílení souborů systému je a poskytuje prostředí, kde mohou spolupracovat na skupinu uživatelů geograficky rozptýlené efektivní práci se soubory a zaručit, aby se vynucují jejich integritu požadavků. Typické místní server zahrnující soubor podporující velký počet souběžných uživatelů a velké množství obsahu položek distribuované replikace systému souborů (DFSR) používá replikace plánování a omezování šířky pásma. 

DFSR používá algoritmus komprese označovaný jako Remote Differential Compression (RDC), který slouží k efektivní aktualizaci souborů přes síť omezenou šířkou pásma. Zjistí vkládání, odstraňování a změnu uspořádání dat v souborech. DFSR je povolený a replikuje pouze změněný soubor bloky, pokud jsou aktualizovány soubory. Existují také soubor serverových prostředích, kde jsou denní zálohy prováděné v časování mimo špičku, které slouží různým potřebovat po havárii. DFSR není implementována.

Následující diagram znázorňuje prostředí souborového serveru s DFSR implementována.
                
![Architektura DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

Na předchozím obrázku několik souborových serverů, které volá členy aktivně účastnit replikaci souborů mezi replikační skupiny. Obsah v replikované složce je k dispozici pro všechny klienty, kteří odesílají žádosti na jednu z členů, i když členem přejde do režimu offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Doporučení pro zotavení po havárii pro souborové servery

* **Souborový server replikovat pomocí Site Recovery**: souborové servery je možné replikovat do Azure pomocí Site Recovery. Pokud jeden nebo víc souborových serverů na místě jsou nedostupné, můžete být zapínají obnovení virtuálních počítačů v Azure. Virtuální počítače můžete pak obsluhovat požadavky od klientů, místní, existuje-li je připojení site-to-site VPN a služby Active Directory je konfigurována v Azure. Tuto metodu v případě prostředí nakonfigurované DFSR nebo v prostředí serverů jednoduchého souboru můžete použít s žádné DFSR. 

* **Rozšíření virtuálního počítače Azure IaaS DFSR**: V prostředí clusterového souborového serveru s DFSR implementována, můžete rozšířit místní DFSR Azure. Virtuální počítač Azure je pak povoleno provádět roli souborového serveru. 

    * Po zpracování závislostí připojení site-to-site VPN a služby Active Directory a DFSR je na místě, když jeden nebo víc souborových serverů na místě jsou nedostupné, klienti mohou připojit k virtuálnímu počítači Azure, která obsluhuje žádosti.

    * Tento postup můžete použít, pokud vaše virtuální počítače konfiguracemi, které nejsou podporovány službou Site Recovery. Příkladem je disk sdíleného clusteru, někdy běžně používaný v serverových prostředích souboru. DFSR také dobře funguje v prostředí s malou šířkou pásma s střední klidové vytížení. Je potřeba zvážit další náklady na virtuální počítač Azure s a všechny doby spuštění. 

* **Používat synchronizaci souboru Azure replikovat vaše soubory**: Pokud budete chtít použít cloud nebo již použít virtuální počítač Azure, můžete použít soubor synchronizaci. Synchronizace souboru nabízí synchronizuje plně spravované sdílené složky v cloudu, které jsou přístupné přes oborových standardů [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokol (SMB). Sdílené složky Azure pak může být připojen současně podle cloudu nebo na místní nasazení systému Windows, Linux a systému macOS. 

Následující diagram vám pomůže určit, jaká strategie pro vaše serverové prostředí a souboru.

![rozhodovací strom.](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktory vzít v úvahu při rozhodování o o zotavení po havárii do Azure

|Prostředí  |Doporučení  |Body, které je třeba zvážit |
|---------|---------|---------|
|Prostředí serveru soubor s nebo bez DFSR|   [Pomocí Site Recovery pro replikaci](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery nepodporuje sdílené diskové clustery nebo úložiště připojené k síti (NAS). Pokud vaše prostředí používá tyto konfigurace, používejte kterýkoli z jiné postupy, podle potřeby. <br> Site Recovery nepodporuje protokol SMB 3.0. Replikovaný virtuální počítač obsahuje změny, pouze v případě změny provedené v souborech se aktualizuje na původním umístění souborů.
|Prostředí serveru soubor s DFSR     |  [Rozšíření DFSR do virtuálního počítače Azure IaaS](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR dobře funguje v prostředí velmi crunched šířky pásma. Tento přístup vyžaduje virtuální počítač Azure, který je v provozu a všechny doby spuštění. Potřebujete účet při plánování nákladů na virtuální počítač.         |
|Virtuálních počítačů Azure IaaS     |     [Synchronizace souboru ](#use-azure-file-sync-service-to-replicate-your-files)   |     Pokud použijete soubor synchronizaci ve scénáři zotavení po havárii, vyžaduje se během převzetí služeb při selhání ručně prováděné akce a ujistěte se, že sdílené složky jsou přístupné pro klientský počítač transparentní způsobem. Soubor Sync vyžaduje port 445 otevřen v klientském počítači.     |


### <a name="site-recovery-support"></a>Podpora pro obnovení lokality
Protože replikace Site Recovery je nezávislá na aplikace, budou tato doporučení platí pro následující scénáře.
| Zdroj    |Sekundární lokality    |To Azure
|---------|---------|---------|
|Azure| -|Ano|
|Hyper-V|   Ano |Ano
|VMware |Ano|   Ano
|Fyzický server|   Ano |Ano
 

> [!IMPORTANT]
> Než budete pokračovat s žádným z následujících tří postupů, ujistěte se, že tyto závislosti bylo postaráno.

**Připojení Site-to-site**: přímé připojení mezi místními servery a síť Azure, musí vytvořit a povolit komunikaci mezi servery. Použití zabezpečeného připojení site-to-site VPN Azure virtuální sítě, který se používá jako lokality pro obnovení po havárii. Další informace najdete v tématu [vytvořit připojení site-to-site VPN mezi místními servery a virtuální síť Azure,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Služby Active Directory**: DFSR závisí na službě Active Directory. To znamená, že doménová struktura služby Active Directory s řadiče místní domény je rozšířeno na web pro zotavení po havárii v Azure. I když nepoužíváte DFSR, pokud potřebujete příslušným uživatelům udělen přístup nebo ověřit přístup, musíte provést tyto kroky. Další informace najdete v tématu [rozšíření místní služby Active Directory do Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Doporučení pro zotavení po havárii pro virtuální počítače Azure IaaS

Pokud konfigurujete a spravovat zotavení po havárii souborových serverů, které jsou hostované na virtuální počítače Azure IaaS, můžete si vybrat mezi dvě možnosti, v závislosti na tom, jestli chcete přesunout do [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Používat synchronizaci souborů](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Pomocí Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Používat synchronizaci souborů k replikaci souborů, které jsou hostované na virtuálním počítači IaaS

Službu Soubory Azure je možné použít k úplnému nahrazení nebo doplnění tradičních místních souborových serverů nebo zařízení NAS. Sdílené složky Azure můžete také replikují se synchronizací souboru na serverech Windows, buď místní nebo v cloudu, výkonu a distribuované ukládání do mezipaměti dat, kde se používá. Následující kroky popisují doporučení pro zotavení po havárii pro virtuální počítače Azure, které provádějí stejné funkce jako tradiční souborových serverů:
* Ochrana počítačů pomocí Site Recovery. Postupujte podle kroků v [replikovat virtuální počítač Azure jiné oblasti Azure](azure-to-azure-quickstart.md).
* Použijte synchronizaci souborů replikovat soubory z virtuálního počítače, který funguje jako souborový server do cloudu.
* Pomocí Site Recovery [plán obnovení](site-recovery-create-recovery-plans.md) funkci přidat skripty, které [připojit sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a přístup do sdílené složky ve virtuálním počítači.

Následující kroky stručně popisují, jak můžete používat synchronizaci souborů:

1. [Vytvořit účet úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste zvolili geograficky redundantní úložiště s přístupem pro čtení pro účty úložiště, získáte přístup pro čtení k datům ze sekundární oblasti v případě havárie. Další informace najdete v tématu [strategie obnovení po havárii sdílenou složku Azure file](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvoření sdílené složky](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Spustit synchronizaci souborů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) na Azure souborovém serveru.
4. Vytvořte skupinu synchronizace. Koncové body v rámci synchronizace skupiny jsou synchronizovány mezi sebou. Synchronizace skupiny musí obsahovat alespoň jeden koncového bodu cloudu, která představuje sdílenou složku Azure. Synchronizace skupiny také musí obsahovat jeden koncový bod serveru, který představuje cestu na serveru systému Windows.
5. Soubory jsou teď byly synchronizovány ve Azure sdílené složky a místní server.
6. V případě havárie v místním prostředí, proveďte převzetí služeb při selhání pomocí [plán obnovení](site-recovery-create-recovery-plans.md). Přidejte skript, který chcete [připojit sdílenou složkou Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a přístup do sdílené složky ve virtuálním počítači.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Pomocí Site Recovery replikovat virtuální počítač serveru soubor IaaS

Pokud máte místní klienty, kteří přistupují virtuální počítač IaaS souborového serveru, proveďte následující kroky. Jinak přejděte ke kroku 3.

1. Vytvořit připojení site-to-site VPN mezi místními servery a síť Azure.
2. Místní službu Active Directory rozšiřte.
3. [Nastavit zotavení po havárii](azure-to-azure-tutorial-enable-replication.md) pro k počítači souborového serveru IaaS v sekundární oblasti.


Další informace o zotavení po havárii v sekundární oblasti najdete v tématu [v tomto článku](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikovat pomocí Site Recovery pro místní souborový server

Následující kroky popisují replikace pro virtuální počítače VMware. Pokyny k replikaci virtuálního počítače technologie Hyper-V, najdete v části [v tomto kurzu](tutorial-hyper-v-to-azure.md).

1. [Příprava prostředků Azure](tutorial-prepare-azure.md) pro replikaci počítačů na místě.
2. Vytvořit připojení site-to-site VPN mezi místními servery a síť Azure. 
3. Místní službu Active Directory rozšiřte.
4. [Příprava na místní servery VMware](tutorial-prepare-on-premises-vmware.md).
5. [Nastavit zotavení po havárii](tutorial-vmware-to-azure.md) do Azure pro virtuální počítače na místě.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozšíření DFSR do virtuálního počítače Azure IaaS

1. Vytvořit připojení site-to-site VPN mezi místními servery a síť Azure. 
2. Místní službu Active Directory rozšiřte.
3. [Vytvořit a zřídit souborový server virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) na virtuální síť Azure.
Ujistěte se, že virtuální počítač je přidat do stejné virtuální síť Azure, který má připojení mezi místní prostředí. 
4. Instalace a [konfigurace DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) v systému Windows Server.
5. [Implementace oboru názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. S oborem názvů DFS implementována lze provést převzetí služeb při selhání sdílených složek z výroby do lokalit pro zotavení po havárii aktualizací cíle složky oboru názvů DFS. Po tyto DFS replikovat změny oboru názvů prostřednictvím služby Active Directory, jsou uživatelé připojeni k cílům příslušné složky transparentně.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Používat synchronizaci souborů replikovat vaše soubory místně
Synchronizace souborů můžete použít k replikaci souborů do cloudu. V případě havárie a nedostupnost váš místní souborový server můžete připojit umístění požadované souborů z cloudu a pokračovat se žádostí o službu z klientských počítačů.
K integraci synchronizaci souborů pomocí Site Recovery:

* Chraňte počítače souborového serveru pomocí Site Recovery. Postupujte podle kroků v [v tomto kurzu](tutorial-vmware-to-azure.md).
* Použijte synchronizaci souborů k replikaci souborů z počítače, který slouží jako souborový server do cloudu.
* Použijte funkci plán obnovení ve službě Site Recovery přidat skripty připojit sdílenou složkou Azure na při selhání souborového serveru virtuálního počítače v Azure.

Postupujte podle těchto kroků můžete používat synchronizaci souborů:

1. [Vytvořit účet úložiště v Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud jste zvolili přístup pro čtení geograficky redundantní úložiště (doporučeno) pro účty úložiště, máte přístup pro čtení k datům ze sekundární oblasti v případě havárie. Další informace najdete v tématu [strategie obnovení po havárii sdílenou složku Azure file](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Vytvoření sdílené složky](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Nasadit soubor synchronizaci](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) ve vaší místní souborový server.
4. Vytvořte skupinu synchronizace. Koncové body v rámci synchronizace skupiny jsou synchronizovány mezi sebou. Synchronizace skupiny musí obsahovat alespoň jeden koncového bodu cloudu, která představuje sdílenou složku Azure. Synchronizace skupiny také musí obsahovat jeden koncový bod serveru, který představuje cestu v místním systému Windows server.
5. Soubory jsou teď byly synchronizovány ve Azure sdílené složky a místní server.
6. V případě havárie v místním prostředí, proveďte převzetí služeb při selhání pomocí [plán obnovení](site-recovery-create-recovery-plans.md). Přidejte skript, který chcete připojit sdílenou složkou Azure a přístup do sdílené složky ve virtuálním počítači.

> [!NOTE]
> Ujistěte se, že je otevřený port 445. Soubory Azure používá protokol SMB. SMB komunikuje přes TCP port 445. Zkontrolujte, pokud brána firewall neblokuje port TCP 445 z klientského počítače.


## <a name="do-a-test-failover"></a>Provést testovací převzetí služeb

1. Přejděte na portál Azure a vyberte trezoru služby zotavení.
2. Vyberte plán obnovení, které jsou vytvořené pro prostředí souboru serveru.
3. Vyberte **testovací převzetí služeb při selhání**.
4. Vyberte bod obnovení a virtuální síť Azure ke spuštění procesu testovací převzetí služeb při selhání.
5. Po sekundární prostředí zapnutý, proveďte vaše ověření.
6. Po dokončení se k ověření, vyberte **vyčistit testovací převzetí služeb při selhání** vyčištění v plánu obnovení a převzetí služeb při selhání testovací prostředí.

Další informace o tom, jak provést testovací převzetí služeb najdete v tématu [testovací převzetí služeb při selhání Site Recovery](site-recovery-test-failover-to-azure.md).

Pokyny k provádění převzetí služeb při selhání pro Active Directory a DNS, najdete v části [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory a DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Selhání

1. Přejděte na portál Azure a vyberte svůj trezor služeb zotavení.
2. Vyberte plán obnovení, které jsou vytvořené pro prostředí souboru serveru.
3. Vyberte **převzetí služeb při selhání**.
4. Vyberte bod obnovení se spustit proces převzetí služeb při selhání.

Další informace o tom, jak provést převzetí služeb při selhání najdete v tématu [převzetí služeb při selhání ve službě Site Recovery](site-recovery-failover.md).
