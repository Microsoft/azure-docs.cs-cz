---
title: Připojení nástroje Operations Manager ke službě Azure Monitor | Dokumenty společnosti Microsoft
description: Nástroj Operations Manager můžete integrovat do svého pracovního prostoru, abyste nepřišli o své investice do systému System Center Operations Manager a mohli využívat rozšířené možnosti Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274343"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Připojení nástroje Operations Manager ke službě Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Chcete-li zachovat stávající investice do [nástroje Operations Manager system center](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) a používat rozšířené funkce pomocí služby Azure Monitor, můžete nástroj Operations Manager integrovat s pracovním prostorem Log Analytics. To vám umožní využít příležitosti protokolů v Azure Monitoru a zároveň pokračovat v používání Operations Manageru k:

* Monitorování stavu vašich IT služeb pomocí nástroje Operations Manager
* Údržba integrace s vašimi řešeními ITSM pro podporu správy incidentů a problémů
* Správa životního cyklu agentů nasazených do virtuálních počítačů IaaS v místním prostředí a ve veřejném cloudu, které monitorujete pomocí nástroje Operations Manager

Integrace s System Center Operations Manager přidává hodnotu vaší strategie provozu služeb pomocí rychlosti a efektivity Azure Monitor při shromažďování, ukládání a analýze dat protokolu z Operations Manager. Dotazy protokolu Azure Monitor pomáhají korelovat a pracovat na identifikaci chyb problémů a navařování opakování na podporu stávajícího procesu správy problémů. Flexibilita dotazovacího stroje prozkoumat data o výkonu, událostech a výstrahách s bohatými řídicími panely a možnostmi vytváření sestav, které smysluplně odhalují tato data, ukazuje sílu, kterou Azure Monitor přináší v complimenting Operations Manageru.

Agenti, kteří se hlásí skupině pro správu nástroje Operations Manager, shromažďují data z vašich serverů na základě zdrojů dat a řešení [Analýzy protokolů,](agent-data-sources.md) které jste povolili ve svém pracovním prostoru. V závislosti na povolených řešeních jsou jejich data odesílána přímo ze serveru pro správu nástroje Operations Manager do služby nebo z důvodu objemu dat shromážděných v systému spravovaném agentem jsou odesílána přímo z agenta do pracovního prostoru Log Analytics. Server pro správu předává data přímo do služby; nikdy je nezapisuje do provozní databáze nebo databáze datového skladu. Když server pro správu ztratí připojení k Azure Monitoru, uloží data místně, dokud nebude obnovena komunikace. Pokud je server pro správu offline z důvodu plánované údržby nebo neplánovaného výpadku, jiný server pro správu ve skupině pro správu obnoví připojení k Azure Monitoru.  

