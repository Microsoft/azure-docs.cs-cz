---
title: Řešení potíží se zřizováním cloudu Azure AD Connect
description: Tento dokument popisuje, jak řešit problémy, které mohou nastat u agenta zřizování cloudu.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794234"
---
# <a name="cloud-provisioning-troubleshooting"></a>Řešení potíží se zřizováním cloudu

Zřizování cloudu se dotýká mnoha různých věcí a má mnoho různých závislostí.  Přirozeně to může vést k různým problémům.  Tento dokument je navržený tak, aby vám začal řešit tyto problémy.  Tento dokument vás seznámí se typickými oblastmi, které byste měli zaměřit na, jak získat další informace a různé techniky, které se dají použít ke sledování problémů.  


## <a name="common-troubleshooting-areas"></a>Běžné oblasti odstraňování potíží

|Name (Název)|Popis|
|-----|-----|
|[Problémy agenta](#agent-issues)|Ověřte, že je agent správně nainstalovaný a komunikuje s Azure AD.|
|[Problémy synchronizace objektů](#object-synchronization-issues)|K řešení potíží se synchronizací objektů použijte protokoly zřizování.|
|[Zřizování problémů karantény](#provisioning-quarantined-issues)|Informace o tom, jak zajistit problém karantény a jak je opravit|


## <a name="agent-issues"></a>Problémy agenta
Některé z prvních věcí, které chcete ověřit u agenta, jsou:


-  je nainstalováno?
-  běží agent místně?
-  je agent na portálu?
-  je agent označen jako dobrý?  

Tyto položky lze ověřit v Azure Portal a na místním serveru, na kterém je spuštěn Agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal

Pokud chcete ověřit, že je agent v Azure a je v pořádku, postupujte podle těchto kroků:

1. Přihlaste se k portálu Azure.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na **Azure AD Connect** a ve středu vyberte **Spravovat zřizování (Preview)** .
3.  Na obrazovce **zřizování Azure AD (Preview)** klikněte na **zkontrolovat všechny agenty**.
 ![zřizování Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **obrazovce místní zřizovací agenti** se zobrazí agenti, které jste nainstalovali.  Ověřte, jestli je na něm daný agent a označený jako **dobrý**.
 agenti zřizování ![](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Ověření portu

Pokud chcete ověřit, že Azure naslouchá na portu 443 a že s ním může komunikovat agent, můžete použít následující nástroj:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, jestli můžou vaši agenti komunikovat s Azure přes port 443.  Otevřete prohlížeč a přejděte na adresu URL na serveru, na kterém je nainstalovaný agent.
 ![Služby](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na místním serveru

Pokud chcete ověřit, jestli je agent spuštěný, postupujte takto:

1.  Na serveru s nainstalovaným agentem otevřete **služby** , a to tak, že k němu přejdete, nebo když přejdete na Start/Run/Services. msc.
2.  V části **služby**se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD připojit** a **Microsoft Azure AD připojit agent zřizování** a že je stav **spuštěno**.
 ![Služby](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Problémy s instalací běžných agentů

Níže jsou uvedeny některé běžné problémy s instalací agenta a vysvětlení typického řešení.

#### <a name="agent-failed-to-start"></a>Agenta se nepodařilo spustit.

Pokud se zobrazí chybová zpráva s oznámením, že:

**Nepovedlo se spustit službu Microsfoft Azure AD Connect zřízení agenta.  Ověřte, zda máte dostatečná oprávnění pro spouštění systémových služeb.** 

To je obvykle způsobeno zásadami skupiny, které znemožňují použití oprávnění pro místní službu NT "přihlašovací účet" vytvořený instalačním programem (NT SERVICE\AADConnectProvisioningAgent). Tato oprávnění jsou nutná ke spuštění služby.

Chcete-li tento problém vyřešit, použijte následující postup:

1.  Přihlaste se k serveru pomocí účtu správce.
2.  Otevřete **služby** tak, že k němu přejdete nebo přejdete na Start/Run/Services. msc.
3.  V části **služby** dvakrát klikněte na **Microsoft Azure AD připojit agent zřizování** .
4. Na kartě změňte přihlašovací účet na správce domény a restartujte službu. 

 ![Služby](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Vypršel časový limit agenta nebo je certifikát neplatný.

Pokud se pokoušíte zaregistrovat agenta, může dojít k následujícím chybám.

 ![Služby](media/how-to-troubleshoot/troubleshoot4.png)

To je obvykle způsobeno tím, že se Agent nemůže připojit ke službě hybridní identity a vyžaduje konfiguraci proxy serveru HTTP.  Pro vyřešení této konfigurace odchozího proxy serveru. 

Agent zřizování podporuje použití odchozího proxy serveru. Můžete ji nakonfigurovat úpravou konfiguračního souboru agenta **C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config**. Do sebe přidejte následující řádky na konci souboru těsně před uzavírací `</configuration>` značku.
Nahraďte proměnné [proxy-server] a [proxy-port] svým názvem proxy server a hodnotami portů.

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

Při instalaci agenta zřizování cloudu se může zobrazit následující chyba.

To je obvykle způsobeno tím, že Agent nemůže spustit registrační skripty PowerShellu z důvodu zásad spouštění v rámci místního prostředí PowerShell.

Chcete-li tento problém vyřešit, změňte zásady spouštění prostředí PowerShell na serveru. Musíte mít zásady počítače a uživatele jako "undefined" nebo "RemoteSigned". Pokud se jedná o "unstricted", zobrazí se tato chyba.  Další informace najdete v tématu [zásady spouštění prostředí PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Soubory protokolu

Ve výchozím nastavení Agent generuje velmi minimální chybové zprávy a informace o trasování zásobníku. Tyto protokoly trasování můžete najít ve složce: **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect Agent\Trace zřizování**

Následující postup použijte k získání dalších podrobností pro řešení potíží souvisejících s agenty.

1. Zastavit **agenta zřizování služby Microsoft Azure AD Connect**
2. Vytvořte kopii původního konfiguračního souboru: **C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config**
3. Nahraďte existující oddíl < System. Diagnostics > následujícím a všechny zprávy trasování budou přejít do souboru **ProvAgentTrace. log.**

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
4. Spusťte službu Service **Microsoft Azure AD Connect zřizování agent** .
5. Následující příkaz můžete použít k zakončení souboru a ladění problémů: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problémy synchronizace objektů

Následující část obsahuje informace o řešení potíží se synchronizací objektů.

### <a name="provisioning-logs"></a>Protokoly zřizování

V Azure Portal můžete použít protokoly zřizování, které vám pomohou sledovat problémy s synchronizací objektů a řešit potíže.  Protokoly zobrazíte tak, že vyberete **protokoly**.
 protokoly zřizování ![](media/how-to-troubleshoot/log1.png)

Protokoly zřizování poskytují spoustu informací o stavu synchronizovaných objektů mezi místním prostředím služby AD a Azure.

 ![Protokoly zřizování](media/how-to-troubleshoot/log2.png)

Můžete použít rozevírací seznam v horní části stránky a filtrovat zobrazení na nulu v konkrétních problémech, datech atd.  Poklikáním na jednotlivou událost se poskytnou další podrobné informace.

 ![Protokoly zřizování](media/how-to-troubleshoot/log3.png)

Tyto informace budou poskytovat podrobný postup a kde dochází k potížím se synchronizací.  Díky tomu můžete v a přesně určit přesný bod problému.


## <a name="provisioning-quarantined-issues"></a>Zřizování problémů v karanténě

Zřizování cloudu monitoruje stav konfigurace a umisťuje objekty, které nejsou v pořádku, do stavu "karanténa". Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nedaří kvůli chybě, například neplatné přihlašovací údaje správce, je úloha zřizování označena jako v karanténě.

 ![Umístit](media/how-to-troubleshoot/quarantine1.png)

Kliknutím na stav můžete zobrazit další informace o karanténě.  Můžete také získat kód chyby a zprávu.

 ![Umístit](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Řešení karantény

- Pomocí Azure Portal restartujte úlohu zřizování. Na stránce Konfigurace agenta vyberte **restartovat zřizování**.

  ![Umístit](media/how-to-troubleshoot/quarantine3.png)

- [Restartujte úlohu zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zvolit vymazání escrows (pro restartování čítače v úschově, který se bude přestavovat do karantény), vymazat karanténu (Pokud chcete aplikaci odebrat z karantény) nebo zrušit meze. Použijte následující požadavek:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)



