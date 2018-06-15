---
title: Vícevrstvé aplikace služby SharePoint pomocí Azure Site Recovery replikovat | Microsoft Docs
description: Tento článek popisuje, jak replikovat vícevrstvé aplikace služby SharePoint pomocí funkcí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: sutalasi
ms.openlocfilehash: de787ec5078d4cec53bcd2fcc24129e651c711bc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896495"
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replikovat vícevrstvé aplikace služby SharePoint pro zotavení po havárii pomocí Azure Site Recovery

Tento článek popisuje podrobný postup k ochraně aplikací služby SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Přehled

Microsoft SharePoint je výkonné aplikace, která může pomoci skupinu nebo oddělení organizace, spolupracovat a sdílet informace. SharePoint může poskytnout intranetu portálů, dokument a správa souborů, spolupráce, sociálních sítí, extranetu, weby, hledání enterprise a business intelligence. Je také integrace systému, proces integrace a možnosti automatizace pracovního postupu. Obvykle organizace považuje za jako aplikace úrovně 1 citlivé ztrátu výpadky a data.

Dnes Microsoft SharePoint neposkytuje žádné možnosti obnovení po havárii se na pole. Bez ohledu na typ a škále havárie obnovení zahrnuje použití pohotovostní datového centra, kterou můžete obnovit do farmy. Pohotovostní datových centrech jsou požadovány pro scénáře, kde místní redundantní systémy a zálohování nelze obnovit z výpadku v primárním datovém centru.

Řešení pro zotavení po havárii dobrý musí umožňovat modelování plány obnovení kolem architektury komplexní aplikace, například SharePoint. Musí být také možnost přidat vlastní postup zpracování aplikace mapování mezi různé úrovně a proto nabízí selhání jedním kliknutím nižší RTO v případě havárie.

Tento článek popisuje podrobný postup k ochraně aplikací služby SharePoint pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčené postupy pro replikaci tři vrstvy aplikace služby SharePoint do Azure, jak můžete provést postupu zotavení po havárii, a jak můžete převzetí služeb při selhání aplikaci do Azure.

Můžete sledovat níže uvedené video o obnovení k více vrstvy aplikaci do Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte následující:

