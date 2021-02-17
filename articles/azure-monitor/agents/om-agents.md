---
title: Připojit Operations Manager k Azure Monitor | Microsoft Docs
description: Nástroj Operations Manager můžete integrovat do svého pracovního prostoru, abyste nepřišli o své investice do systému System Center Operations Manager a mohli využívat rozšířené možnosti Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: f9418b9a4bac9c458c530b246f7400ac067f5623
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611194"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Připojit Operations Manager k Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud chcete zachovat stávající investice do [System Center Operations Manager](/system-center/scom/key-concepts) a využívat rozšířené možnosti s Azure monitor, můžete Operations Manager s pracovním prostorem Log Analytics integrovat. Díky tomu můžete využívat možnosti protokolů v Azure Monitor a přitom dál používat Operations Manager k těmto akcím:

* Monitorování stavu vašich IT služeb pomocí nástroje Operations Manager
* Údržba integrace s vašimi řešeními ITSM pro podporu správy incidentů a problémů
* Správa životního cyklu agentů nasazených do virtuálních počítačů IaaS v místním prostředí a ve veřejném cloudu, které monitorujete pomocí nástroje Operations Manager

Integrace s System Center Operations Manager přidává k strategii operací služby hodnotu pomocí rychlosti a efektivity Azure Monitor při shromažďování, ukládání a analýze dat protokolů z Operations Manager. Azure Monitor dotazy protokolu pomůžou korelovat a pracovat směrem k identifikaci chyb problémů a zpřístupnění opakování v podpoře stávajícího procesu správy problémů. Flexibilita dotazovacího stroje k prohlédnutí dat o výkonu, událostech a výstrahách, s využitím řídicích panelů a možností generování sestav, aby tato data zveřejnila smysluplnější způsob, předvádí sílu Azure Monitor Operations Manager.

Agenti, kteří vytvářejí sestavy Operations Manager skupiny pro správu, shromažďují data ze serverů na základě [log Analyticsch zdrojů dat](../agents/agent-data-sources.md) a řešení, která jste povolili v pracovním prostoru. V závislosti na povolených řešeních se data odesílají přímo z Operations Manager management server do služby nebo z důvodu objemu dat shromážděných v systému spravovaném agentem se odesílají přímo z agenta do pracovního prostoru Log Analytics. Server pro správu předává data přímo do služby; nikdy je nezapisuje do provozní databáze nebo databáze datového skladu. Když management server ztratí spojení s Azure Monitor, uloží data místně do mezipaměti, dokud nebude znovu navázána komunikace. Pokud je management server v režimu offline kvůli plánované údržbě nebo neplánovanému výpadku, další management server ve skupině pro správu obnoví připojení pomocí Azure Monitor.  

Následující diagram znázorňuje připojení mezi servery pro správu a agenty ve System Center Operations Manager skupině pro správu a Azure Monitor, včetně směrování a portů.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače ve vaší síti připojovaly k Internetu, servery pro správu je možné nakonfigurovat tak, aby se připojovaly k bráně Log Analytics a přijímaly informace o konfiguraci a odesílali shromážděná data v závislosti na povolených řešeních. Další informace a postup konfigurace skupiny pro správu Operations Manager pro komunikaci prostřednictvím Log Analytics brány k Azure Monitor najdete v tématu [připojení počítačů k Azure monitor pomocí brány Log Analytics](./gateway.md).  

## <a name="prerequisites"></a>Požadavky

Než začnete, přečtěte si následující požadavky.

* Azure Monitor podporuje jenom System Center Operations Manager 2016 nebo novější, Operations Manager 2012 SP1 UR6 nebo novější a Operations Manager 2012 R2 UR2 nebo novější. V nástrojích Operations Manager 2012 SP1 UR7 a Operations Manager 2012 R2 UR3 je přidaná podpora proxy serverů.
* Integrace System Center Operations Manager 2016 s cloudem pro státní správu USA vyžaduje aktualizovaný poradce Management Pack, který je součástí kumulativní aktualizace 2 nebo novější. System Center Operations Manager 2012 R2 vyžaduje aktualizovaný poradce Management Pack, který je součástí kumulativní aktualizace 3 nebo novější.
* Všichni agenti nástroje Operations Manager musí splňovat minimální požadavky na podporu. Ujistěte se, že agenti mají minimální aktualizaci, jinak komunikace s agentem Windows se nemusí zdařit a generovat chyby v protokolu událostí Operations Manager.
* Pracovní prostor služby Log Analytics. Další informace najdete v článku [přehled Log Analytics pracovního prostoru](../platform/design-logs-deployment.md).
* Pomocí účtu, který je členem [role přispěvatel Log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions), se ověříte na Azure.

