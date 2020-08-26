---
title: Zabránit převzetí subdomény pomocí záznamů Azure DNS aliasu a ověření vlastní domény Azure App Service
description: Zjistěte, jak se vyhnout běžné hrozbě vysoké závažnosti při převzetí subdomény.
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890820"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Zabránit položkám DNS v dangling a vyhnout se převzetí subdomény

Tento článek popisuje běžnou bezpečnostní hrozbu převzetí subdomény a postup, který je proti němu možné zmírnit.


## <a name="what-is-subdomain-takeover"></a>Co je převzetí subdomény?

Převzetí subdomény představují běžnou, vysoce závažnou hrozbu pro organizace, které pravidelně vytvářejí a odstraňují spoustu prostředků. K převzetí subdomény může dojít, když máte záznam DNS, který odkazuje na prostředek Azure, který je k dispozici. Tyto záznamy DNS se také označují jako položky DNS dangling. Záznamy CNAME jsou pro tuto hrozbu obzvláště zranitelné.

Běžný scénář pro převzetí subdomény:

1. Vytvoří se web. 

    V tomto příkladu `app-contogreat-dev-001.azurewebsites.net` .

1. Záznam CNAME se přidá do DNS ukazující na web. 

    V tomto příkladu byl vytvořen následující popisný název: `greatapp.contoso.com` .

1. Po několika měsících už není lokalita potřebná, takže se odstraní, **aniž by se musela** odstranit odpovídající položka DNS. 

    Záznam DNS CNAME je nyní "dangling".

1. Téměř okamžitě po odstranění lokality objekt actor pro hrozbu zjistí chybějící lokalitu a vytvoří svůj vlastní web na adrese `app-contogreat-dev-001.azurewebsites.net` .

    Nyní provoz určený pro `greatapp.contoso.com` přejde na web Azure actor útočníka a útočník hrozby nařídí zobrazený obsah. 

    Služba DNS dangling byla zneužita a subdoménou společnosti Contoso "GreatApp" bylo oběť převzetí subdomény. 

