---
title: Připojení nástroje Operations Manager ke službě Log Analytics | Microsoft Docs
description: Nástroj Operations Manager můžete integrovat do svého pracovního prostoru, abyste nepřišli o své investice do systému System Center Operations Manager a mohli využívat rozšířené možnosti Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: eed4029e6b7e8c7bea4bdcd39193748321272be5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728204"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Připojení nástroje Operations Manager ke službě Log Analytics
Chcete-li zachovat stávající investice do [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) a použití rozšířené možnosti službou Log Analytics, můžete integrovat nástroje Operations Manager se službou pracovního prostoru Log Analytics. Díky tomu budete moct využívat to, co nabízí Log Analytics, a současně nadále používat nástroj Operations Manager pro tyto činnosti:

* Monitorování stavu vašich IT služeb pomocí nástroje Operations Manager
* Údržba integrace s vašimi řešeními ITSM pro podporu správy incidentů a problémů
* Správa životního cyklu agentů nasazených do virtuálních počítačů IaaS v místním prostředí a ve veřejném cloudu, které monitorujete pomocí nástroje Operations Manager

Integrace s nástrojem System Center Operations Manager zvyšuje hodnotu strategie operací služby tím, že využívá rychlost a efektivitu Log Analytics při shromažďování, ukládání a analýzách dat z nástroje Operations Manager. Služba Log Analytics usnadňuje hledání korelací a práci na identifikaci příčin problémů a odhalování opakovaných vzorců, což představuje podporu pro váš stávající proces správy problémů. Síla Log Analytics jako doplňku nástroje Operations Manager spočívá ve flexibilitě vyhledávacího webu, který se používá při zkoumání dat o výkonu, událostech a výstrahách, a v bohatých řídicích panelech a možnostech vytváření sestav, ve kterých je pak možné tato data smysluplně prezentovat.

Agenti odesílající data do skupiny pro správu nástroje Operations Manager shromažďují data z vašich serverů, a to na základě zdrojů dat a řešení služby Log Analytics, které jste ve svém pracovním prostoru aktivovali. V závislosti na aktivovaných řešeních se tato data do dané služby odesílají buď přímo ze serveru pro správu nástroje Operations Manager, nebo se do Log Analytics kvůli objemu dat shromážděných v systému spravovaném agenty odesílají přímo z agenta. Server pro správu předává data přímo do služby; nikdy je nezapisuje do provozní databáze nebo databáze datového skladu. Pokud server pro správu ztratí spojení s Log Analytics, ukládá data místně do mezipaměti, dokud se komunikace s Log Analytics neobnoví. Pokud je server pro správu offline z důvodu plánované údržby nebo neplánovanému výpadku, naváže připojení k Log Analytics jiný server pro správu ve skupině pro správu.  

