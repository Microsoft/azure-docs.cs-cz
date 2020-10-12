---
title: Zotavení po havárii pro vícevrstvou aplikaci SharePoint pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro vícevrstvou aplikaci SharePoint pomocí možností Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135403"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Nastavení zotavení po havárii pro vícevrstvou aplikaci SharePoint pro zotavení po havárii pomocí Azure Site Recovery

Tento článek podrobně popisuje, jak chránit aplikaci SharePoint pomocí  [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Přehled

Microsoft SharePoint je výkonné aplikace, která může pomáhat skupině nebo oddělením organizovat, spolupracovat a sdílet informace. SharePoint nabízí intranetové portály, správu dokumentů a souborů, spolupráci, sociální sítě, extranety, weby, hledání v podniku a business intelligence. Má taky integraci systému, integraci procesů a možnosti automatizace pracovních postupů. Organizace je obvykle považují za aplikace vrstvy 1 citlivé na výpadky a ztráty dat.

V dnešní době neposkytuje Microsoft SharePoint žádné možnosti zotavení po havárii. Bez ohledu na typ a rozsah havárie vyžaduje obnovení použití pohotovostního datového centra, ve kterém můžete farmu obnovit. Pohotovostní datová centra se vyžadují pro scénáře, kdy se místní redundantní systémy a zálohy nemůžou v primárním datovém centru zotavit z výpadku.

Dobré řešení zotavení po havárii by mělo umožňovat modelování plánů obnovení okolo složitých aplikačních architektur, jako je třeba SharePoint. Měl by taky mít možnost přidat vlastní kroky pro zpracování mapování aplikací mezi různými úrovněmi a tím zajistit převzetí služeb při selhání jedním kliknutím a s nižší RTOou v případě havárie.

Tento článek podrobně popisuje, jak chránit aplikaci SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se věnuje osvědčeným postupům pro replikaci aplikace služby SharePoint na tři vrstvy do Azure, jak můžete provést postup zotavení po havárii a jak můžete převzít služby při selhání do Azure.

Můžete se podívat na následující video o obnovování vícevrstvé aplikace do Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následujícímu:

1. [Replikace virtuálního počítače do Azure](./vmware-azure-tutorial.md)
2. [Návrh sítě pro obnovení](./concepts-on-premises-to-azure-networking.md)
3. [Test převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
4. [Převzetí služeb při selhání do Azure](site-recovery-failover.md)
5. Postup [replikace řadiče domény](site-recovery-active-directory.md)
6. Postup [replikace SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura služby SharePoint

Službu SharePoint lze nasadit na jeden nebo více serverů pomocí vrstvené topologie a role serveru pro implementaci návrhu farmy, který splňuje určité cíle a cíle. Typickou velkou, vysoce potřebnou farmou serveru SharePoint, která podporuje vysoký počet souběžných uživatelů a velký počet položek obsahu, používá seskupení služeb jako součást strategie škálovatelnosti. Tento přístup zahrnuje spuštěné služby na vyhrazených serverech, seskupovat tyto služby dohromady a potom škálovat servery jako skupinu. Následující topologie znázorňuje seskupení služeb a serverů pro farmu serveru SharePoint Server na třetí úrovni. Podrobné pokyny k různým topologiím SharePointu najdete v dokumentaci k SharePointu a architekturách produktového řádku. Další podrobnosti o nasazení SharePoint 2013 najdete v [tomto dokumentu](/SharePoint/sharepoint-server).



![Vzor nasazení 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Site Recovery je nezávislá aplikace a měl by fungovat s libovolnou verzí SharePointu spuštěnou na podporovaném počítači. Pro vytvoření tohoto článku se použily virtuální počítače VMware s Windows Serverem 2012 R2 Enterprise. Používaly se SharePoint 2013 Enterprise Edition a SQL Server 2014 Enterprise Edition.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Fyzický server** | Yes | Yes
**Azure** | Není k dispozici | Ano


### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

Pokud v aplikaci používáte sdílený cluster založený na discích jako libovolná úroveň, nebudete moct k replikaci těchto virtuálních počítačů použít replikaci Site Recovery. Můžete použít nativní replikaci poskytovanou aplikací a potom použít [plán obnovení](site-recovery-create-recovery-plans.md) pro převzetí služeb při selhání všech vrstev.

## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Podle [těchto pokynů](./vmware-azure-tutorial.md) spusťte replikaci virtuálního počítače do Azure.

* Po dokončení replikace se ujistěte, že jste přešli na všechny virtuální počítače každé úrovně, a v části replikovaná položka > nastavení vyberte stejnou skupinu dostupnosti > vlastnosti > COMPUTE a Network. Pokud má vaše webová vrstva například 3 virtuální počítače, zajistěte, aby všechny 3 virtuální počítače byly nakonfigurované jako součást stejné sady dostupnosti v Azure.

    ![Set-Availability-set](./media/site-recovery-sharepoint/select-av-set.png)

* Pokyny k ochraně služby Active Directory a DNS najdete v tématu [ochrana služby Active Directory a dokumentu DNS](site-recovery-active-directory.md) .

* Pokyny k ochraně databázových vrstev běžících na SQL serveru najdete v tématu [ochrana SQL Server](site-recovery-sql.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

### <a name="network-properties"></a>Vlastnosti sítě

* U virtuálních počítačů aplikace a webové vrstvy nakonfigurujte nastavení sítě v Azure Portal tak, aby se virtuální počítače po převzetí služeb při selhání připojily k pravé síti DR.

    ![Vybrat síť](./media/site-recovery-sharepoint/select-network.png)


* Pokud používáte statickou IP adresu, zadejte IP adresu, kterou má virtuální počítač převzít, do pole **cílová IP adresa** .

    ![Nastavit statickou IP adresu](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Směrování DNS a provozu

Pro internetové weby vytvořte v předplatném Azure [profil Traffic Manager typu priorita](../traffic-manager/quickstart-create-traffic-manager-profile.md) . Pak následujícím způsobem nakonfigurujte DNS a profil Traffic Manager.


| **,** | **Zdroj** | **Cílové**|
| --- | --- | --- |
| Veřejná služba DNS | Veřejné DNS pro weby služby SharePoint <br/><br/> Např.: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Místní DNS | sharepointonprem.contoso.com | Veřejná IP adresa v místní farmě |


V profilu Traffic Manager [vytvořte primární koncové body a koncových bodů obnovení](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Použijte externí koncový bod pro místní koncový bod a veřejnou IP adresu pro koncový bod Azure. Ujistěte se, že je priorita nastavená na hodnotu vyšší na místní koncový bod.

Hostování testovací stránky na specifickém portu (například 800) ve webové vrstvě služby SharePoint, aby Traffic Manager automaticky zjišťoval dostupnost po převzetí služeb při selhání. Toto je alternativní řešení pro případ, že nemůžete povolit anonymní ověřování na žádném z vašich webů SharePointu.

[Nakonfigurujte profil Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) s níže uvedeným nastavením.

* Metoda směrování – priorita
* Hodnota TTL (Time to Live) pro DNS (TTL) – 30 sekund
* Nastavení monitorování koncového bodu – Pokud můžete povolit anonymní ověřování, můžete dát konkrétnímu koncovému bodu webu. Nebo můžete použít testovací stránku na určitém portu (například 800).

## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Plán obnovení umožňuje sekvencování převzetí služeb při selhání různých vrstev v vícevrstvé aplikaci, takže udržování konzistence aplikací. Při vytváření plánu obnovení vícevrstvé webové aplikace postupujte podle následujících kroků. [Přečtěte si další informace o vytvoření plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

1. Vytvořte plán obnovení přidáním virtuálních počítačů aplikace a webové vrstvy.
2. Pokud chcete seskupit virtuální počítače, klikněte na přizpůsobit. Ve výchozím nastavení jsou všechny virtuální počítače součástí skupiny 1.

    ![Přizpůsobení RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Vytvořte další skupinu (skupinu 2) a přesuňte virtuální počítače webové vrstvy do nové skupiny. Virtuální počítače vrstvy aplikace by měly být součástí skupiny 1 a virtuální počítače webové vrstvy by měly být součástí skupiny 2. K tomu je potřeba zajistit, aby se virtuální počítače na úrovni aplikace nejdřív spouštěly, a potom virtuální počítače webové vrstvy.


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidávání skriptů do plánu obnovení

Nejčastěji používané Azure Site Recovery skripty můžete nasadit do svého účtu Automation kliknutím na tlačítko nasadit do Azure níže. Při použití publikovaného skriptu se ujistěte, že budete postupovat podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript pre-Action do skupiny 1 pro převzetí služeb při selhání skupiny dostupnosti SQL. Použijte skript ASR-SQL-FailoverAG, který je publikovaný ve vzorových skriptech. Ujistěte se, že dodržujete pokyny ve skriptu a patřičně proveďte požadované změny ve skriptu.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Přidejte skript akce post pro připojení nástroje pro vyrovnávání zatížení ve virtuálních počítačích převzetí služeb při selhání webové vrstvy (skupina 2). Použijte skript ASR-AddSingleLoadBalancer, který je publikovaný ve vzorových skriptech. Ujistěte se, že dodržujete pokyny ve skriptu a patřičně proveďte požadované změny ve skriptu.

    ![Add-9,1-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-9,1-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Přidejte ruční krok, který aktualizuje záznamy DNS tak, aby odkazovaly na novou farmu v Azure.

    * Pro internetové weby nejsou nutné žádné aktualizace DNS po převzetí služeb při selhání. Postupujte podle kroků popsaných v části "doprovodné materiály k síti" a nakonfigurujte Traffic Manager. Pokud byl profil Traffic Manager nastavený jak je popsáno v předchozí části, přidejte skript pro otevření fiktivního portu (v příkladu 800) na virtuálním počítači Azure.

    * U interních webů přidejte ruční krok, který aktualizuje záznam DNS tak, aby odkazoval na novou IP adresu nástroje pro vyrovnávání zatížení virtuálního počítače webové vrstvy.

4. Přidejte ruční krok k obnovení vyhledávací aplikace ze zálohy nebo spusťte novou vyhledávací službu.

5. Chcete-li obnovit aplikaci Vyhledávací služby ze zálohy, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že záloha aplikace Search Service byla provedena před závažnou událostí a že záloha je k dispozici na webu DR.
    * To je možné snadno dosáhnout naplánováním zálohování (například jednou za den) a použitím postupu kopírování k umístění zálohy na webu DR. Procedury kopírování můžou zahrnovat skriptované programy, jako je například AzCopy (Azure Copy) nebo nastavení služby DFSR (replikace distribuovaných souborových služeb).
    * Teď, když je farma služby SharePoint spuštěná, přejděte do centrální správy, zálohování a obnovení a vyberte obnovit. Obnovení interrogates zadané umístění zálohy (možná budete muset hodnotu aktualizovat). Vyberte zálohu Search Service aplikace, kterou chcete obnovit.
    * Hledání je obnoveno. Mějte na paměti, že obnovení očekává vyhledání stejné topologie (stejného počtu serverů) a stejných písmen pevného disku přiřazených k těmto serverům. Další informace najdete v dokumentu ["obnovení aplikace Vyhledávací služby v SharePoint 2013"](/SharePoint/administration/restore-a-search-service-application) .


6. Pokud chcete začít s novou aplikací Vyhledávací služby, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že je na webu DR k dispozici záloha databáze pro hledání.
    * Vzhledem k tomu, že ostatní databáze aplikace Search Service nejsou replikovány, je nutné je znovu vytvořit. Provedete to tak, že přejdete do centrální správy a odstraníte aplikaci Search Service. Na všech serverech, které jsou hostiteli indexu hledání, odstraňte soubory indexů.
    * Opětovné vytvoření aplikace Search Service a tím znovu vytvoří databáze. Doporučuje se mít připravený skript, který znovu vytvoří tuto aplikaci služby, protože není možné provádět všechny akce prostřednictvím grafického uživatelského rozhraní. Například nastavení umístění jednotky indexu a konfigurace topologie hledání je možné pouze pomocí rutin PowerShellu pro SharePoint. Použijte rutinu prostředí Windows PowerShell Restore-SPEnterpriseSearchServiceApplication a zadejte databázi pro správu s protokolem a replikovaným hledáním Search_Service__DB. Tato rutina poskytuje konfiguraci hledání, schéma, spravované vlastnosti, pravidla a zdroje a vytvoří výchozí sadu dalších součástí.
    * Po opětovném vytvoření aplikace Search Service musíte spustit úplné procházení pro každý zdroj obsahu, aby se Search Service obnovil. Ztratíte některé informace o analýze z místní farmy, například doporučení pro hledání.

7. Po dokončení všech kroků uložíte plán obnovení a konečný plán obnovení bude vypadat nějak takto.

    ![Uložený RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Test převzetí služeb při selhání
Při provádění testovacího převzetí služeb při selhání postupujte podle [těchto pokynů](site-recovery-test-failover-to-azure.md) .

1.  Přejít na Azure Portal a vyberte svůj trezor služby Recovery Services.
2.  Klikněte na plán obnovení vytvořený pro aplikaci SharePoint.
3.  Klikněte na testovací převzetí služeb při selhání.
4.  Vyberte bod obnovení a virtuální síť Azure a spusťte proces testovacího převzetí služeb při selhání.
5.  Jakmile je sekundární prostředí nahoru, můžete provést své ověření.
6.  Po dokončení ověření můžete kliknout na vyčistit testovací převzetí služeb při selhání v plánu obnovení a vyčistit prostředí testovacího převzetí služeb při selhání.

Pokyny k provádění testovacího převzetí služeb při selhání pro AD a DNS najdete v tématu [testování převzetí služeb při selhání](site-recovery-active-directory.md#test-failover-considerations) pro službu AD a dokument DNS.

Pokyny k provádění testovacího převzetí služeb při selhání pro skupiny dostupnosti Always ON SQL serveru najdete v tématu věnovaném [provádění aplikace Dr s Azure Site Recovery a provádění testovacího](site-recovery-sql.md#disaster-recovery-of-an-application) souboru

## <a name="doing-a-failover"></a>Provedení převzetí služeb při selhání
Postupujte podle [těchto pokynů](site-recovery-failover.md) pro převzetí služeb při selhání.

1.  Přejít na Azure Portal a vybrat Recovery Services trezor.
2.  Klikněte na plán obnovení vytvořený pro aplikaci SharePoint.
3.  Klikněte na převzetí služeb při selhání.
4.  Vyberte bod obnovení pro spuštění procesu převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
Další informace o [replikaci dalších aplikací](site-recovery-workload.md) pomocí Site Recovery najdete v.
