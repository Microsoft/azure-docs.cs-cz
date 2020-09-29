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
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: bde4b21f9dfff62ef43afc9c9d8e5a858631d304
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447369"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Zabránit položkám DNS v dangling a vyhnout se převzetí subdomény

Tento článek popisuje běžnou bezpečnostní hrozbu převzetí subdomény a postup, který je proti němu možné zmírnit.


## <a name="what-is-subdomain-takeover"></a>Co je převzetí subdomény?

Převzetí subdomény představují běžnou, vysoce závažnou hrozbu pro organizace, které pravidelně vytvářejí a odstraňují spoustu prostředků. K převzetí subdomény může dojít, když máte [záznam DNS](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) , který odkazuje na prostředek Azure, který je k dispozici. Tyto záznamy DNS se také označují jako položky DNS dangling. Záznamy CNAME jsou pro tuto hrozbu obzvláště zranitelné. Převzetí subdomény umožňují škodlivým objektům actor přesměrovat provoz určený pro doménu organizace na lokalitu, která provádí škodlivou aktivitu.

Běžný scénář pro převzetí subdomény:

1. **VYTVOŘENA**

    1. Prostředek Azure zřizujete pomocí plně kvalifikovaného názvu domény (FQDN) `app-contogreat-dev-001.azurewebsites.net` .

    1. V zóně DNS přiřadíte záznam CNAME s subdoménou `greatapp.contoso.com` , která směruje provoz do vašeho prostředku Azure.

1. **ZRUŠENÍ zřízení**

    1. Prostředek Azure se po nepotřebném zrušení zřídí nebo odstraní. 
    
        V tuto chvíli `greatapp.contoso.com` *by se měl* záznam CNAME odebrat ze zóny DNS. Pokud záznam CNAME není odebraný, inzeruje se jako aktivní doména, ale nesměruje provoz na aktivní prostředek Azure. Toto je definice záznamu DNS "dangling".

    1. Subdoména dangling `greatapp.contoso.com` je teď zranitelná a je možné ji převzít prostřednictvím přiřazení k jinému prostředku předplatného Azure.

1. **VZETÍ**

    1. Pomocí běžně dostupných metod a nástrojů vyhledá objekt actor hrozby subdoménou dangling.  

    1. Objekt actor Threat zřídí prostředek Azure se stejným plně kvalifikovaným názvem domény dříve řízeného prostředku. V tomto příkladu `app-contogreat-dev-001.azurewebsites.net` .

    1. Provoz odeslaný do subdomény `myapp.contoso.com` je nyní směrován do prostředku objektu pro škodlivý objekt actor, kde tento obsah ovládá.



