---
title: Zotavení po havárii pro vícevrstvou sharepointovou aplikaci využívající Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro vícevrstvou sharepointovou aplikaci pomocí možností obnovení webu Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74706368"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Nastavení zotavení po havárii pro vícevrstvou sharepointovou aplikaci pro zotavení po havárii pomocí Azure Site Recovery

Tento článek podrobně popisuje, jak chránit aplikaci SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Přehled

Microsoft SharePoint je výkonná aplikace, která pomáhá skupině nebo oddělení organizovat, spolupracovat a sdílet informace. SharePoint může poskytovat intranetové portály, správu dokumentů a souborů, spolupráci, sociální sítě, extranety, weby, podnikové vyhledávání a business intelligence. Má také možnosti systémové integrace, integrace procesů a automatizace pracovních postupů. Organizace ji obvykle považují za aplikaci úrovně 1 citlivou na výpadky a ztrátu dat.

V současné době služba Microsoft SharePoint neposkytuje žádné možnosti zotavení po havárii před po vybalení. Bez ohledu na typ a rozsah havárie zahrnuje obnovení použití pohotovostního datového centra, do kterého můžete farmu obnovit. Pohotovostní datová centra jsou vyžadována pro scénáře, kdy se místní redundantní systémy a zálohy nemohou zotavit z výpadku v primárním datovém centru.

Dobré řešení zotavení po havárii by mělo umožnit modelování plánů obnovení kolem složitých architektur aplikací, jako je SharePoint. Měl by mít také možnost přidat vlastní kroky pro zpracování mapování aplikací mezi různými vrstvami a tím poskytuje převzetí služeb při selhání s jedním kliknutím s nižším RTO v případě havárie.

Tento článek podrobně popisuje, jak chránit aplikaci SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se bude zabývat doporučenými postupy pro replikaci třívrstvé sharepointové aplikace do Azure, jak můžete provést cvičení pro zotavení po havárii a jak můžete převést na tlačítko při převzetí služeb při selhání aplikace do Azure.

Můžete sledovat následující video o obnovení vícevrstvé aplikace do Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následujícím uvažnostem:

