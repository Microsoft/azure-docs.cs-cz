---
title: Nastavení zotavení po havárii pro vícevrstvou aplikaci služby SharePoint pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit zotavení po havárii pro vícevrstvou aplikaci služby SharePoint pomocí možností Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 1f3168cef503fed0aea09228c9bc11dfb456f2ab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848472"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Nastavení zotavení po havárii pro vícevrstvou aplikaci SharePoint pro zotavení po havárii pomocí Azure Site Recovery

Tento článek popisuje podrobně k ochraně aplikací SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Přehled

Microsoft SharePoint je výkonné aplikace, které mohou pomoci skupiny nebo oddělení organizace, spolupráce a sdílení informací. SharePoint může poskytnout intranetu portálů, správy dokumentů a souborů, spolupráci, sociálních sítí, extranetů, weby, podnikové vyhledávání a business intelligence. Má také systémové integrace, integrace procesů a možnosti automatizace pracovního postupu. Obvykle organizace vezměte v úvahu to jako 1. úrovně aplikace citlivé na výpadky a ztráty dat.

Microsoft SharePoint ještě dnes, neposkytuje žádné možnosti zotavení po havárii out-of-the-box. Bez ohledu na typ a škálování havárii obnovení zahrnuje použití pohotovostní datového centra, kterou můžete obnovit farmu, do. Pohotovostní datová centra jsou požadovány pro scénáře, kde místní systémy redundantní a zálohování nelze zotavení z výpadku v primárním datovém centru.

Řešení pro zotavení po havárii dobré by měla umožňovat modelování plány obnovení kolem komplexních aplikačních architektur, jako je SharePoint. Musí být také možnost přidávat vlastní postup zpracování aplikace mapování mezi různými úrovněmi a proto převzetí služeb při selhání jedním kliknutím poskytuje nižší RTO v případě havárie.

Tento článek popisuje podrobně k ochraně aplikací SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčené postupy pro replikaci tři vrstvy aplikace služby SharePoint do Azure, jak zotavení po havárii a jak můžete převzetí služeb při selhání aplikace do Azure.

Můžete se podívat níže uvedené video o obnovení vrstvy aplikace s více do Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následující:

