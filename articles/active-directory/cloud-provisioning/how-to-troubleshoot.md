---
title: Řešení potíží s zřizováním cloudu Azure AD Connect
description: Tento článek popisuje, jak řešit problémy, které mohou nastat s agentem zřizování cloudu.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549481"
---
# <a name="cloud-provisioning-troubleshooting"></a>Řešení potíží s zřizováním cloudu

Cloud zřizování se dotýká mnoha různých věcí a má mnoho různých závislostí. Tato široká oblast působnosti může vést k různým problémům. Tento článek vám pomůže tyto problémy vyřešit. Představuje typické oblasti, na které se můžete zaměřit, jak získat další informace a různé techniky, které můžete použít k vysledování problémů.


## <a name="common-troubleshooting-areas"></a>Běžné oblasti řešení potíží

|Name (Název)|Popis|
|-----|-----|
|[Problémy s agentem](#agent-problems)|Ověřte, že agent byl správně nainstalován a že komunikuje s Azure Active Directory (Azure AD).|
|[Problémy se synchronizací objektů](#object-synchronization-problems)|Protokoly zřizování slouží k řešení problémů se synchronizací objektů.|
|[Zřizování problémů v karanténě](#provisioning-quarantined-problems)|Pochopit problémy s karanténou zřizování a jak je opravit.|


## <a name="agent-problems"></a>Problémy s agentem
Některé z prvních věcí, které chcete ověřit u agenta jsou:

-  Je nainstalován?
-  Je agent spuštěn místně?
-  Je ten agent na portálu?
-  Je agent označen jako zdravý?

Tyto položky lze ověřit na portálu Azure a na místním serveru, který je spuštěn agent.

### <a name="azure-portal-agent-verification"></a>Ověření agenta portálu Azure

Chcete-li ověřit, že agent je vidět v Azure a je v pořádku, postupujte takto.

1. Přihlaste se k portálu Azure.
1. Na levé straně vyberte **Azure Active Directory** > **Azure AD Connect**. Ve středu vyberte **Spravovat zřizování (náhled).**
1. Na obrazovce **Zřizování Azure AD (náhled)** vyberte **Zkontrolovat všechny agenty**.

   ![Zkontrolovat všechny agenty](media/how-to-install/install7.png)</br>
 
1. Na obrazovce **Místní zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, zda je daný agent k dispozici a je označen *v pořádku*.

   ![Obrazovka Místních agentů zřizování](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Ověření portu

Chcete-li ověřit, že Azure naslouchá na portu 443 a že s ním váš agent může komunikovat, použijte následující nástroj:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, že vaši agenti mohou komunikovat s Azure přes port 443. Otevřete prohlížeč a přejděte na předchozí adresu URL ze serveru, na kterém je agent nainstalován.

![Ověření dosažitelnosti portu](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na místním serveru

Chcete-li ověřit, zda je agent spuštěn, postupujte takto.

1. Na serveru s nainstalovaným agentem otevřete **služby** tak, že na něj přejdete nebo přejdete na **soubor Run** > **Services.msc****Run** > .
1. V části **Služby**zkontrolujte, jestli jsou k dispozici **zprostředkovateli microsoft azure a připojení k připojení** a že je *spuštěný*agent Microsoft Azure **AD Connect.**

   ![Obrazovka Služby](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Běžné problémy s instalací agenta

Následující části popisují některé běžné problémy s instalací agenta a typická řešení.

#### <a name="agent-failed-to-start"></a>Agentu se nepodařilo spustit.

Může se zobrazit chybová zpráva, která uvádí:

**Službu Microsoft Azure AD Connect Provisioning Agent se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění ke spuštění systémových služeb.** 

Tento problém je obvykle způsoben zásadou skupiny, která bránila použití oprávnění na místní účet pro přihlášení služby NT vytvořený instalačním programem (NT SERVICE\AADConnectProvisioningAgent). Tato oprávnění jsou vyžadována ke spuštění služby.

Chcete-li tento problém vyřešit, postupujte takto.

1. Přihlaste se k serveru pomocí účtu správce.
1. Spusťte **služby** tak, že na něj přejdete nebo přejdete na **Spustit** > **soubor** > **Services.msc**.
1. V části **Služby**poklikejte na **Agenta zřizování**připojení Microsoft Azure AD .
1. Na kartě **Přihlásit** se změňte **tento účet** na správce domény. Potom restartujte službu. 

   ![Karta Přihlásit se](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Časový opovánění nebo platnost certifikátu agenta je neplatná.

Při pokusu o registraci agenta se může stát následující chybová zpráva.

![Chybová zpráva o časovém výpadku](media/how-to-troubleshoot/troubleshoot4.png)

Tento problém je obvykle způsoben tím, že agent není schopen se připojit ke službě hybridní identity a vyžaduje konfiguraci proxy protokolu HTTP. Chcete-li tento problém vyřešit, nakonfigurujte odchozí proxy server. 

Zřizovací agent podporuje použití odchozí proxy serveru. Můžete jej nakonfigurovat úpravou konfiguračního souboru agenta *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Přidejte do něj následující řádky ke konci souboru `</configuration>` těsně před uzavírací značku.
Nahraďte `[proxy-server]` proměnné `[proxy-port]` a název proxy serveru a hodnoty portů.

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

#### <a name="agent-registration-fails-with-security-error"></a>Registrace agenta se nezdaří s chybou zabezpečení

Při instalaci agenta zřizování cloudu se může stát chybová zpráva.

Tento problém je obvykle způsoben tím, že agent nemůže spustit registrační skripty prostředí PowerShell z důvodu místních zásad spuštění prostředí PowerShell.

Chcete-li tento problém vyřešit, změňte zásady spuštění prostředí PowerShell na serveru. Je třeba mít zásady počítače a uživatele nastaveny jako *Nedefinované* nebo *RemoteSigned*. Pokud jsou nastaveny jako *Neomezené*, zobrazí se tato chyba. Další informace naleznete v [tématu Zásady spuštění prostředí PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Soubory protokolu

Ve výchozím nastavení agent vydává minimální chybové zprávy a informace o trasování zásobníku. Tyto protokoly trasování najdete ve složce *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

Chcete-li získat další podrobnosti pro řešení potíží souvisejících s agentem, postupujte takto.

1. Zastavte službu **Microsoft Azure AD Connect Provisioning Agent**.
1. Vytvořte kopii původního konfiguračního souboru: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Nahraďte `<system.diagnostics>` existující oddíl následujícím a všechny trasovací zprávy přejdou do souboru *ProvAgentTrace.log*.

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
1. Spusťte službu **Microsoft Azure AD Connect Provisioning Agent**.
1. Pomocí následujícího příkazu můžete sledovat problémy se souborem a laděním. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problémy se synchronizací objektů

Následující část obsahuje informace o řešení potíží se synchronizací objektů.

### <a name="provisioning-logs"></a>Protokoly zřizování

Na webu Azure Portal zřizování protokoly můžete použít ke sledování a řešení problémů synchronizace objektů. Chcete-li protokoly zobrazit, vyberte **možnost Protokoly**.

![Tlačítko Protokoly](media/how-to-troubleshoot/log1.png)

Zřizovací protokoly poskytují velké množství informací o stavu objektů, které jsou synchronizovány mezi místním prostředím služby Active Directory a Azure.

![Obrazovka Zřizování protokolů](media/how-to-troubleshoot/log2.png)

Rozevírací seznamy v horní části stránky můžete použít k filtrování zobrazení tak, aby se zaměřilo na konkrétní problémy, například data. Poklepáním na jednotlivé události zobrazíte další informace.

![Informace rozevíracího pole Zřizování protokolů](media/how-to-troubleshoot/log3.png)

Tyto informace poskytují podrobné kroky a kde dochází k problému synchronizace. Tímto způsobem můžete určit přesné místo problému.


## <a name="provisioning-quarantined-problems"></a>Zřizování problémů v karanténě

Cloud zřizování monitoruje stav konfigurace a umístí nefunkční objekty do stavu karantény. Pokud většina nebo všechna volání proti cílovému systému konzistentně selžou z důvodu chyby, například neplatných přihlašovacích údajů správce, úloha zřizování je označena jako v karanténě.

![Stav karantény](media/how-to-troubleshoot/quarantine1.png)

Výběrem stavu zobrazíte další informace o karanténě. Můžete také získat kód chyby a zprávu.

![Informace o stavu karantény](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Řešení karantény

- K restartování úlohy zřizování použijte portál Azure. Na stránce konfigurace agenta vyberte **Restartovat zřizování**.

  ![Restartovat zřizování](media/how-to-troubleshoot/quarantine3.png)

- Restartování [úlohy zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)pomocí aplikace Microsoft Graph Budete mít plnou kontrolu nad tím, co restartujete. Můžete se rozhodnout vymazat:
  - Escrows, restartovat počítadlo úschovy, které narůstá směrem k karanténnímu stavu.
  - Karanténa, chcete-li aplikaci z karantény odebrat.
  - Vodoznaky. 
  
  Použijte následující žádost:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)