* Podporované oblasti: System Center Operations Manager pro připojení k pracovnímu prostoru Log Analytics podporuje jenom následující oblasti Azure:
    - USA – středozápad
    - Austrálie – jihovýchod
    - West Europe
    - East US
    - Jihovýchodní Asie
    - Japan East
    - Spojené království – jih
    - Indie – střed
    - Střední Kanada
    - Západní USA 2

>[!NOTE]
>Nedávné změny rozhraní API Azure znemožní zákazníkům úspěšně konfigurovat integraci mezi skupinou pro správu a Azure Monitor pro první spuštění. Pro zákazníky, kteří již mají integrovanou skupinu pro správu se službou, nebudete mít vliv na to, pokud nebudete muset překonfigurovat existující připojení.  
>Pro následující verze Operations Manager byl vydán nový Management Pack:
> - Pro System Center Operations Manager 2019 je tento Management Pack součástí zdrojového média a instaluje se při instalaci nové skupiny pro správu nebo během upgradu.
>- Management Pack Operations Manager 1801 je také možné použít i pro Operations Manager 1807.
>- Management Pack System Center Operations Manager 1801 si můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=57173).
>- Pro System Center 2016-Operations Manager Stáhněte Management Pack [odsud](https://www.microsoft.com/download/details.aspx?id=57172).  
>- Pro System Center Operations Manager 2012 R2 Stáhněte Management Pack [odsud](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Síť

Níže uvedené informace uvádějí informace o konfiguraci proxy serveru a brány firewall vyžadované pro agenta Operations Manager, servery pro správu a konzoli Operations Console ke komunikaci s Azure Monitor. Provoz z každé součásti je odchozí ze sítě do Azure Monitor.

|Prostředek | Číslo portu| Obejití kontroly protokolu HTTP|  
|---------|------|-----------------------|  
|**Agenta**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Yes|  
|\*.blob.core.windows.net| 443|Yes|  
|\*.azure-automation.net| 443|Yes|  
|**Server pro správu**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Yes|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Yes|  
|**Operations Manager konzolu pro Azure Monitor**|||  
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

### <a name="tls-12-protocol"></a>Protokol TLS 1,2

Aby se zajistilo zabezpečení dat při přenosu do Azure Monitor, důrazně doporučujeme nakonfigurovat agenta a skupinu pro správu tak, aby používaly minimálně TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje** se. Další informace najdete v [zabezpečeném posílání dat pomocí TLS 1,2](../platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Připojení Operations Manager k Azure Monitor

Provedením následujícího postupu nakonfigurujete skupinu pro správu nástroje Operations Manager na připojení k jednomu z vašich pracovních prostorů Log Analytics.

Při počáteční registraci skupiny pro správu Operations Manager pomocí pracovního prostoru Log Analytics není v konzoli Operations Console k dispozici možnost zadat konfiguraci proxy serveru pro skupinu pro správu.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Chcete-li se tomuto problému vyhnout, je třeba aktualizovat konfiguraci proxy systému pomocí příkazu Netsh v systému, z něhož spouštíte konzolu Operations Console, a všechny servery pro správu ve skupině pro správu.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.
   a. Klikněte na **Start** a zadejte **příkaz cmd**.
   b. Klikněte pravým tlačítkem myši na **příkazový řádek** a vyberte Spustit jako správce * *.
1. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení následujících kroků pro integraci s Azure Monitor můžete konfiguraci odebrat spuštěním `netsh winhttp reset proxy` a pak pomocí možnosti **konfigurovat proxy server** v konzoli Operations Console zadat proxy server nebo server brány Log Analytics.

1. V konzole nástroje Operations Manager vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Přejděte na odkaz **Zaregistrovat ve službě Operations Management Suite**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite** zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k vašemu předplatnému OMS, a klikněte na **Přihlásit se**.

   >[!NOTE]
   >Název sady Operations Management Suite byl vyřazen.

1. Po úspěšném ověření můžete v **Průvodci registrací v Operations Management Suite: vyberte stránku pracovní prostor** , zobrazí se výzva, abyste vybrali klienta Azure, předplatné a Log Analytics pracovní prostor. Pokud máte více než jeden pracovní prostor, vyberte z rozevíracího seznamu ten, který chcete zaregistrovat ve skupině pro správu nástroje Operations Manager, a pak klikněte na **Další**.

   > [!NOTE]
   > Operations Manager podporuje vždy jenom jeden pracovní prostor služby Log Analytics. Připojení a počítače, které byly zaregistrovány pro Azure Monitor s předchozím pracovním prostorem, se z Azure Monitor odebraly.
   >
   >
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: souhrn** ověřte nastavení, a pokud je správné, klikněte na **Vytvořit**.
1. Na stránce **Průvodce registrací ve službě Operations Management Suite: dokončení** klikněte na **Zavřít**.

### <a name="add-agent-managed-computers"></a>Přidání počítačů spravovaných agenty

Když nakonfigurujete integraci s vaším pracovním prostorem Log Analytics, naváže se jenom připojení ke službě, z agentů, které hlásí do vaší skupiny pro správu, nebudou shromažďována žádná data. K tomu nedojde, dokud neurčíte, které konkrétní počítače spravované agentem shromažďují data protokolu pro Azure Monitor. Objekty počítačů můžete vybrat buď jednotlivě, nebo jako skupinu obsahující objekty počítačů s Windows. Nemůžete vybrat skupinu, která obsahuje instance jiné třídy, jako třeba logické disky nebo databáze SQL.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte uzel Operations Management Suite a klikněte na **Připojení**.
1. Klikněte na odkaz **Přidat počítač či skupinu** pod záhlavím Akce na pravé straně podokna.
1. V dialogovém okně **Hledání počítače** můžete vyhledat počítače nebo skupiny sledované nástrojem Operations Manager. Vyberte počítače nebo skupiny, včetně Operations Manager Management serveru, který chcete Azure Monitor, klikněte na **Přidat** a pak klikněte na **OK**.

Počítače a skupiny, které jsou nakonfigurované na shromažďování dat, si můžete zobrazit z uzlu Spravované počítače pod položkou Operations Management Suite v pracovním prostoru **Správa** v konzole Operations Console. Odsud můžete počítače a skupiny podle potřeby přidávat nebo odebírat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurace nastavení proxy v konzole Operations Console

Pokud je interní proxy server mezi skupinou pro správu a Azure Monitor, proveďte následující kroky. Tato nastavení se centrálně spravují ze skupiny pro správu a distribuují se do systémů spravovaných agenty, které jsou zahrnuté v oboru pro shromažďování dat protokolu pro Azure Monitor.  To je výhodné v případech, kdy určitá řešení obcházejí server pro správu a odesílají data přímo do služby.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. Rozbalte Operations Management Suite a pak klikněte na **Připojení**.
1. V zobrazení Připojení OMS klikněte na **Konfigurovat proxy server**.
1. Na stránce **Průvodce nastavením služby Operations Management Suite: proxy server** vyberte **Pro přístup ke službě Operations Management Suite použít proxy server**, potom zadejte adresu URL s číslem portu, například http://corpproxy:80, a pak klikněte na **Dokončit**.

Pokud vaše proxy server vyžaduje ověření, proveďte následující kroky, abyste nakonfigurovali přihlašovací údaje a nastavení, které je potřeba rozšířit na spravované počítače, které sestavy budou Azure Monitor ve skupině pro správu.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
1. V části **Konfigurace RunAs** vyberte **Profily**.
1. Otevřete profil **Proxy server profilu Spustit jako služby System Center Advisor**.
1. Pokud chcete použít účet Spustit jako, klikněte v Průvodci profilem Spustit jako na Přidat. [Účet Spustit jako](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) můžete vytvořit, případně použít existující účet. Tento účet musí mít dostatečná oprávnění k průchodu proxy serverem.
1. Pokud chcete nastavit účet k provedení správy, zvolte **Vybraná třída, skupina nebo objekt**, klikněte na **Vybrat** a potom na **Skupina**, čímž otevřete okno **Hledání skupiny**.
1. Vyhledejte a pak vyberte **Skupina monitorovacích serverů služby Microsoft System Center Advisor**. Po výběru skupiny zavřete kliknutím na **OK** okno **Hledání skupiny**.
1. Kliknutím na tlačítko **OK** zavřete pole **Přidat účet Spustit jako** .
1. Kliknutím na **Uložit** dokončete průvodce a uložte změny.

Po vytvoření připojení a nakonfigurujete, kteří agenti budou shromažďovat a hlásit data protokolu pro Azure Monitor, ve skupině pro správu se použije následující konfigurace, ne nutně v uvedeném pořadí:

* Vytvoří se účet Spustit jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Je přidružený k profilu Spustit jako **Objekt blob profilu Spustit jako služby Microsoft System Center Advisor** a je zacílený na dvě třídy: **Server kolekcí** a **Skupina správy nástroje Operations Manager**.
* Vytvoří se dva konektory.  První s názvem **Microsoft.SystemCenter. Advisor. Dataconnecter** a automaticky se konfiguruje s předplatným, které přepošle všechny výstrahy generované z instancí všech tříd ve skupině pro správu do Azure monitor. Druhý konektor je **konektor služby Advisor**, který zodpovídá za komunikaci s Azure monitor a sdílení dat.
* Agenti a skupiny, které jste vybrali pro shromažďování dat ve skupině pro správu, budou přidáni do **Skupiny monitorovacích serverů služby Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizace sad Management Pack

Po dokončení konfigurace Operations Manager skupina pro správu naváže spojení s Azure Monitor. Server pro správu se synchronizuje s webovou službou a přijímá aktualizované konfigurační informace ve formě sad Management Pack pro vámi povolená řešení, která se integrují s nástrojem Operations Manager. Operations Manager kontroluje aktualizace těchto sad Management Pack a automaticky je stáhne a naimportuje, jakmile jsou k dispozici. Toto chování řídí obzvláště dvě pravidla:

* **Microsoft.SystemCenter. Advisor. MPUpdate** – aktualizuje základní sady management Pack Azure monitor. Ve výchozím nastavení se spouští každých 12 hodin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Aktualizuje sady Management Pack řešení, které jsou povolené ve vašem pracovním prostoru. Ve výchozím nastavení se spouští každých pět (5) minut.

Tato dvě pravidla můžete přepsat tak, aby se zabránilo automatickému stahování, a to tak, že je zakážete nebo upravíte frekvenci, jak často se management server synchronizuje s Azure Monitor, abyste zjistili, jestli je k dispozici nový Management Pack a že se má stáhnout. Podle kroků [pro přepsání pravidla nebo monitorování](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) změňte parametr **Frekvence** na jinou hodnotou v sekundách (pokud chcete změnit plán synchronizace) nebo přepněte parametr **Povoleno** (pokud chcete pravidla zakázat). Přepsané hodnoty zacilte na všechny objekty třídy Skupina správy nástroje Operations Manager.

Chcete-li pokračovat podle stávajícího procesu řízení změn pro řízení Management Packch verzí ve skupině pro správu produkčního prostředí, můžete pravidla zakázat a povolit je v určitých časech, pokud jsou povoleny aktualizace. Pokud máte ve svém prostředí skupiny pro správu určenou pro vývoj nebo kontrolu kvality a ta má připojení k internetu, můžete pro podporu tohoto scénáře u této skupiny nakonfigurovat pracovní prostor služby Log Analytics. To vám umožní zkontrolovat a vyhodnotit iterativní verze Azure Monitor sad Management Pack před jejich uvolněním do produkční skupiny pro správu.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Převedení skupiny nástroje Operations Manager na nový pracovní prostor služby Log Analytics

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** a pak vytvořte pracovní prostor.  
1. Otevřete konzolu nástroje Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager, a vyberte pracovní prostor **Správa**.
1. Rozbalte položku Log Analytics a vyberte možnost **připojení**.
1. Vyberte odkaz **Znovu nakonfigurovat službu Operations Management Suite** uprostřed podokna.
1. Postupujte podle pokynů **Průvodce registrací Log Analytics** a zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružený k novému pracovnímu prostoru Log Analytics.

   > [!NOTE]
   > Stránka **Průvodce registrací ve službě Operations Management Suite: výběr pracovního prostoru** uvádí existující pracovní prostor, který se používá.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Ověřit integraci Operations Manager s Azure Monitor

K získání propojených instancí Operations Manager použijte následující dotaz:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Odebrat integraci s Azure Monitor

Když už integraci mezi vaší skupinou pro správu nástroje Operations Manager a pracovním prostorem služby Log Analytics nebudete potřebovat, bude třeba ke správnému odebrání připojení a konfigurace ze skupiny provést několik kroků. Následující postup umožňuje aktualizovat pracovní prostor Log Analytics tím, že se odstraní odkaz na vaši skupinu pro správu, odstraní konektory Azure Monitor a pak odstraní sady Management Pack podporující integraci se službou.  

Sady Management Pack pro řešení, které jste povolili pro integraci s Operations Manager a sady Management Pack vyžadované pro podporu integrace s Azure Monitor nelze snadno odstranit ze skupiny pro správu. Důvodem je, že některé sady Management Pack Azure Monitor mají závislosti na dalších souvisejících sadách Management Pack. Pokud chcete odstranit sady Management Pack se závislostmi na jiných sadách, stáhněte si skript pro [odebrání sady Management Pack se závislostmi](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z webu TechNet Script Center.  

1. Otevřete prostředí příkazového řádku pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.

    > [!WARNING]
    > Než budete pokračovat, ujistěte se, že nemáte žádné vlastní sady Management Pack se slovem Advisor nebo IntelligencePack v názvu, protože jinak tímto postupem ze skupiny pro správu odstraníte i je.
    >

1. V prostředí příkazového řádku zadejte `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pak zadejte `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pokud chcete odstranit všechny zbývající sady Management Pack, které jsou závislé na jiných sadách pro System Center Advisor, použijte skript *RecursiveRemove.ps1*, který jste si předtím stáhli z webu TechNet Script Center.  

    > [!NOTE]
    > Krok odebrání sad Management Pack služby Advisor pomocí prostředí PowerShell nebude automaticky odstraňovat interní sady Management Pack nástroje Microsoft System Center Advisor nebo Microsoft System Center Advisor.  Nepokoušejte se je odstranit.  
    >  

1. Otevřete konzolu Operations Console pro Operations Manager pomocí účtu, který je členem role Administrators nástroje Operations Manager.
1. V části **Správa** vyberte uzel **Sady Management Pack**, do pole **Hledat** zadejte **Advisor** a ověřte, že ve vaší skupině pro správu jsou stále naimportovány následující sady Management Pack:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. V Azure Portal klikněte na dlaždici **Nastavení** .
1. Vyberte **připojené zdroje**.
1. V tabulce v části System Center Operations Manager by se měl zobrazit název skupiny pro správu, kterou chcete z pracovního prostoru odebrat. Ve sloupci **Poslední data** klikněte na tlačítko **Odebrat**.  

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

Pokud plánujete znovu připojit skupinu pro správu k pracovnímu prostoru Log Analytics, je potřeba znovu naimportovat `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` soubor Management Pack. V závislosti na verzi nástroje System Center Operations Manager nasazené ve vašem prostředí ho najdete v následujícím umístění:

* Na zdrojovém médiu ve složce `\ManagementPacks` pro System Center 2016 – Operations Manager a vyšší.
* V nejnovější kumulativní aplikaci použité u vaší skupiny pro správu. V případě Operations Manager 2012 je zdrojová složka `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` a pro 2012 R2 v umístění `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` .

## <a name="next-steps"></a>Další kroky

Pokud chcete přidat funkce a shromažďovat data, přečtěte si téma [přidání Azure monitor řešení z galerie řešení](../insights/solutions.md).

