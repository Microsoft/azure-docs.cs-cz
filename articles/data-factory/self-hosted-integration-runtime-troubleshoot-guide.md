---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907279"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Řešení potíží s místním hostováním Integration runtime

Tento článek popisuje běžné metody řešení potíží pro prostředí Integration runtime v místním prostředí v Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Běžné chyby a řešení

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Chybová zpráva: Integration runtime v místním prostředí se nemůže připojit ke cloudové službě.

- **Příznak**: 

    ![Problém s připojením IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Příčina**: místní prostředí Integration runtime se nemůže připojit ke službě Data Factory (back-end). Nejčastěji než to je způsobeno nastavením sítě v bráně firewall.

- **Řešení:** 

    1. Ověřte, zda je spuštěna služba Integration Runtime služby systému Windows.
    
        ![Stav spuštění služby IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Pokud je spuštěná služba systému Windows, jak je uvedeno v [1], postupujte podle potřeby podle následujících pokynů:

        1. Pokud "proxy" není nakonfigurováno v místním prostředí Integration runtime (výchozí nastavení není žádná konfigurace proxy serveru), spusťte níže uvedený příkaz prostředí PowerShell v počítači, ve kterém je nainstalován místní prostředí Integration Runtime: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > Adresa URL služby se může lišit v závislosti na umístění vaší datové továrny. Adresu URL služby najdete v části ADF UI – > připojení – > Integration runtime – > Upravit místně hostované uzly IR-> – > Zobrazit adresy URL služby.
            
            Níže je uvedená očekávaná odpověď:
            
            ![Odezva příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Pokud je odpověď odlišná, postupujte podle potřeby podle následujících pokynů:
            
            * Pokud se zobrazí chyba "vzdálený název nelze přeložit", dojde k potížím se službou DNS. Pokud chcete získat problém s překladem názvů DNS, obraťte se na kontaktování se síťovým týmem. 
            * Pokud se zobrazí chyba "certifikát SSL/TLS není důvěryhodný", zkontrolujte prosím, jestli je certifikát pro https://wu2.frontend.clouddatahub.net/v počítači důvěryhodný, nainstalujte veřejný certifikát pomocí Správce certifikátů, který by měl tento problém zmírnit.
            * Podívejte se na Windows – > prohlížeče událostí (protokoly) – > protokoly aplikací a služeb – > Integration Runtime pro případ selhání, většinou způsobená DNS, pravidlem brány firewall a nastavením sítě společnosti (vynuceně uzavřete připojení). Pro tento problém Zapojte svůj síťový tým pro další Troubleshot, protože každá společnost má vlastní nastavení sítě.

        2. Pokud je v místním prostředí Integration runtime nakonfigurovaný "proxy server", ověřte, jestli proxy server mít přístup k našemu koncovému bodu služby. Ukázku příkazu najdete v [tomto](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)tématu.    
                
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

            Níže je uvedená očekávaná odpověď:
            
            ![Odezva příkazu PowerShellu 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Hlediska proxy serveru:
            > * Ověřte, zda proxy server vyžaduje přidávání do seznamu povolených. V takovém případě mají [tyto domény](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) na seznamu povolených.
            > * Ověřte, jestli je u proxy server důvěryhodný certifikát TLS/SSL pro "wu2.frontend.clouddatahub.net/".
            > * Pokud na proxy serveru používáte ověřování pomocí služby Active Directory, změňte účet služby na uživatelský účet, který bude mít přístup k proxy serveru jako "Integration Runtime služba".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Chybová zpráva: uzel Integration runtime (v místním prostředí)/logický SHIR je v neaktivním stavu (s omezením).

- **Příčina**: uzel IR v místním prostředí se může zobrazit v neaktivním stavu, jak je znázorněno na následujícím snímku obrazovky:

    ![Neaktivní místní hostitelský uzel IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    K tomu dochází, když uzly vzájemně nemůžou komunikovat. 

- **Řešení:** 

    Přihlaste se k virtuálnímu počítači hostovaného v uzlu a otevřete zobrazení událostí v části protokoly aplikací a služeb – > Integration Runtime vyfiltrujte všechny protokoly chyb. 

     1. Pokud protokol chyb obsahuje: 
    
        **Chybový protokol**: System. ServiceModel. EndpointNotFoundException: Nelze se připojit k NET. TCP://xxxxxxx.bwld.com: 8060/ExternalService. svc/WorkerManager. Pokus o připojení uplynulý po časovém intervalu 00:00:00.9940994. Kód chyby TCP 10061: nebylo možné navázat spojení, protože cílový počítač ho aktivně odmítl 10.2.4.10:8060.  ---> System .NET. Sockets. SocketException: nebylo možné navázat spojení, protože cílový počítač ho aktivně odmítl 10.2.4.10:8060
    
           v System .NET. Sockets. Socket. DoConnect (koncový bod endPointSnapshot, SocketAddress socketAddress)
           
           v System .NET. Sockets. Socket. Connect (koncový bod remoteEP)
           
           v System. ServiceModel. Channels. SocketConnectionInitiator. Connect (identifikátor URI URI, časový limit TimeSpan)
    
        **Řešení:** spusťte příkazový řádek: telnet 10.2.4.10 8060
        
        Pokud se vám zobrazí chybová zpráva, požádejte prosím o pomoc s opravou tohoto problému své kyberbezpečnosti. Po úspěšném dokončení programu Telnet se obraťte na podporu Microsoftu, pokud stále máte problémy se stavem uzlu IR.
        
        ![Chyba příkazového řádku](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Pokud protokol chyb obsahuje:
     
        **Protokol chyb:** Nelze se připojit ke Správci pracovních procesů: NET. TCP://xxxxxx: 8060/ExternalService. svc/pro azranlcir01r1 hostitele nejsou k dispozici žádné položky DNS. Žádný takový hostitel není známý podrobnosti o výjimce: System. ServiceModel. EndpointNotFoundException: pro hostitele xxxxx neexistují žádné položky DNS. ---> System .NET. Sockets. SocketException: žádný takový hostitel není známý v System .NET. DNS. GetAddrInfo (název řetězce) na System .NET. DNS. InternalGetHostByName (řetězec hostName, Boolean includeIPv6) na System .NET. DNS. GetHostEntry (String hostNameOrAddress ) v typu System. ServiceModel. Channels. DnsCache. Resolve (identifikátor URI URI)---konci trasování zásobníku vnitřních výjimek---trasování zásobníku serveru: v System. ServiceModel. Channels. DnsCache. Resolve (identifikátor URI URI) 
    
        **Řešení:** Problém můžete vyřešit pomocí jedné z následujících dvou akcí:
         1. Vložte všechny uzly do stejné domény.
         2. Přidejte IP adresu do mapování hostitele ve všech souborech hostitelů hostovaného virtuálního počítače.


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog věnovaný Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem datových toků mapování ADF](concepts-data-flow-performance.md)
