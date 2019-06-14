---
title: Připojení nástroje Operations Manager k monitorování Azure | Dokumentace Microsoftu
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
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 19ae3322d26447cf7c7dd94d06f073ccf013738e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60255101"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Připojení nástroje Operations Manager k monitorování Azure

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Chcete-li zachovat stávající investice do [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) a rozšířené možnosti pomocí služby Azure Monitor, pracovního prostoru Log Analytics můžete integrovat nástroje Operations Manager. Díky tomu, že využít příležitosti protokolů ve službě Azure Monitor přitom dál používat nástroj Operations Manager do:

* Monitorování stavu vašich IT služeb pomocí nástroje Operations Manager
* Údržba integrace s vašimi řešeními ITSM pro podporu správy incidentů a problémů
* Správa životního cyklu agentů nasazených do virtuálních počítačů IaaS v místním prostředí a ve veřejném cloudu, které monitorujete pomocí nástroje Operations Manager

Integrace s nástrojem System Center Operations Manager přidá hodnotu ke strategii pro operace služby s použitím tak rychle a efektivně služby Azure Monitor v shromažďování, ukládání a analýzu dat protokolu z nástroje Operations Manager. Azure Monitor protokolu dotazy nápovědy korelaci a práce směrem k identifikaci chyb, problémů a zpřístupnění opakování podporu stávajícího procesu správy problémů. V nástroji Operations Manager complimenting přináší flexibilitu dotazovací modul pro zjištění výkonu, událostí a upozornění, že data pomocí podrobných řídicích panelů a možnosti vytváření sestav k získání těchto dat smysluplnějšími způsoby ukazuje sílu Azure Monitor.

Agenty posílající sestavy skupině pro správu nástroje Operations Manager shromažďování dat ze serverů na základě [zdroje dat Log Analytics](agent-data-sources.md) a řešení, které jste povolili ve vašem pracovním prostoru. V závislosti na řešení povolené, jejich data jsou buď odesílají přímo ze serveru pro správu Operations Manageru ke službě, nebo z důvodu objem dat shromážděných v systém spravovaný bez agenta odesílají přímo z agenta k pracovnímu prostoru Log Analytics. Server pro správu předává data přímo do služby; nikdy je nezapisuje do provozní databáze nebo databáze datového skladu. Když server pro správu ztratí spojení s Azure Monitor, se data uloží do místní mezipaměti dokud znovu navázat komunikaci. Pokud server pro správu je v režimu offline kvůli plánované údržbě nebo neplánované výpadky, jinému serveru pro správu ve skupině pro správu obnoví připojení k službě Azure Monitor.  

