---
title: VMware do Azure replikace architektury v Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje přehled součásti a architektura použít při replikaci místní virtuální počítače VMware do Azure s Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30184577"
---
# <a name="vmware-to-azure-replication-architecture"></a>Z VMware do Azure replikace architektura

Tento článek popisuje architektuře a procesech používá při replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů VMware (VM) mezi místními servery VMware a Azure pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Komponenty architektury

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty používané pro replikaci VMware do Azure.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure, účet úložiště Azure a sítě Azure. | Replikovaná data z místně virtuálních počítačů je uložený v účtu úložiště. Virtuální počítače Azure vytvořené mají replikovaných dat při spuštění převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření.
**Konfigurace počítače serveru** | Jeden na místním počítači. Doporučujeme, abyste spustili ho jako virtuální počítače VMware, které se dá nasadit z ve stažené šabloně OVF.<br/><br/> Počítač se spustí všechny součásti Site Recovery na místě, například konfigurační server, procesový server a hlavní cílový server. | **Konfigurační server**: koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.<br/><br/> **Procesový server**: nainstalované ve výchozím nastavení na konfiguračním serveru. Obdrží data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování; a odešle ji do úložiště Azure. Procesový server nainstaluje taky službu Azure Site Recovery Mobility na virtuálních počítačích, které chcete replikovat, a provádí automatického zjišťování počítačů místně. S růstem nasazení můžete přidat další, samostatný proces serverů, aby zvládla větší objemy přenosů replikace.<br/><br/> **Hlavní cílový server**: nainstalované ve výchozím nastavení na konfiguračním serveru. Zpracovává replikační data během navrácení služeb po obnovení z Azure. Nasazení ve velkých organizacích můžete přidat další, samostatné hlavní cílový server navrácení služeb po obnovení.
**Servery VMware** | Virtuální počítače VMware jsou hostovány na místních vSphere ESXi serverů. Doporučujeme, abyste server vCenter pro správu hostitele. | Během nasazování Site Recovery přidat servery VMware do trezoru služeb zotavení.
**Replikované počítače** | Služba mobility je nainstalovaná na jednotlivé virtuální počítače VMware, kterého budete replikovat. | Doporučujeme vám, že povolíte automatické instalace z procesového serveru. Alternativně můžete nainstalovat službu ručně nebo použít metodu automatického nasazení, jako je například System Center Configuration Manager.

**Architektura replikace z VMware do Azure**

