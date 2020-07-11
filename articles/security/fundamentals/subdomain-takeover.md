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
ms.openlocfilehash: a7ff8a0cf23bf0701a7cc35cb137ec0965f295ec
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223971"
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

    - Udržujte katalog služeb pro koncové body plně kvalifikovaného názvu domény (FQDN) Azure a vlastníky aplikace. Pokud chcete sestavit katalog služeb, spusťte následující dotaz Azure Resource Graph (ARG) s parametry z následující tabulky:
    
        >[!IMPORTANT]
        > **Oprávnění** – spusťte dotaz jako uživatel s přístupem ke všem předplatným Azure. 
        >
        > **Omezení** – Azure Resource Graph má omezení a omezení stránkování, které byste měli zvážit, pokud máte velké prostředí Azure. [Přečtěte si další informace](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) o práci s velkými sadami dat prostředků Azure.  

        ```powershell
        Search-AzGraph -Query "resources | where type == '<ResourceType>' | 
        project tenantId, subscriptionId, type, resourceGroup, name, 
        endpoint = <FQDNproperty>"
        ``` 

        Pro parametry služby pro dotaz ARG:

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

        
        **Příklad 1** – tento dotaz vrací prostředky z Azure App Service: 

        ```powershell
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | 
        project tenantId, subscriptionId, type, resourceGroup, name, 
        endpoint = properties.defaultHostName"
        ```
        
        **Příklad 2** – tento dotaz kombinuje více typů prostředků, aby se vracely prostředky z Azure App Service **a** Azure App Service Sloty:

        ```powershell
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 
        'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, 
        resourceGroup, name, endpoint = properties.defaultHostName"
        ```


- **Vytvořit procedury pro nápravu:**
    - Když se najde dangling položky DNS, váš tým musí prozkoumat, jestli došlo k nějakému ohrožení zabezpečení.
    - Zjistěte, proč se adresa po vyřazení prostředku nezměnila.
    - Odstraňte záznam DNS, pokud se už nepoužívá, nebo ho nasměrujte na správný prostředek Azure (FQDN) patřící vaší organizaci.
 

## <a name="next-steps"></a>Další kroky

Další informace o souvisejících službách a funkcích Azure, které můžete použít k obraně před převzetím subdomény, najdete na následujících stránkách.

- [Azure DNS podporuje použití záznamů aliasů pro vlastní domény.](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Při přidávání vlastních domén do Azure App Service použijte ID ověření domény.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)