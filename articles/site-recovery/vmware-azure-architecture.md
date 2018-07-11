---
title: Z VMware do Azure replikace architektura v Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při replikaci místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 48adf61dc0f1796b820e1e14ca509d4618c6256b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920562"
---
# <a name="vmware-to-azure-replication-architecture"></a>Z VMware do Azure replikace architektury

Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovovat virtuální počítače VMware (VM) mezi místní lokality VMware a Azure s použitím [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a obrázek poskytuje souhrnný přehled komponenty používané pro replikaci VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a síť Azure. | Replikovaná data z místních virtuálních počítačů se ukládají v účtu úložiště. Virtuální počítače Azure se vytvoří s replikovanými daty při spuštění převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurace počítače serveru** | Jeden místní počítač. Doporučujeme vám, že jej spouštíte jako virtuální počítač VMware, který je možné nasadit z ve stažené šabloně OVF.<br/><br/> Počítači běží všechny místní komponenty Site Recovery, mezi které patří konfigurační server, procesový server a hlavní cílový server. | **Konfigurační server**: koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.<br/><br/> **Procesový server**: ve výchozím nastavení na konfiguračním serveru nainstalované. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do služby Azure Storage. Procesní server nainstaluje služba Mobility Azure Site Recovery na virtuálních počítačích, které chcete replikovat, a provádí automatické zjišťování místních počítačů. Jak vaše nasazení poroste, můžete přidat další, samostatné procesní servery pro zpracování větší objemy přenosů replikace.<br/><br/> **Hlavní cílový server**: ve výchozím nastavení na konfiguračním serveru nainstalované. Zpracovává replikační data během navrácení služeb po obnovení z Azure. Pro velká nasazení můžete přidat další, samostatný hlavní cílový server navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware jsou hostované na místních serverech vSphere ESXi. Doporučujeme, abyste server vCenter pro správu hostitele. | Během nasazování Site Recovery servery VMware přidáte do trezoru služby Recovery Services.
**Replikované počítače** | Služba mobility je nainstalovaná na každý virtuální počítač VMware, které replikujete. | Doporučujeme vám, že povolíte automatické instalace z procesového serveru. Alternativně můžete ručně nainstalovat službu nebo použít metody automatického nasazení, jako je System Center Configuration Manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Postup konfigurace

Obecných kroků pro nastavení VMware na zotavení po havárii Azure nebo migrace jsou následující:

1. **Nastavení Azure komponent**. Potřebujete účet Azure s příslušným oprávněním, účet úložiště Azure, službě Azure virtual network a trezor služby Recovery Services. [Další informace](tutorial-prepare-azure.md).
2. **Nastavit místní**. Mezi ně patří vytvoření účtu na serveru VMware tak, aby Site Recovery může automaticky zjistit virtuální počítače k replikaci, nastavíte účet, který je možné nainstalovat službu mobility na virtuální počítače, které chcete replikovat a ověření této servery VMware a virtuální počítače v souladu s požadavky. Také v případě potřeby můžete připravit pro připojení k těmto virtuálním počítačům Azure po převzetí služeb při selhání. Site Recovery replikuje data virtuálního počítače do účtu služby Azure storage a vytvoří virtuální počítače Azure s využitím dat při spuštění převzetí služeb při selhání do Azure. [Další informace](vmware-azure-tutorial-prepare-on-premises.md).
3. **Nastavení replikace**. Můžete zvolit, zda chcete replikovat. Nakonfigurujete zdrojové prostředí replikace nastavením VMware jednomu místnímu virtuálnímu počítači (konfigurační server), na kterém běží všechny místní komponenty Site Recovery, které potřebujete. Po dokončení instalace je zaregistrovat počítače konfigurace serveru v trezoru služby Recovery Services. Potom vyberte nastavení cíle. [Další informace](vmware-azure-tutorial.md).
4. **Vytvoření zásady replikace**. Vytvoříte zásady replikace, která určuje, jak by měla probíhat replikace. 
    - **Prahová hodnota cíle bodu obnovení**: Tato nastavení monitorování stavu, pokud replikace není v zadané době, výstrahu (a volitelně e-mailu) vydává. Například pokud nastavená prahová hodnota cíle bodu obnovení na 30 minut, a problém brání replikace provádění po dobu 30 minut, je vygenerována událost. Toto nastavení nemá vliv na replikaci. Průběžné replikace a body obnovení jsou vytvářeny každých pár minut
    - **Uchování**: uchovávání informací Určuje, jak dlouho se body obnovení bodu obnovení se uchovávají v Azure. Zadejte hodnotu mezi 0 a 24 hodin pro premium storage, nebo až 72 hodin pro úložiště úrovně standard. Můžete převzít služby do nejnovějšího bodu obnovení, nebo do uloženého bodu Pokud nastavíte hodnotu vyšší než nula. Po interval uchovávání dat jsou vymazány body obnovení.
    - **Snímky konzistentní při selhání**: ve výchozím nastavení, Site Recovery pořizuje snímky konzistentní při selhání a vytvoří body obnovení s nimi každých několik minut. Bod obnovení je konzistentní, pokud jsou všechny komponenty vzájemně propojených dat zápisu pořadí konzistentní s havárií, jako kdyby byly v okamžiku vytvoření bodu obnovení. Abyste lépe pochopili, imagine po výpadku napájení nebo podobná událost stav dat na pevném disku počítače. Bod obnovení konzistentní při selhání je obvykle stačí, pokud vaše aplikace je navržen pro zotavení z chyb bez nekonzistencím dat.
    - **Snímky konzistentní s**: Pokud tuto hodnotu není nula, služby Mobility spuštěné na virtuálním počítači pokusí vygenerovat snímky konzistentní vzhledem k systému souborů a body obnovení. Po dokončení počáteční replikace se pořídí první snímek. Potom snímky se pořídí na frekvenci, kterou zadáte. Vytvoření bodu obnovení je konzistentní s aplikací, pokud kromě toho, že pořadí zápisu žádosti konzistentní vzhledem k aplikacím, spuštěné dokončení všech operací a vyprázdní vyrovnávací paměti na disk (uvedení aplikace). Body obnovení konzistentní se doporučují pro databázové aplikace, jako je SQL, Oracle nebo Exchange. Pokud snímek konzistentní při selhání je dostačující, můžete tato hodnota nastavena na hodnotu 0.  
    - **Konzistence více virtuálních počítačů**: Volitelně můžete vytvořit replikační skupinu. Když povolíte replikaci, bude možné shromažďovat virtuálních počítačů do této skupiny. Virtuální počítače ve skupině replikace seskupit replikace a mít sdílené body obnovení konzistentní při selhání a konzistentní při převzetí služeb při selhání. Tuto možnost by měla používejte opatrně, protože může ovlivnit výkon úloh, jako třeba na víc počítačů se dají shromáždit snímky. Udělat jen tehdy, pokud virtuální počítače běží stejné zatížení a musí být konzistentní vzhledem k aplikacím a virtuální počítače mají podobné churns. Můžete přidat až 8 virtuálních počítačů do skupiny. 
5. **Povolení replikace virtuálních počítačů**. A konečně povolíte replikaci pro virtuální počítače VMware v místním. Pokud jste vytvořili účet, který chcete nainstalovat službu Mobility a zadat, že Site Recovery proveďte nabízenou instalaci, pak služby Mobility se nainstaluje na každý virtuální počítač, pro kterou můžete povolit replikaci. [Další informace](vmware-azure-tutorial.md#enable-replication). Pokud vytvoříte skupiny replikace pro zajištění konzistence více virtuálních počítačů, můžete přidat virtuální počítače do této skupiny.
6. **Testovací převzetí služeb při selhání**. Poté, co všechno je nastavené, můžete provést testovací převzetí služeb ke kontrole, jestli virtuální počítače převzetí služeb při selhání do Azure podle očekávání. [Další informace](tutorial-dr-drill-azure.md).
7. **Převzetí služeb při selhání**. Pokud právě migrace virtuálních počítačů do Azure – spuštění úplné převzetí služeb při selhání, který chcete. Pokud při nastavování zotavení po havárii, můžete spustit úplné převzetí služeb při selhání pro potřeby. Pro úplné havárii můžete po převzetí služeb při selhání do Azure, předat zpět do vaší místní lokalitě jako a je k dispozici. [Další informace](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začne replikovat v souladu se zásadami replikace. 
2. Provoz replikuje do služby Azure storage veřejné koncové body přes internet. Alternativně můžete použít Azure ExpressRoute s [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Přenos replikačních dat přes virtuální privátní síť site-to-site (VPN) z místní lokality do Azure se nepodporuje.
3. Počáteční kopie dat virtuálního počítače se replikují do úložiště Azure.
4. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
5. Komunikace se stane, následujícím způsobem:

    - Virtuální počítače komunikovat s místní konfigurační server na portu HTTPS 443 příchozí kvůli správě replikace.
    - Konfigurační server orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Příchozí data replikace k procesového serveru (běžícího na počítači serveru configuration) na příchozím portu HTTPS 9443 poslat virtuálních počítačů. Tento port je možné upravit.
    - Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.


**Z VMware do Azure replikaci**

![Proces replikace](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění postupu zotavení po havárii (testovací převzetí služeb) zkontrolujte, že vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení pro potřeby.

1. Spustit selhání pro jeden počítač nebo vytvořit plány na převzetí služeb při selhání několika virtuálních počítačů ve stejnou dobu obnovení. Výhodou plánu obnovení spíše než převzetí služeb při selhání jednoho počítače patří:
    - Závislosti aplikace lze modelovat zahrnutím všech virtuálních počítačů napříč aplikace v jednom plánu obnovení.
    - Můžete přidat skripty, runbooky v Azure a pozastavit pro ruční akce.
2. Po aktivaci počáteční převzetí služeb při selhání, potvrdíte ho začít používat úlohu z virtuálního počítače Azure.
3. Pokud vaše místní lokalita opět dostupná, můžete připravit pro navrácení služeb po obnovení. Aby bylo možné po navrácení služeb po obnovení, budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:

    * **Dočasný procesní server v Azure**: selhání obnovení z Azure, můžete nastavit virtuální počítač Azure tak, aby fungoval jako procesový server pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení k síti VPN**: K navrácení služeb po obnovení, potřebujete připojení VPN (nebo ExpressRoute) ze sítě Azure k místní lokalitě.
    * **Samostatný hlavní cílový server**: ve výchozím nastavení, hlavní cílový server, který se nainstaloval s konfiguračním serverem na virtuálních počítačů VMware v místním zpracovává navrácení služeb po obnovení. Pokud potřebujete selhání zpět velký objem provozu, nastavte samostatný místní hlavní cílový server pro tento účel.
    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada byla vytvořena automaticky při vytvoření zásad replikace z místního do Azure.
4. Po součásti jsou na místě, dojde k navrácení služeb po obnovení v tři akce:

    - Fáze 1: Znovunastavení ochrany virtuálních počítačů Azure tak, aby se replikace z Azure zpět do místních virtuálních počítačů VMware.
    -  Fáze 2: Spuštění převzetí služeb při selhání do místní lokality.
    - Fáze 3: Po úloh se nepodařilo vrátit, povolíte replikaci místních virtuálních počítačů.
    
 
**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](vmware-azure-tutorial.md) k povolení z VMware do Azure replikace.