![Převzetí subdomény z nezajištěné webové stránky](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Rizika převzetí subdomény

Když záznam DNS odkazuje na prostředek, který není k dispozici, měl by být samotný záznam z vaší zóny DNS odebrán. Pokud se neodstranil, jedná se o záznam DNS dangling a vytvoří možnost převzetí subdomény.

Dangling položky DNS umožňují aktérům hrozeb převzít kontrolu nad přidruženým názvem DNS a hostovat škodlivý web nebo službu. Škodlivé stránky a služby v subdoméně organizace můžou mít za následek:

- **Ztráta kontroly nad obsahem subdomény** – negativně stiskněte informace o neschopnosti vaší organizace při zabezpečování obsahu a také o poškození značky a o ztrátách důvěry.

- **Soubory cookie získané z nepodezřelých návštěvníků** – je běžné, že webové aplikace zveřejňují soubory cookie relací pro subdomény (*. contoso.com), a proto k nim mají přístup všechny subdomény. Aktéri hrozeb můžou pomocí převzetí subdomény vytvořit autentickou stránku, která uživatele navštíví a nashromáždění jejich souborů cookie (i zabezpečených souborů cookie). Běžný nepojmový pojem použití certifikátů SSL chrání váš web a soubory cookie uživatelů od převzetí. Aktér hrozby však může použít napadenou subdoménu k uplatnění pro a získat platný certifikát SSL. Platné certifikáty SSL udělují jim přístup k zabezpečeným souborům cookie a můžou dál zvyšovat vnímaný legitimitu škodlivého webu.

- **Podvodné kampaně** – v kampaních phishing se můžou používat i domény s vyhledáním ověřených subdomén. To platí pro škodlivé weby a pro záznamy MX, které by útočníkovi umožnil příjem e-mailů, které jsou adresovány na oprávněnou subdoménu známek se známou bezpečností.

- **Další rizika** – škodlivé weby mohou sloužit k eskalaci dalších klasických útoků, jako jsou XSS, CSRF, CORS a další.



## <a name="identify-dangling-dns-entries"></a>Identifikace položek DNS dangling

Pokud chcete identifikovat položky DNS v rámci vaší organizace, které by mohly být dangling, použijte nástroje PowerShellu hostované na GitHubu od Microsoftu ["Get-DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains).

Tento nástroj pomáhá zákazníkům Azure seznam všech domén s CNAME přidruženým k existujícímu prostředku Azure, který se vytvořil na svých předplatných nebo klientech.

Pokud se vaše záznamy CNAME nacházejí v jiných službách DNS a odkazují na prostředky Azure, Poskytněte nástroji záznamy CNAME ve vstupním souboru.

Nástroj podporuje prostředky Azure uvedené v následující tabulce. Nástroj extrahuje nebo přebírá jako vstupy všechny záznamy CNAME tenanta.


| Služba                   | Typ                                        | FQDNproperty                               | Příklad                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | Microsoft. Network/frontdoors                | vlastnosti. cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | Microsoft. Storage/storageaccounts           | Properties. primaryEndpoints. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | Microsoft. CDN/Profiles/koncových bodů            | vlastnosti. název hostitele                        | `abc.azureedge.net`             |
| Veřejné IP adresy       | Microsoft. Network/publicipaddresses         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | Microsoft. Network/trafficmanagerprofiles    | Properties. dnsConfig. plně kvalifikovaný název domény                  | `abc.trafficmanager.net`        |
| Instance kontejneru Azure  | Microsoft. containerinstance/containergroups | vlastnosti. ipAddress. plně kvalifikovaný název domény                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | Microsoft. apimanagement/Service             | Properties. hostnameConfigurations. název_hostitele | `abc.azure-api.net`             |
| Azure App Service         | Microsoft. Web/weby                         | Properties. defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service – sloty | Microsoft. Web/weby/sloty                   | Properties. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Požadavky

Spusťte dotaz jako uživatel, který má:

- minimální přístup k předplatným Azure na úrovni čtenářů
- přístup pro čtení do grafu prostředků Azure

Pokud jste globálním správcem tenanta vaší organizace, zvyšte svůj účet tak, aby měl přístup ke všem předplatným vaší organizace pomocí pokynů v části [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> Azure Resource Graph má omezení a omezení stránkování, které byste měli zvážit, pokud máte velké prostředí Azure. 
> 
> [Přečtěte si další informace o práci s velkými sadami dat prostředků Azure](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Nástroj používá dávkování předplatného, aby tato omezení nedocházelo.

### <a name="run-the-script"></a>Spuštění skriptu

Přečtěte si další informace o skriptu PowerShellu, **Get-DanglingDnsRecords.ps1**a Stáhněte si ho z GitHubu: https://aka.ms/DanglingDNSDomains .

## <a name="remediate-dangling-dns-entries"></a>Napravit dangling položky DNS 

Zkontrolujte zóny DNS a Identifikujte záznamy CNAME, které se dangling nebo převzaly. Pokud se naleznou subdomény, které mají být dangling nebo převezmou, odeberte zranitelné subdomény a zmírnit rizika pomocí následujících kroků:

1. Z vaší zóny DNS odeberte všechny záznamy CNAME, které ukazují na plně kvalifikované názvy domén prostředků, které už nejsou zřízené.

1. Pokud chcete povolit směrování provozu do prostředků v ovládacím prvku, zřiďte další prostředky s plně kvalifikovanými názvy domén, které jsou uvedené v záznamech CNAME dangling subdomén.

1. Zkontrolujte kód aplikace pro odkazy na konkrétní subdomény a aktualizujte všechny nesprávné nebo zastaralé odkazy na poddomény.

1. Prozkoumejte, jestli došlo k ohrožení, a proveďte akci na postupy reakce na incidenty vaší organizace. Tipy a osvědčené postupy pro zkoumání tohoto problému najdete níže.

    Pokud je vaše aplikační logika taková, že tajné klíče, jako jsou přihlašovací údaje OAuth, se poslaly do subdomény dangling, nebo se informace citlivé na soukromí poslaly do subdomén dangling, mohla by být tato data vystavena třetím stranám.

1. Zjistěte, proč se záznam CNAME při zrušení zřízení prostředku neodebral z vaší zóny DNS, a proveďte kroky k tomu, abyste zajistili, že se záznamy DNS patřičně aktualizují při zrušení zřízení prostředků Azure v budoucnu.


## <a name="prevent-dangling-dns-entries"></a>Zabránit záznamům DNS v dangling

Ujistěte se, že vaše organizace implementovala procesy, které zabrání položkám DNS v dangling a výsledné převzetí subdomény, je zásadní součástí vašeho programu zabezpečení.

Některé služby Azure nabízí funkce, které pomáhají při vytváření preventivních opatření a jsou popsané níže. Jiné metody, jak zabránit tomuto problému, se musí navázat v rámci osvědčených postupů nebo standardních provozních postupů vaší organizace.


### <a name="use-azure-dns-alias-records"></a>Použití záznamů aliasů Azure DNS

[Záznamy aliasů](../../dns/dns-alias.md#scenarios) Azure DNS můžou zabránit odkazům na dangling prostřednictvím propojení životního cyklu záznamu DNS s prostředkem Azure. Předpokládejme například, že záznam DNS, který je kvalifikován jako záznam aliasu, odkazuje na veřejnou IP adresu nebo profil Traffic Manager. Pokud tyto podkladové prostředky odstraníte, bude se záznam aliasu DNS nacházet v prázdné sadě záznamů. Již neodkazuje na odstraněný prostředek. Je důležité si uvědomit, že existují omezení k tomu, co můžete chránit pomocí záznamů aliasů. V dnešní době je seznam omezen na:

- Azure Front Door
- Profily služby Traffic Manager
- Koncové body služby Azure Content Delivery Network (CDN)
- Veřejné IP adresy

Bez ohledu na to, co je dnes omezené nabídky služeb, doporučujeme pomocí záznamů aliasů chránit před převzetím subdomény, kdykoli je to možné.

[Přečtěte si další informace o možnostech záznamů aliasů Azure DNS](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Použít vlastní ověření domény Azure App Service

Při vytváření položek DNS pro Azure App Service vytvořte asuid. subdomény Záznam TXT s IDENTIFIKÁTORem ověřování domény Pokud takový záznam TXT existuje, žádné jiné předplatné Azure nedokáže ověřit vlastní doménu, která je přebírat. 

Tyto záznamy nebrání někomu v vytvoření Azure App Service se stejným názvem, který se nachází v záznamu CNAME. Bez možnosti prokázat vlastnictví názvu domény nemůžou aktéri hrozeb přijímat přenosy ani řídit obsah.

[Přečtěte si další informace o mapování stávajícího vlastního názvu DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Sestavování a automatizace procesů pro zmírnění hrozby

Pro vývojáře a provozní týmy je často možné spouštět procesy čištění, abyste se vyhnuli dangling hrozbám DNS. Následující postupy vám pomůžou zajistit, aby vaše organizace nebránila této hrozbě. 

- **Vytvořit postupy pro prevenci:**

    - Informování vývojářů vaší aplikace o přesměrování adres pokaždé, když odstraní prostředky.

    - Při vyřazení služby ze seznamu požadovaných kontrol vložte položku "odebrat položku DNS".

    - Pro všechny prostředky, které mají vlastní položku DNS, umístěte [zámky pro odstranění](../../azure-resource-manager/management/lock-resources.md) . Zámek proti odstranění slouží jako indikátor, že mapování musí být odebráno před zrušením zřízení prostředku. Takové míry můžou fungovat jenom v kombinaci s interními vzdělávacími programy.

- **Vytvořit procedury pro zjišťování:**

    - Pravidelně kontrolujte své záznamy DNS, abyste měli jistotu, že jsou všechny subdomény namapované na prostředky Azure, které:

        - Existují – dotazování na zóny DNS pro prostředky odkazující na poddomény Azure, jako je například *. azurewebsites.net nebo *. cloudapp.azure.com (viz [referenční seznam domén Azure](azure-domains.md)).
        - Vlastníte – potvrďte, že vlastníte všechny prostředky, na které vaše subdomény DNS cílí.

    - Udržujte katalog služeb pro koncové body plně kvalifikovaného názvu domény (FQDN) Azure a vlastníky aplikace. Pokud chcete sestavit katalog služeb, spusťte následující skript dotazu Azure Resource Graph. Tento skript vypisuje informace o koncovém názvu domény (FQDN) prostředků, ke kterým máte přístup, a vytváří jejich výstupy v souboru CSV. Pokud máte přístup ke všem předplatným pro vašeho tenanta, tento skript posuzuje všechna tato předplatná, jak je znázorněno v následujícím ukázkovém skriptu. Pokud chcete výsledky omezit na konkrétní sadu předplatných, upravte skript tak, jak je znázorněno na obrázku.


- **Vytvořit procedury pro nápravu:**
    - Když se najde dangling položky DNS, váš tým musí prozkoumat, jestli došlo k nějakému ohrožení zabezpečení.
    - Zjistěte, proč se adresa po vyřazení prostředku nezměnila.
    - Odstraňte záznam DNS, pokud se už nepoužívá, nebo ho nasměrujte na správný prostředek Azure (FQDN) patřící vaší organizaci.
 

## <a name="next-steps"></a>Další kroky

Další informace o souvisejících službách a funkcích Azure, které můžete použít k obraně před převzetím subdomény, najdete na následujících stránkách.

- [Prevence dangling záznamů DNS pomocí Azure DNS](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Při přidávání vlastních domén do Azure App Service použít ID ověření domény](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)