1. [Replikace virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
2. Jak [návrh sítě pro zotavení](site-recovery-network-design.md)
3. [Provádění testovací převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
4. [Provádění převzetí služeb při selhání do Azure](site-recovery-failover.md)
5. Jak [replikace řadiče domény](site-recovery-active-directory.md)
6. Jak [replikace SQL serveru](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura služby SharePoint

SharePoint je možné nasadit na jeden nebo více serverů pomocí vrstvené topologie a role serveru implementovat návrh farmy, který splňuje zvláštní cíle a úkoly. Typické velkých, vysoce žádané farmu služby SharePoint server, který podporuje velký počet souběžných uživatelů a velký počet položek obsahu pomocí služby seskupování jako součást své strategie škálovatelnost. Tento přístup zahrnuje spuštění služby na vyhrazené servery, seskupování tyto služby a potom horizontální navýšení kapacity na servery jako skupinu. Následující topologie znázorňuje služby a seskupení pro třívrstvou farmu serveru SharePoint server. Najdete dokumentaci k Sharepointu a architektury produktu řádku podrobné informace o různé topologie služby SharePoint. Můžete najít další podrobnosti o nasazení Sharepointu 2013 v [tento dokument](https://technet.microsoft.com/library/cc303422.aspx).



![Model nasazení 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro vytvoření tohoto článku, byly použity virtuální počítače VMware s Windows Server 2012 R2 Enterprise. Byly použity SharePoint 2013 Enterprise edition a SQL server 2014 Enterprise edition. Flexibilní použití je replikace Site Recovery se očekává doporučení uvedená tady přidržte i následující scénáře.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano
**Azure** | Není k dispozici | Ano

### <a name="sharepoint-versions"></a>Verze služby SharePoint
Jsou podporovány následující verze serveru SharePoint.

* SharePoint server 2013 Standard
* SharePoint server 2013 Enterprise
* SharePoint server 2016 Standard
* SharePoint serveru 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti

Pokud používáte sdílené založené na disku clusteru jako všechny úrovně ve vaší aplikaci pak nebude možné použít replikace Site Recovery k replikaci těchto virtuálních počítačů. Můžete použít nativní replikace poskytovaný aplikací a pak použít [plánu obnovení](site-recovery-create-recovery-plans.md) převzetí služeb při selhání všech úrovní.

## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Postupujte podle [návod](site-recovery-vmware-to-azure.md) ke spuštění replikace virtuálního počítače do Azure.

* Po dokončení replikace, ujistěte se, že přejdete do každého virtuálního počítače na jednotlivé vrstvy a vyberte stejné skupině dostupnosti "replikovaná položka > Nastavení > Vlastnosti > výpočty a síť". Například pokud vaše webové vrstvy 3 virtuální počítače, ujistěte se, že jsou 3 virtuální počítače nakonfigurované jako součást stejné skupině dostupnosti v Azure.

    ![Sady dostupnosti](./media/site-recovery-sharepoint/select-av-set.png)

* Pokyny k ochraně služby Active Directory a DNS, najdete v tématu [ochrana služby Active Directory a DNS](site-recovery-active-directory.md) dokumentu.

* Informace o ochraně databázová vrstva spouštěný na SQL serveru najdete v tématu [chránit SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

### <a name="network-properties"></a>Vlastnosti sítě

* Pro aplikace a virtuální počítače webové vrstvy konfigurace nastavení sítě na webu Azure portal tak, aby virtuální počítače získat připojen ke správné síti zotavení po Havárii po převzetí služeb při selhání.

    ![Vyberte síť](./media/site-recovery-sharepoint/select-network.png)


* Pokud používáte statické IP adresy, zadejte IP adresu, která má virtuální počítač do **cílová IP adresa** pole

    ![Nastavení statické IP adresy](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS a směrování provozu

U lokalit, směřujících do Internetu [vytvořit profil služby Traffic Manager typu "Priority"](../traffic-manager/traffic-manager-create-profile.md) v rámci předplatného Azure. A potom nakonfigurujte profil DNS a Traffic Manager následujícím způsobem.


| **kde** | **Zdroj** | **Cíl**|
| --- | --- | --- |
| Veřejná služba DNS | Veřejná služba DNS pro weby služby SharePoint <br/><br/> Příklad: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| On-premises DNS | sharepointonprem.contoso.com | Veřejné IP adresy na farmě místní |


V profilu služby Traffic Manager [vytvoření koncových bodů primárními a obnovovacími](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Použijte externí koncový bod pro místní koncový bod a veřejnou IP adresu pro koncový bod Azure. Ujistěte se, že je priorita nastavena tak, aby místní koncový bod.

Zkušební stránku na určitém portu (např. 800) hostovat ve webové vrstvě služby SharePoint v pořadí pro Traffic Manageru automaticky zjišťovat dostupnost příspěvek převzetí služeb při selhání. Toto je alternativní řešení v případě, že na žádném z webů služby SharePoint nelze povolit anonymní ověřování.

[Konfigurace profilu Traffic Manageru](../traffic-manager/traffic-manager-configure-priority-routing-method.md) se následující nastavení.

* Metody směrování – "Priority"
* DNS hodnota time to live (TTL) – 30 sekund.
* Nastavení sledování koncových bodů – Pokud povolíte anonymní ověřování, můžete zadat koncový bod konkrétního webu. Nebo můžete použít zkušební stránku na určitém portu (např. 800).

## <a name="creating-a-recovery-plan"></a>Vytváří se plán obnovení

Plán obnovení umožňuje pořadí převzetí služeb při selhání v různých vrstvách vícevrstvou aplikaci, proto zachování konzistence aplikace. Postupujte podle níže uvedený postup při vytváření plánu obnovení, který pro vícevrstvou webovou aplikaci. [Další informace o vytvoření plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupiny převzetí služeb při selhání

1. Vytvořte plán obnovení tak, že přidáte aplikace a virtuální počítače webové vrstvy.
2. Klikněte na "Vlastní" seskupení virtuálních počítačů. Ve výchozím nastavení všechny virtuální počítače jsou součástí "Skupina 1".

    ![Přizpůsobení RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Vytvořit jinou skupinu (skupiny 2) a přesunout virtuální počítače webové vrstvy do nové skupiny. Vaše virtuální počítače vrstvy aplikace by měly být součástí skupiny 1' a virtuální počítače webové vrstvy musí být součástí "Skupina2". Toto je zajistit, aby spouštěcí virtuální počítače vrstvy aplikace si nejprve následovaný virtuální počítače webové vrstvy.


### <a name="adding-scripts-to-the-recovery-plan"></a>Přidávání skriptů do plánu obnovení

Nejčastěji používané skriptů Azure Site Recovery můžete nasadit do účtu Automation, kliknutím na tlačítko 'Nasadit do Azure' níže. Při použití všechny publikované skripty, zajistěte, aby že postupujte podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidáte skript akce před "Skupina 1" do skupiny dostupnosti SQL převzetí služeb při selhání. Pomocí skriptu "Azure Site Recovery-SQL-FailoverAG" publikované v ukázkových skriptech. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Přidat skript akce post připojit nástroj pro vyrovnávání zatížení se oproti virtuálním počítačům webová vrstva (2. skupina). Pomocí skriptu "Azure Site Recovery-AddSingleLoadBalancer" publikované v ukázkových skriptech. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Přidejte ruční krok k aktualizaci záznamů DNS tak, aby odkazoval na nové farmě v Azure.

    * Pro internetové weby nejsou žádné aktualizace DNS požadovaný příspěvek převzetí služeb při selhání. Postupujte podle kroků popsaných v části "Síťové pokyny" Konfigurace Traffic Manageru. Pokud profil Traffic Manageru je nastavený podle popisu v předchozí části, přidejte skript, který otevře fiktivní port (800 v příkladu) ve virtuálním počítači Azure.

    * Pro interní weby přístupné pro zákazníky přidejte provedení ručního kroku aktualizujte záznam DNS tak, aby odkazoval na nové webové vrstvy Virtuálního počítače IP adresu nástroje pro vyrovnávání zatížení.

4. Přidejte ruční krok vyhledávací aplikaci obnovení ze zálohy nebo spustit novou vyhledávací službu.

5. Pro aplikaci služby Search obnovení ze zálohy, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že zálohu aplikace Vyhledávací služby byla provedena před katastrofickými událostí a zda je záloha dostupná v lokalitě zotavení po Havárii.
    * Toho lze dosáhnout snadné plánování zálohování (například jednou denně) a použitím procedury kopírování umístit zálohy v lokalitě zotavení po Havárii. Kopírování postupy mohou zahrnovat skriptované programů, jako jsou nástroje AzCopy (kopie Azure) nebo nastavení DFSR (distribuované služby replikace souborů).
    * Teď, když je farma služby SharePoint spuštěna, přejděte centrální správy 'Zálohování a obnovení' a výběr obnovení. Obnovení interrogates zadané umístění zálohy (budete muset aktualizovat hodnotu). Vyberte zálohu aplikace služby Search, kterou chcete obnovit.
    * Hledání se obnoví. Pamatujte, že obnovení očekává stejné topologie (stejný počet serverů) a stejné pevný disk písmena přiřazené na tyto servery. Další informace najdete v tématu [obnovení vyhledávací služby aplikace v Sharepointu 2013](https://technet.microsoft.com/library/ee748654.aspx) dokumentu.


6. Pro spuštění pomocí nové aplikace služby vyhledávání, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že zálohy databáze "Správa vyhledávání" je k dispozici v lokalitě zotavení po Havárii.
    * Vzhledem k tomu, že ostatní databáze aplikace služby Search se nereplikují, musí být znovu vytvořena. Uděláte to tak, přejděte do centrální správy a odstraňte aplikaci Vyhledávací služby. Na všechny servery, které jsou hostiteli Index vyhledávání odstraňte soubory indexu.
    * Znovu vytvořte aplikaci Vyhledávací služby a tím se znovu vytvoří databáze. Doporučujeme mít připravenou skript, který se znovu vytvoří tuto aplikaci služby, protože není možné provádět všechny akce prostřednictvím grafického uživatelského rozhraní. Například index umístění disku a konfiguraci topologie vyhledávání jsou možné pouze pomocí rutin prostředí PowerShell služby SharePoint. Použijte rutinu prostředí Windows PowerShell SPEnterpriseSearchServiceApplication obnovení a zadejte zasílání protokolů a replikované správy vyhledávání databázi, Search_Service__DB. Tato rutina poskytuje konfigurace vyhledávání, schéma, spravované vlastnosti, pravidla a zdroje a vytvoří výchozí sadu ostatní součásti.
    * Jakmile aplikaci Vyhledávací služby je potřeba znovu vytvořit, je nutné spustit úplné procházení pro každý zdroj obsahu pro obnovení služby Search. Můžete přijít o některé informace analytics z místní farmy, jako je například hledat doporučení.

7. Jakmile se dokončí všechny kroky, uložení plánu obnovení a plán konečného bude vypadat jako následující.

    ![Uložené RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Provádění testovací převzetí služeb při selhání
Postupujte podle [návod](site-recovery-test-failover-to-azure.md) provést testovací převzetí služeb.

1.  Přejděte na webu Azure portal a vyberte váš trezor služby Recovery Services.
2.  Klikněte na tlačítko v plánu obnovení, který je vytvořen pro aplikaci služby SharePoint.
3.  Klikněte na "Testovací převzetí služeb".
4.  Vyberte bod obnovení a Azure virtual network, chcete-li spustit proces testovacího převzetí služeb při selhání.
5.  Jakmile je sekundární prostředí, můžete provádět vaše ověření.
6.  Po dokončení ověření můžete kliknout na 'Vyčištění testovacího převzetí služeb při selhání' v plánu obnovení a vyčistit testovací převzetí služeb při selhání prostředí.

Pokyny k provádění testovacího převzetí služeb při selhání pro službu AD a DNS, najdete [testovací převzetí služeb při selhání důležité informace týkající se AD a DNS](site-recovery-active-directory.md#test-failover-considerations) dokumentu.

Pokyny k provádění testovacího převzetí služeb při selhání pro SQL vždy na skupiny dostupnosti, najdete v tématu [provedení testu převzetí služeb při selhání SQL serveru Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentu.

## <a name="doing-a-failover"></a>Převzetím služeb
Postupujte podle [návod](site-recovery-failover.md) plnit převzetí služeb při selhání.

1.  Přejděte na webu Azure portal a vyberte svůj trezor služby Recovery Services.
2.  Klikněte na tlačítko v plánu obnovení, který je vytvořen pro aplikaci služby SharePoint.
3.  Klikněte na "Převzetí služeb při selhání".
4.  Vyberte bod obnovení, zahájíte proces převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
Další informace o [replikace jiných aplikací](site-recovery-workload.md) pomocí služby Site Recovery.
