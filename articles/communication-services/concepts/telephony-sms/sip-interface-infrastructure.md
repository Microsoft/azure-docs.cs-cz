---
title: Požadavky na infrastrukturu rozhraní SIP – komunikační služby Azure
description: Seznamte se s požadavky na infrastrukturu pro konfiguraci rozhraní SIP služby Azure Communications.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 432a9dab851bda65ecf8736d725b08c5e726ac16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659252"
---
# <a name="sip-interface-infrastructure-requirements"></a>Požadavky na infrastrukturu rozhraní SIP 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Tento článek popisuje informace o dostupnosti infrastruktury, licencování a kontroleru hraničního připojení (mezipaměť softwarové sběrnice), které byste měli mít na paměti při naplánování vašeho nasazení rozhraní SIP.


## <a name="infrastructure-requirements"></a>Požadavky na infrastrukturu
Požadavky na infrastrukturu pro podporované sítě SBCs, domény a další požadavky na připojení k síti pro nasazení rozhraní SIP jsou uvedené v následující tabulce:  

|Požadavek infrastruktury|Potřebujete následující:|
|:--- |:--- |
|Kontroler ohraničení relace (mezipaměť softwarové sběrnice)|Podporované mezipaměť softwarové sběrnice. Další informace najdete v tématu [podporovaná SBCs](#supported-session-border-controllers-sbcs).|
|Telefonní šachty připojené k mezipaměť softwarové sběrnice|Jeden nebo více zákabelů, které jsou připojené ke službě mezipaměť softwarové sběrnice. Na jednom konci se mezipaměť softwarové sběrnice připojuje ke službě Azure Communication Service prostřednictvím rozhraní SIP. MEZIPAMĚŤ softwarové sběrnice se může také připojit k entitám telefonního subsystému třetích stran, jako jsou PBXs, analogové telefonní adaptéry atd. Všechny možnosti připojení PSTN připojené k mezipaměť softwarové sběrnice budou fungovat. (Pro konfiguraci zástupných informací na mezipaměť softwarové sběrnice se podívejte na dodavatele mezipaměť softwarové sběrnice nebo poskytovatele vedení).|
|Předplatné Azure|Předplatné Azure, které použijete k vytvoření prostředku ACS, a konfiguraci a připojení k mezipaměť softwarové sběrnice.|
|Přístupový token služby Communication Services|Chcete-li volat, potřebujete platný přístupový token s `voip` oborem. Zobrazit [přístupové tokeny](../identity-model.md#access-tokens)|
|Veřejná IP adresa pro mezipaměť softwarové sběrnice|Veřejná IP adresa, která se dá použít pro připojení k mezipaměť softwarové sběrnice. V závislosti na typu mezipaměť softwarové sběrnice může mezipaměť softwarové sběrnice používat překlad adres (NAT).|
|Plně kvalifikovaný název domény (FQDN) pro mezipaměť softwarové sběrnice|Plně kvalifikovaný název domény pro mezipaměť softwarové sběrnice, kde část domény doménového názvu FQDN neodpovídá registrovaným doménám ve vaší organizaci Microsoft 365 nebo Office 365. Další informace najdete v tématu [názvy domén mezipaměť softwarové sběrnice](#sbc-domain-names).|
|Veřejné položky DNS pro mezipaměť softwarové sběrnice |Veřejné položky DNS mapují mezipaměť softwarové sběrnice plně kvalifikovaný název domény na veřejnou IP adresu. |
|Veřejný důvěryhodný certifikát pro mezipaměť softwarové sběrnice |Certifikát pro mezipaměť softwarové sběrnice, který se má použít pro veškerou komunikaci s rozhraním SIP. Další informace najdete v tématu [veřejný důvěryhodný certifikát pro mezipaměť softwarové sběrnice](#public-trusted-certificate-for-the-sbc).|
|IP adresy a porty brány firewall pro signalizaci a médium SIP |MEZIPAMĚŤ softwarové sběrnice komunikuje s následujícími službami v cloudu:<br/><br/>Proxy server SIP, který zpracovává signalizaci<br/>Procesor médií, který zpracovává média<br/><br/>Tyto dvě služby mají samostatné IP adresy v Microsoft Cloud popsané dále v tomto dokumentu.


## <a name="sbc-domain-names"></a>Názvy domén mezipaměť softwarové sběrnice

Zákazníci bez Office 365 můžou použít libovolný název domény, pro který by mohli získat veřejný certifikát.

V následující tabulce jsou uvedeny příklady názvů DNS zaregistrovaných pro klienta, zda je možné název použít jako plně kvalifikovaný název domény (FQDN) pro mezipaměť softwarové sběrnice, a příklady platných názvů plně kvalifikovaného názvu domény (FQDN):

|Název DNS|Dá se použít pro mezipaměť softwarové sběrnice plně kvalifikovaný název domény.|Příklady názvů plně kvalifikovaného názvu domény|
|:--- |:--- |:--- |
contoso.com|Ano|**Platné názvy:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Ne|Použití domén *. onmicrosoft.com se u názvů mezipaměť softwarové sběrnice nepodporuje.

Pokud jste zákazníkem se sadou Office 365, název domény mezipaměť softwarové sběrnice nesmí souhlasit s registrací v doménách klienta sady Office 365. Níže je uveden příklad koexistence služeb Office 365 a Azure Communication Service:

|Doména registrovaná v Office 365|Příklady mezipaměť softwarové sběrnice plně kvalifikovaného názvu domény v týmech|Příklady názvů mezipaměť softwarové sběrnice plně kvalifikovaného názvu domény ve službě ACS|
|:--- |:--- |:--- |
**contoso.com** (doména druhé úrovně)|**SBC.contoso.com** (název v doméně druhé úrovně)|**SBC.ACS.contoso.com** (název v doméně třetí úrovně)<br/>**SBC.fabrikam.com** (libovolný název v jiné doméně)|
|**O365.contoso.com** (doména třetí úrovně)|**SBC.O365.contoso.com** (název v doméně třetí úrovně)|**SBC.contoso.com** (název v doméně druhé úrovně)<br/>**SBC.ACS.O365.contoso.com** (název v doméně čtvrté úrovně)<br/>**SBC.fabrikam.com** (libovolný název v jiné doméně)

Párování mezipaměť softwarové sběrnice funguje na úrovni prostředků komunikačních služeb, což znamená, že můžete spárovat mnoho SBCs s jedním prostředkem komunikačních služeb, ale nemůžete spárovat jeden mezipaměť softwarové sběrnice s více než jedním prostředkem komunikačních služeb. Pro párování s různými prostředky jsou vyžadovány jedinečné plně kvalifikované názvy domény mezipaměť softwarové sběrnice.

## <a name="public-trusted-certificate-for-the-sbc"></a>Veřejný důvěryhodný certifikát pro mezipaměť softwarové sběrnice

Společnost Microsoft doporučuje, abyste si vyžádali certifikát pro mezipaměť softwarové sběrnice vygenerováním žádosti o podepsání certifikátu (CSR). Konkrétní pokyny k vygenerování CSR pro mezipaměť softwarové sběrnice najdete v pokynech k propojení nebo dokumentaci od vašich dodavatelů mezipaměť softwarové sběrnice. 

  > [!NOTE]
  > Většina certifikačních autorit (CAs) vyžaduje, aby velikost privátního klíče byla aspoň 2048. Mějte na paměti, že při generování CSR.

Certifikát musí mít mezipaměť softwarové sběrnice plně kvalifikovaný název domény jako běžný název (CN) nebo alternativní název subjektu (SAN). Certifikát by měl být vydaný přímo od certifikační autority, nikoli od zprostředkujícího poskytovatele.

Rozhraní SIP komunikačních služeb taky podporuje zástupný znak v CN a/nebo SAN a zástupný znak musí odpovídat standardnímu [RFC HTTP přes TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Použijte příklad `\*.contoso.com` , který by odpovídal mezipaměť softwarové sběrnice plně kvalifikovanému názvu domény `sbc.contoso.com` , ale neshoduje se s `sbc.test.contoso.com` .

Certifikát musí být vygenerován jedním z následujících kořenových certifikačních autorit:

- AffirmTrust
- Kořenová AddTrust externí certifikační autority
- Kořenová Baltimore CyberTrust *
- Buypass
- CyberTrust
- Veřejná primární certifikační autorita třídy 3
- Comodo zabezpečená kořenová certifikační autorita
- Německý Telekom 
- Globální kořenová certifikační autorita DigiCert
- Kořenová certifikační autorita DigiCert High Assurance
- Svěří
- GlobalSign
- Přejít na Daddy
- Nedůvěra
- VeriSign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile root pro Microsoft 
- SwissSign
- CA pro časová razítka Thawte
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (německá Telekom)
- QuoVadis

Microsoft pracuje na přidávání dalších certifikačních autorit na základě zákaznických požadavků. 

## <a name="sip-signaling-fqdns"></a>Signalizace SIP: plně kvalifikované názvy domén 

Rozhraní protokolu SIP pro komunikační služby pro komunikaci má následující tři plně kvalifikované názvy domén:

- **SIP.pstnhub.Microsoft.com** – globální plně kvalifikovaný název domény – musí se vyzkoušení provést jako první. Když mezipaměť softwarové sběrnice odešle požadavek na překlad tohoto názvu, Microsoft Azure servery DNS vrátí IP adresu odkazující na primární datacentrum Azure přiřazené ke službě mezipaměť softwarové sběrnice. Přiřazení vychází z metrik výkonu datových center a zeměpisné blízkosti mezipaměť softwarové sběrnice. Vrácená IP adresa odpovídá primárnímu plně kvalifikovanému názvu domény.
- **sip2.pstnhub.Microsoft.com** – sekundární plně kvalifikovaný název domény – geograficky se mapuje na druhou oblast priority.
- **sip3.pstnhub.Microsoft.com** – terciární plně kvalifikovaný název domény – geograficky se mapuje na třetí prioritní oblast.

Umístění těchto tří plně kvalifikovaných názvů domén v uvedeném pořadí je povinné:

- Poskytněte optimální prostředí (které je k dispozici a nejbližší k datovému centru mezipaměť softwarové sběrnice přiřazené dotazem na první plně kvalifikovaný název domény).
- Poskytněte převzetí služeb při selhání při připojení z mezipaměť softwarové sběrnice k datovému centru, ve kterém dochází k dočasnému problému. Další informace najdete v části [mechanismus převzetí služeb při selhání](#failover-mechanism-for-sip-signaling) níže.  

Plně kvalifikované názvy domén – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com a sip3.pstnhub.microsoft.com – budou přeloženy na jednu z následujících IP adres:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Otevřete porty brány firewall pro tyto IP adresy, abyste umožnili příchozí a odchozí provoz do a z adres pro signalizaci. Pokud vaše brána firewall podporuje názvy DNS, plně kvalifikovaný název domény se `sip-all.pstnhub.microsoft.com` přeloží na všechny tyto IP adresy. 

## <a name="sip-signaling-ports"></a>Signalizace SIP: porty

Pro rozhraní komunikačních služeb SIP použijte následující porty:

|Provoz|Z|Záměr|Zdrojový port|Cílový port|
|:--- |:--- |:--- |:--- |:--- |
|PROTOKOL SIP/TLS|Proxy SIP|MEZIPAMĚŤ softwarové sběrnice|1024 – 65535|Definováno na mezipaměť softwarové sběrnice (pro Office 365 je nutné použít pouze port 5061 na úrovni služby)|
PROTOKOL SIP/TLS|MEZIPAMĚŤ softwarové sběrnice|Proxy SIP|Definováno na mezipaměť softwarové sběrnice|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Mechanismus převzetí služeb při selhání pro signalizaci SIP

MEZIPAMĚŤ softwarové sběrnice vytvoří dotaz DNS k překladu sip.pstnhub.microsoft.com. V závislosti na umístění mezipaměť softwarové sběrnice a metrikách výkonu datového centra se vybere primární datacentrum. Pokud se v primárním datacentru vyskytne problém, mezipaměť softwarové sběrnice zkusí sip2.pstnhub.microsoft.com, který se přeloží na druhé přiřazené datové centrum, a v nečastém případě, že datacentra ve dvou oblastech nejsou k dispozici, se mezipaměť softwarové sběrnice pokusí poslední plně kvalifikovaný název domény (sip3.pstnhub.microsoft.com), který poskytuje terciární IP adresu datacentra.

## <a name="media-traffic-ip-and-port-ranges"></a>Přenos médií: rozsahy IP adres a portů

Přenos médií vede k samostatné službě s názvem procesor médií. V okamžiku publikování mediálního procesoru pro službu ACS může použít libovolnou IP adresu Azure. Stáhněte si [úplný seznam adres](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Rozsah portů
Rozsah portů mediálních procesorů je uveden v následující tabulce: 

|Provoz|Z|Záměr|Zdrojový port|Cílový port|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Procesor médií|MEZIPAMĚŤ softwarové sběrnice|3478-3481 a 49152 – 53247|Definováno na mezipaměť softwarové sběrnice|
|UDP/SRTP|MEZIPAMĚŤ softwarové sběrnice|Procesor médií|Definováno na mezipaměť softwarové sběrnice|3478-3481 a 49152 – 53247|

  > [!NOTE]
  > Společnost Microsoft doporučuje na mezipaměť softwarové sběrnice aspoň dva porty na souběžné volání.


## <a name="media-traffic-media-processors-geography"></a>Multimediální provoz: geografické procesory

Přenos multimédií se zaznamená prostřednictvím komponent nazývaných multimediální procesory. Multimediální procesory se umístí do stejných datových center jako proxy SIP. Existují taky další multimediální procesory, které optimalizují Media Flow. Například nemusíte mít komponentu proxy SIP v Austrálii (toky SIP přes Singapur nebo Hongkong), ale v Austrálii máme místně i procesor médií. Nutnost místních procesorů médií je vyřízena latencí, které se používá při posílání provozu na dálku, například z Austrálie do Singapuru nebo Hongkong. I když je latence v příkladu přenosu dat z Austrálie do Hongkongu nebo Singapuru přijatelná, aby se zajistila dobrá kvalita volání pro přenosy SIP, a to v reálném čase, jako u datových médií v reálném čase.

Umístění, kde jsou nasazené součásti proxy SIP a multimediálního procesoru:
- US (2 v datových centrech USA – západ a USA – východ)
- Evropa (Amsterdam a datacentra Dublin)
- Asie (Singapur a Hongkong – zvláštní datacentra)
- Austrálie (datacentra AU a jihovýchod)

Umístění, ve kterých jsou nasazeny pouze procesory médií (v rámci nejbližšího datového centra je to přes protokol SIP):
- Japonsko (datacentra JP východní a západní oblasti)


## <a name="media-traffic-codecs"></a>Přenos multimédií: kodeky

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Fáze mezi mezipaměť softwarové sběrnice a cloudovým procesorem nebo klientem Microsoft Teams
Platí pro případ obcházení médií i pro případy neobejití.

Rozhraní přímého směrování na nohy mezi řadičem hraničních řešení relace a procesorem cloudového média může používat následující kodeky:

- HEDVÁBÍ, G. 711, G. 722, G. 729

Můžete vynutit použití konkrétního kodeku na hraničním řadiči relace vyloučením nežádoucích kodeků z nabídky.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Nožka mezi aplikací ACS SDK a procesorem cloudového média

Na nohy mezi procesorem cloudového média a aplikací ACS SDK se používá hedvábí nebo G. 722. Výběr kodeku na této nožkě vychází z algoritmů Microsoftu, které berou v úvahu víc parametrů. 

## <a name="supported-session-border-controllers-sbcs"></a>Podporované řadiče pro ohraničení relací (SBCs)

Probíhá certifikace. Mezitím můžou zákazníci použít [řadiče pro ohraničení certifikované relace](/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Další kroky

### <a name="conceptual-documentation"></a>Koncepční dokumentace

- [Koncept telefonního subsystému](./telephony-concept.md)
- [Typy telefonních čísel v komunikačních službách Azure](./plan-solution.md)
- [Ceny](../pricing.md)

### <a name="quickstarts"></a>Rychlé starty

- [Zavolat na telefon](../../quickstarts/voice-video-calling/pstn-call.md)