---
title: Instalace SAP HANA na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Průvodce instalací SAP HANA na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123349"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalace SAP HANA na virtuálních počítačích Azure
## <a name="introduction"></a>Úvod
Tato příručka vám pomůže přejděte na správné prostředky k úspěšnému nasazení HANA ve virtuálních počítačích Azure. Tato příručka vás přejde na prostředky dokumentace, které je třeba zkontrolovat před instalací SAP HANA do virtuálního počítače Azure. Takže můžete provést správné kroky k ukončení s podporovanou konfigurací SAP HANA ve virtuálních počítačích Azure.  

> [!NOTE]
> Tato příručka popisuje nasazení SAP HANA do virtuálních počítačů Azure. Informace o tom, jak nasadit SAP HANA do velkých instancí HANA, najdete v [tématu Jak nainstalovat a nakonfigurovat SAP HANA (velké instance) v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Požadavky
Tato příručka také předpokládá, že jste obeznámeni s:
* SAP HANA a SAP NetWeaver a jak je nainstalovat místně.
* Jak nainstalovat a provozovat instance aplikací SAP HANA a SAP v Azure.
* Pojmy a postupy zdokumentované v:
   * Plánování nasazení SAP v Azure, které zahrnuje plánování virtuální sítě Azure a využití úložiště Azure. Viz [SAP NetWeaver na virtuálních počítačích Azure – průvodce plánováním a implementací](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Principy nasazení a způsoby nasazení virtuálních počítačů v Azure. Viz [nasazení virtuálních počítačů Azure pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Koncepty vysoké dostupnosti pro SAP HANA, jak je zdokumentováno v [SAP HANA vysoká dostupnost pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Krok za krokem před nasazením
V této části jsou uvedeny různé kroky, které je třeba provést před zahájením instalace SAP HANA ve virtuálním počítači Azure. Pořadí je vyjmenována a jako takové by měly být dodrženy prostřednictvím ve výčtu:

1. V Azure nejsou podporovány všechny možné scénáře nasazení. Proto byste měli zkontrolovat úlohy dokumentu [SAP na scénářích podporovaných virtuálním počítačem Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) pro scénář, který máte na mysli s nasazením SAP HANA. Pokud scénář není uveden, je třeba předpokládat, že nebyl testován a v důsledku toho není podporována
2. Za předpokladu, že máte hrubou představu o požadavcích na paměť pro nasazení SAP HANA, musíte najít vhodný virtuální počítač Azure. Ne všechny virtuální uživatele, které jsou certifikované pro SAP NetWeaver, jak je popsáno v [poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533), jsou certifikovány SAP HANA. Zdrojem pravdy pro virtuální počítače Azure s certifikací SAP HANA je adresář [hardwaru SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Jednotky začínající s **jsou** [jednotky velkých instancí HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a ne virtuální počítače Azure.
3. Různé typy virtuálních počítačů Azure mají různé minimální verze operačního systému pro SUSE Linux nebo Red Hat Linux. Na webových stránkách [sap hana hardwarového adresáře](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), musíte kliknout na položku v seznamu sap hana certifikovaných jednotek získat podrobná data této jednotky. Kromě podporované hořce HANA jsou uvedeny verze operačního systému, které jsou podporovány s těmito jednotkami pro SAP HANA.
4. Od vydání operačního systému je třeba zvážit určité minimální verze jádra. Tyto minimální verze jsou popsány v těchto poznámkách podpory SAP:
    - [Poznámka podpory SAP #2814271 zálohování SAP HANA se nezdaří v Azure s chybou kontrolního součtu](https://launchpad.support.sap.com/#/notes/2814271)
    - [Poznámka podpory SAP #2753418 potenciální snížení výkonu z důvodu záložního časovače](https://launchpad.support.sap.com/#/notes/2753418)
    - [Poznámka podpory SAP #2791572 snížení výkonu z důvodu chybějící podpory VDSO pro technologie Hyper-V v Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Na základě verze operačního systému, která je podporována pro typ virtuálního počítače volby, je třeba zkontrolovat, zda požadovaná verze SAP HANA je podporována s vydáním operačního systému. Přečtěte si [poznámku podpory SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) pro matici podpory verzí SAP HANA s různými verzemi operačního systému.
5. Vzhledem k tomu, že jste možná našli platnou kombinaci typu virtuálního počítače Azure, vydání operačního systému a verze SAP HANA, je třeba zkontrolovat matici dostupnosti produktu SAP. V matici dostupnosti SAP můžete zjistit, zda je podporován produkt SAP, který chcete spustit proti databázi SAP HANA.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Podrobné informace o nasazení virtuálních počítače a o hostování
V této fázi je třeba projít kroky nasazení virtuálních počítačů k instalaci HANA a nakonec optimalizovat zvolený operační systém po instalaci.

1. V galerii Azure vyberte základní bitovou kopii. Pokud chcete vytvořit vlastní image operačního systému pro SAP HANA, musíte znát všechny různé balíčky, které jsou nezbytné pro úspěšnou instalaci SAP HANA. V opačném případě se doporučuje používat suse a red hat image pro SAP nebo SAP HANA z galerie obrázků Azure. Tyto bitové kopie obsahují balíčky nezbytné pro úspěšnou instalaci HANA. Na základě smlouvy o podpoře s poskytovatelem operačního systému musíte zvolit bitovou kopii, ve které si přinesete vlastní licenci. Nebo zvolíte obrázek operačního systému, který obsahuje podporu
2. Pokud jste si vybrali obrázek hostovaného operačního systému, který vyžaduje, abyste si přinesli vlastní licenci, musíte zaregistrovat bitovou kopii operačního systému s předplatným, abyste si mohli stáhnout a použít nejnovější opravy. Tento krok bude vyžadovat přístup k veřejnému internetu. Pokud nenastavíte privátní instanci, například server SMT v Azure.
3. Rozhodněte o konfiguraci sítě virtuálního počítače. Další informace si můžete přečíst v dokumentu [KONFIGURACE A Provoz infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Nezapomeňte, že neexistují žádné kvóty propustnost sítě, které můžete přiřadit virtuálním síťovým kartám v Azure. V důsledku toho je jediným účelem řízení provozu prostřednictvím různých virtuálních měn založen na aspektech zabezpečení. Věříme, že najdete podpůrný kompromis mezi složitostí směrování provozu prostřednictvím více virtuálních vnek a požadavky vynucenými bezpečnostními aspekty.
3. Po nasazení a registraci virtuálního virtuálního montovana použijte nejnovější opravy v operačním systému. Registrováno buď s vlastním předplatným. Nebo v případě, že jste si vybrali image, která zahrnuje podporu operačního systému, virtuální ho virtuální ho k dispozici už by měl mít přístup k opravám. 
4. Použijte melodie potřebné pro SAP HANA. Tyto melodie jsou uvedeny v těchto poznámkách podpory SAP:

    - [Poznámka podpory SAP #2694118 – doplněk Red Hat Enterprise Linux HA HA v Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Poznámka podpory SAP #1984787 - SUSE LINUX Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Poznámka podpory SAP #2578899 - SUSE Linux Enterprise Server 15: Poznámka k instalaci](https://launchpad.support.sap.com/#/notes/2578899)
    - [Poznámka podpory SAP #2002167 – Red Hat Enterprise Linux 7.x: Instalace a upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Poznámka podpory SAP #2292690 - SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Poznámka podpory SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalace a konfigurace](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Poznámka podpory SAP #2777782 - SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Poznámka podpory SAP #2455582 - Linux: Spuštění sap aplikací sestavených s GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Poznámka podpory SAP #2382421 – optimalizace konfigurace sítě na úrovni HANA a OS](https://launchpad.support.sap.com/#/notes/2382421)

1. Vyberte typ úložiště Azure pro SAP HANA. V tomto kroku se musíte rozhodnout o rozložení úložiště pro instalaci SAP HANA. Budete používat připojené disky Azure nebo nativní sdílené složky služby Azure NFS. Typy úložiště Azure, které nebo podporované a kombinace různých typů úložišť Azure, které lze použít, jsou dokumentovány v [konfiguracích úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Vezměte konfigurace zdokumentované jako výchozí bod. Pro neprodukční systémy může být možné nakonfigurovat nižší propustnost nebo viposlužby. Pro produkční účely může být nutné nakonfigurovat trochu větší propustnost a viposlužby.
2. Ujistěte se, že jste nakonfigurovali [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) pro vaše svazky, které obsahují protokoly transakcí DBMS nebo protokoly opakování při použití m-series nebo Mv2-Series virtuálnípočítače. Uvědomte si omezení pro akcelerátor zápisu, jak je zdokumentováno.
2. Zkontrolujte, jestli je na nasazených virtuálních počítačích povolené azure [accelerated networking.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)

> [!NOTE]
> Ne všechny příkazy v různých profilech sap-tune nebo jak je popsáno v poznámkách může úspěšně spustit v Azure. Příkazy, které by manipulovat režim napájení virtuálních počítačů obvykle vrátí s chybou, protože režim napájení základního hostitelského hardwaru Azure nelze manipulovat.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Podrobné přípravy specifické pro virtuální počítače Azure
Jedním z specifik Azure je instalace rozšíření virtuálního počítače Azure, které poskytuje data monitorování pro hostitele SAP agenta. Podrobnosti o instalaci tohoto rozšíření monitorování jsou popsány v:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) popisuje rozšířené monitorování SAP s virtuálními počítači SIP v Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) popisuje informace o SAPOSCOL na Linuxu 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) popisuje klíčové metriky monitorování pro SAP v Microsoft Azure
-  [Nasazení virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalace SAP HANA
S nasazenými virtuálními počítači Azure a registrovanými a nakonfigurovanými operačními systémy můžete SAP HANA nainstalovat podle instalace SAP. Jako dobrý začátek, jak se dostat k této dokumentaci, začněte s těmito zdroji WEBOVÝCH STRÁNEK [SAP HANA](https://www.sap.com/products/hana/implementation/resources.html)

Pro konfigurace SAP HANA horizontálnínavýšení kapacity pomocí přímo připojených disků Azure Premium Storage nebo Ultra disk, přečtěte si specifika v dokumentu [SAP HANA konfigurace infrastruktury a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Další prostředky pro zálohování SAP HANA
Informace o tom, jak zálohovat databáze SAP HANA na virtuálních počítačích Azure, najdete v tématu:
* [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Zálohování SAP HANA Azure na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Další kroky
Přečtěte si dokumentaci:

- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