1. [Replikace virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
2. Jak [navrhnout síť pro obnovení](site-recovery-network-design.md)
3. [Provedení testovacího převzetí služeb při selhání v Azure](site-recovery-test-failover-to-azure.md)
4. [Převzetí služeb při selhání v Azure](site-recovery-failover.md)
5. [Replikace řadiče domény](site-recovery-active-directory.md)
6. Replikace [serveru SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura SharePointu

SharePoint lze nasadit na jeden nebo více serverů pomocí vrstvené topologie a role serveru k implementaci návrhu farmy, který splňuje konkrétní cíle a cíle. Typická velká, vysoce žádaná serverová farma SharePoint, která podporuje vysoký počet souběžných uživatelů a velký počet položek obsahu, používá seskupení služeb jako součást své strategie škálovatelnosti. Tento přístup zahrnuje spouštění služeb na dedikovaných serverech, seskupování těchto služeb dohromady a následné horizontální navýšení kapacity serverů jako skupiny. Následující topologie ilustruje seskupování služeb a serverů pro třívrstvou serverovou farmu SharePoint. Podrobné pokyny k různým topologiím SharePointu a architekturách produktových řad najdete v dokumentaci ke službě SharePoint a architekturách produktových řad. Další podrobnosti o nasazení SharePointu 2013 najdete v [tomto dokumentu](https://technet.microsoft.com/library/cc303422.aspx).



![Vzor nasazení 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Site Recovery je aplikace nezávislá a měla by fungovat s libovolnou verzí služby SharePoint spuštěnou v podporovaném počítači. Pro vytvoření tohoto článku byly použity virtuální počítače VMware se systémem Windows Server 2012 R2 Enterprise. Byly použity sharepointové edice Enterprise a SQL server 2014 Enterprise edition.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**Vmware** | Ano | Ano
**Fyzický server** | Ano | Ano
**Azure** | Není k dispozici | Ano


### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

Pokud používáte cluster založený na sdíleném disku jako libovolnou vrstvu ve vaší aplikaci, nebudete moci k replikaci těchto virtuálních počítačů použít replikaci site recovery. Můžete použít nativní replikaci poskytovanou aplikací a pak použít [plán obnovení](site-recovery-create-recovery-plans.md) pro převzetí služeb při selhání všech úrovních.

## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Podle [těchto pokynů](site-recovery-vmware-to-azure.md) začněte replikovat virtuální počítač do Azure.

* Po dokončení replikace se ujistěte, že přejdete do každého virtuálního počítače každé vrstvy a vyberete stejnou sadu dostupnosti v části Replikovaná položka > Nastavení > vlastnosti > výpočetní ch odvoz a síť.Once the replication is complete is complete, make sure you go to each virtual machine of each tier and select same availability set in 'Replicated item > Settings > Properties > Compute and Network'. Například pokud vaše webová vrstva má 3 virtuální počítače, ujistěte se, že všechny 3 virtuální počítače jsou nakonfigurované jako součást stejné sady dostupnosti v Azure.

    ![Set-Dostupnost-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Pokyny k ochraně služby Active Directory a služby DNS naleznete v dokumentu [Protect Active Directory a DNS.](site-recovery-active-directory.md)

* Pokyny k ochraně databázové vrstvy spuštěné na serveru SQL naleznete v dokumentu [Protect SQL Server.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Konfigurace sítě

### <a name="network-properties"></a>Vlastnosti sítě

* Pro virtuální počítače aplikační a webové vrstvy nakonfigurujte nastavení sítě na webu Azure Portal tak, aby se virtuální počítače po převzetí služeb při selhání připojily ke správné síti zotavení po havárii.

    ![Vybrat síť](./media/site-recovery-sharepoint/select-network.png)


* Pokud používáte statickou IP adresu, zadejte adresu IP, kterou má virtuální počítač provést v poli **Cílová adresa IP.**

    ![Nastavit statickou IP adresu](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Směrování DNS a provozu

Pro internetové weby [vytvořte profil Traffic Manageru typu Priorita](../traffic-manager/traffic-manager-create-profile.md) v předplatném Azure. A pak nakonfigurujte profil DNS a Traffic Manager následujícím způsobem.


| **Kde** | **Zdroj** | **Cíl**|
| --- | --- | --- |
| Veřejná služba DNS | Veřejné DNS pro sharepointové weby <br/><br/> Příklad: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Místní DNS | sharepointonprem.contoso.com | Veřejné duševní vlastnictví v místním hospodářství |


V profilu Traffic Manager [vytvořte primární koncové body a koncové body obnovení](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Externí koncový bod použijte pro místní koncový bod a veřejnou IP adresu pro koncový bod Azure. Ujistěte se, že priorita je nastavena vyšší na místní koncový bod.

Hostujte testovací stránku na konkrétním portu (například 800) na webové vrstvě SharePointu, aby Správce provozu automaticky zjistil dostupnost převzetí služeb při selhání. Toto řešení je v případě, že nelze povolit anonymní ověřování na žádném z webů služby SharePoint.

[Nakonfigurujte profil Traffic Manageru](../traffic-manager/traffic-manager-configure-priority-routing-method.md) pomocí níže uvedených nastavení.

* Metoda směrování - "Priorita"
* DNS čas žít (TTL) - '30 sekund'
* Nastavení monitoru koncového bodu – Pokud můžete povolit anonymní ověřování, můžete poskytnout konkrétní koncový bod webu. Nebo můžete použít testovací stránku na konkrétním portu (například 800).

## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Plán obnovení umožňuje sekvencování převzetí služeb při selhání různých vrstev v aplikaci s více vrstvami, tedy zachování konzistence aplikace. Při vytváření plánu obnovení pro vícevrstvou webovou aplikaci postupujte podle následujících kroků. [Další informace o vytvoření plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin převzetí služeb při selhání

1. Vytvořte plán obnovení přidáním virtuálních stránek aplikace a webové vrstvy.
2. Klikněte na 'Přizpůsobit' pro seskupení virtuálních počítačů. Ve výchozím nastavení jsou všechny virtuální aplikace součástí skupiny 1.

    ![Přizpůsobit RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Vytvořte jinou skupinu (skupina 2) a přesuňte virtuální uživatele webové vrstvy do nové skupiny. Virtuální počítače úrovně aplikace by měly být součástí skupiny 1 a virtuální počítače webové úrovně by měly být součástí skupiny 2. Tím zajistíte, že virtuální servery na úrovni aplikace se spustí jako první následované virtuálními servery webové vrstvy.


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení

Nejčastěji používané skripty Azure Site Recovery můžete nasadit do svého účtu Automation kliknutím na tlačítko "Nasadit do Azure" níže. Pokud používáte jakýkoli publikovaný skript, ujistěte se, že postupujte podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript před akcí do skupiny 1 do skupiny dostupnosti SQL s podporou převzetí služeb při selhání. Použijte skript ASR-SQL-FailoverAG publikovaný v ukázkových skriptech. Ujistěte se, že postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.

    ![Přidat-AG-Script-Krok-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Přidat-AG-Script-Krok-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Přidejte skript akce příspěvku, který připojí nástroj pro vyrovnávání zatížení na selhání virtuálních počítačů webové vrstvy (skupina 2). Použijte skript ASR-AddSingleLoadBalancer publikovaný v ukázkových skriptech. Ujistěte se, že postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.

    ![Přidat-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Přidat-LB-Script-Krok-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Přidejte ruční krok k aktualizaci záznamů DNS tak, aby ukazovaly na novou farmu v Azure.

    * U internetových webů nejsou nutné žádné aktualizace DNS po převzetí služeb při selhání. Chcete-li nakonfigurovat Traffic Manager, postupujte podle pokynů popsaných v části Pokyny k síti. Pokud profil Traffic Manager byl nastaven tak, jak je popsáno v předchozí části, přidejte skript pro otevření fiktivní port (800 v příkladu) na virtuálním počítači Azure.

    * U interních webů přidejte ruční krok k aktualizaci záznamu DNS tak, aby ukazoval na IP adresu pro vyrovnávání zatížení nové ho webu.

4. Přidejte ruční krok k obnovení vyhledávací aplikace ze zálohy nebo spuštění nové vyhledávací služby.

5. Obnovení aplikace služby Search ze zálohy postupujte podle následujících kroků.

    * Tato metoda předpokládá, že záloha aplikace vyhledávací služby byla provedena před katastrofickou událostí a že záloha je k dispozici v lokalitě zotavení po Havárii.
    * Toho lze snadno dosáhnout naplánováním zálohy (například jednou denně) a použitím postupu kopírování k umístění zálohy do lokality zotavení po Havárii. Postupy kopírování mohou zahrnovat skriptované programy, jako je AzCopy (Azure Copy) nebo nastavení dfsr (Replikace distribuovaných souborových služeb).
    * Teď, když je spuštěná sharepointová farma, přejděte do centrální správy, "Zálohování a obnovení" a vyberte Obnovit. Obnovení vyslýchá zadané umístění zálohy (může být nutné aktualizovat hodnotu). Vyberte zálohu aplikace vyhledávací služby, kterou chcete obnovit.
    * Hledání je obnoveno. Mějte na paměti, že obnovení očekává, že najde stejnou topologii (stejný počet serverů) a stejné písmena pevných disků přiřazené těmto serverům. Další informace najdete [v tématu Obnovení aplikace vyhledávací služby v SharePointu 2013.](https://technet.microsoft.com/library/ee748654.aspx)


6. Pokud začnete s novou aplikací vyhledávací služby, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že záloha databáze "Správa vyhledávání" je k dispozici v lokalitě zotavení po Havárii.
    * Vzhledem k tomu, že ostatní databáze aplikací vyhledávací služby nejsou replikovány, je třeba je znovu vytvořit. Chcete-li tak učinit, přejděte na centrální správu a odstraňte aplikaci vyhledávací služby. Na všech serverech, které jsou hostiteli indexu vyhledávání, odstraňte soubory indexu.
    * Znovu vytvořte aplikaci vyhledávací služby a to znovu vytvoří databáze. Doporučuje se mít připravený skript, který znovu vytvoří tuto aplikaci služby, protože není možné provádět všechny akce prostřednictvím grafického uživatelského rozhraní. Například nastavení umístění jednotky indexu a konfigurace topologie vyhledávání jsou možné pouze pomocí rutin prostředí SharePoint PowerShell. Použijte rutinu Obnovení prostředí Windows PowerShell-SPEnterpriseSearchServiceApplication a zadejte databázi správy vyhledávání dodané protokolem a replikovanou databází správy vyhledávání Search_Service__DB. Tato rutina poskytuje konfiguraci hledání, schéma, spravované vlastnosti, pravidla a zdroje a vytvoří výchozí sadu ostatních součástí.
    * Po opětovném vytvoření aplikace vyhledávací služby je nutné spustit úplné procházení pro každý zdroj obsahu, abyste obnovili vyhledávací službu. Ztratíte některé analytické informace z místní farmy, jako jsou doporučení pro vyhledávání.

7. Po dokončení všech kroků uložte plán obnovení a konečný plán obnovení bude vypadat takto.

    ![Uložené RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Provedení zkušebního převzetí služeb při selhání
Postupujte podle [těchto pokynů](site-recovery-test-failover-to-azure.md) provést test převzetí služeb při selhání.

1.  Přejděte na portál Azure a vyberte trezor služby recovery Service.
2.  Klikněte na plán obnovení vytvořený pro aplikaci SharePoint.
3.  Klikněte na "Test failover".
4.  Vyberte bod obnovení a virtuální síť Azure a spusťte proces převzetí služeb při selhání testu.
5.  Jakmile je sekundární prostředí nahoru, můžete provést ověření.
6.  Po dokončení ověření můžete kliknout na tlačítko "Ověření selhání testu vyčištění" v plánu obnovení a testovací prostředí převzetí služeb při selhání je vyčištěno.

Pokyny k provedení testování převzetí služeb při selhání pro služby AD a DNS naleznete v úvahu test převzetí služeb při selhání pro dokument [služby AD a DNS.](site-recovery-active-directory.md#test-failover-considerations)

Pokyny k provádění testování převzetí služeb při selhání pro sql vždy na skupiny dostupnosti, najdete v článku [provádění dr. aplikace s Azure Site Recovery a provádění test převzetí služeb při selhání](site-recovery-sql.md#disaster-recovery-of-an-application) dokumentu.

## <a name="doing-a-failover"></a>Převzetí služeb při selhání
Postupujte podle [tohoto návodu](site-recovery-failover.md) pro provedení převzetí služeb při selhání.

1.  Přejděte na portál Azure a vyberte trezor služby Recovery Services.
2.  Klikněte na plán obnovení vytvořený pro aplikaci SharePoint.
3.  Klikněte na 'Převzetí služeb při selhání'.
4.  Vyberte bod obnovení a spusťte proces převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
Další informace o [replikaci jiných aplikací](site-recovery-workload.md) pomocí site recovery.