Následující diagram znázorňuje propojení mezi servery pro správu a agenty ve skupině pro správu nástroje System Center Operations Manager a službou Log Analytics, včetně směru a portů.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Pokud zásady zabezpečení IT neumožňují počítačů ve vaší síti pro připojení k Internetu, servery pro správu je nakonfigurovat pro připojení k bráně Log Analytics pro příjem informací o konfiguraci a posílání shromážděných dat v závislosti na řešení povolené. Další informace a postup konfigurace skupiny pro správu nástroje Operations Manager ke komunikaci přes bránu Log Analytics ke službě Log Analytics najdete v tématu [připojení počítačů ke službě Log Analytics pomocí Log Analytics gateway](../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Požadavky 
Než začnete, zkontrolujte následující požadavky.

* Log Analytics podporuje pouze System Center Operations Manager 1807, Operations Manager 1801, Operations Manager 2016, Operations Manager 2012 SP1 UR6 nebo vyšší a Operations Manager 2012 R2 UR2 nebo novější. V nástrojích Operations Manager 2012 SP1 UR7 a Operations Manager 2012 R2 UR3 je přidaná podpora proxy serverů.
* Všichni agenti nástroje Operations Manager musí splňovat minimální požadavky na podporu. Ujistěte se, že jsou agenti na minimální aktualizace, jinak komunikaci agenta Windows může selhat a způsobit chyby v protokolu událostí nástroje Operations Manager.
* Pracovní prostor Log Analytics. Další informace najdete v tématu [přehled pracovního prostoru Log Analytics](log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json).
* Ověření pro Azure pomocí účtu, který je členem skupiny [role Přispěvatel Log Analytics](log-analytics-manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Nedávné změny rozhraní API Správce Azure zabrání zákazníci nebudou moct úspěšně konfigurovat integraci mezi jejich skupiny pro správu a Log Analytics poprvé. Pro zákazníky, kteří už integrovali jejich skupiny pro správu ve službě nejsou vliv, pokud je potřeba překonfigurovat existující připojení.  
>V následujících verzích nástroje Operations Manager byla vydala novou sadu management pack:
>  
>* Pro System Center Operations Manageru 1801 stažení management packu z [zde](https://www.microsoft.com/download/details.aspx?id=57173)  
>* System Center 2016 – Operations Manager, stáhněte si sadu management pack z [zde](https://www.microsoft.com/download/details.aspx?id=57172)  
>* System Center Operations Manager 2012 R2, stáhněte si sadu management pack z [zde](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Tato aktualizace management pack neplatí pro System Center Operations Manager 1807, což je o verzi aktualizace z verze 1801 a úplné sestavení produktu.   

### <a name="network"></a>Síť
V následujícím seznamu najdete konfiguraci pro proxy a firewall nutnou pro komunikaci agenta Operations Manager, serverů pro správu a konzoly Operations Console s Log Analytics. Přenosy z jednotlivých součástí odchází z vaší sítě do služby Log Analytics.   

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
|**Konzola nástroje Operations Manager ke službě Log Analytics**|||  
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
– Pomáhat zajistit zabezpečení dat při přenosu do služby Log Analytics, důrazně doporučujeme, abyste ke konfiguraci agenta a Správa skupiny používat alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**. Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-log-analytics"></a>Připojení nástroje Operations Manager ke službě Log Analytics
Provedením následujícího postupu nakonfigurujete skupinu pro správu nástroje Operations Manager na připojení k jednomu z vašich pracovních prostorů Log Analytics.

Při počáteční registraci skupiny pro správu Operations Manageru k pracovnímu prostoru Log Analytics můžete zadat konfiguraci proxy serveru pro skupinu pro správu není k dispozici v konzoli Operations console.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Chcete-li tento problém obejít, budete muset aktualizovat konfiguraci proxy serveru systému pomocí nástroje Netsh v systému konzole Operations console z systémem konfiguraci integrace a všechny servery pro správu ve skupině pro správu.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.
   a. Přejděte na **Start** a typ **cmd**.
   b. Klikněte pravým tlačítkem na **příkazového řádku** a vyberte spustit jako správce **.
1. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení následujících kroků k integraci s Log Analytics, můžete odebrat konfiguraci spuštěním `netsh winhttp reset proxy` a pak použít **konfigurovat proxy server** možnost zadat proxy server nebo protokolu v konzoli Operations console Server brány Analytics. 

1. V konzole nástroje Operations Manager vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Přejděte na odkaz **Zaregistrovat ve službě Operations Management Suite**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite** zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k vašemu předplatnému OMS, a klikněte na **Přihlásit se**.

   >[!NOTE]
   >OMS se teď označuje jako Log Analytics.
   
1. Když jste úspěšně ověřeni, na **Průvodce registrací v Operations Management Suite: vybrat pracovní prostor** stránce se zobrazí výzva k výběru tenantovi Azure, předplatné a pracovní prostor Log Analytics. Pokud máte více než jeden pracovní prostor, vyberte z rozevíracího seznamu ten, který chcete zaregistrovat ve skupině pro správu nástroje Operations Manager, a pak klikněte na **Další**.
   
   > [!NOTE]
   > Operations Manager podporuje vždy jenom jeden pracovní prostor Log Analytics. Připojení a počítače, které byly v Log Analytics zaregistrované s předchozím pracovním prostorem, budou z Log Analytics odebrány.
   > 
   > 
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: souhrn** ověřte nastavení, a pokud je správné, klikněte na **Vytvořit**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: dokončení** klikněte na **Zavřít**.

### <a name="add-agent-managed-computers"></a>Přidání počítačů spravovaných agenty
Jakmile nakonfigurujete integraci s pracovního prostoru Log Analytics, pouze vytvoří připojení ke službě, je nebyla shromážděna žádná data z agentů odesílajících sestavy do vaší skupiny pro správu. To se neprovede až po dokončení konfigurace, které konkrétní počítače spravované bez agenta shromažďování dat pro Log Analytics. Objekty počítačů můžete vybrat buď jednotlivě, nebo jako skupinu obsahující objekty počítačů s Windows. Nemůžete vybrat skupinu, která obsahuje instance jiné třídy, jako třeba logické disky nebo databáze SQL.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Klikněte na odkaz **Přidat počítač či skupinu** pod záhlavím Akce na pravé straně podokna.
1. V dialogovém okně **Hledání počítače** můžete vyhledat počítače nebo skupiny sledované nástrojem Operations Manager. Vyberte počítače nebo skupiny, které chcete zahrnout do služby Log Analytics, a klikněte na **Přidat** a potom na **OK**.

Počítače a skupiny, které jsou nakonfigurované na shromažďování dat, si můžete zobrazit z uzlu Spravované počítače pod položkou Operations Management Suite v pracovním prostoru **Správa** v konzole Operations Console. Odsud můžete počítače a skupiny podle potřeby přidávat nebo odebírat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurace nastavení proxy v konzole Operations Console
Pokud je interní proxy server mezi skupinou správy a služba Log Analytics, proveďte následující kroky. Tato nastavení se řídí centrálně ze skupiny pro správu a distribuují se do systémů spravovaných agenty, které spadají do rozsahu shromažďování dat pro Log Analytics.  To je výhodné v případech, kdy určitá řešení obcházejí server pro správu a odesílají data přímo do služby.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte Operations Management Suite a pak klikněte na **Připojení**.
1. V zobrazení Připojení OMS klikněte na **Konfigurovat proxy server**.
1. Na stránce **Průvodce nastavením služby Operations Management Suite: proxy server** vyberte **Pro přístup ke službě Operations Management Suite použít proxy server**, potom zadejte adresu URL s číslem portu, například http://corpproxy:80, a pak klikněte na **Dokončit**.

Pokud váš proxy server vyžaduje ověření, proveďte následující kroky při konfiguraci přihlašovacích údajů a nastavení, které je nutné rozšířit i na spravované počítače, které se hlásí do Log Analytics ve skupině pro správu.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. V části **Konfigurace RunAs** vyberte **Profily**.
1. Otevřete profil **Proxy server profilu Spustit jako služby System Center Advisor**.
1. Pokud chcete použít účet Spustit jako, klikněte v Průvodci profilem Spustit jako na Přidat. [Účet Spustit jako](https://technet.microsoft.com/library/hh321655.aspx) můžete vytvořit, případně použít existující účet. Tento účet musí mít dostatečná oprávnění k průchodu proxy serverem.
1. Pokud chcete nastavit účet k provedení správy, zvolte **Vybraná třída, skupina nebo objekt**, klikněte na **Vybrat** a potom na **Skupina**, čímž otevřete okno **Hledání skupiny**.
1. Vyhledejte a pak vyberte **Skupina monitorovacích serverů služby Microsoft System Center Advisor**. Po výběru skupiny zavřete kliknutím na **OK** okno **Hledání skupiny**.
1. Kliknutím na **OK** zavřete okno **Přidat účet Spustit jako**.
1. Kliknutím na **Uložit** dokončete průvodce a uložte změny.

Po vytvoření připojení a konfiguraci agentů, kteří budou shromažďovat a odesílat data do Log Analytics, se ve skupině pro správu použije tato konfigurace (nemusí to být v tomto pořadí):

* Vytvoří se účet Spustit jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Je přidružený k profilu Spustit jako **Objekt blob profilu Spustit jako služby Microsoft System Center Advisor** a je zacílený na dvě třídy: **Server kolekcí** a **Skupina správy nástroje Operations Manager**.
* Vytvoří se dva konektory.  První má název **Microsoft.SystemCenter.Advisor.DataConnector** a je v něm automaticky nakonfigurovaný odběr, který předává všechny výstrahy generované z instancí všech tříd ve skupině pro správu do Log Analytics. Druhý konektor je **konektor služby Advisor**, který je zodpovědný za sdílení dat a komunikovat s Log Analytics.
* Agenti a skupiny, které jste vybrali pro shromažďování dat ve skupině pro správu, budou přidáni do **Skupiny monitorovacích serverů služby Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizace sad Management Pack
Po dokončení konfigurace naváže skupina pro správu nástroje Operations Manager připojení ke službě Log Analytics. Server pro správu se synchronizuje s webovou službou a přijímá aktualizované konfigurační informace ve formě sad Management Pack pro vámi povolená řešení, která se integrují s nástrojem Operations Manager. Nástroj Operations Manager zjišťuje dostupnost aktualizací těchto sad management Pack a automaticky stáhnout a naimportuje je, když jsou k dispozici. Toto chování řídí obzvláště dvě pravidla:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – Aktualizuje základní sady Management Pack pro Log Analytics. Ve výchozím nastavení se spouští každých 12 hodin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Aktualizuje sady Management Pack řešení, které jsou povolené ve vašem pracovním prostoru. Ve výchozím nastavení se spouští každých pět (5) minut.

Tyto dvě pravidla buď zabránit automatické stahování tím, že je zakážete nebo upravit frekvenci jak často server pro správu synchronizuje se službou Log Analytics pro určení, zda je k dispozici nové sady management pack a mají stažené můžete přepsat. Podle kroků [pro přepsání pravidla nebo monitorování](https://technet.microsoft.com/library/hh212869.aspx) změňte parametr **Frekvence** na jinou hodnotou v sekundách (pokud chcete změnit plán synchronizace) nebo přepněte parametr **Povoleno** (pokud chcete pravidla zakázat). Přepsané hodnoty zacilte na všechny objekty třídy Skupina správy nástroje Operations Manager.

Chcete-li pokračovat po vaší existující proces řízení změn řízení verzí management pack v provozní skupině pro správu, můžete zakázat pravidla a povolit v určité době, kdy jsou aktualizace povoleny. Pokud máte ve svém prostředí skupiny pro správu určenou pro vývoj nebo kontrolu kvality a ta má připojení k internetu, můžete pro podporu tohoto scénáře u této skupiny nakonfigurovat pracovní prostor Log Analytics. To vám umožní kontrolovat a vyhodnocovat iterativní verze sad Management Pack pro Log Analytics před jejich uvolněním pro provozní skupinu pro správu.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Převedení skupiny nástroje Operations Manager na nový pracovní prostor Log Analytics
1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** a pak vytvořte pracovní prostor.  
1. Otevřete konzolu nástroje Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager, a vyberte pracovní prostor **Správa**.
1. Rozbalte Log Analytics a vyberte **připojení**.
1. Vyberte odkaz **Znovu nakonfigurovat službu Operations Management Suite** uprostřed podokna.
1. Postupujte podle **Průvodce registrací v Log Analytics** a zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k novému pracovnímu prostoru Log Analytics.
   
   > [!NOTE]
   > Stránka **Průvodce registrací ve službě Operations Management Suite: výběr pracovního prostoru** uvádí existující pracovní prostor, který se používá.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Ověření integrace nástroje Operations Manager se službou Log Analytics
Existuje několik různých způsobů, jak ověřit, že integrace Log Analytics s nástrojem Operations Manager proběhla úspěšně.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Ověření integrace z portálu Azure Portal
1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu.
1. V seznamu pracovních prostorů Log Analytics vyberte příslušný pracovní prostor.  
1. Vyberte **Upřesňující nastavení**, **Připojené zdroje**a potom **System Center**. 
1. V tabulce v části System Center Operations Manager byste měli vidět název skupiny pro správu i s počtem agentů a stavem uvádějícím, když byla naposledy přijata data.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Ověření integrace z konzoly Operations Console
1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Vyberte **Sady Management Pack** a do textového pole **Hledat** zadejte **Advisor** nebo **Intelligence**.
1. V závislosti na řešeních, která jste aktivovali, se ve výsledcích hledání zobrazí odpovídající sada Management Pack.  Pokud jste například povolili řešení Alert Management, bude v seznamu sada Microsoft System Center Advisor Alert Management.
1. Ze zobrazení **Sledování** přejděte do zobrazení **Operations Management Suite\Stav systému**.  Vyberte server pro správu v podokně **Stav serveru pro správu** a v podokně **Podrobné zobrazení** ověřte, že hodnota vlastnosti **Identifikátor URI ověřovací služby** odpovídá příslušnému ID pracovního prostoru Log Analytics.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Odebrání integrace se službou Log Analytics
Když už integraci mezi vaší skupinou pro správu nástroje Operations Manager a pracovním prostorem Log Analytics nebudete potřebovat, bude třeba ke správnému odebrání připojení a konfigurace ze skupiny provést několik kroků. V následujícím postupu aktualizujete pracovní prostor Log Analytics odstraněním odkazu na skupinu pro správu, odstraníte konektory pro Log Analytics a pak odstraníte sady Management Pack podporující integraci s touto službou.  

Sady Management Pack pro vámi aktivovaná řešení, které se integrují s nástrojem Operations Manager, a sady Management Pack potřebné pro podporu integrace se službou Log Analytics nejde ze skupiny pro správu snadno odstranit. Je to proto, že některé sady Management Pack pro Log Analytics mají závislosti v jiných souvisejících sadách. Pokud chcete odstranit sady Management Pack se závislostmi na jiných sadách, stáhněte si skript pro [odebrání sady Management Pack se závislostmi](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z webu TechNet Script Center.  

1. Otevřete prostředí příkazového řádku pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.
   
    > [!WARNING]
    > Než budete pokračovat, ujistěte se, že nemáte žádné vlastní sady Management Pack se slovem Advisor nebo IntelligencePack v názvu, protože jinak tímto postupem ze skupiny pro správu odstraníte i je.
    > 

1. V prostředí příkazového řádku zadejte `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pak zadejte `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pokud chcete odstranit všechny zbývající sady Management Pack, které jsou závislé na jiných sadách pro System Center Advisor, použijte skript *RecursiveRemove.ps1*, který jste si předtím stáhli z webu TechNet Script Center.  
 
    > [!NOTE]
    > Krok odebrat sady Advisor management Pack s prostředím PowerShell neodstraní automaticky sady management Pack Microsoft System Center Advisor nebo Microsoft System Center Advisor interní.  Nepokoušejte se k jejich odstranění.  
    >  

1. Otevřete konzolu Operations Console pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.
1. V části **Správa** vyberte uzel **Sady Management Pack**, do pole **Hledat** zadejte **Advisor** a ověřte, že ve vaší skupině pro správu jsou stále naimportovány následující sady Management Pack:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Na webu Azure Portal, klikněte na tlačítko **nastavení** dlaždici.
1. Vyberte **připojené zdroje**.
1. V tabulce v části System Center Operations Manager měli byste vidět název skupiny pro správu, kterou chcete odebrat z pracovního prostoru. Ve sloupci **Poslední data** klikněte na tlačítko **Odebrat**.  
   
    > [!NOTE]
    > Pokud u připojené skupiny pro správu nebyla zjištěna žádná aktivita, bude odkaz **Odebrat** k dispozici až po uplynutí 14 dnů.  
    > 

1. Otevře se okno s žádostí o potvrzení, že chcete pokračovat v odebírání.  Pokud chcete pokračovat, klikněte na **Ano**. 

Pokud chcete odstranit oba konektory (Microsoft.SystemCenter.Advisor.DataConnector a Konektor služby Advisor), uložte si níže uvedený skript prostředí PowerShell do počítače a spusťte ho podle následujících příkladů:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Pokud počítač, ze kterého tento skript spouštíte, nepředstavuje server pro správu, měl by mít nainstalované prostředí příkazového řádku pro Operations Manager v závislosti na verzi vaší skupiny pro správu.
> 
> 

```
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

V budoucnu Pokud máte v plánu na opětovné připojení skupiny pro správu k pracovnímu prostoru Log Analytics, budete muset znovu naimportovat `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` soubor sady management pack. V závislosti na verzi nástroje System Center Operations Manager nasazené ve vašem prostředí ho najdete v následujícím umístění:

* Na zdrojovém médiu ve složce `\ManagementPacks` pro System Center 2016 – Operations Manager a vyšší.
* V nejnovější kumulativní aplikaci použité u vaší skupiny pro správu. V případě verze Operations Manager 2012 jde o zdrojovou složku ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` a v případě verze 2012 R2 jde o umístění `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Další postup
Informace o přidávání funkcí a shromažďování dat najdete v článku [Přidání řešení Log Analytics z galerie řešení](../azure-monitor/insights/solutions.md).


