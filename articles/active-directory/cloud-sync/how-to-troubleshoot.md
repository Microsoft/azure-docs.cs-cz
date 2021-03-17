---
title: Řešení potíží s Azure AD Connect synchronizace cloudu
description: Tento článek popisuje, jak řešit problémy, které mohou nastat u agenta zřizování cloudu.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 174ec8c42ea17ccae04769d7c0baaa91b8e7025b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517867"
---
# <a name="cloud-sync-troubleshooting"></a>Řešení potíží s synchronizací cloudu

Synchronizace cloudu se dotýká celé řady různých věcí a má celou řadu různých závislostí. Tento široký rozsah může vést k různým problémům. Tento článek vám pomůže vyřešit tyto problémy. Toto téma představuje obvyklé oblasti, na které byste se měli zaměřit, postup shromáždění dalších informací a různé techniky, které můžete využít k odhalování problémů.


## <a name="common-troubleshooting-areas"></a>Běžné oblasti odstraňování potíží

|Název|Description|
|-----|-----|
|[Problémy agenta](#agent-problems)|Ověřte, že je agent nainstalovaný správně a komunikuje s Azure Active Directory (Azure AD).|
|[Problémy synchronizace objektů](#object-synchronization-problems)|K řešení problémů se synchronizací objektů použijte protokoly zřizování.|
|[Zřizování problémů v karanténě](#provisioning-quarantined-problems)|Pochopení problémů se zřizováním karantény a jejich oprava.|


## <a name="agent-problems"></a>Problémy agenta
Některé z prvních věcí, které chcete ověřit u agenta, jsou:

-  Je nainstalováno?
-  Běží agent místně?
-  Je agent na portálu?
-  Je agent označen jako dobrý?

Tyto položky lze ověřit v Azure Portal a na místním serveru, na kterém je spuštěn Agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal

Pokud chcete ověřit, jestli je agent v Azure a je v pořádku, postupujte podle těchto kroků.

1. Přihlaste se k portálu Azure.
1. Na levé straně vyberte **Azure Active Directory**  >  **Azure AD Connect**. V centru vyberte **spravovat synchronizaci**.
1. Na obrazovce **Azure AD Connect synchronizace cloudu** vyberte **zkontrolovat všechny agenty**.

   ![Zkontrolovat všechny agenty](media/how-to-install/install-7.png)</br>
 
1. Na obrazovce místní **zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, jestli je na něm daný agent a označený jako *dobrý*.

   ![Obrazovka místních zřizovacích agentů](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Ověření portu

Ověřte, že Azure naslouchá na portu 443 a že s ním může komunikovat agent. 

Tento test ověřuje, jestli můžou vaši agenti komunikovat s Azure přes port 443. Otevřete prohlížeč a na serveru, na kterém je nainstalovaný agent, se dostanete na předchozí adresu URL.

![Ověření dostupnosti portů](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Na místním serveru

Pokud chcete ověřit, jestli je agent spuštěný, postupujte podle těchto kroků.

1. Na serveru s nainstalovaným agentem otevřete **služby** , a to tak, že k němu přejdete, nebo když přejdete na **Start**  >  **Spustit**  >  **Services. msc**.
1. V části **služby** se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD Connect** a **Microsoft Azure AD připojení zřizování** , a jejich stav je *spuštěný*.

   ![Obrazovka služby](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Problémy s instalací běžných agentů

Následující části popisují některé běžné problémy s instalací agenta a typická řešení.

#### <a name="agent-failed-to-start"></a>Agenta se nepodařilo spustit.

Může se zobrazit chybová zpráva s oznámením, že:

**Službu Microsoft Azure AD Connect zřizování Agent se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění pro spouštění systémových služeb.** 

K tomuto problému obvykle dochází v důsledku zásad skupiny, které znemožňují použití oprávnění na účet místního přihlášení služby NT vytvořeného instalačním programem (NT SERVICE\AADConnectProvisioningAgent). Tato oprávnění se vyžadují ke spuštění služby.

Chcete-li tento problém vyřešit, postupujte podle následujících kroků.

1. Přihlaste se k serveru pomocí účtu správce.
1. Otevřete **služby** tak, že k němu přejdete, nebo když přejdete na **Spustit**  >  **Spustit**  >  **Services. msc**.
1. V části **služby** dvakrát klikněte na **Microsoft Azure AD připojit zřizovacího agenta**.
1. Na kartě **přihlášení** změňte **Tento účet** na správce domény. Pak službu restartujte. 

   ![Karta přihlášení](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Vypršel časový limit agenta nebo je certifikát neplatný.

Při pokusu o registraci agenta se může zobrazit následující chybová zpráva.

![Chybová zpráva s časovým limitem](media/how-to-troubleshoot/troubleshoot-4.png)

Příčinou tohoto problému je obvykle to, že se agent nemůže připojit k hybridní službě identit a vyžaduje po vás konfiguraci proxy serveru HTTP. Pokud chcete tento problém vyřešit, nakonfigurujte odchozí proxy server. 

Agent zřizování podporuje použití odchozího proxy serveru. Můžete ji nakonfigurovat úpravou konfiguračního souboru agenta *C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config*. Přidejte do něj následující řádky na konec souboru těsně před uzavírací `</configuration>` značku.
Nahraďte proměnné `[proxy-server]` a `[proxy-port]` názvem proxy server a hodnotami portů.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Registrace agenta se nezdařila s chybou zabezpečení

Při instalaci agenta zřizování cloudu se může zobrazit chybová zpráva.

K tomuto problému obvykle dochází v důsledku toho, že agent nedokáže spustit registrační skripty PowerShellu kvůli zásadám spouštění v prostředí PowerShell.

Chcete-li tento problém vyřešit, změňte zásady spouštění prostředí PowerShell na serveru. Je nutné, aby byly zásady počítače a uživatele nastaveny jako *nedefinované* nebo *RemoteSigned*. Pokud jsou nastavené jako *neomezená*, zobrazí se tato chyba. Další informace najdete v tématu [zásady spouštění prostředí PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>Soubory protokolu

Ve výchozím nastavení agent generuje minimální množství chybových zpráv a informací o trasování zásobníku. Tyto protokoly trasování najdete ve složce **C:\ProgramData\Microsoft\Azure zřizování služby AD Connect Agent\Trace**.

Pokud chcete získat další podrobnosti o řešení problémů souvisejících s agentem, postupujte podle těchto kroků.

1.  Nainstalujte modul prostředí PowerShell pro AADCloudSyncTools, jak je popsáno [zde](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. `Export-AADCloudSyncToolsLogs`Zachyťte informace pomocí rutiny prostředí PowerShell.  Pomocí následujících přepínačů můžete vyladit shromažďování dat.
      - SkipVerboseTrace jenom k exportu aktuálních protokolů bez zachycení podrobných protokolů (výchozí = false)
      - TracingDurationMins určit jinou dobu trvání zachycení (výchozí = 3 minuty)
      - OutputPath zadejte jinou výstupní cestu (výchozí = dokumenty uživatele).


## <a name="object-synchronization-problems"></a>Problémy synchronizace objektů

Následující část obsahuje informace o řešení potíží se synchronizací objektů.

### <a name="provisioning-logs"></a>Protokoly zřizování

V Azure Portal můžete použít protokoly zřizování, které vám pomohou sledovat problémy při synchronizaci objektů a řešit potíže. Protokoly zobrazíte tak, že vyberete **protokoly**.

![Tlačítko protokoly](media/how-to-troubleshoot/log-1.png)

Protokoly zřizování poskytují spoustu informací o stavu objektů, které se synchronizují mezi místním prostředím Active Directory a Azure.

![Obrazovka zřizovacích protokolů](media/how-to-troubleshoot/log-2.png)

V rozevíracích seznamech v horní části stránky můžete filtrovat zobrazení na nulu v konkrétních problémech, jako jsou například data. Dvojím kliknutím na jednotlivé události zobrazíte další informace.

![Informace rozevíracího seznamu protokolů zřizování](media/how-to-troubleshoot/log-3.png)

Tyto informace obsahují podrobný postup a kde dochází k potížím se synchronizací. Tímto způsobem můžete přesně určit přesný bod problému.


## <a name="provisioning-quarantined-problems"></a>Zřizování problémů v karanténě

Synchronizace cloudu monitoruje stav konfigurace a umisťuje objekty, které nejsou v pořádku, do karantény. Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nezdařila kvůli chybě, například neplatné přihlašovací údaje správce, úloha synchronizace je označena jako v karanténě.

![Stav karantény](media/how-to-troubleshoot/quarantine-1.png)

Výběrem stavu můžete zobrazit další informace o karanténě. Můžete také získat kód chyby a zprávu.

![Snímek obrazovky, který obsahuje další informace o karanténě.](media/how-to-troubleshoot/quarantine-2.png)

Kliknutím pravým tlačítkem na stav zobrazíte další možnosti:
    
   - Zobrazit protokoly zřizování
   - Zobrazit agenta
   - Vymazat karanténu

![Snímek obrazovky, který zobrazuje možnosti nabídky po kliknutí pravým tlačítkem myši](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Řešení karantény
Existují dva různé způsoby, jak přeložit karanténu.  Jsou to tyto:

  - Vymazat karanténní – vymaže vodoznak a spustí rozdílovou synchronizaci.
  - restartování úlohy zřizování – vymaže meze a spustí počáteční synchronizaci.

#### <a name="clear-quarantine"></a>Vymazat karanténu
Pokud chcete vodoznak vymazat a po ověření spustit rozdílovou synchronizaci na úloze zřizování, stačí kliknout pravým tlačítkem na stav a vybrat **Vymazat karanténu**.

Mělo by se zobrazit upozornění, že se karanténa maže.

![Snímek obrazovky ukazující, že umístění karantény je smazáno.](media/how-to-troubleshoot/quarantine-5.png)

Pak byste měli vidět stav svého agenta jako v pořádku.

![Informace o stavu karantény](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Restartujte úlohu zřizování.
Pomocí Azure Portal restartujte úlohu zřizování. Na stránce Konfigurace agenta vyberte **restartovat zřizování**.

  ![Restartovat zřizování](media/how-to-troubleshoot/quarantine-3.png)

- [Restartujte úlohu zřizování](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zrušit výběr:
  - Escrows pro restartování čítače v úschově, který se bude nabíhat směrem k karanténě stavu.
  - Umístit do karantény, aby se aplikace odstranila z karantény.
  - Vodoznaky. 
  
  Použijte následující žádost:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Opravuje se účet cloudové synchronizační služby.
Pokud potřebujete opravit účet cloudové synchronizační služby, můžete použít `Repair-AADCloudSyncToolsAccount` .  


   1.  Začněte tím, že použijete instalační kroky popsané [tady](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) a potom pokračujte ve zbývajících krocích.
   2.  Z relace prostředí Windows PowerShell s oprávněními správce zadejte nebo zkopírujte a vložte následující: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Zadejte svoje přihlašovací údaje globálního správce Azure AD.
   4. Zadejte nebo zkopírujte a vložte následující text: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Po dokončení by se měl účet úspěšně opravit.

## <a name="next-steps"></a>Další kroky 

- [Známá omezení](how-to-prerequisites.md#known-limitations)
- [Kódy chyb](reference-error-codes.md)
