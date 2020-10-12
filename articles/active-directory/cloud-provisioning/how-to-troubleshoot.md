---
title: Řešení potíží se zřizováním cloudu Azure AD Connect
description: Tento článek popisuje, jak řešit problémy, které mohou nastat u agenta zřizování cloudu.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 34796a435536a48100b7434ed5267802cd2d549f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226943"
---
# <a name="cloud-provisioning-troubleshooting"></a>Řešení potíží se zřizováním cloudu

Zřizování cloudu se dotýká mnoha různých věcí a má mnoho různých závislostí. Tento rozsáhlý rozsah může vést k různým problémům. Tento článek vám pomůže vyřešit tyto problémy. Zavádí typické oblasti, na které se můžete soustředit, jak získat další informace a různé techniky, které můžete použít ke sledování problémů.


## <a name="common-troubleshooting-areas"></a>Běžné oblasti odstraňování potíží

|Název|Popis|
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
1. Na levé straně vyberte **Azure Active Directory**  >  **Azure AD Connect**. V centru vyberte **Spravovat zřizování (Preview)**.
1. Na obrazovce **Azure AD zřizování (Preview)** vyberte **zkontrolovat všechny agenty**.

   ![Zkontrolovat všechny agenty](media/how-to-install/install7.png)</br>
 
1. Na obrazovce místní **zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, jestli je na něm daný agent a označený jako *dobrý*.

   ![Obrazovka místních zřizovacích agentů](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Ověření portu

Ověřte, že Azure naslouchá na portu 443 a že s ním může komunikovat agent. 

Tento test ověřuje, jestli můžou vaši agenti komunikovat s Azure přes port 443. Otevřete prohlížeč a na serveru, na kterém je nainstalovaný agent, se dostanete na předchozí adresu URL.

![Ověření dostupnosti portů](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na místním serveru

Pokud chcete ověřit, jestli je agent spuštěný, postupujte podle těchto kroků.

1. Na serveru s nainstalovaným agentem otevřete **služby** , a to tak, že k němu přejdete, nebo když přejdete na **Start**  >  **Spustit**  >  **Services. msc**.
1. V části **služby**se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD Connect** a **Microsoft Azure AD připojení zřizování** , a jejich stav je *spuštěný*.

   ![Obrazovka služby](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problémy s instalací běžných agentů

Následující části popisují některé běžné problémy s instalací agenta a typická řešení.

#### <a name="agent-failed-to-start"></a>Agenta se nepodařilo spustit.

Může se zobrazit chybová zpráva s oznámením, že:

**Službu Microsoft Azure AD Connect zřizování Agent se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění pro spouštění systémových služeb.** 

K tomuto problému obvykle dochází v důsledku zásad skupiny, které znemožňují použití oprávnění na účet místního přihlášení služby NT vytvořeného instalačním programem (NT SERVICE\AADConnectProvisioningAgent). Tato oprávnění se vyžadují ke spuštění služby.

Chcete-li tento problém vyřešit, postupujte podle následujících kroků.

1. Přihlaste se k serveru pomocí účtu správce.
1. Otevřete **služby** tak, že k němu přejdete, nebo když přejdete na **Spustit**  >  **Spustit**  >  **Services. msc**.
1. V části **služby**dvakrát klikněte na **Microsoft Azure AD připojit zřizovacího agenta**.
1. Na kartě **přihlášení** změňte **Tento účet** na správce domény. Pak službu restartujte. 

   ![Karta přihlášení](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Vypršel časový limit agenta nebo je certifikát neplatný.

Při pokusu o registraci agenta se může zobrazit následující chybová zpráva.

![Chybová zpráva s časovým limitem](media/how-to-troubleshoot/troubleshoot4.png)

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

Chcete-li tento problém vyřešit, změňte zásady spouštění prostředí PowerShell na serveru. Je nutné, aby byly zásady počítače a uživatele nastaveny jako *nedefinované* nebo *RemoteSigned*. Pokud jsou nastavené jako *neomezená*, zobrazí se tato chyba. Další informace najdete v tématu [zásady spouštění prostředí PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Soubory protokolů

Ve výchozím nastavení agent generuje minimální množství chybových zpráv a informací o trasování zásobníku. Tyto protokoly trasování najdete ve složce *C:\ProgramData\Microsoft\Azure zřizování služby AD Connect Agent\Trace*.

Pokud chcete získat další podrobnosti o řešení problémů souvisejících s agentem, postupujte podle těchto kroků.

1. Zastavení služby **Microsoft Azure AD připojení agenta zřizování**.
1. Vytvořte kopii původního konfiguračního souboru: *C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config*.
1. Existující oddíl nahraďte `<system.diagnostics>` následujícím a všechny zprávy trasování budou přejít do souboru *ProvAgentTrace. log*.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Spusťte službu **Microsoft Azure AD připojte agenta zřizování**.
1. Použijte následující příkaz k zakončení souboru a ladění problémů. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problémy synchronizace objektů

Následující část obsahuje informace o řešení potíží se synchronizací objektů.

### <a name="provisioning-logs"></a>Protokoly zřizování

V Azure Portal můžete použít protokoly zřizování, které vám pomohou sledovat problémy při synchronizaci objektů a řešit potíže. Protokoly zobrazíte tak, že vyberete **protokoly**.

![Tlačítko protokoly](media/how-to-troubleshoot/log1.png)

Protokoly zřizování poskytují spoustu informací o stavu objektů, které se synchronizují mezi místním prostředím Active Directory a Azure.

![Obrazovka zřizovacích protokolů](media/how-to-troubleshoot/log2.png)

V rozevíracích seznamech v horní části stránky můžete filtrovat zobrazení na nulu v konkrétních problémech, jako jsou například data. Dvojím kliknutím na jednotlivé události zobrazíte další informace.

![Informace rozevíracího seznamu protokolů zřizování](media/how-to-troubleshoot/log3.png)

Tyto informace obsahují podrobný postup a kde dochází k potížím se synchronizací. Tímto způsobem můžete přesně určit přesný bod problému.


## <a name="provisioning-quarantined-problems"></a>Zřizování problémů v karanténě

Zřizování cloudu sleduje stav konfigurace a umisťuje objekty, které nejsou v pořádku, do karantény. Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nezdařila kvůli chybě, například neplatné přihlašovací údaje správce, je úloha zřizování označena jako v karanténě.

![Stav karantény](media/how-to-troubleshoot/quarantine1.png)

Výběrem stavu můžete zobrazit další informace o karanténě. Můžete také získat kód chyby a zprávu.

![Informace o stavu karantény](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Řešení karantény

- Pomocí Azure Portal restartujte úlohu zřizování. Na stránce Konfigurace agenta vyberte **restartovat zřizování**.

  ![Restartovat zřizování](media/how-to-troubleshoot/quarantine3.png)

- [Restartujte úlohu zřizování](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zrušit výběr:
  - Escrows pro restartování čítače v úschově, který se bude nabíhat směrem k karanténě stavu.
  - Umístit do karantény, aby se aplikace odstranila z karantény.
  - Vodoznaky. 
  
  Použijte následující žádost:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)