![Komponenty](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Kroky konfigurace

Obecné kroky pro nastavení VMware pro zotavení po havárii Azure nebo migrace jsou následující:

1. **Nastavení Azure součásti**. Budete potřebovat účet s potřebnými oprávněními Azure, účet úložiště Azure, virtuální sítě Azure a trezoru služeb zotavení. [Další informace](tutorial-prepare-azure.md).
2. **Nastavit místní**. Jedná se o vytvoření účtu na serveru VMware server tak, aby Site Recovery může automaticky zjistit virtuální počítače k replikaci, nastavení účtu, který lze použít k instalaci součásti služby Mobility na virtuálních počítačích, které chcete replikovat a ověření, že servery VMware a virtuální počítače v souladu s požadavky. Volitelně můžete připravit pro připojení k tyto virtuální počítače Azure po převzetí služeb při selhání. Obnovení lokality replikují data virtuálního počítače k účtu úložiště Azure a vytvoří virtuální počítače Azure pomocí dat při spuštění převzetí služeb při selhání do Azure. [Další informace](vmware-azure-tutorial-prepare-on-premises.md).
3. **Nastavení replikace**. Vybrat místo chcete replikovat. Můžete nakonfigurovat prostředí replikace zdroje podle nastavení VMware jeden místní virtuální počítač (konfigurační server), který spouští všechny místní součásti Site Recovery, které potřebujete. Po dokončení instalace je počítač serveru konfigurace zaregistrovat v trezoru služeb zotavení. Potom vyberte nastavení cíle. [Další informace](vmware-azure-tutorial.md).
4. **Vytvořte zásadu replikace**. Můžete vytvořit zásadu replikace, která určuje, jak by měla replikace provedena. 
    - **Prahová hodnota RPO**: Tato nastavení monitorování stavy, pokud replikace neprobíhá v zadané době, výstrahu (a volitelně e-mailu) se objeví. Například pokud nastavíte prahovou hodnotu RPO na 30 minut, a problém brání replikace nedocházelo 30 minut, je vygenerována událost. Toto nastavení nemá vliv replikace. Replikace je souvislý a body obnovení jsou vytvářeny každých několik minut
    - **Uchování**: bod obnovení uchování Určuje, jak dlouho body obnovení, by měly být udržovány v Azure. Zadejte hodnotu mezi 0 a 24 hodin pro storage úrovně premium, nebo až 72 hodin pro standardní úložiště. Pokud nastavíte hodnotu vyšší než nula můžete převzetí služeb při selhání do nejnovějšího bodu obnovení, nebo uloženého bodu. Po interval uchovávání dat jsou vymazány body obnovení.
    - **Snímky konzistentní při selhání**: ve výchozím nastavení, Site Recovery trvá snímky konzistentní při selhání a vytvoří body obnovení s nimi každých několik minut. Bod obnovení je konzistentní, pokud jsou všechny komponenty vzájemně související data konzistentní zápisu pořadí havárie, jako byly v okamžiku vytvoření bodu obnovení. Abyste lépe pochopili, představte si stav dat na pevném disku počítače po výpadku napájení nebo podobné události. Bod obnovení konzistentní při selhání je obvykle stačí, když vaše aplikace je obnovit z havárie bez nekonzistencím data.
    - **Snímky konzistentní aplikace**: není-li tuto hodnotu nula, pokusí se služba Mobility na virtuální počítač spuštěna generovat snímky konzistentní vzhledem k systému souborů a body obnovení. Pořízení snímku první po dokončení počáteční replikace. Potom pořizování snímků frekvencí, které zadáte. Bod obnovení je konzistentní s aplikací, pokud nejen zápisu pořadí konzistentní a spuštěné aplikace dokončit všechny činnosti a vyprázdní vyrovnávací paměti na disk (uvedení aplikace). Body obnovení konzistentní se doporučují pro databázové aplikace jako SQL, Oracle a Exchange. Pokud je dostatečná snímku konzistentní při selhání, můžete tato hodnota nastavena na hodnotu 0.  
    - **Konzistence pro víc Virtuálních**: Volitelně můžete vytvořit skupiny replikace. Když povolíte replikaci, je možné shromažďovat virtuálních počítačů do této skupiny. Virtuální počítače v replikační skupiny replikovat společně a mít sdílené body obnovení konzistentní při selhání a konzistentní při převzetí služeb při selhání. Tato možnost by měla použít pečlivě, protože může ovlivnit výkon zatížení jako snímky potřeba shromáždit napříč více počítačů. Uděláte to jenom, pokud virtuální počítače používají stejné úlohy a musí být konzistentní a virtuální počítače mají podobné konví. Až 8 virtuálních počítačů můžete přidat do skupiny. 
5. **Povolení replikace virtuálních počítačů**. Nakonec povolíte replikaci pro virtuální počítače VMware na místě. Pokud jste vytvořili účet pro instalaci služby Mobility a zadat, že by měl Site Recovery provádět nabízenou instalaci, bude služba Mobility nainstalována na každý virtuální počítač, pro které povolíte replikaci. [Další informace](vmware-azure-tutorial.md#enable-replication). Pokud jste vytvořili skupinu replikace pro konzistence více virtuálních počítačů, můžete přidat virtuální počítače do této skupiny.
6. **Testovací převzetí služeb při selhání**. Po všechno, co je nastavení, můžete provést testovací převzetí služeb zkontrolujte, že virtuální počítače převzetí služeb při selhání do Azure podle očekávání. [Další informace](tutorial-dr-drill-azure.md).
7. **Převzetí služeb při selhání**. Pokud jste právě migraci virtuálních počítačů do Azure – spuštění úplné převzetí služeb při selhání na to. Pokud nastavujete zotavení po havárii, jako je třeba můžete spustit úplné převzetí služeb při selhání. Úplné zotavení po havárii může po převzetí služeb při selhání do Azure, selhat zpět na místní web jako a není k dispozici. [Další informace](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začne replikovat v souladu se zásadami replikace. 
2. Provoz replikuje do úložiště Azure veřejné koncové body přes internet. Alternativně můžete použít Azure ExpressRoute s [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikace provozu přes virtuální privátní síť site-to-site (VPN) z místního serveru do Azure se nepodporuje.
3. Počáteční kopii data virtuálního počítače se replikují do úložiště Azure.
4. Po dokončení počáteční replikace začne replikace rozdílové změny do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
5. Komunikace se stane, následujícím způsobem:

    - Virtuální počítače komunikovat s místní konfigurační server na portu 443 protokolu HTTPS příchozí pro správu replikace.
    - Konfigurační server orchestruje replikaci pomocí Azure přes port HTTPS 443 odchozí.
    - Virtuální počítače odesílat data replikace na procesní server (spuštěn na serveru, konfigurace) na port HTTPS 9443 příchozí. Tento port se dá změnit.
    - Procesový server přijímá data replikace, optimalizuje je zašifruje a odešle ji do úložiště Azure přes port 443 odchozí.


**Z VMware do Azure replikační proces**

![Proces replikace](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spustíte postupu zotavení po havárii (převzetí služeb při selhání), zkontrolujte, zda vše funguje podle očekávání, můžete spustit převzetí služeb při selhání a navrácení služeb po obnovení je potřeba pro.

1. Spouštění služeb při selhání pro jeden počítač, nebo vytvořit plány na převzetí služeb při selhání víc virtuálních počítačů ve stejnou dobu obnovení. Výhodou plán obnovení spíše než jeden počítač převzetí služeb při selhání zahrnují:
    - Závislosti aplikace můžete model zahrnutím všech virtuálních počítačů mezi aplikace v jednom plánu obnovení.
    - Můžete přidat skripty, runbooky služby Azure a pozastavit pro ruční akce.
2. Po spuštění počáteční převzetí služeb při selhání, můžete provést tak, aby přístup k zatížení z virtuálního počítače Azure.
3. Když primární místní lokalitu opět k dispozici, můžete připravit pro navrácení. Chcete-li navrácení služeb po obnovení, budete muset nastavit infrastrukturu navrácení služeb po obnovení, včetně:

    * **Dočasný procesní server v Azure**: při selhání zpátky do Azure, musíte nastavit virtuální počítač Azure tak, aby fungoval jako server proces pro zpracování replikace z Azure. Tento virtuální počítač je možné po navrácení služeb po obnovení odstranit.
    * **Připojení k síti VPN**: pro navrácení služeb po obnovení, musíte připojení VPN (nebo ExpressRoute) ze sítě Azure k místní lokalitě.
    * **Samostatné hlavní cílový server**: ve výchozím nastavení, který byl nainstalován pomocí konfigurační server na virtuálních počítačů VMware na místní hlavní cílový server zpracovává navrácení služeb po obnovení. Pokud potřebujete selhání zpět velké objemy přenosů, nastavte samostatný místní hlavní cílový server pro tento účel.
    * **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Tato zásada byla vytvořena automaticky při vytvoření vaší zásady replikace z místního do Azure.
4. Jakmile jsou součástí na místě, dojde k navrácení služeb po obnovení v tři kroky:

    - Fáze 1: Nastavte znovu virtuální počítače Azure tak, aby se replikace z Azure zpátky na virtuální počítače VMware na místě.
    -  Fáze 2: Spuštění převzetí služeb při selhání k místní lokalitě.
    - Fáze 3: Po úlohy se nezdařilo zpět, je-li znovu povolit replikaci pro virtuální počítače na místě.
    
 
**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Další postup

Postupujte podle [v tomto kurzu](vmware-azure-tutorial.md) povolit VMware do Azure replikace.