![Převzetí subdomény z nezajištěné webové stránky](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Rizika převzetí subdomény

Když záznam DNS odkazuje na prostředek, který není k dispozici, měl by být samotný záznam z vaší zóny DNS odebrán. Pokud se neodstranil, jedná se o záznam DNS dangling a vytvoří možnost převzetí subdomény.

Dangling položky DNS umožňují aktérům hrozeb převzít kontrolu nad přidruženým názvem DNS a hostovat škodlivý web nebo službu. Škodlivé stránky a služby v subdoméně organizace můžou mít za následek:

- **Ztráta kontroly nad obsahem subdomény** – negativně stiskněte informace o neschopnosti vaší organizace při zabezpečování obsahu a také o poškození značky a o ztrátách důvěry.

- **Soubory cookie získané z nepodezřelých návštěvníků** – je běžné, že webové aplikace zveřejňují soubory cookie relací pro subdomény (*. contoso.com), a proto k nim mají přístup všechny subdomény. Aktéri hrozeb můžou pomocí převzetí subdomény vytvořit autentickou stránku, která uživatele navštíví a nashromáždění jejich souborů cookie (i zabezpečených souborů cookie). Běžný nepojmový pojem použití certifikátů SSL chrání váš web a soubory cookie uživatelů od převzetí. Aktér hrozby však může použít napadenou subdoménu k uplatnění pro a získat platný certifikát SSL. Platné certifikáty SSL udělují jim přístup k zabezpečeným souborům cookie a můžou dál zvyšovat vnímaný legitimitu škodlivého webu.

- **Podvodné kampaně** – na základě závazného způsobu, které se dají použít v kampaních phishing. To platí pro škodlivé weby a také pro záznamy MX, které by útočníkovi umožnil příjem e-mailů, které jsou adresovány na oprávněnou subdoménu známé značky.

- **Další rizika** – škodlivé weby je možné využít k eskalaci dalších klasických útoků, jako jsou například XSS, CSRF, CORS a další.



## <a name="preventing-dangling-dns-entries"></a>Prevence dangling záznamů DNS

Ujistěte se, že vaše organizace implementovala procesy, které zabrání položkám DNS v dangling a výsledné převzetí subdomény, je zásadní součástí vašeho programu zabezpečení.

Níže jsou uvedené preventivní míry, které jsou dnes k dispozici.


### <a name="use-azure-dns-alias-records"></a>Použití záznamů aliasů Azure DNS

[Záznamy aliasů](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) Azure DNS můžou zabránit odkazům na dangling prostřednictvím propojení životního cyklu záznamu DNS s prostředkem Azure. Předpokládejme například, že záznam DNS, který je kvalifikován jako záznam aliasu, odkazuje na veřejnou IP adresu nebo profil Traffic Manager. Pokud tyto podkladové prostředky odstraníte, bude se záznam aliasu DNS nacházet v prázdné sadě záznamů. Již neodkazuje na odstraněný prostředek. Je důležité si uvědomit, že existují omezení k tomu, co můžete chránit pomocí záznamů aliasů. V dnešní době je seznam omezen na:

- Azure Front Door
- Profily služby Traffic Manager
- Koncové body služby Azure Content Delivery Network (CDN)
- Veřejné IP adresy

Bez ohledu na to, co je dnes omezené nabídky služeb, doporučujeme pomocí záznamů aliasů chránit před převzetím subdomény, kdykoli je to možné.

[Přečtěte si další informace](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) o možnostech záznamů aliasů Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Použít vlastní ověření domény Azure App Service

Při vytváření položek DNS pro Azure App Service vytvořte asuid. subdomény Záznam TXT s IDENTIFIKÁTORem ověřování domény Pokud takový záznam TXT existuje, žádné jiné předplatné Azure nedokáže ověřit vlastní doménu, která je přebírat. 

Tyto záznamy nebrání někomu v vytvoření Azure App Service se stejným názvem, který se nachází v záznamu CNAME. Bez možnosti prokázat vlastnictví názvu domény nemůžou aktéri hrozeb přijímat přenosy ani řídit obsah.

[Přečtěte si další informace](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) o mapování stávajícího vlastního názvu DNS na Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Sestavování a automatizace procesů pro zmírnění hrozby

Pro vývojáře a provozní týmy je často možné spouštět procesy čištění, abyste se vyhnuli dangling hrozbám DNS. Následující postupy vám pomůžou zajistit, aby vaše organizace nebránila této hrozbě. 

- **Vytvořit postupy pro prevenci:**

    - Informování vývojářů vaší aplikace o přesměrování adres pokaždé, když odstraní prostředky.

    - Při vyřazení služby ze seznamu požadovaných kontrol vložte položku "odebrat položku DNS".

    - Pro všechny prostředky, které mají vlastní položku DNS, umístěte [zámky pro odstranění](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) . Zámek proti odstranění slouží jako indikátor, že mapování musí být odebráno před zrušením zřízení prostředku. Takové míry můžou fungovat jenom v kombinaci s interními vzdělávacími programy.

- **Vytvořit procedury pro zjišťování:**

    - Pravidelně kontrolujte své záznamy DNS, abyste měli jistotu, že jsou všechny subdomény namapované na prostředky Azure, které:

        - Existují – dotaz na zóny DNS pro prostředky odkazující na subdomény Azure, jako je například *. azurewebsites.net nebo *. cloudapp.azure.com (viz [Tento seznam odkazů](azure-domains.md)).
        - Vlastníte – potvrďte, že vlastníte všechny prostředky, na které vaše subdomény DNS cílí.

    - Udržujte katalog služeb pro koncové body plně kvalifikovaného názvu domény (FQDN) Azure a vlastníky aplikace. Pokud chcete sestavit katalog služeb, spusťte následující skript dotazu Azure Resource Graph. Tento skript vypisuje informace o koncovém názvu domény (FQDN) prostředků, ke kterým máte přístup, a vytváří jejich výstupy v souboru CSV. Pokud máte přístup ke všem předplatným pro vašeho tenanta, tento skript posuzuje všechna tato předplatná, jak je znázorněno v následujícím ukázkovém skriptu. Pokud chcete výsledky omezit na konkrétní sadu předplatných, upravte skript tak, jak je znázorněno na obrázku.

        >[!IMPORTANT]
        > **Oprávnění** – spusťte dotaz jako uživatel, který má přístup ke všem předplatným Azure. 
        >
        > **Omezení** – Azure Resource Graph má omezení a omezení stránkování, které byste měli zvážit, pokud máte velké prostředí Azure. [Přečtěte si další informace](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) o práci s velkými sadami dat prostředků Azure. Následující vzorový skript používá dávkování předplatných, aby tato omezení nedocházelo.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Seznam typů a jejich `FQDNProperty` hodnot, jak je uvedeno v předchozím dotazu grafu prostředků:

        |Název prostředku  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|Microsoft. Network/frontdoors|vlastnosti. cName|
        |Azure Blob Storage|Microsoft. Storage/storageaccounts|Properties. primaryEndpoints. blob|
        |Azure CDN|Microsoft. CDN/Profiles/koncových bodů|vlastnosti. název hostitele|
        |Veřejné IP adresy|Microsoft. Network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Azure Traffic Manager|Microsoft. Network/trafficmanagerprofiles|Properties. dnsConfig. plně kvalifikovaný název domény|
        |Instance kontejneru Azure|Microsoft. containerinstance/containergroups|vlastnosti. ipAddress. plně kvalifikovaný název domény|
        |Azure API Management|Microsoft. apimanagement/Service|Properties. hostnameConfigurations. název_hostitele|
        |Azure App Service|Microsoft. Web/weby|Properties. defaultHostName|
        |Azure App Service – sloty|Microsoft. Web/weby/sloty|Properties. defaultHostName|


- **Vytvořit procedury pro nápravu:**
    - Když se najde dangling položky DNS, váš tým musí prozkoumat, jestli došlo k nějakému ohrožení zabezpečení.
    - Zjistěte, proč se adresa po vyřazení prostředku nezměnila.
    - Odstraňte záznam DNS, pokud se už nepoužívá, nebo ho nasměrujte na správný prostředek Azure (FQDN) patřící vaší organizaci.
 

## <a name="next-steps"></a>Další kroky

Další informace o souvisejících službách a funkcích Azure, které můžete použít k obraně před převzetím subdomény, najdete na následujících stránkách.

- [Azure DNS podporuje použití záznamů aliasů pro vlastní domény.](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Při přidávání vlastních domén do Azure App Service použijte ID ověření domény.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
