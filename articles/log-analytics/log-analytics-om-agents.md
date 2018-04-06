---
title: Připojení nástroje Operations Manager k analýze protokolů | Microsoft Docs
description: Pokud chcete zachovat existující investic do služby System Center Operations Manager a používat rozšířené možnosti s analýzy protokolů, můžete integrovat nástroje Operations Manager s vaším pracovním prostorem OMS.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: magoedte
ms.openlocfilehash: 6db47c7baa0a345a32d26d56e843acd0204ae50b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Připojení nástroje Operations Manager k analýze protokolů
Pokud chcete zachovat existující investic do služby System Center Operations Manager a používat rozšířené možnosti s analýzy protokolů, můžete integrovat nástroje Operations Manager s vaším pracovním prostorem OMS.  To umožňuje že využít příležitostí OMS a nadále používat nástroj Operations Manager:

* Pokračovat v monitorování stavu vašich IT služeb s nástrojem Operations Manager
* Udržovat integrace s vašimi řešeními ITSM podpora správy incidentů a problémů
* Správa životního cyklu agenty nasazené do místní a virtuální počítače IaaS veřejného cloudu, které můžete monitorovat pomocí nástroje Operations Manager

Integrace s nástrojem System Center Operations Manager přidá hodnotu strategie operace služby s použitím zrychlení a zefektivnění OMS v shromažďování, ukládání a analýzu dat z nástroje Operations Manager.  OMS pomáhá korelaci a usilovat identifikace chyb, problémů a zpřístupnění opakování na podporu vašeho stávajícího procesu správy problém.   Flexibilitu vyhledávacího webu na výkon a vyhledejte události dat výstrah pomocí podrobných řídicích panelů a možnosti vytváření sestav ke zveřejnění těchto dat smysluplnějšími způsoby, ukazuje síly, které přináší OMS v complimenting nástroje Operations Manager.

Agenty do skupiny pro správu nástroje Operations Manager reporting shromažďování dat ze serverů, na základě analýzy protokolů zdroje dat a řešení, které jste povolili ve vašem předplatném OMS.  V závislosti na řešení jste povolili, data z těchto řešení buď odešlou přímo z serveru pro správu nástroje Operations Manager k webové službě OMS nebo z důvodu objem dat shromážděných v systému spravováno agentem, jsou odesílány přímo z agenta OMS webové služby. Server pro správu předává OMS data přímo k webové službě OMS; nikdy je zapsán do databáze OperationsManager nebo OperationsManagerDW.  Pokud server pro správu dojde ke ztrátě připojení k webové službě OMS, se ukládá do mezipaměti data místně, dokud komunikace je navázat znovu s OMS.  Pokud server pro správu offline z důvodu plánované údržby nebo neplánovanému výpadku, jinému serveru pro správu ve skupině pro správu obnoví připojení k OMS.  