1. [Replikaci virtuálního počítače do Azure](site-recovery-vmware-to-azure.md)
2. Postup [návrh k síti pro obnovení](site-recovery-network-design.md)
3. [Provádění převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
4. [Provádění převzetí služeb při selhání do Azure](site-recovery-failover.md)
5. Postup [replikace řadiče domény](site-recovery-active-directory.md)
6. Postup [replikaci systému SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura služby SharePoint

Služby SharePoint můžete nasadit na jeden nebo více serverů pomocí vrstvené topologie a role serveru k implementaci návrhu farmy, který splňuje specifické cíle a cílů. Typické velkých, vysoce vyžádání farmy služby SharePoint server podporující velký počet souběžných uživatelů a velký počet položek obsahu pomocí služby seskupování v rámci své strategie škálovatelnost. Tento postup zahrnuje službami na vyhrazené servery, seskupování tyto služby a škálování servery jako skupina. Následující topologie znázorňuje služby a serveru seskupení pro vrstvu tři serverové farmy služby SharePoint. Naleznete v dokumentaci k nástroji služby SharePoint a architektury řádku produktu podrobné informace o různé topologie služby SharePoint. Můžete najít další podrobnosti o nasazení služby SharePoint 2013 v [tento dokument](https://technet.microsoft.com/library/cc303422.aspx).



![Vzor nasazení 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Podpora pro obnovení lokality

Pro vytvoření tohoto článku, používaly virtuální počítače VMware s Windows Server 2012 R2 Enterprise. Byly použity SharePoint 2013 Enterprise edition a SQL server 2014 Enterprise edition. Site Recovery replikace je nezávislá na aplikace, doporučení uvedená tady se očekává přidržte také následující scénáře.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano
**Azure** | Není k dispozici | Ano

### <a name="sharepoint-versions"></a>Verze služby SharePoint
Podporovány jsou následující verze serveru SharePoint.

* SharePoint server 2013 standardní
* SharePoint server 2013 Enterprise
* SharePoint server 2016 standardní
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Co je potřeba mějte na paměti

Pokud používáte sdíleného disku clusteru jako libovolné úrovně ve vaší aplikaci pak nebude možné použít replikace Site Recovery k replikaci těchto virtuálních počítačů. Můžete použít nativní replikaci poskytuje aplikace a pak použít [plán obnovení](site-recovery-create-recovery-plans.md) převzetí služeb při selhání, všechny úrovně.

## <a name="replicating-virtual-machines"></a>Replikace virtuálních počítačů

Postupujte podle [v tomto návodu](site-recovery-vmware-to-azure.md) k zahájení replikace virtuálního počítače do Azure.

* Po dokončení replikace Ujistěte se, přejděte na každý virtuální počítač každý vrstvy a vyberte stejné skupině dostupnosti, replikované položky > Nastavení > Vlastnosti > výpočty a síť ". Například pokud vaše webová vrstva 3 virtuální počítače, ujistěte se, že jsou 3 virtuální počítače nakonfigurované jako součást stejné dostupnosti v Azure.

    ![Sada sady dostupnosti.](./media/site-recovery-sharepoint/select-av-set.png)

* Pokyny o ochraně Active Directory a DNS, najdete v tématu [chránit Active Directory a DNS](site-recovery-active-directory.md) dokumentu.

* Pokyny k ochraně databázové vrstvy systémem SQL server, najdete v části [chránit SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

### <a name="network-properties"></a>Vlastnosti sítě

* Pro aplikaci a webovou vrstvu virtuálních počítačů nakonfigurujte nastavení sítě na portálu Azure tak, aby získat virtuální počítače připojené k správného síťového zotavení po Havárii po převzetí služeb při selhání.

    ![Vyberte síť](./media/site-recovery-sharepoint/select-network.png)


* Pokud používáte statickou IP adresu, zadejte IP adresa, která má virtuální počítač přijmout **cílová IP adresa** pole

    ![Nastavit statickou IP adresu](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS a směrování provozu

Pro internetové weby [vytvořit profil Traffic Manageru typu "Priority"](../traffic-manager/traffic-manager-create-profile.md) v rámci předplatného Azure. A pak nakonfigurujte DNS a Traffic Manager profilu následujícím způsobem.


| **kde** | **Zdroj** | **cíl**|
| --- | --- | --- |
| Veřejné služby DNS | Veřejné služby DNS pro weby služby SharePoint <br/><br/> Například: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Místní DNS | sharepointonprem.contoso.com | Veřejná IP adresa na místní farmě |


V profilu Traffic Manageru [vytvoření koncových bodů primárními a obnovovacími](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Použijte externí koncový bod pro místní koncový bod a veřejnou IP adresu pro koncový bod Azure. Ujistěte se, že je priorita nastavena tak, aby místní koncový bod.

Zkušební stránku na určitém portu (například 800) hostitele ve vrstvě web služby SharePoint v pořadí pro správce provozu automaticky zjišťovat dostupnost post převzetí služeb při selhání. Toto je alternativní řešení, v případě, že na všech webů služby SharePoint nelze povolit anonymní ověřování.

[Nakonfigurujte profil služby Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) se následující nastavení.

* Metody směrování - 'Priority.
* DNS hodnota time to live (TTL) – 30 sekund.
* Nastavení monitorování koncového bodu – Pokud povolíte anonymní ověřování, můžete udělit koncový bod konkrétní web. Nebo můžete použít zkušební stránku na určitém portu (například 800).

## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení

Plán obnovení umožňuje pořadí převzetí služeb při selhání v různých vrstvách vícevrstvé aplikace, proto zachování konzistence aplikace. Použijte následujících kroků při vytváření plánu obnovení vícevrstvých webových aplikací. [Další informace o vytváření plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Probíhá přidávání virtuálních počítačů do skupin převzetí služeb při selhání

1. Vytvoření plánu obnovení přidáním aplikace a webové vrstvy virtuálních počítačů.
2. Klikněte na "Upravit" skupiny virtuálních počítačů. Ve výchozím nastavení všechny virtuální počítače jsou součástí skupiny 1, který.

    ![Přizpůsobení RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Vytvořte další skupinu (skupiny 2) a přesuňte webovou vrstvu virtuálních počítačů do nové skupiny. Vaše aplikace vrstvy virtuální počítače by měly být součástí skupiny 1, a webovou vrstvu virtuálních počítačů musí být součástí 'Skupiny 2'. Toto je zajistit, aby spouštěcí virtuální počítače vrstvy aplikace si nejprve následuje webovou vrstvu virtuálních počítačů.


### <a name="adding-scripts-to-the-recovery-plan"></a>Probíhá přidávání skriptů do plánu obnovení

Nejčastěji používané skripty Azure Site Recovery můžete nasadit do vašeho účtu Automation klepnutím na tlačítko 'nasadit do Azure, níže. Při použití všech publikovaných skriptů, zajistěte, aby že podle pokynů uvedených ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidáte skript předběžné akce na '1 skupiny' převzetí služeb při selhání skupiny dostupnosti SQL. Použít skript, automatické obnovení systému SQL FailoverAG' publikován v ukázkové skripty. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu správně.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Přidat skript akce post připojit nástroj pro vyrovnávání zatížení na neúspěšný přes virtuální počítače webová vrstva (2. skupina). Použít skript, automatické obnovení systému-AddSingleLoadBalancer' publikován v ukázkové skripty. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu správně.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Přidejte ruční krok k aktualizaci záznamů DNS tak, aby odkazoval na nové farmě v Azure.

    * Pro internetové weby nejsou žádné aktualizace DNS požadované post převzetí služeb při selhání. Postupujte podle kroků popsaných v části "pokyny sítě, ke konfiguraci Traffic Manager. Pokud se profil služby Traffic Manager nastavena tak jak je popsáno v předchozí části, přidejte skript pro otevření portu fiktivní (800 v příkladu) ve virtuálním počítači Azure.

    * Pro interní weby přidejte ruční krok k aktualizaci záznamu DNS tak, aby odkazoval na IP adresu služby Vyrovnávání zatížení nové webové vrstvy Virtuálního počítače.

4. Přidejte ruční krok vyhledávací aplikaci obnovit ze zálohy nebo spuštění nové služby vyhledávání.

5. Pro aplikaci služby vyhledávání obnovení ze zálohy, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že zálohy aplikace Vyhledávací služby byla provedena před závažné události a zda je záloha dostupná v lokalitě zotavení po Havárii.
    * To lze snadno dosáhnout plánování zálohování (například, jednou denně) a postupem kopie umístit zálohování v lokalitě zotavení po Havárii. Kopírování postupů může obsahovat skriptované programy jako AzCopy (kopie Azure) nebo nastavení služby DFSR (distribuované služby replikace souborů).
    * Teď, když běží farmy služby SharePoint, přejděte centrální správy, zálohování a obnovení' a vyberte možnost obnovit. Obnovení interrogates zadané umístění zálohy (budete muset aktualizovat hodnotu). Vyberte zálohu aplikace Vyhledávací služby, které chcete obnovit.
    * Hledání je obnoven. Mějte na paměti, která očekává obnovení najít stejné topologie (stejný počet serverů) a stejná pevný disk písmena přiřazené k těmto serverům. Další informace najdete v tématu [obnovit vyhledávání služby aplikace v SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx) dokumentu.


6. Pro spuštění s novou aplikaci Vyhledávací služby, postupujte podle následujících kroků.

    * Tato metoda předpokládá, že zálohy databáze "Správa vyhledávání" je k dispozici v lokalitě zotavení po Havárii.
    * Vzhledem k tomu, že ostatní databáze aplikace služby vyhledávání nejsou replikovány, musí být vytvořeny znovu. Uděláte to tak, přejděte do centrální správy a delete pro aplikaci služby vyhledávání. Na všechny servery, které jsou hostiteli indexu vyhledávání odstraňte soubory indexu.
    * Znovu vytvořte aplikaci Vyhledávací služby a tím se znovu vytvoří databáze. Doporučuje se mít připravené skript, který znovu vytvoří tuto aplikaci služby, protože to není možné provést všechny akce prostřednictvím grafického uživatelského rozhraní. Například umístění jednotky index nastavení a konfiguraci topologie vyhledávání jsou možná jenom pomocí rutin prostředí PowerShell služby SharePoint. Použijte rutinu prostředí Windows PowerShell obnovení SPEnterpriseSearchServiceApplication a zadejte zasílání protokolů a replikované vyhledávání databázi správy, Search_Service__DB. Tato rutina poskytuje konfiguraci hledání, schéma, spravované vlastnosti, pravidla a zdroje a vytvoří výchozí sadu ostatní součásti.
    * Jakmile aplikaci Vyhledávací služby je potřeba znovu vytvořit, je nutné spustit úplné procházení pro každý zdroj obsahu obnovit službu vyhledávání. Přijdete o některé informace analytics z farmy místního, jako je například vyhledávání doporučení.

7. Po dokončení všech kroků, uložení plánu obnovení a plán konečné obnovení bude vypadat podobně jako následující.

    ![Uložené RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Provádění testovací převzetí služeb
Postupujte podle [v tomto návodu](site-recovery-test-failover-to-azure.md) provedete testovací převzetí služeb.

1.  Přejděte na portál Azure a vyberte trezoru služby zotavení.
2.  Kliknutím na vytvořit pro aplikaci SharePoint plánu obnovení.
3.  Klikněte na "Testovací převzetí služeb".
4.  Vyberte bod obnovení a virtuální síť Azure ke spuštění procesu testovací převzetí služeb při selhání.
5.  Jakmile sekundární prostředí zapnutý, můžete provést vaše ověření.
6.  Po dokončení k ověření se 'vyčistit testovací převzetí služeb při selhání, můžete kliknutím na plánu obnovení a vyčištění prostředí testovacího převzetí služeb při selhání.

Pokyny k provádění převzetí služeb při selhání pro AD a DNS, odkazovat na [testovací převzetí služeb při selhání aspekty AD a DNS](site-recovery-active-directory.md#test-failover-considerations) dokumentu.

Pokyny k provádění převzetí služeb při selhání pro SQL vždy na skupiny dostupnosti, najdete v části [provádění testovací převzetí služeb při selhání pro SQL serveru Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentu.

## <a name="doing-a-failover"></a>Převzetím služeb
Postupujte podle [v tomto návodu](site-recovery-failover.md) pro převzetím služeb.

1.  Přejděte na portál Azure a vyberte svůj trezor služeb zotavení.
2.  Kliknutím na vytvořit pro aplikaci SharePoint plánu obnovení.
3.  Klikněte na 'Převzetí služeb při selhání'.
4.  Vyberte bod obnovení se spustit proces převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
Další informace o [replikace jiné aplikace](site-recovery-workload.md) pomocí Site Recovery.
