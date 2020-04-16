---
title: Poradce při potížích s runtime vlastní integrace v Azure Data Factory
description: Zjistěte, jak řešit problémy s runtime vlastní hostované integrace v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414881"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Poradce při potížích s prostředím runtime vlastní hostované integrace

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek zkoumá běžné metody řešení potíží pro runtime integrace s vlastním hostitelem v Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Běžné chyby a jejich řešení

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Chybová zpráva: Prostředí runtime integrace s vlastním hostitelem se nemůže připojit ke cloudové službě

![Problém s refračerveným připojením s vlastním hostitelem](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina 

Prostředí runtime integrace s vlastním hostitelem se nemůže připojit ke službě Data Factory (back-end). Tento problém je obvykle způsoben nastavením sítě v bráně firewall.

#### <a name="resolution"></a>Řešení

1. Zkontrolujte, zda je spuštěna služba integračního běhu.
    
   ![Stav spuštěné infračervené služby s vlastním hostitelem](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Pokud je služba spuštěna, přejděte ke kroku 3.

1. Pokud v prostředí runtime integrace s vlastním hostitelem není žádný proxy server (což je výchozí nastavení), spusťte v počítači, ve kterém je nainstalován runtime s vlastním hostitelem, následující příkaz Prostředí PowerShell:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adresa URL služby se může lišit v závislosti na umístění datové továrny. Adresu URL služby**Nodes** > najdete v**části Runtimes** > integrace**připojení ui** >  >  **služby ADF**Upravit adresy**URL služby****s vlastním hostitelem.** > 
            
    Následuje očekávaná odpověď:
            
    ![Odpověď příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Pokud neobdržíte očekávanou odpověď, použijte jednu z následujících metod podle vaší situace:
            
    * Pokud se zobrazí zpráva "Vzdálený název nelze vyřešit", dojde k problému se systémem DNS (Domain Name System). Chcete-li tento problém vyřešit, obraťte se na svůj síťový tým.
    * Pokud obdržíte zprávu "ssl/tls cert is not trusted" https://wu2.frontend.clouddatahub.net/ zkontrolujte, zda je certifikát pro v počítači důvěryhodný, a nainstalujte veřejný certifikát pomocí Správce certifikátů. Tato akce by měla zmírnit problém.
    * Přejděte na prostředí **Windows** > **Event Viewer (protokoly)** > **Aplikace a protokoly** > služeb**Integration Runtime** a zkontrolujte, zda nenastala jakákoli chyba způsobená službou DNS, pravidlem brány firewall nebo nastavením sítě společnosti. (Pokud zjistíte takové selhání, násilně zavřete připojení.) Vzhledem k tomu, že každá společnost má vlastní nastavení sítě, obraťte se na svůj síťový tým k řešení těchto problémů.

1. Pokud byl v prostředí runtime integrace s vlastním hostitelem nakonfigurován "proxy", ověřte, zda má server proxy přístup ke koncovému bodu služby. Ukázkový příkaz najdete v [tématu PowerShell, webové požadavky a proxy servery](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

Následuje očekávaná odpověď:
            
![Odpověď příkazu Powershell2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy úvahy:
> *    Zkontrolujte, zda je třeba server proxy umístit do seznamu Bezpečných příjemců. Pokud ano, ujistěte se, že [tyto domény](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) jsou v seznamu Bezpečných příjemců.
> *    Zkontrolujte, zda je certifikát TLS/SSL "wu2.frontend.clouddatahub.net/" na serveru proxy důvěryhodný.
> *    Pokud používáte ověřování služby Active Directory na serveru proxy, změňte účet služby na uživatelský účet, který má přístup k serveru proxy jako "Integrační runtime služba".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Chybová zpráva: Samoobslužný integrační runtime uzel/ logický kód SHIR je ve stavu Neaktivní/ "Spuštěno (omezeno)"

#### <a name="cause"></a>Příčina 

Integrovaný uzel runtime s vlastním hostitelem může mít **neaktivní** stav, jak je znázorněno na následujícím snímku obrazovky:

![Neaktivní samoobslužný infračervený uzel](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

K tomuto chování dochází, když uzly nemohou komunikovat mezi sebou.

#### <a name="resolution"></a>Řešení

1. Přihlaste se k virtuálnímu virtuálnímu ms hostovanému uzly. V části **Aplikace a služby protokoly** > **integrace Runtime**, otevřete Prohlížeč událostí a filtrovat všechny protokoly chyb.

1. Zkontrolujte, zda protokol chyb obsahuje následující chybu: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