Následující diagram znázorňuje připojení mezi servery pro správu a agentů skupiny pro správu System Center Operations Manager a OMS, včetně směr a portů.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Pokud vaše zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu, servery pro správu lze nakonfigurovat pro připojení k bráně OMS shromážděná data v závislosti na řešení, které jste povolili odesílat a přijímat informace o konfiguraci.  Další informace a kroky pro konfiguraci skupiny pro správu nástroje Operations Manager ke komunikaci přes bránu OMS ke službě OMS najdete v tématu [počítače připojit k OMS pomocí brány OMS](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Požadavky na systém
Než začnete, zkontrolujte následující podrobnosti k ověření, že splňujete požadavky.

* OMS podporuje pouze Operations Manager 2016 UR6 Operations Manager 2012 SP1 a vyšší a UR2 Operations Manager 2012 R2 a vyšší.  V nástrojích Operations Manager 2012 SP1 UR7 a Operations Manager 2012 R2 UR3 je přidaná podpora proxy serverů.
* Všichni agenti nástroje Operations Manager musí splňovat požadavky na minimální podporu. Zkontrolujte, zda jsou agenti na minimální aktualizace, v opačném případě může selhat provoz agenta Windows a mnoho chyb může vyplnit protokolu událostí nástroje Operations Manager.
* Předplatné OMS.  Další informace najdete v tématu [začít pracovat s analýzy protokolů](log-analytics-get-started.md).

### <a name="network"></a>Síť
Informace o následující seznam konfigurace proxy a firewall informace požadované pro agenta nástroje Operations Manager, servery pro správu a konzole Operations console ke komunikaci s OMS.  Provoz z jednotlivých součástí je odchozí z vaší sítě do služby OMS.     

|Prostředek | Číslo portu| Vynechat kontroly protokolu HTTP|  
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
|**Konzola nástroje Operations Manager k OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 a 443||  
|\*.microsoft.com| 80 a 443||  
|\*.microsoftonline.com| 80 a 443||  
|\*.mms.microsoft.com| 80 a 443||  
|login.windows.net| 80 a 443||  


## <a name="connecting-operations-manager-to-oms"></a>Připojení nástroje Operations Manager k OMS
Proveďte následující řadu postup konfigurace skupiny pro správu nástroje Operations Manager pro připojení k jednomu z vašich pracovních prostorů OMS.

1. V konzole nástroje Operations Manager, vyberte **správy** prostoru.
2. Rozbalte uzel Operations Management Suite a klikněte na tlačítko **připojení**.
3. Klikněte **registraci ve službě Operations Management Suite** odkaz.
4. Na **Průvodce registrací ve službě Operations Management Suite: ověřování** stránky, zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, která souvisí s vaším předplatným OMS a klikněte na **přihlášení**.
5. Poté, co jste úspěšně přihlášeni, na **Průvodce registrací ve službě Operations Management Suite: Vyberte pracovní prostor** stránky, zobrazí se výzva k výběru vaším pracovním prostorem OMS.  Pokud máte více než jednoho pracovního prostoru, vyberte pracovní prostor, které chcete zaregistrovat u skupiny pro správu nástroje Operations Manager z rozevíracího seznamu a pak klikněte na **Další**.
   
   > [!NOTE]
   > Nástroj Operations Manager podporuje jenom jeden pracovní prostor OMS najednou. Připojení a počítače, které byly zaregistrované k OMS s předchozí pracovního prostoru jsou vyřazeny z OMS.
   > 
   > 
6. Na **Průvodce registrací ve službě Operations Management Suite: Souhrn** , potvrďte nastavení a pokud jsou správné, klikněte na tlačítko **vytvořit**.
7. Na **Průvodce registrací ve službě Operations Management Suite: dokončení** klikněte na tlačítko **Zavřít**.

### <a name="add-agent-managed-computers"></a>Přidání počítačů spravovaných agentem
Jakmile nakonfigurujete integraci s vaším pracovním prostorem OMS, to jenom naváže připojení se OMS, je od agentů skupiny pro správu sestav nebyla shromážděna žádná data. Tím nedojde k ní až po dokončení konfigurace, které konkrétní počítače spravované pomocí agentů shromažďuje data pro analýzu protokolu. Objekty počítače můžete vybrat buď jednotlivě nebo můžete vybrat skupinu, která obsahuje objekty počítače Windows. Nelze vybrat skupinu, která obsahuje instancí jiné třídy, jako jsou logické disky nebo databáze SQL.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
2. Rozbalte uzel Operations Management Suite a klikněte na tlačítko **připojení**.
3. Klikněte **přidat počítač nebo skupinu** odkaz v části Akce záhlaví na pravé straně v podokně.
4. V **hledání počítače** dialogové okno, můžete vyhledat počítače nebo skupiny sledovaných nástrojem Operations Manager. Vyberte počítače nebo skupiny mohl připojit k OMS, klikněte na **přidat**a potom klikněte na **OK**.

Můžete zobrazit počítače a skupiny, které jsou nakonfigurované ke shromažďování dat z uzlu spravované počítače v rámci služby Operations Management Suite **správy** prostoru v konzoli Operations Console.  Tady můžete přidat nebo odebrat počítače a skupiny podle potřeby.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Konfigurace nastavení proxy serveru OMS v konzoli Operations console
Pokud je interní proxy server mezi skupinou správy a OMS webové služby, proveďte následující kroky.  Tato nastavení jsou centrálně spravovat ze skupiny pro správu a distribuci do spravovaných agentem systémy, které jsou zahrnuty v oboru ke shromažďování dat pro OMS.  To je výhodné pro při určité řešení Nepoužívat server pro správu a odesílání dat přímo OMS webové služby.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
2. Rozbalte Operations Management Suite a pak klikněte na tlačítko **připojení**.
3. V zobrazení Připojení OMS klikněte na **Konfigurovat proxy server**.
4. Na **Průvodce Operations Management Suite: Proxy Server** vyberte **používat proxy server pro přístup k službě Operations Management Suite**, a poté zadejte adresu URL s číslem portu, například http://corpproxy:80 a pak klikněte na **Dokončit**.

Pokud proxy server vyžaduje ověřování, proveďte následující postup pro konfiguraci přihlašovacích údajů a nastavení, které je potřeba rozšířit do spravovaných počítačů, které sestavy OMS ve skupině pro správu.

1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
2. V části **Konfigurace RunAs** vyberte **Profily**.
3. Otevřete profil **Proxy server profilu Spustit jako služby System Center Advisor**.
4. V Průvodce profilem spustit jako klikněte na Přidat a používat účet Spustit jako. Můžete vytvořit [účet Spustit jako](https://technet.microsoft.com/library/hh321655.aspx) nebo použít existující účet. Tento účet musí mít dostatečná oprávnění k průchodu proxy serverem.
5. Pokud chcete nastavit účet, který chcete spravovat, vyberte **vybranou třídu, skupinu nebo objekt**, klikněte na tlačítko **vyberte...** a pak klikněte na tlačítko **skupiny...** Chcete-li otevřít **skupiny vyhledávání** pole.
6. Vyhledejte a pak vyberte **monitorování skupiny serveru serveru Microsoft System Center Advisor**.  Klikněte na tlačítko **OK** po výběru skupiny zavřete **skupiny vyhledávání** pole.
7. Klikněte na tlačítko **OK** zavřete **přidat účet Spustit jako** pole.
8. Klikněte na tlačítko **Uložit** uložte změny a dokončete průvodce.

Po vytvoření připojení a nakonfigurujete, jaké agenty bude shromažďovat a odesílat data OMS, tyto konfigurace platí ve skupině pro správu, nemusí v pořadí:

* Účet Spustit jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** je vytvořena.  Je přidružen k profilu spustit jako **Microsoft System Center Advisor spustit jako profil Blob** a je zacílený na dvě třídy - **Server kolekce** a **skupina správy nástroje Operations Manager**.
* Jsou vytvořeny dva konektory.  První je s názvem **Microsoft.SystemCenter.Advisor.DataConnector** a je automaticky nakonfigurovaný pomocí odběr, který předává všechny výstrahy generované z instancí všechny třídy ve skupině pro správu k analýze protokolů OMS. Je druhý konektor **konektor služby Advisor**, který zodpovídá za komunikaci s webovou službou OMS a sdílení dat.
* Agenty a skupiny, které jste vybrali pro shromažďování dat ve skupině pro správu je přidat do **monitorování skupiny serveru serveru Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizace sad Management pack
Po dokončení konfigurace skupiny pro správu nástroje Operations Manager naváže připojení se službou OMS.  Server pro správu synchronizuje pomocí webové služby a získat aktualizovanou konfiguraci informace ve formě sady management Pack pro řešení, které jste povolili, které se integrují s nástrojem Operations Manager.   Nástroj Operations Manager kontroluje aktualizace těchto sad management Pack a automaticky stáhnout a importuje je, pokud jsou k dispozici.  Existují dvě pravidla na konkrétní kterého ovládacího prvku toto chování:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -aktualizace základní sady management Pack pro OMS. Ve výchozím nastavení spouští každých 12 hodin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** -aktualizace řešení management Pack, která je povolena v pracovním prostoru. Ve výchozím nastavení spouští každých pět (5) minut.

Tyto dvě pravidla buď zabránit automatické stahování zakázáním je nebo upravit frekvenci jak často se serverem pro správu synchronizuje s OMS k určení, pokud je k dispozici nové sady management pack a by měl být stažen můžete přepsat.  Postupujte podle kroků [postupu přepsání pravidla nebo monitorování](https://technet.microsoft.com/library/hh212869.aspx) změnit **frekvence** parametr s hodnotou v sekundách, pokud chcete změnit plán synchronizace, nebo upravit **povoleno** parametr zakázání pravidla.  Vybrat přepsání pro všechny objekty třídy skupina správy nástroje Operations Manager.

Pokud chcete pokračovat následující stávajícího procesu řízení změn pro řízení verzí management pack v provozní skupina pro správu, můžete zakázat pravidla a povolit v určité době, kdy jsou povoleny aktualizace. Pokud máte na vývoj nebo skupiny pro správu QA ve vašem prostředí a má připojení k Internetu, můžete tuto skupinu pro správu nakonfigurovat pracovním prostorem OMS pro podporu tohoto scénáře.  To umožňuje kontrola a vyhodnocení iterativní verze sady management Pack OMS před uvolněním je do provozní skupina pro správu.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Přepnout na nový pracovní prostor OMS skupinu nástroje Operations Manager
1. Přihlaste se k předplatnému OMS a vytvořit pracovní prostor v [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Otevřete konzolu nástroje Operations Manager pomocí účtu, který je členem role Správci nástroje Operations Manager a vyberte **správy** prostoru.
3. Rozbalte Operations Management Suite a vyberte **připojení**.
4. Vyberte **znovu nakonfigurovat operace Management Suite** odkaz na straně středním podokně.
5. Postupujte podle **Průvodce registrací ve službě Operations Management Suite** a zadejte e-mailovou adresu nebo telefonní číslo a heslo účtu správce, který je přidružen nový pracovní prostor OMS.
   
   > [!NOTE]
   > **Průvodce registrací ve službě Operations Management Suite: Vyberte pracovní prostor** stránky uvede existující pracovní prostor, který je používán.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Ověření integrace nástroje Operations Manager s OMS
Existuje několik různých způsobů, můžete ověřit, že vaše OMS k integraci nástroje Operations Manager byla úspěšně dokončena.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Potvrďte integrace z portálu OMS
1. Na portálu OMS, klikněte **nastavení** dlaždici
2. Vyberte **připojené zdroje**.
3. V tabulce v části System Center Operations Manager měli byste vidět název skupiny pro správu označené počet agentů a stavu, pokud byl naposled přijal data.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Poznámka: **ID pracovního prostoru** hodnotu levé straně stránky nastavení.  Ověření proti níže skupiny pro správu nástroje Operations Manager.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Potvrďte integrace z konzole Operations console
1. Otevřete konzolu nástroje Operations Manager a vyberte pracovní prostor **Správa**.
2. Vyberte **sady Management Pack** a v **vyhledejte:** textového pole zadejte **Advisor** nebo **Intelligence**.
3. V závislosti na řešení, které jste povolili zobrazí odpovídající sada management pack uvedené ve výsledcích hledání.  Například pokud jste povolili řešení pro správu výstrah, sada management pack Microsoft System Center Advisor výstrahy Management je v seznamu.
4. Z **monitorování** zobrazit, přejděte do **Operations Management Suite\Health stavu** zobrazení.  Vyberte server pro správu v části **stav serveru pro správu** podokně a v **podrobné zobrazení** podokně potvrďte hodnotu pro vlastnost **identifikátor URI ověřovací služby** odpovídá pracovním prostorem OMS ID.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Odebrat integraci s OMS
Když už nepotřebujete integrace mezi vaší skupiny pro správu nástroje Operations Manager a pracovní prostor OMS, existuje několik kroků potřebných k správně odebrání připojení a konfiguraci ve skupině pro správu. Následující postup se aktualizovat pracovní prostor OMS odstraněním odkaz na skupiny pro správu, odstraňte OMS konektory a pak odstraňte sady management Pack, podpůrné OMS.   

Sady Management Pack pro řešení jste povolili které integrovat s nástrojem Operations Manager a sady management Pack, který je potřeba pro podporu integrace se službou OMS nelze snadno odstranit ze skupiny pro správu.  Je to proto, že některé sady management Pack OMS mají závislosti na jiných sadách pro správu související.  Chcete-li odstranit sady management Pack se závislostí na jiných sadách management Pack, stáhněte skript [odebrat sadu management pack se závislostmi](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z centra skriptů TechNet.  

1. Otevřete příkazové prostředí nástroje Operations Manager pomocí účtu, který je členem role Správci nástroje Operations Manager.
   
    > [!WARNING]
    > Ověřte, že nemáte žádné vlastní sady management Pack s slovo Advisor nebo IntelligencePack v názvu než budete pokračovat, jinak tyto kroky je odstranit ze skupiny pro správu.
    > 

2. Z prostředí příkazového řádku zadejte `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Další typ `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Pokud chcete odstranit všechny sady management Pack zbývající, které jsou závislé na jiných sadách management Pack System Center Advisor, použijte skript *RecursiveRemove.ps1* předtím stáhli z centra skriptů webu TechNet.  
 
    > [!NOTE]
    > Neodstraňujte sady management Pack Microsoft System Center Advisor nebo Microsoft System Center Advisor interní.  
    >  

5. Otevřete konzoli Operations Manager Operations console pomocí účtu, který je členem role Správci nástroje Operations Manager.
6. V části **správy**, vyberte **sady Management Pack** uzel a v **vyhledejte:** zadejte **Advisor** a ověřte ve vaší skupině pro správu jsou stále naimportovány následující sady management Pack:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor interní
7. Na portálu OMS klikněte na dlaždici **Nastavení**.
8. Vyberte **připojené zdroje**.
9. V tabulce v části System Center Operations Manager měli byste vidět název skupiny pro správu, které chcete odebrat z pracovního prostoru.  Ve sloupci **poslední Data**, klikněte na tlačítko **odebrat**.  
   
    > [!NOTE]
    > **Odebrat** odkaz nebudete mít k dispozici až po dobu 14 dnů. Pokud nebude žádná aktivita nalezena, od připojené skupiny pro správu.  
    > 

10. Otevře se okno s žádostí o potvrzení, že chcete pokračovat s odstraněním.  Klikněte na tlačítko **Ano** pokračovat. 

Chcete-li odstranit dva konektory - Microsoft.SystemCenter.Advisor.DataConnector a konektor služby Advisor, uložte níže uvedený skript prostředí PowerShell do počítače a spuštění pomocí následující příklady:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Počítač, spusťte tento skript z, není-li server pro správu, by měl mít příkazové okno nástroje Operations Manager nainstalována v závislosti na verzi vaší skupině pro správu.
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

V budoucnu Pokud plánujete připojení skupiny pro správu k pracovnímu prostoru OMS, budete muset znovu naimportovat `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` soubor sady management pack z nejnovější kumulativní aktualizace, které jsou použity k vaší skupině pro správu.  Tento soubor můžete najít `%ProgramFiles%\Microsoft System Center 2012` nebo `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` složky.

## <a name="next-steps"></a>Další postup
Přidání funkcí a shromažďování dat najdete v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).


