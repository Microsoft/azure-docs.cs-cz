---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f27132eb21d245d0d26de910abba088ba3b8efde
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170967"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Řešení potíží s místním hostováním Integration runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro prostředí Integration runtime v místním prostředí v Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Běžné chyby a jejich řešení

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Chybová zpráva: Integration runtime v místním prostředí se nemůže připojit ke cloudové službě.

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Chybová zpráva: uzel Integration runtime (v místním prostředí)/logický SHIR je v neaktivním stavu (s omezením).

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


## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce mapováním výkonu datových toků](concepts-data-flow-performance.md)
