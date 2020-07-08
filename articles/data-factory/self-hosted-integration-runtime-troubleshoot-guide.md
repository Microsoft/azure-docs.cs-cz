---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315147"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Řešení potíží s místním hostováním Integration runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro prostředí Integration runtime v místním prostředí v Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Běžné chyby a jejich řešení

### <a name="error-message"></a>Chybová zpráva: 

`Self-hosted integration runtime can't connect to cloud service`

![Problém s připojením IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina 

Místní prostředí Integration runtime se nemůže připojit ke službě Data Factory (back-end). K tomuto problému obvykle dochází v důsledku nastavení sítě v bráně firewall.

#### <a name="resolution"></a>Řešení

1. Ověřte, jestli je spuštěná služba Integration runtime.
    
   ![Stav spuštění služby IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Pokud je služba spuštěná, pokračujte krokem 3.

1. Pokud není v místním prostředí Integration runtime nakonfigurovaný žádný proxy server (což je výchozí nastavení), spusťte následující příkaz PowerShellu na počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adresa URL služby se může lišit v závislosti na umístění Data Factory. Adresu URL služby najdete v části připojení k **uživatelským rozhraním ADF**  >  **Connections**  >  **Integration modul runtime**upravit uzly v místním prostředí  >  **IR**  >  **Nodes**  >  **Zobrazit adresy URL služby**.
            
    Očekává se následující odpověď:
            
    ![Odezva příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Pokud neobdržíte očekávanou odpověď, použijte jednu z následujících metod, která je vhodná pro vaši situaci:
            
    * Pokud se zobrazí zpráva "vzdálený název nelze rozpoznat", dojde k problému se službou DNS (Domain Name System). Pokud chcete tento problém vyřešit, obraťte se na svého síťového týmu.
    * Pokud se zobrazí zpráva "certifikát SSL/TLS není důvěryhodný", zkontrolujte, zda je certifikát pro https://wu2.frontend.clouddatahub.net/ důvěryhodný na počítači a pak nainstalujte veřejný certifikát pomocí Správce certifikátů. Tato akce by měla zmírnit problém.
    * V prohlížeči událostí **systému Windows**  >  **(protokoly)**  >  **protokoly aplikací a služeb**  >  **Integration runtime** a vyhledejte všechny chyby, které způsobila služba DNS, pravidlo brány firewall nebo nastavení sítě společnosti. (Pokud narazíte na chybu, nuceně uzavřete připojení.) Vzhledem k tomu, že každá společnost má vlastní nastavení sítě, požádejte o řešení těchto problémů svého síťového týmu.

1. Pokud je v místním prostředí Integration runtime nakonfigurovaný proxy server, ověřte, že váš proxy server má přístup ke koncovému bodu služby. Vzorový příkaz najdete v tématu [PowerShell, webové požadavky a proxy servery](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Očekává se následující odpověď:
            
![Odezva příkazu PowerShellu 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Hlediska proxy serveru:
> *    Ověřte, zda proxy server musí být vloženy do seznamu bezpečných příjemců. Pokud ano, ujistěte se, že jsou [tyto domény](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) v seznamu bezpečných příjemců.
> *    Ověřte, jestli je certifikát TLS/SSL wu2.frontend.clouddatahub.net/na proxy server důvěryhodný.
> *    Pokud na proxy serveru používáte ověřování pomocí služby Active Directory, změňte účet služby na uživatelský účet, který bude mít přístup k proxy serveru jako služba Integration Runtime.

### <a name="error-message"></a>Chybová zpráva: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Příčina 

Hostující uzel integrovaného modulu runtime může mít **neaktivní** stav, jak je znázorněno na následujícím snímku obrazovky:

![Neaktivní místní hostitelský uzel IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

K tomuto chování dochází, když uzly nemůžou vzájemně komunikovat.

#### <a name="resolution"></a>Řešení

1. Přihlaste se k virtuálnímu počítači hostovanému na uzlu. V části **protokoly aplikací a služeb**  >  **Integration runtime**otevřete Prohlížeč událostí a vyfiltrujte všechny protokoly chyb.

1. Zkontroluje, jestli protokol chyb obsahuje tuto chybu: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Pokud se zobrazí tato chyba, spusťte na příkazovém řádku následující příkaz: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Pokud se zobrazí následující chyba, obraťte se na oddělení IT, kde najdete nápovědu k vyřešení tohoto problému. Po úspěšném dokončení programu Telnet kontaktujte podpora Microsoftu, pokud stále máte problémy se stavem uzlu modulu runtime integrační.
        
   ![Chyba příkazového řádku](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Ověřte, zda protokol chyb obsahuje následující:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Chcete-li tento problém vyřešit, zkuste jednu nebo obě následující metody:
    - Vložte všechny uzly do stejné domény.
    - Přidejte IP adresu do mapování hostitele ve všech souborech hostitelů hostovaného virtuálního počítače.


## <a name="troubleshoot-connectivity-issue"></a>Řešení potíží s připojením

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Řešení potíží s připojením mezi místním prostředím IR a Data Factorym nebo místním prostředím IR a zdrojem dat/jímky

Pokud chcete řešit potíže s připojením k síti, měli byste vědět, jak [shromažďovat trasování sítě](#how-to-collect-netmon-trace), pochopit, jak ho používat, a [analyzovat trasování Netmon](#how-to-analyze-netmon-trace) před použitím nástrojů Netmon v reálných případech z místního prostředí IR.

Při řešení potíží s připojením, jako je například níže mezi místním prostředím IR a Data Factory v místním prostředí, může dojít k potížím: 

![Požadavek HTTP se nezdařil.](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Nebo jeden mezi místním a datovým prostředím v místním prostředí – dojde k následujícím chybám:

**Chybová zpráva:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Chybová zpráva:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Řešení:** Pokud se setkáte s výše uvedenými problémy, přečtěte si následující pokyny k řešení potíží:

Netmon trasování a proveďte další analýzu.
- Za prvé můžete nastavit filtr tak, aby se na straně klienta zobrazily všechny resetované informace ze serveru. V níže uvedeném příkladu uvidíte stranu serveru Data Factory serveru.

    ![Server datové továrny](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Po získání balíčku pro obnovení můžete najít konverzaci pomocí protokolu TCP.

    ![Najít konverzaci](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Pak můžete pomocí odebrání filtru získat převod mezi klientem a Data Factory serverem.

    ![Získat konverzaci](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Na základě shromážděného trasování Netmon můžeme říct, že celková hodnota TTL (TimeToLive) je 64. V závislosti na **výchozích hodnotách TTL a segmentů směrování** uvedených v [tomto článku](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (jak jsou extrahovány níže) vidíte, že se jedná o systém Linux, který resetuje balíček a způsobuje odpojení.

    Výchozí hodnoty TTL a mezních hodnot směrování se mezi různými operačními systémy liší, Toto jsou výchozí hodnoty pro několik:
    - Linux kernel 2,4 (CIRCA 2001): 255 pro TCP, UDP a ICMP
    - Linux kernel 4,10 (2015): 64 pro TCP, UDP a ICMP
    - Windows XP (2001): 128 pro TCP, UDP a ICMP
    - Windows 10 (2015): 128 pro TCP, UDP a ICMP
    - Windows Server 2008:128 pro TCP, UDP a ICMP
    - Windows Server 2019 (2018): 128 pro TCP, UDP a ICMP
    - macOS (2001): 64 pro TCP, UDP a ICMP

    ![HODNOTA TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Je ale zobrazená jako 61 namísto 64 výše uvedeného příkladu, protože když síťový balíček dosáhne cíle, musí jít o jiný segment směrování, jako jsou směrovače nebo síťová zařízení. Počet směrovačů/síťových zařízení bude v konečné hodnotě TTL snížen.
    V tomto případě můžeme vidět, že se resetování může odeslat ze systému Linux s hodnotou TTL 64.

- Musíme zkontrolovat čtvrtý segment směrování z místního prostředí IR a potvrdit, odkud zařízení pro resetování může pocházet.
 
    *Síťový balíček ze systému Linux A s hodnotou TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 v místním prostředí IR*

- V ideálním případě bude hodnota TTL 128, což znamená, že systém Windows spouští naši Data Factory. Jak je znázorněno v následujícím příkladu, *128 – 107 = 21 směrování*, což znamená, že 21 směrování pro balíček bylo odesláno z Data Factory do místního prostředí IR během ověřování TCP 3.
 
    ![HODNOTA TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Proto je potřeba zapojit síťový tým a ověřit, co je čtvrtý segment směrování z místního prostředí IR. Pokud se jedná o bránu firewall jako systém Linux, zkontrolujte všechny protokoly, proč zařízení obnoví balíček po TCP 3 handshake. Pokud si ale nejste jistí, kde se má prozkoumat, zkuste získat trasování Netmon z místního prostředí IR a brány firewall během problematické doby, abyste zjistili, které zařízení může resetovat tento balíček a způsobit odpojení. V takovém případě je potřeba, abyste svůj síťový tým přesunuli vpřed.

### <a name="how-to-collect-netmon-trace"></a>Jak shromažďovat Netmon trasování

1.  Stáhněte si nástroje Netmon z [tohoto webu](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)a nainstalujte je na serverovém počítači (jakýkoli server s problémem) a klienta (jako je například prostředí IR pro místní hostování).

2.  Vytvořte složku, například v následující cestě: *D:\netmon*. Ujistěte se, že má dostatek místa pro uložení protokolu.

3.  Zaznamenejte informace o IP adrese a portu. 
    1. Spuštění příkazového řádku
    2. Vyberte Spustit jako správce a spusťte následující příkaz:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Zachyťte trasování Netmon (síťový balíček).
    1. Spuštění příkazového řádku
    2. Vyberte Spustit jako správce a spusťte následující příkaz:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Můžete použít tři různé příkazy pro zachycení síťové stránky:
        - Možnost A: příkaz souboru RoundRobin (zachytí se jenom jeden soubor a přepíše staré protokoly).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Možnost B: příkaz zřetězeného souboru (vytvoří se nový soubor, pokud se dosáhne 200 MB).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Možnost C: příkaz plánovaného souboru.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Stisknutím **kombinace kláves CTRL + C** zastavíte zachytávání trasování Netmon.
 
> [!NOTE]
> Pokud je na klientském počítači možné shromažďovat pouze trasování Netmon, Získejte prosím adresu IP serveru, která vám usnadní analýzu tohoto trasování.

### <a name="how-to-analyze-netmon-trace"></a>Jak analyzovat trasování Netmon

Když se pokusíte o **8.8.8.8 888** s výše shromážděným trasováním Netmon, měli byste vidět pod trasováním:

![Netmon trasování 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon trasování 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

To znamená, že nemůžete na straně serveru **8.8.8.8** vytvořit připojení TCP na základě portu **888**, takže uvidíte dva další balíčky **SynReTransmit** . Vzhledem k tomu, že source **hostitel2** se nepovedlo vytvořit připojení k **8.8.8.8** v prvním balíčku, zůstane při vytváření připojení.

> [!TIP]
> - Můžete kliknout na možnost **Load Filter**  ->  **Standard Filter**  ->  **adresy**  ->  **IPv4**.
> - Zadejte **IPv4. Address = = 8.8.8.8** as Filter a klikněte na **použít**. Pak se zobrazí jenom komunikace z místního počítače do cílového **8.8.8.8**.

![filtrovat adresy 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtrovat adresy 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Níže uvedený příklad ukazuje, jak dobrý scénář vypadá. 

- Pokud služba Telnet **8.8.8.8 53** funguje bez jakýchkoli potíží, uvidíte, že se provede metoda HANDSHAKE protokolu TCP 3 a pak se relace dokončí s metodou HANDSHAKE protokolu TCP 4.

    ![dobrý příklad scénáře 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![dobrý příklad scénáře 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- V závislosti na výše uvedené signalizaci TCP 3 se můžete podívat pod pracovní postup:

    ![Pracovní postup handshake protokolu TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Metoda handshake protokolu TCP 4 pro dokončení relace a její pracovní postup se zobrazí následujícím způsobem:

    ![Protokol handshake protokolu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Pracovní postup handshake protokolu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce mapováním výkonu datových toků](concepts-data-flow-performance.md)