Následující diagram znázorňuje připojení mezi servery pro správu a agenty ve skupině pro správu nástroje System Center Operations Manager a azure monitoru, včetně směru a portů.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Pokud zásady zabezpečení IT neumožňují počítačům v síti připojení k Internetu, lze servery pro správu nakonfigurovat tak, aby se připojovaly k bráně Log Analytics a přijímaly informace o konfiguraci a odesílaly shromážděná data v závislosti na řešeních. Povoleno. Další informace a kroky, jak nakonfigurovat skupinu pro správu Operations Manageru tak, aby komunikovala prostřednictvím brány Log Analytics do služby Azure Monitor, najdete v [tématu Připojení počítačů ke službě Azure Monitor pomocí brány Log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Požadavky

Před zahájením zkontrolujte následující požadavky.

* Azure Monitor podporuje jenom System Center Operations Manager 2016 nebo novější, Operations Manager 2012 SP1 UR6 nebo novější a Operations Manager 2012 R2 UR2 nebo novější. V nástrojích Operations Manager 2012 SP1 UR7 a Operations Manager 2012 R2 UR3 je přidaná podpora proxy serverů.
* Integrace System Center Operations Manager 2016 s americkým vládním cloudem vyžaduje aktualizovanou sadu Management Pack poradce, která je součástí kumulativní aktualizace 2 nebo novější. System Center Operations Manager 2012 R2 vyžaduje aktualizovanou sadu management pack poradce, která je součástí kumulativní aktualizace 3 nebo novější.
* Všichni agenti nástroje Operations Manager musí splňovat minimální požadavky na podporu. Ujistěte se, že agenti jsou na minimální aktualizaci, jinak může dojít k selhání komunikace agenta systému Windows a generovat chyby v protokolu událostí nástroje Operations Manager.
* Pracovní prostor služby Log Analytics. Další informace naleznete v přehledu [pracovního prostoru Analýzy protokolů](design-logs-deployment.md). 
* Do Azure se ověřujete pomocí účtu, který je členem [role přispěvatele analýzy protokolů](manage-access.md#manage-access-using-azure-permissions).

* Podporované oblasti – Správce operations manageru System Center pro připojení k pracovnímu prostoru Analýzy protokolů podporuje pouze následující oblasti Azure:
    - USA – středozápad
    - Austrálie – jihovýchod
    - Západní Evropa
    - USA – východ
    - Jihovýchodní Asie
    - Japonsko – východ
    - Spojené království – jih
    - Indie – střed
    - Střední Kanada
    - USA – západ 2

>[!NOTE]
>Nedávné změny rozhraní API Azure zabrání zákazníkům, aby mohli poprvé úspěšně nakonfigurovat integraci mezi svou skupinou pro správu a službou Azure Monitor. Pro zákazníky, kteří již integrovali svou skupinu pro správu se službou, nejste ovlivněni, pokud nepotřebujete překonfigurovat stávající připojení.  
>Pro následující verze nástroje Operations Manager byla vydána nová sada Management Pack:
> - Pro system center operations manager 2019 je tato sada Management Pack součástí zdrojového média a nainstalována během instalace nové skupiny pro správu nebo během upgradu.
>- Sada Operations Manager 1801 management pack je použitelná také pro nástroj Operations Manager 1807.
>- Nástroj System Center Operations Manager 1801 stáhněte sadu Management Pack [zde](https://www.microsoft.com/download/details.aspx?id=57173).
>- Pro System Center 2016 - Operations Manager stáhněte sadu Management Pack [zde](https://www.microsoft.com/download/details.aspx?id=57172).  
>- Pro nástroj System Center Operations Manager 2012 R2 stáhněte sadu Management Pack [zde](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Network (Síť)

Níže uvedené informace uvádějí informace o konfiguraci serveru proxy a brány firewall, které jsou vyžadovány pro agenta nástroje Operations Manager, servery pro správu a konzolu Operations Console pro komunikaci s nástrojem Azure Monitor. Provoz z každé součásti je odchozí ze sítě do Azure Monitoru.

|Prostředek | Číslo portu| Obejití kontroly protokolu HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Ano|  
|\*.oms.opinsights.azure.com| 443|Ano|  
|\*.blob.core.windows.net| 443|Ano|  
|\*.azure-automation.net| 443|Ano|  
|**Server pro správu**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ano|  
|\*.ods.opinsights.azure.com| 443| Ano|  
|*.azure-automation.net | 443| Ano|  
|**Konzola Operations Manager u Azure Monitoru**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 a 443||  
|\*.microsoft.com| 80 a 443||  
|\*.microsoftonline.com| 80 a 443||  
|\*.mms.microsoft.com| 80 a 443||  
|login.windows.net| 80 a 443||  
|portal.loganalytics.io| 80 a 443||
|api.loganalytics.io| 80 a 443||
|docs.loganalytics.io| 80 a 443||  

### <a name="tls-12-protocol"></a>Protokol TLS 1.2

Chcete-li zajistit zabezpečení dat při přenosu do Azure Monitoru, důrazně doporučujeme nakonfigurovat agenta a skupinu pro správu tak, aby používali alespoň zabezpečení transportní vrstvy (TLS) 1.2. Starší verze vrstvy TLS/SSockets Layer (SSL) byly shledány zranitelnými a zatímco v současné době pracují na povolení zpětné kompatibility, **nejsou doporučeny**. Další informace naleznete v recenzi [Odesílání dat bezpečně pomocí TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Připojení nástroje Operations Manager ke službě Azure Monitor

Provedením následujícího postupu nakonfigurujete skupinu pro správu nástroje Operations Manager na připojení k jednomu z vašich pracovních prostorů Log Analytics.

Při počáteční registraci skupiny pro správu nástroje Operations Manager s pracovním prostorem Log Analytics není v konzole Operations Console k dispozici možnost určit konfiguraci serveru proxy pro skupinu pro správu.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Chcete-li tento problém vyřešit, je třeba aktualizovat konfiguraci systémového proxy serveru pomocí netsh v systému, ze které používáte konzolu Operations console, aby bylo možné konfigurovat integraci, a všechny servery pro správu ve skupině pro správu.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.
   a. Přejděte na **úvodní obrazovku** a zadejte **příkaz cmd**.
   b. Klikněte pravým tlačítkem myši na **příkazový řádek** a vyberte Spustit jako správce**.
1. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení následujících kroků pro integraci s Azure Monitor, můžete odebrat konfiguraci spuštěním `netsh winhttp reset proxy` a potom pomocí **možnosti Konfigurovat proxy server** v konzole Operations console určit server brány proxy nebo Log Analytics brány.

1. V konzole nástroje Operations Manager vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Přejděte na odkaz **Zaregistrovat ve službě Operations Management Suite**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite** zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k vašemu předplatnému OMS, a klikněte na **Přihlásit se**.

   >[!NOTE]
   >Název sady Operations Management Suite byl vyřazen.

1. Po úspěšném ověření se na **stránce Operations Management Suite Onboarding Wizard: Select Workspace** zobrazí výzva k výběru pracovního prostoru klienta Azure, předplatného a analýzy protokolů. Pokud máte více než jeden pracovní prostor, vyberte z rozevíracího seznamu ten, který chcete zaregistrovat ve skupině pro správu nástroje Operations Manager, a pak klikněte na **Další**.

   > [!NOTE]
   > Operations Manager podporuje vždy jenom jeden pracovní prostor služby Log Analytics. Připojení a počítače, které byly registrované na Azure Monitor s předchozím pracovním prostorem jsou odebrány z Azure Monitoru.
   >
   >
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: souhrn** ověřte nastavení, a pokud je správné, klikněte na **Vytvořit**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: dokončení** klikněte na **Zavřít**.

### <a name="add-agent-managed-computers"></a>Přidání počítačů spravovaných agenty

Po konfiguraci integrace s pracovním prostorem Log Analytics naváže pouze připojení ke službě, žádná data se shromažďují z agentů, kteří se hlásí do vaší skupiny pro správu. K tomu nedojde až po konfiguraci, které konkrétní počítače spravované agentem shromažďují data protokolu pro Azure Monitor. Objekty počítačů můžete vybrat buď jednotlivě, nebo jako skupinu obsahující objekty počítačů s Windows. Nemůžete vybrat skupinu, která obsahuje instance jiné třídy, jako třeba logické disky nebo databáze SQL.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Klikněte na odkaz **Přidat počítač či skupinu** pod záhlavím Akce na pravé straně podokna.
1. V dialogovém okně **Hledání počítače** můžete vyhledat počítače nebo skupiny sledované nástrojem Operations Manager. Vyberte počítače nebo skupiny včetně serveru Operations Manager Management Server, chcete-li přejít k připojení ke službě Azure Monitor, klepněte na tlačítko **Přidat**a potom klepněte na tlačítko **OK**.

Počítače a skupiny, které jsou nakonfigurované na shromažďování dat, si můžete zobrazit z uzlu Spravované počítače pod položkou Operations Management Suite v pracovním prostoru **Správa** v konzole Operations Console. Odsud můžete počítače a skupiny podle potřeby přidávat nebo odebírat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurace nastavení proxy v konzole Operations Console

Pokud je mezi skupinou pro správu a službou Azure Monitor, proveďte následující kroky. Tato nastavení jsou centrálně spravované ze skupiny pro správu a distribuovány do systémů spravovaných agentem, které jsou zahrnuty v oboru pro sběr dat protokolu pro Azure Monitor.  To je výhodné v případech, kdy určitá řešení obcházejí server pro správu a odesílají data přímo do služby.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte Operations Management Suite a pak klikněte na **Připojení**.
1. V zobrazení Připojení OMS klikněte na **Konfigurovat proxy server**.
1. Na stránce **Průvodce nastavením služby Operations Management Suite: proxy server** vyberte **Pro přístup ke službě Operations Management Suite použít proxy server**, potom zadejte adresu URL s číslem portu, například http://corpproxy:80, a pak klikněte na **Dokončit**.

Pokud váš proxy server vyžaduje ověření, proveďte následující kroky ke konfiguraci přihlašovacích údajů a nastavení, které je třeba množit do spravovaných počítačů, které jsou ve skupině pro správu podřízeny službě Azure Monitor.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. V části **Konfigurace RunAs** vyberte **Profily**.
1. Otevřete profil **Proxy server profilu Spustit jako služby System Center Advisor**.
1. Pokud chcete použít účet Spustit jako, klikněte v Průvodci profilem Spustit jako na Přidat. [Účet Spustit jako](https://technet.microsoft.com/library/hh321655.aspx) můžete vytvořit, případně použít existující účet. Tento účet musí mít dostatečná oprávnění k průchodu proxy serverem.
1. Pokud chcete nastavit účet k provedení správy, zvolte **Vybraná třída, skupina nebo objekt**, klikněte na **Vybrat** a potom na **Skupina**, čímž otevřete okno **Hledání skupiny**.
1. Vyhledejte a pak vyberte **Skupina monitorovacích serverů služby Microsoft System Center Advisor**. Po výběru skupiny zavřete kliknutím na **OK** okno **Hledání skupiny**.
1. Klepnutím na **tlačítko OK** zavřete pole Přidat účet **Spustit jako.**
1. Kliknutím na **Uložit** dokončete průvodce a uložte změny.

Po vytvoření připojení a nakonfigurujete, kteří agenti budou shromažďovat a vykazovat data protokolu do služby Azure Monitor, použije se ve skupině pro správu následující konfigurace, ne nutně v pořadí:

* Vytvoří se účet Spustit jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Je přidružený k profilu Spustit jako **Objekt blob profilu Spustit jako služby Microsoft System Center Advisor** a je zacílený na dvě třídy: **Server kolekcí** a **Skupina správy nástroje Operations Manager**.
* Vytvoří se dva konektory.  První se jmenuje **Microsoft.SystemCenter.Advisor.DataConnector** a je automaticky nakonfigurován s předplatným, které předává všechny výstrahy generované z instancí všech tříd ve skupině pro správu do Azure Monitor. Druhý konektor je **Poradce Konektor**, který je zodpovědný za komunikaci s Azure Monitor a sdílení dat.
* Agenti a skupiny, které jste vybrali pro shromažďování dat ve skupině pro správu, budou přidáni do **Skupiny monitorovacích serverů služby Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizace sad Management Pack

Po dokončení konfigurace vytvoří skupina pro správu operations manageru připojení k Azure Monitoru. Server pro správu se synchronizuje s webovou službou a přijímá aktualizované konfigurační informace ve formě sad Management Pack pro vámi povolená řešení, která se integrují s nástrojem Operations Manager. Nástroj Operations Manager zkontroluje aktualizace těchto sad Management Pack a automaticky je stáhne a importuje, jakmile budou k dispozici. Toto chování řídí obzvláště dvě pravidla:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – aktualizuje základní sady pro správu Azure Monitoru. Ve výchozím nastavení se spouští každých 12 hodin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Aktualizuje sady Management Pack řešení, které jsou povolené ve vašem pracovním prostoru. Ve výchozím nastavení se spouští každých pět (5) minut.

Tato dvě pravidla můžete přepsat, abyste zabránili automatickému stahování jejich zakázáním, nebo upravit frekvenci, jak často se server pro správu synchronizuje s Azure Monitorem, abyste zjistili, jestli je k dispozici nová sada Management Pack a měla by být stažena. Podle kroků [pro přepsání pravidla nebo monitorování](https://technet.microsoft.com/library/hh212869.aspx) změňte parametr **Frekvence** na jinou hodnotou v sekundách (pokud chcete změnit plán synchronizace) nebo přepněte parametr **Povoleno** (pokud chcete pravidla zakázat). Přepsané hodnoty zacilte na všechny objekty třídy Skupina správy nástroje Operations Manager.

Chcete-li pokračovat v dodržování stávajícího procesu řízení změn pro řízení verzí sady Management Pack ve skupině pro správu výroby, můžete zakázat pravidla a povolit je v určitých časech, kdy jsou povoleny aktualizace. Pokud máte ve svém prostředí skupiny pro správu určenou pro vývoj nebo kontrolu kvality a ta má připojení k internetu, můžete pro podporu tohoto scénáře u této skupiny nakonfigurovat pracovní prostor služby Log Analytics. To vám umožní zkontrolovat a vyhodnotit iterativní verze sad Management Pack Azure Monitor před jejich uvolněním do vaší skupiny pro správu produkčního prostředí.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Převedení skupiny nástroje Operations Manager na nový pracovní prostor služby Log Analytics

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** a pak vytvořte pracovní prostor.  
1. Otevřete konzolu nástroje Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager, a vyberte pracovní prostor **Správa**.
1. Rozbalte analýzu protokolů a vyberte **Připojení**.
1. Vyberte odkaz **Znovu nakonfigurovat službu Operations Management Suite** uprostřed podokna.
1. Postupujte podle **Průvodce registrací analýzy protokolů** a zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružen k vašemu novému pracovnímu prostoru Log Analytics.

   > [!NOTE]
   > Stránka **Průvodce registrací ve službě Operations Management Suite: výběr pracovního prostoru** uvádí existující pracovní prostor, který se používá.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Ověření integrace nástroje Operations Manager pomocí azure monitoru

Existuje několik různých způsobů, jak můžete ověřit, že integrace Azure Monitor u Operations Manageru je úspěšná.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Ověření integrace z portálu Azure Portal

1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu.
1. V seznamu pracovních prostorů Log Analytics vyberte příslušný pracovní prostor.  
1. Vyberte **Upřesňující nastavení**, **Připojené zdroje**a potom **System Center**.
1. V tabulce v části System Center Operations Manager byste měli vidět název skupiny pro správu i s počtem agentů a stavem uvádějícím, když byla naposledy přijata data.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Ověření integrace z konzoly Operations Console

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Vyberte **Sady Management Pack** a do textového pole **Hledat** zadejte **Advisor** nebo **Intelligence**.
1. V závislosti na řešeních, která jste aktivovali, se ve výsledcích hledání zobrazí odpovídající sada Management Pack.  Pokud jste například povolili řešení Alert Management, bude v seznamu sada Microsoft System Center Advisor Alert Management.
1. Ze zobrazení **Sledování** přejděte do zobrazení **Operations Management Suite\Stav systému**.  Vyberte server pro správu v podokně **Stav serveru pro správu** a v podokně **Podrobné zobrazení** ověřte, že hodnota vlastnosti **Identifikátor URI ověřovací služby** odpovídá příslušnému ID pracovního prostoru služby Log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Odebrání integrace pomocí Azure Monitoru

Když už integraci mezi vaší skupinou pro správu nástroje Operations Manager a pracovním prostorem služby Log Analytics nebudete potřebovat, bude třeba ke správnému odebrání připojení a konfigurace ze skupiny provést několik kroků. Následující postup má aktualizovat pracovní prostor Log Analytics odstraněním odkazu na vaši skupinu pro správu, odstranit konektory Azure Monitor a potom odstranit sady Management Pack podporující integraci se službou.  

Sady Management Pack pro řešení, která jste povolili a která se integrují s Operations Managerem a sadami management packy potřebnými pro podporu integrace s Azure Monitorem, nelze snadno odstranit ze skupiny pro správu. Důvodem je, že některé sady pro správu Azure Monitoru mají závislosti na jiných souvisejících sadách Management Pack. Pokud chcete odstranit sady Management Pack se závislostmi na jiných sadách, stáhněte si skript pro [odebrání sady Management Pack se závislostmi](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z webu TechNet Script Center.  

1. Otevřete prostředí příkazového řádku pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.

    > [!WARNING]
    > Než budete pokračovat, ujistěte se, že nemáte žádné vlastní sady Management Pack se slovem Advisor nebo IntelligencePack v názvu, protože jinak tímto postupem ze skupiny pro správu odstraníte i je.
    >

1. V prostředí příkazového řádku zadejte `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pak zadejte `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pokud chcete odstranit všechny zbývající sady Management Pack, které jsou závislé na jiných sadách pro System Center Advisor, použijte skript *RecursiveRemove.ps1*, který jste si předtím stáhli z webu TechNet Script Center.  

    > [!NOTE]
    > Krok k odebrání sad management pack Poradce pomocí prostředí PowerShell automaticky neodstraní poradce pro systém Microsoft System Center Advisor nebo interní sady management Pack Microsoft System Center Advisor.  Nepokoušejte se je odstranit.  
    >  

1. Otevřete konzolu Operations Console pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.
1. V části **Správa** vyberte uzel **Sady Management Pack**, do pole **Hledat** zadejte **Advisor** a ověřte, že ve vaší skupině pro správu jsou stále naimportovány následující sady Management Pack:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Na webu Azure Portal klikněte na dlaždici **Nastavení.**
1. Vyberte **Připojené zdroje**.
1. V tabulce v části System Center Operations Manager by se měl zobrazit název skupiny pro správu, kterou chcete odebrat z pracovního prostoru. Ve sloupci **Poslední data** klikněte na tlačítko **Odebrat**.  

    > [!NOTE]
    > Pokud u připojené skupiny pro správu nebyla zjištěna žádná aktivita, bude odkaz **Odebrat** k dispozici až po uplynutí 14 dnů.  
    >

1. Otevře se okno s žádostí o potvrzení, že chcete pokračovat v odebírání.  Pokud chcete pokračovat, klikněte na **Ano**.

Pokud chcete odstranit oba konektory (Microsoft.SystemCenter.Advisor.DataConnector a Konektor služby Advisor), uložte si níže uvedený skript prostředí PowerShell do počítače a spusťte ho podle následujících příkladů:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Pokud počítač, ze kterého tento skript spouštíte, nepředstavuje server pro správu, měl by mít nainstalované prostředí příkazového řádku pro Operations Manager v závislosti na verzi vaší skupiny pro správu.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Pokud v budoucnu plánujete znovu připojit skupinu pro správu k pracovnímu prostoru `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` Log Analytics, budete muset znovu importovat soubor sady Management Pack. V závislosti na verzi nástroje System Center Operations Manager nasazené ve vašem prostředí ho najdete v následujícím umístění:

* Na zdrojovém médiu ve složce `\ManagementPacks` pro System Center 2016 – Operations Manager a vyšší.
* V nejnovější kumulativní aplikaci použité u vaší skupiny pro správu. Pro Operations Manager 2012, `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` zdrojová složka je a pro 2012 R2, je umístěn v `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Další kroky

Pokud chcete přidat funkce a shromáždit data, přečtěte si informace [o přidání řešení Azure Monitoru z Galerie řešení](../../azure-monitor/insights/solutions.md).