Následující diagram znázorňuje připojení mezi servery pro správu a agenty ve skupině pro správu System Center Operations Manageru a Azure Monitor, včetně směr a porty.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Pokud zásady zabezpečení IT neumožňují počítačů ve vaší síti pro připojení k Internetu, servery pro správu je nakonfigurovat pro připojení k bráně Log Analytics pro příjem informací o konfiguraci a posílání shromážděných dat v závislosti na řešení povolené. Další informace a postup konfigurace skupiny pro správu nástroje Operations Manager ke komunikaci přes bránu Log Analytics pro monitorování Azure, najdete v článku [připojení počítačů k Azure monitorování pomocí Log Analytics gateway](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Požadavky 

Než začnete, zkontrolujte následující požadavky.

* Azure Monitor podporuje pouze System Center Operations Manageru 2016 nebo novější, Operations Manager 2012 SP1 UR6 nebo vyšší a Operations Manager 2012 R2 UR2 nebo novější. V nástrojích Operations Manager 2012 SP1 UR7 a Operations Manager 2012 R2 UR3 je přidaná podpora proxy serverů.
* Integrace nástroje System Center Operations Manageru 2016 s využitím cloudu pro státní správu USA vyžaduje aktualizovaný balíček správy Advisor zahrnuty s kumulativní aktualizací 2 nebo novější. System Center Operations Manager 2012 R2 vyžaduje aktualizovaný balíček správy Advisor zahrnuty s kumulativní aktualizací 3 nebo novější.
* Všichni agenti nástroje Operations Manager musí splňovat minimální požadavky na podporu. Ujistěte se, že jsou agenti na minimální aktualizace, jinak komunikaci agenta Windows může selhat a způsobit chyby v protokolu událostí nástroje Operations Manager.
* Pracovní prostor služby Log Analytics. Další informace najdete v tématu [přehled pracovního prostoru Log Analytics](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Ověření pro Azure pomocí účtu, který je členem skupiny [role Přispěvatel Log Analytics](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Nedávné změny rozhraní API Správce Azure zabrání zákazníci nebudou moct úspěšně konfigurovat integraci mezi jejich skupiny pro správu a monitorování Azure poprvé. Pro zákazníky, kteří už integrovali jejich skupiny pro správu ve službě nejsou vliv, pokud je potřeba překonfigurovat existující připojení.  
>V následujících verzích nástroje Operations Manager byla vydala novou sadu management pack:
>  
>* Pro System Center Operations Manageru 1801 stažení management packu z [zde](https://www.microsoft.com/download/details.aspx?id=57173)  
>* System Center 2016 – Operations Manager, stáhněte si sadu management pack z [zde](https://www.microsoft.com/download/details.aspx?id=57172)  
>* System Center Operations Manager 2012 R2, stáhněte si sadu management pack z [zde](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Tato aktualizace management pack neplatí pro System Center Operations Manager 1807, což je o verzi aktualizace z verze 1801 a úplné sestavení produktu.   

### <a name="network"></a>Síť

Informace o seznamu proxy a firewallu informace o konfiguraci vyžadované pro agenta nástroje Operations Manager, servery pro správu a konzole Operations console pro komunikaci se službou Azure Monitor níže. Provoz z jednotlivých komponent je výstupní z vaší sítě do Azure monitoru.   

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
|**Konzola nástroje Operations Manager do Azure monitoru**|||  
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

– Pomáhat zajistit zabezpečení dat při přenosu do Azure monitoru, důrazně doporučujeme, abyste ke konfiguraci agenta a Správa skupiny používat alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**. Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-azure-monitor"></a>Propojení Operations Manageru do Azure monitoru

Provedením následujícího postupu nakonfigurujete skupinu pro správu nástroje Operations Manager na připojení k jednomu z vašich pracovních prostorů Log Analytics.

Při počáteční registraci skupiny pro správu Operations Manageru k pracovnímu prostoru Log Analytics můžete zadat konfiguraci proxy serveru pro skupinu pro správu není k dispozici v konzoli Operations console.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Chcete-li tento problém obejít, budete muset aktualizovat konfiguraci proxy serveru systému pomocí nástroje Netsh v systému konzole Operations console z systémem konfiguraci integrace a všechny servery pro správu ve skupině pro správu.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.
   a. Přejděte na **Start** a typ **cmd**.
   b. Klikněte pravým tlačítkem na **příkazového řádku** a vyberte spustit jako správce **.
1. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení následujících kroků k integraci se službou Azure Monitor, můžete odebrat konfiguraci spuštěním `netsh winhttp reset proxy` a pak použít **konfigurovat proxy server** možnost zadat proxy server nebo protokolu v konzoli Operations console Server brány Analytics. 

1. V konzole nástroje Operations Manager vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Přejděte na odkaz **Zaregistrovat ve službě Operations Management Suite**.
1. Na **Průvodce registrací v Operations Management Suite: Ověřování** stránky, zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je spojen s vaším předplatným OMS a klikněte na tlačítko **přihlášení**.

   >[!NOTE]
   >Název Operations Management Suite je vyřazený. 
   
1. Když jste úspěšně ověřeni, dále **Průvodce registrací v Operations Management Suite: Vyberte pracovní prostor** stránce se zobrazí výzva k výběru tenantovi Azure, předplatné a pracovní prostor Log Analytics. Pokud máte více než jeden pracovní prostor, vyberte z rozevíracího seznamu ten, který chcete zaregistrovat ve skupině pro správu nástroje Operations Manager, a pak klikněte na **Další**.
   
   > [!NOTE]
   > Operations Manager podporuje vždy jenom jeden pracovní prostor služby Log Analytics. Připojení a počítačů, které jste zaregistrovali do Azure monitoru s předchozím pracovním prostoru se odeberou ze služby Azure Monitor.
   > 
   > 
1. Na **Průvodce registrací v Operations Management Suite: Souhrn** stránce Potvrďte nastavení a pokud jsou správné, klikněte na tlačítko **vytvořit**.
1. Na **Průvodce registrací v Operations Management Suite: Dokončit** klikněte na **Zavřít**.

### <a name="add-agent-managed-computers"></a>Přidání počítačů spravovaných agenty

Jakmile nakonfigurujete integraci s pracovního prostoru Log Analytics, pouze vytvoří připojení ke službě, je nebyla shromážděna žádná data z agentů odesílajících sestavy do vaší skupiny pro správu. To se neprovede až po dokončení konfigurace, které konkrétní agentem spravované počítače shromažďovat data protokolu pro monitorování Azure. Objekty počítačů můžete vybrat buď jednotlivě, nebo jako skupinu obsahující objekty počítačů s Windows. Nemůžete vybrat skupinu, která obsahuje instance jiné třídy, jako třeba logické disky nebo databáze SQL.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Klikněte na odkaz **Přidat počítač či skupinu** pod záhlavím Akce na pravé straně podokna.
1. V dialogovém okně **Hledání počítače** můžete vyhledat počítače nebo skupiny sledované nástrojem Operations Manager. Vyberte počítače nebo skupiny mohl připojit k Azure Monitor, klikněte na **přidat**a potom klikněte na tlačítko **OK**.

Počítače a skupiny, které jsou nakonfigurované na shromažďování dat, si můžete zobrazit z uzlu Spravované počítače pod položkou Operations Management Suite v pracovním prostoru **Správa** v konzole Operations Console. Odsud můžete počítače a skupiny podle potřeby přidávat nebo odebírat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurace nastavení proxy v konzole Operations Console

Pokud je interní proxy server mezi skupinou správy a monitorování Azure, proveďte následující kroky. Tato nastavení jsou centrálně spravovaná ze skupiny pro správu a distribuovaných systémech spravovaných agentem, které jsou zahrnuty v oboru shromažďovat data protokolu pro monitorování Azure.  To je výhodné v případech, kdy určitá řešení obcházejí server pro správu a odesílají data přímo do služby.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte Operations Management Suite a pak klikněte na **Připojení**.
1. V zobrazení Připojení OMS klikněte na **Konfigurovat proxy server**.
1. Na **Průvodce Operations Management Suite: Proxy Server** stránce **použít proxy server pro přístup k Operations Management Suite**, a poté zadejte adresu URL s číslem portu, například http://corpproxy:80 a potom klikněte na tlačítko **Dokončit** .

Pokud váš proxy server vyžaduje ověření, proveďte následující kroky při konfiguraci přihlašovacích údajů a nastavení, které je nutné rozšířit i na spravované počítače, které se hlásí do Azure monitoru ve skupině pro správu.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. V části **Konfigurace RunAs** vyberte **Profily**.
1. Otevřete profil **Proxy server profilu Spustit jako služby System Center Advisor**.
1. Pokud chcete použít účet Spustit jako, klikněte v Průvodci profilem Spustit jako na Přidat. [Účet Spustit jako](https://technet.microsoft.com/library/hh321655.aspx) můžete vytvořit, případně použít existující účet. Tento účet musí mít dostatečná oprávnění k průchodu proxy serverem.
1. Pokud chcete nastavit účet k provedení správy, zvolte **Vybraná třída, skupina nebo objekt**, klikněte na **Vybrat** a potom na **Skupina**, čímž otevřete okno **Hledání skupiny**.
1. Vyhledejte a pak vyberte **Skupina monitorovacích serverů služby Microsoft System Center Advisor**. Po výběru skupiny zavřete kliknutím na **OK** okno **Hledání skupiny**.
1. Kliknutím na **OK** zavřete okno **Přidat účet Spustit jako**.
1. Kliknutím na **Uložit** dokončete průvodce a uložte změny.

Po vytvoření připojení a nakonfigurujete kterých agentů se shromažďování a vytváření sestav dat protokolu do Azure monitoru, platí následující konfigurace ve skupině pro správu, ne tedy nutně v pořadí:

* Vytvoří se účet Spustit jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Je přidružený k profilu Spustit jako **Objekt blob profilu Spustit jako služby Microsoft System Center Advisor** a je zacílený na dvě třídy: **Server kolekcí** a **Skupina správy nástroje Operations Manager**.
* Vytvoří se dva konektory.  První jmenuje **Microsoft.SystemCenter.Advisor.DataConnector** a je automaticky nakonfigurovaný pomocí předplatné, které předává všech výstrah generovaných z instancí všechny třídy ve skupině pro správu do Azure monitoru. Druhý konektor je **konektor služby Advisor**, která zodpovídá za komunikaci se službou Azure Monitor a sdílení dat.
* Agenti a skupiny, které jste vybrali pro shromažďování dat ve skupině pro správu, budou přidáni do **Skupiny monitorovacích serverů služby Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizace sad Management Pack

Po dokončení konfigurace skupiny pro správu nástroje Operations Manager vytvoří připojení prostřednictvím služby Azure Monitor. Server pro správu se synchronizuje s webovou službou a přijímá aktualizované konfigurační informace ve formě sad Management Pack pro vámi povolená řešení, která se integrují s nástrojem Operations Manager. Nástroj Operations Manager zjišťuje dostupnost aktualizací těchto sad management Pack a automaticky stáhnout a naimportuje je, když jsou k dispozici. Toto chování řídí obzvláště dvě pravidla:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – aktualizuje základní sady management Pack pro monitorování Azure. Ve výchozím nastavení se spouští každých 12 hodin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Aktualizuje sady Management Pack řešení, které jsou povolené ve vašem pracovním prostoru. Ve výchozím nastavení se spouští každých pět (5) minut.

Můžete přepsat tyto dvě pravidla buď zabránit automatické stahování tím, že je zakážete nebo upravit frekvenci jak často server pro správu synchronizuje se službou Azure Monitorto určení, zda je k dispozici nové sady management pack a mají být stažené. Podle kroků [pro přepsání pravidla nebo monitorování](https://technet.microsoft.com/library/hh212869.aspx) změňte parametr **Frekvence** na jinou hodnotou v sekundách (pokud chcete změnit plán synchronizace) nebo přepněte parametr **Povoleno** (pokud chcete pravidla zakázat). Přepsané hodnoty zacilte na všechny objekty třídy Skupina správy nástroje Operations Manager.

Chcete-li pokračovat po vaší existující proces řízení změn řízení verzí management pack v provozní skupině pro správu, můžete zakázat pravidla a povolit v určité době, kdy jsou aktualizace povoleny. Pokud máte ve svém prostředí skupiny pro správu určenou pro vývoj nebo kontrolu kvality a ta má připojení k internetu, můžete pro podporu tohoto scénáře u této skupiny nakonfigurovat pracovní prostor služby Log Analytics. To umožňuje kontrola a vyhodnocení iterativní verzí sad management Pack monitorování Azure před uvolněním v provozní skupině pro správu.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Převedení skupiny nástroje Operations Manager na nový pracovní prostor služby Log Analytics

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** a pak vytvořte pracovní prostor.  
1. Otevřete konzolu nástroje Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager, a vyberte pracovní prostor **Správa**.
1. Rozbalte Log Analytics a vyberte **připojení**.
1. Vyberte odkaz **Znovu nakonfigurovat službu Operations Management Suite** uprostřed podokna.
1. Postupujte podle **Průvodce registrací v Log Analytics** a zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k novému pracovnímu prostoru Log Analytics.
   
   > [!NOTE]
   > **Průvodce registrací v Operations Management Suite: Vyberte pracovní prostor** stránky zobrazí existující pracovní prostor, který se používá.
   > 
   > 

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Ověření integrace nástroje Operations Manager pomocí Azure monitoru

Existuje několik různých způsobů, jak můžete ověřit, že Azure Monitor k integraci nástroje Operations Manager byla úspěšně dokončena.

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

## <a name="remove-integration-with-azure-monitor"></a>Odebrat integraci se službou Azure Monitor

Když už integraci mezi vaší skupinou pro správu nástroje Operations Manager a pracovním prostorem služby Log Analytics nebudete potřebovat, bude třeba ke správnému odebrání připojení a konfigurace ze skupiny provést několik kroků. Následující postup se můžete aktualizovat tak, že odstraníte odkaz na vaši skupinu pro správu pracovního prostoru Log Analytics, odstraňte konektory Azure Monitor a pak odstraňte sady management Pack podporuje integraci se službou.  

Sady Management Pack pro řešení povolíte, které se integrují s nástrojem Operations Manager a sady management Pack, který je potřeba pro podporu integrace s Azure Monitor nelze snadno odstranit ze skupiny pro správu. Je to proto, že některé sady management Pack Azure monitoru mají závislosti na jiných sadách souvisejících management Pack. Pokud chcete odstranit sady Management Pack se závislostmi na jiných sadách, stáhněte si skript pro [odebrání sady Management Pack se závislostmi](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z webu TechNet Script Center.  

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

V budoucnu Pokud máte v plánu na opětovné připojení skupiny pro správu k pracovnímu prostoru Log Analytics, budete muset znovu naimportovat `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` soubor sady management pack. V závislosti na verzi nástroje System Center Operations Manager nasazené ve vašem prostředí ho najdete v následujícím umístění:

* Na zdrojovém médiu ve složce `\ManagementPacks` pro System Center 2016 – Operations Manager a vyšší.
* V nejnovější kumulativní aplikaci použité u vaší skupiny pro správu. Pro nástroj Operations Manager 2012, je zdrojové složce `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` a 2012 R2, je umístěn v `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Další postup

Přidání funkcí a shromažďování dat, naleznete v tématu [řešení pro monitorování Azure přidat z Galerie řešení](../../azure-monitor/insights/solutions.md).