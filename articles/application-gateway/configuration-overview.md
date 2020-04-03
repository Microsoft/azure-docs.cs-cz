---
title: Přehled konfigurace brány aplikace Azure
description: Tento článek popisuje, jak nakonfigurovat součásti Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: f08cfab8f8de9183e6bee241959f7feabc31c8e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585909"
---
# <a name="application-gateway-configuration-overview"></a>Přehled konfigurace aplikační brány

Azure Application Gateway se skládá z několika součástí, které můžete nakonfigurovat různými způsoby pro různé scénáře. Tento článek ukazuje, jak nakonfigurovat jednotlivé součásti.

![Vývojový diagram součástí aplikační brány](./media/configuration-overview/configuration-overview1.png)

Tento obrázek znázorňuje aplikaci, která má tři naslouchací procesy. První dva jsou více-site `http://acme.com/*` posluchače pro a `http://fabrikam.com/*`, resp. Oba poslouchají na portu 80. Třetí je základní naslouchací proces, který má end-to-end transportní vrstvy zabezpečení (TLS) ukončení, dříve známé jako ssl (Secure Sockets Layer) ukončení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuální síť Azure a vyhrazená podsíť

Aplikační brána je vyhrazené nasazení ve vaší virtuální síti. V rámci virtuální sítě je pro aplikační bránu vyžadována vyhrazená podsíť. V podsíti můžete mít více instancí daného nasazení aplikační brány. Můžete také nasadit další aplikační brány v podsíti. Ale nelze nasadit žádné jiné prostředky v podsíti aplikační brány.

> [!NOTE]
> Nelze kombinovat Standard_v2 a standardní brány aplikace Azure ve stejné podsíti.

#### <a name="size-of-the-subnet"></a>Velikost podsítě

Aplikační brána používá jednu privátní IP adresu pro jednu instanci a další privátní IP adresu, pokud je nakonfigurována privátní front-endová IP adresa.

Azure také rezervuje pět IP adres v každé podsíti pro interní použití: první čtyři a poslední IP adresy. Zvažte například 15 instancí aplikační brány bez privátní ip adresy front-endu. Pro tuto podsíť potřebujete alespoň 20 IP adres: pět pro interní použití a 15 pro instance aplikační brány. Takže potřebujete /27 velikost podsítě nebo větší.

Zvažte podsíť, která má 27 instancí aplikační brány a IP adresu pro privátní front-endIP. V takovém případě potřebujete 33 IP adres: 27 pro instance aplikační brány, jednu pro privátní front-end a pět pro interní použití. Takže potřebujete /26 velikost podsítě nebo větší.

Doporučujeme použít velikost podsítě alespoň /28. Tato velikost vám dává 11 použitelných IP adres. Pokud zatížení aplikace vyžaduje více než 10 instancí aplikační brány, zvažte velikost podsítě /27 nebo /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Skupiny zabezpečení sítě v podsíti Aplikační brána

Skupiny zabezpečení sítě (NSG) jsou podporovány v application gateway. Existují však určitá omezení:

- Příchozí internetový provoz na portech TCP 65503-65534 je nutné povolit pro skladovou položku SKU brány aplikace v1 a porty TCP 65200-65535 pro skladovou položku v2 s cílovou podsítí jako **libovolnou** a zdroj jako značku služby **GatewayManager.** Tento rozsah portů je vyžadován pro komunikaci infrastruktury Azure. Tyto porty jsou chráněné (uzamčeny) certifikáty Azure. Externí entity, včetně zákazníků těchto bran, nemohou komunikovat v těchto koncových bodech.

- Odchozí připojení k internetu nelze blokovat. Výchozí odchozí pravidla v souboru zabezpečení sítě umožňují připojení k internetu. Doporučený postup:

  - Neodstraňujte výchozí odchozí pravidla.
  - Nevytvářejte další odchozí pravidla, která odpírají jakékoli odchozí připojení.

- Provoz ze značky **AzureLoadBalancer** musí být povolen.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Povolit přístup aplikační brány k několika zdrojovým IP adresy

V tomto scénáři použijte skupiny nsg v podsíti Aplikační brána. V tomto pořadí podle priority uložte do podsítě následující omezení:

1. Povolit příchozí provoz ze zdrojové IP nebo IP oblasti s cílem jako celý rozsah adres podsítě aplikační brány a cílový port jako vstupní přístupový port, například port 80 pro přístup HTTP.
2. Povolit příchozí požadavky ze zdroje jako značku služby **GatewayManager** a cíl jako **libovolné** a cílové porty jako 65503-65534 pro aplikační bránu v1 SKU a porty 65200-65535 pro v2 Skladovou položku pro [komunikaci stavu back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Tento rozsah portů je vyžadován pro komunikaci infrastruktury Azure. Tyto porty jsou chráněné (uzamčeny) certifikáty Azure. Bez příslušných certifikátů na místě externí entity nelze iniciovat změny na těchto koncových bodů.
3. Povolit příchozí sondy Nástroje pro vyrovnávání zatížení Azure (značka*AzureLoadBalancer)* a příchozí provoz virtuální sítě (značka*VirtualNetwork)* ve [skupině zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Zablokujte všechny ostatní příchozí přenosy pomocí pravidla odepřít vše.
5. Povolit odchozí provoz na internetu pro všechny cíle.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy definované uživatelem podporované v podsíti služby Application Gateway.

> [!IMPORTANT]
> Použití udr. [back-end health view](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) **Unknown** Může také způsobit generování protokolů a metrik aplikační brány k selhání. Doporučujeme nepoužívat UDR v podsíti Aplikační brána, abyste mohli zobrazit stav back-endu, protokoly a metriky.

- **v1**

   Pro skladovou položku v1 jsou v podsíti aplikační brány podporovány uživatelem definované trasy (UDR), pokud nemění komunikaci mezi koncovými požadavky a odpověďmi. Můžete například nastavit UDR v podsíti Aplikační brána tak, aby ukazovala na zařízení brány firewall pro kontrolu paketů. Ale musíte se ujistit, že paket může dosáhnout zamýšleného cíle po kontrole. Pokud tak neučiníte, může dojít k nesprávné mu znehodnocení stavu nebo chování směrování provozu. To zahrnuje učené trasy nebo výchozí trasy 0.0.0.0/0, které jsou šířeny branami Azure ExpressRoute nebo VPN ve virtuální síti.

- **v2**

   Pro skladovou položku v2 existují podporované a nepodporované scénáře:

   **podporované scénáře v2**
   > [!WARNING]
   > Nesprávná konfigurace směrovací tabulky může mít za následek asymetrické směrování v application gateway v2. Ujistěte se, že veškerý provoz roviny správy/řízení je odesílán přímo na Internet a ne prostřednictvím virtuálního zařízení. Mohlo by to mít vliv i na protokolování a metriky.


  **Scénář 1:** UDR zakázat šíření směrování protokolu NGP protokolu NGP protokolu NGP (Border Gateway Protocol) do podsítě Aplikační brána

   Někdy je výchozí trasa brány (0.0.0.0/0) inzerována prostřednictvím bran ExpressRoute nebo VPN přidružených k virtuální síti Aplikační brána. Tím se přeruší provoz roviny správy, což vyžaduje přímou cestu k Internetu. V takových scénářích lze použít UDR zakázat šíření trasy Protokolu BGP. 

   Chcete-li zakázat šíření trasy protokolu BGP, postupujte takto:

   1. Vytvořte prostředek tabulky trasy v Azure.
   2. Zakažte parametr **šíření trasy brány virtuální sítě.** 
   3. Přidružte tabulku trasy k příslušné podsíti. 

   Povolení UDR pro tento scénář by nemělo přerušit žádné existující nastavení.

  **Scénář 2**: UDR na přímé 0.0.0.0/0 na Internet

   Můžete vytvořit UDR pro odesílání 0.0.0.0/0 přenosů přímo do Internetu. 

  **Scénář 3:** UDR pro službu Azure Kubernetes kubenet

  Pokud používáte kubenet se službou Azure Kubernetes Service (AKS) a řadičem příchozího přenosu dat aplikační brány (AGIC), musíte nastavit směrovací tabulku, která umožní směrování odeslané do podů do správného uzlu. To nebude nutné, pokud používáte Azure CNI. 

   Chcete-li nastavit směrovací tabulku tak, aby kubenet fungoval, použijte následující kroky:

  1. Vytvořte prostředek tabulky trasy v Azure. 
  2. Po vytvoření přejděte na stránku **Trasy.** 
  3. Přidejte novou trasu:
     - Předpona adresy by měla být rozsah IP podů, které chcete dosáhnout v AKS. 
     - Další typ směrování by měl být **Virtual Appliance**. 
     - Další adresa směrování by měla být IP adresa uzlu hostujícího pody v rozsahu IP adres definovaném v poli předpony adresy. 
    
  **v2 nepodporované scénáře**

  **Scénář 1**: UDR pro virtuální zařízení

  Jakýkoli scénář, kde 0.0.0.0/0 musí být přesměrovány prostřednictvím libovolného virtuálního zařízení, centrální/paprskové virtuální sítě nebo místní (vynucené tunelové propojení) není podporován pro verzi v2 public Preview. 

## <a name="front-end-ip"></a>Ip adresa front-endu

Můžete nakonfigurovat aplikační bránu tak, aby měla veřejnou IP adresu, privátní IP adresu nebo obojí. Veřejná IP adresa je vyžadována, pokud hostujete back-end, ke kterému musí klienti přistupovat přes internet prostřednictvím virtuální IP adresy (VIP) orientované na internet. 

Veřejná IP adresa není vyžadována pro interní koncový bod, který není vystaven internetu. To se označuje jako vnitřní koncový bod *vyvažovače zatížení* (ILB) nebo privátní ip adresy front-endu. ILB aplikační brány je užitečné pro interní obchodní aplikace, které nejsou vystaveny internetu. Je také užitečné pro služby a vrstvy ve vícevrstvé aplikaci v rámci hranice zabezpečení, které nejsou vystaveny internetu, ale které vyžadují rozložení zatížení kruhového dotazování, lepivost relace nebo ukončení TLS.

Je podporována pouze 1 veřejná IP adresa nebo jedna privátní IP adresa. Front-endOVOU IP adresu zvolíte při vytváření aplikační brány.

- Pro veřejnou IP adresu můžete vytvořit novou veřejnou IP adresu nebo použít existující veřejnou IP adresu ve stejném umístění jako aplikační brána. Další informace naleznete [v tématu static kárně vs. dynamická veřejná IP adresa](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Pro privátní IP adresu můžete zadat privátní IP adresu z podsítě, kde je vytvořena aplikační brána. Pokud ji nezadáte, bude z podsítě automaticky vybrána libovolná adresa IP. Typ IP adresy, který vyberete (statický nebo dynamický), nelze později změnit. Další informace naleznete [v tématu Vytvoření aplikační brány s interním mechanismem vyrovnávání zatížení](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Front-endová IP adresa je přidružena k *naslouchací procesu*, který kontroluje příchozí požadavky na front-end IP.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení pomocí portu, protokolu, hostitele a adresy IP. Při konfiguraci naslouchací proces, musíte zadat hodnoty pro tyto, které odpovídají odpovídající hodnoty v příchozí požadavek na bránu.

Když vytvoříte aplikační bránu pomocí portálu Azure, vytvoříte také výchozí naslouchací proces výběrem protokolu a portu pro naslouchací proces. Můžete zvolit, zda chcete povolit podporu HTTP2 na naslouchací proces. Po vytvoření brány aplikace můžete upravit nastavení tohoto výchozího naslouchací hoschu *(appGatewayHttpListener)* nebo vytvořit nové naslouchací procesy.

### <a name="listener-type"></a>Typ posluchače

Při vytváření nového naslouchací proces, můžete si vybrat mezi [ *základní* a *více webu*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Pokud chcete, aby všechny vaše požadavky (pro libovolnou doménu) byly přijaty a předány do back-endových fondů, zvolte základní. [Naučte se, jak vytvořit aplikační bránu se základním naslouchací proces](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Pokud chcete předat požadavky do různých back-endových fondů na základě hlavičky *hostitele* nebo názvu hostitele, zvolte naslouchací proces s více weby, kde je také nutné zadat název hostitele, který odpovídá příchozí mu. Důvodem je, že aplikační brána spoléhá na hlavičky hostitele HTTP 1.1 pro hostování více než jednoho webu na stejné veřejné IP adrese a portu.

#### <a name="order-of-processing-listeners"></a>Pořadí zpracování posluchačů

Pro skladovou položku v1 jsou požadavky spárovány podle pořadí pravidel a typu naslouchacího procesu. Pokud pravidlo se základním naslouchací proces je na prvním místě v pořadí, je zpracována jako první a přijme jakýkoli požadavek na tento port a kombinaci IP. Chcete-li tomu zabránit, nejprve nakonfigurujte pravidla s naslouchacími procesy s více weby a posuňte pravidlo se základním naslouchacíproces na poslední v seznamu.

Pro skladovou položku v2 jsou posluchače s více pracemi zpracovány před základními naslouchacími procesy.

### <a name="front-end-ip"></a>Ip adresa front-endu

Zvolte front-endIP adresu, kterou chcete přidružit k tomuto naslouchacímu procesu. Naslouchací proces bude poslouchat příchozí požadavky na této IP adresy.

### <a name="front-end-port"></a>Front-end port

Zvolte port front-end. Vyberte existující port nebo vytvořte nový. Zvolte libovolnou [hodnotu z povoleného rozsahu portů](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Můžete použít nejen známé porty, například 80 a 443, ale všechny povolené vlastní porty, které jsou vhodné. Port lze použít pro veřejné naslouchací procesy nebo soukromé naslouchací procesy.

### <a name="protocol"></a>Protocol (Protokol)

Zvolte HTTP nebo HTTPS:

- Pokud zvolíte HTTP, provoz mezi klientem a aplikační bránou je nešifrovaný.

- Pokud chcete, aby [protokol TLS ukončil](features.md#secure-sockets-layer-ssltls-termination) nebo ukončil protokol [TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview), zvolte protokol HTTPS . Provoz mezi klientem a aplikační bránou je šifrován. A připojení TLS končí na aplikační bráně. Pokud chcete šifrování TLS od konce, musíte zvolit protokol HTTPS a nakonfigurovat nastavení **http back-end.** Tím je zajištěno, že provoz je znovu zašifrována při cestě z brány aplikace do back-endu.


Chcete-li nakonfigurovat ukončení TLS a end-to-end šifrování TLS, musíte přidat certifikát na schůdku, aby aplikační brána mohla odvodit symetrický klíč. To je dáno specifikací protokolu TLS. Symetrický klíč se používá k šifrování a dešifrování přenosů odeslaných do brány. Certifikát brány musí být ve formátu PFX (Personal Information Exchange). Tento formát umožňuje exportovat soukromý klíč, který brána používá k šifrování a dešifrování provozu.

#### <a name="supported-certificates"></a>Podporované certifikáty

Viz [certifikáty podporované pro ukončení TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Další podpora protokolu

#### <a name="http2-support"></a>Podpora HTTP2

Podpora protokolu HTTP/2 je k dispozici klientům, kteří se připojují pouze k naslouchacím chposluchačům aplikační brány. Komunikace s fondy serverů back-end je přes HTTP/1.1. Ve výchozím nastavení je podpora http/2 zakázána. Následující fragment kódu Azure PowerShellu ukazuje, jak to povolit:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Podpora protokolu WebSocket

Podpora websocketu je ve výchozím nastavení povolena. Neexistuje žádné uživatelsky konfigurovatelné nastavení, které by ho povolilo nebo zakázalo. WebSockets můžete použít s http a https naslouchací procesy.

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Vlastní chybu můžete definovat na globální úrovni nebo na úrovni posluchače. Ale vytváření vlastních chybových stránek na globální úrovni z portálu Azure není momentálně podporované. Můžete nakonfigurovat vlastní chybovou stránku pro chybu brány firewall webové aplikace 403 nebo stránku údržby 502 na úrovni posluchače. Je také nutné zadat veřejně přístupnou adresu URL objektu blob pro daný kód stavu chyby. Další informace najdete v tématu [Vytvoření vlastních chybových stránek služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Chybové kódy brány aplikace](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pokud chcete nakonfigurovat globální vlastní chybovou stránku, [přečtěte si informace o konfiguraci Prostředí Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Zásady TLS

Můžete centralizovat správu certifikátů TLS/SSL a snížit režii dešifrování pro serverovou farmu back-end. Centralizované zpracování TLS také umožňuje určit centrální zásady TLS, které jsou vhodné pro vaše požadavky na zabezpečení. Můžete zvolit *výchozí*, *předdefinované*nebo *vlastní* zásady TLS.

Zásady TLS nakonfigurujete tak, aby řídily verze protokolu TLS. Můžete nakonfigurovat aplikační bránu tak, aby používala minimální verzi protokolu pro protokoly TLS handshakes z protokolů TLS1.0, TLS1.1 a TLS1.2. Ve výchozím nastavení jsou ssl 2.0 a 3.0 zakázány a nelze je konfigurovat. Další informace naleznete v [tématu Přehled zásad TLS aplikační brány](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po vytvoření naslouchací proces, přidružení k pravidlo směrování požadavků. Toto pravidlo určuje, jak jsou požadavky přijaté na naslouchací proces směrovány do back-endu.

## <a name="request-routing-rules"></a>Požádat o pravidla směrování

Když vytvoříte aplikační bránu pomocí portálu Azure, vytvoříte výchozí pravidlo *(pravidlo1).* Toto pravidlo váže výchozí naslouchací proces *(appGatewayHttpListener)* s výchozím back-end fondem *(appGatewayBackendPool)* a výchozím nastavením HTTP back-end *(appGatewayBackendHttpSettings).* Po vytvoření brány můžete upravit nastavení výchozího pravidla nebo vytvořit nová pravidla.

### <a name="rule-type"></a>Typ pravidla

Při vytváření pravidla si můžete vybrat mezi [ *základní* a *na základě cesty*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Zvolte základní, pokud chcete předat všechny požadavky na přidružený naslouchací proces (například *blog<i></i>.contoso.com/)\** do jednoho back-endového fondu.
- Pokud chcete směrovat požadavky z konkrétních cest URL do konkrétních back-endových fondů, zvolte na základě cesty. Vzor cesty se použije pouze na cestu adresy URL, nikoli na parametry dotazu.

#### <a name="order-of-processing-rules"></a>Pořadí pravidel zpracování

Pro skladovou položku v1 je porovnávání vzorků příchozích požadavků zpracováno v pořadí, v jakém jsou cesty uvedeny v mapě cesty URL pravidla založeného na cestě. Pokud požadavek odpovídá vzoru ve dvou nebo více cestách v mapě cesty, cesta, která je uvedena jako první, je spárována. A požadavek je předán na zadní konec, který je spojen s touto cestou.

Pro skladovou položku v2 je přesná shoda vyšší prioritou než pořadí cest v mapě cesty URL. Pokud požadavek odpovídá vzoru ve dvou nebo více cest, požadavek je předán do back-endu, který je spojen s cestou, která přesně odpovídá požadavku. Pokud cesta v příchozí žádosti přesně neodpovídá žádné cestě v mapě, porovnávání vzorků požadavku se zpracuje v seznamu pořadí mapy cesty pro pravidlo založené na cestě.

### <a name="associated-listener"></a>Přidružený naslouchací proces

Přidružit naslouchací proces k pravidlu tak, aby *pravidlo směrování požadavku,* které je přidruženo k naslouchací proces je vyhodnocena k určení back-end fondu pro směrování požadavku.

### <a name="associated-back-end-pool"></a>Přidružený back-endový fond

Přidružte k pravidlu back-endový fond, který obsahuje back-endové cíle, které obsluhují požadavky, které posluchač obdrží.

 - Pro základní pravidlo je povolen pouze jeden fond back-end. Všechny požadavky na přidružené naslouchací proces jsou předány do fondu back-end.

 - Pro pravidlo založené na cestě přidejte více back-endových fondů, které odpovídají každé cestě url. Požadavky, které odpovídají zadané cestě url, jsou předány do odpovídajícího back-endového fondu. Přidejte také výchozí fond back-end. Požadavky, které neodpovídají žádné cestě URL v pravidle, jsou předány do tohoto fondu.

### <a name="associated-back-end-http-setting"></a>Přidružené nastavení HTTP back-endu

Přidejte back-end nastavení HTTP pro každé pravidlo. Požadavky jsou směrovány z brány aplikace do back-endových cílů pomocí čísla portu, protokolu a dalších informací, které jsou zadány v tomto nastavení.

Pro základní pravidlo je povoleno pouze jedno back-endové nastavení HTTP. Všechny požadavky na přidružené naslouchací proces jsou předány odpovídající back-end cíle pomocí tohoto nastavení HTTP.

Pro pravidlo založené na cestě přidejte více back-endových nastavení HTTP, které odpovídají každé cestě URL. Požadavky, které odpovídají cestě URL v tomto nastavení jsou předány odpovídající back-end cíle pomocí nastavení HTTP, které odpovídají každé cestě URL. Přidejte také výchozí nastavení HTTP. Požadavky, které neodpovídají žádné cestě URL v tomto pravidle, jsou předány do výchozího back-endového fondu pomocí výchozího nastavení HTTP.

### <a name="redirection-setting"></a>Nastavení přesměrování

Pokud přesměrování je nakonfigurován pro základní pravidlo, všechny požadavky na přidružené naslouchací proces jsou přesměrovány na cíl. Tohle je *globální* přesměrování. Pokud je pro pravidlo založené na cestě nakonfigurováno přesměrování, budou přesměrovány pouze požadavky v určité oblasti lokality. Příkladem je oblast nákupního košíku označená *parametrem /cart/\**. Toto je přesměrování *založené na cestě.*

Další informace o přesměrováních naleznete v tématu [Application Gateway redirect overview](redirect-overview.md).

#### <a name="redirection-type"></a>Typ přesměrování

Zvolte typ požadovaného přesměrování: *Permanent (301)*, *Temporary(307),* *Found(302)* nebo *See other(303)*.

#### <a name="redirection-target"></a>Cíl přesměrování

Jako cíl přesměrování zvolte jiný naslouchací proces nebo externí web.

##### <a name="listener"></a>Naslouchací proces

Zvolte naslouchací proces jako cíl přesměrování přesměrovat provoz z jednoho naslouchací proces na jiný na bránu. Toto nastavení je vyžadováno, pokud chcete povolit přesměrování HTTP-HTTPS. Přesměruje provoz ze zdrojového naslouchacího procesu, který kontroluje příchozí požadavky HTTP na cílový naslouchací proces, který kontroluje příchozí požadavky HTTPS. Můžete také zahrnout řetězec dotazu a cestu z původního požadavku v požadavku, který je předán do cíle přesměrování.

![Dialogové okno Součásti aplikační brány](./media/configuration-overview/configure-redirection.png)

Další informace o přesměrování HTTP-HTTPS naleznete v tématu:
- [Přesměrování HTTP na HTTPS pomocí portálu Azure](redirect-http-to-https-portal.md)
- [Přesměrování HTTP-HTTPS pomocí prostředí PowerShell](redirect-http-to-https-powershell.md)
- [Přesměrování http-https pomocí azure cli](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Externí web

Pokud chcete přesměrovat provoz na naslouchací proces, který je přidružen k tomuto pravidlu, na externí web, zvolte externí web. Můžete zahrnout řetězec dotazu z původního požadavku v požadavku, který je předán do cíle přesměrování. Cestu k externímu webu, který byl v původním požadavku, nelze předat dál.

Další informace o přesměrování naleznete v tématu:
- [Přesměrování provozu na externí web pomocí Prostředí PowerShell](redirect-external-site-powershell.md)
- [Přesměrování provozu na externí web pomocí rozhraní se knám se klis](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Přepsání nastavení záhlaví protokolu HTTP

Toto nastavení přidává, odebere nebo aktualizuje hlavičky požadavků a odpovědí HTTP, zatímco pakety požadavků a odpovědí se pohybují mezi klientským a back-endovým fondem. Další informace naleznete v tématu:

 - [Přepsat přehled záhlaví protokolu HTTP](rewrite-http-headers.md)
 - [Konfigurace přepisu hlavičky HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Nastavení HTTP

Aplikační brána směruje provoz na servery back-end pomocí konfigurace, kterou zde zadáte. Po vytvoření nastavení protokolu HTTP je nutné přidružit k jednomu nebo více pravidlům směrování požadavků.

### <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie.

Azure Application Gateway používá soubory cookie spravované bránou pro údržbu uživatelských relací. Když uživatel odešle první požadavek na application gateway, nastaví soubor cookie spřažení v odpovědi s hodnotou hash, která obsahuje podrobnosti relace, takže následné požadavky nesoucí soubor cookie spřažení budou směrovány na stejný back-endový server pro zachování lepivost. 

Tato funkce je užitečná, pokud chcete zachovat relaci uživatele na stejném serveru a pokud je stav relace uložen místně na serveru pro relaci uživatele. Pokud aplikace nemůže zpracovat spřažení založenou na souborech cookie, nelze tuto funkci použít. Chcete-li jej použít, ujistěte se, že klienti podporují soubory cookie.

Aktualizace [chromového prohlížeče](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) přinesla mandát, kde http cookies bez [atributu SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) musí být považovány za SameSite =Lax. V případě požadavků CORS (Cross-Origin Resource Sharing) pokud má být soubor cookie odeslán v kontextu třetí strany, musí použít *SameSite=None; Zabezpečené* atributy a měly by být odesílány pouze přes protokol HTTPS. V opačném případě v případě pouze http prohlížeč neodesílá soubory cookie v kontextu třetích stran. Cílem této aktualizace z Chromu je zvýšit zabezpečení a vyhnout se útokům na vyžádání mezi webovými servery (CSRF). 

Pro podporu této změny, počínaje únorem 17 2020, application gateway (všechny typy SKU) vloží další soubor cookie s názvem *ApplicationGatewayAffinityCORS* kromě existující ho souboru cookie *ApplicationGatewayAffinity.* Soubor cookie *ApplicationGatewayAffinityCORS* obsahuje další dva atributy (*"SameSite=None; Secure"*), takže lepkavá relace jsou udržovány i pro požadavky napříč původem.

Všimněte si, že výchozí název souboru cookie spřažení je *ApplicationGatewayAffinity* a můžete jej změnit. V případě, že používáte vlastní název souboru cookie spřažení, je jako přípona přidán další soubor cookie s CORS. Například *CustomCookieNameCORS*.

> [!NOTE]
> Pokud je nastaven atribut *SameSite=None,* je povinné, aby soubor cookie také obsahoval příznak *Zabezpečený* a musí být odeslán přes protokol HTTPS.  Pokud je vyžadována spřažení relace přes CORS, musíte migrovat úlohu na https. Informace o překládce TLS a dokumentaci od koncového tls pro aplikační bránu najdete tady – [Přehled](ssl-overview.md), [Konfigurace aplikační brány s ukončením Protokolu TLS pomocí portálu Azure](create-ssl-portal.md), Konfigurace [komplexního tls pomocí aplikace brány s portálem](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení pomáhá řádně odebrat členy fondu back-end během plánovaných aktualizací služby. Toto nastavení můžete použít na všechny členy back-endového fondu během vytváření pravidel. Zajišťuje, že všechny instance zrušení registrace back-endového fondu nadále udržují existující připojení a obsluhují průběžné požadavky na konfigurovatelný časový čas a nepřijímají žádné nové požadavky nebo připojení. Jedinou výjimkou jsou požadavky vázané na zrušení registrace instancí z důvodu spřažení relací spravované bránou a budou nadále předávány do instancí zrušení registrace. Vyprázdnění připojení platí pro back-endové instance, které jsou explicitně odebrány z fondu back-end.

### <a name="protocol"></a>Protocol (Protokol)

Aplikační brána podporuje protokol HTTP i protokol HTTPS pro směrování požadavků na servery back-end. Pokud zvolíte HTTP, provoz na servery back-end je nešifrovaný. Pokud nešifrovaná komunikace není přijatelná, zvolte HTTPS.

Toto nastavení v kombinaci s https v naslouchací proces podporuje [end-to-end TLS](ssl-overview.md). To vám umožní bezpečně přenášet citlivá data šifrovaná do back-endu. Každý server back-end v back-end fondu, který má povolenou platnost tls typu ukončení, musí být nakonfigurován s certifikátem umožňujícím zabezpečenou komunikaci.

### <a name="port"></a>Port

Toto nastavení určuje port, ve kterém servery back-end poslouchají přenosy z aplikační brány. Porty můžete konfigurovat od 1 do 65535.

### <a name="request-timeout"></a>Časový výtok požadavku

Toto nastavení je počet sekund, po které aplikační brána čeká na odpověď ze serveru back-end.

### <a name="override-back-end-path"></a>Přepsat zadní cestu

Toto nastavení umožňuje nakonfigurovat volitelnou vlastní cestu pro předávání, která se použije při předání požadavku do back-endu. Do předané cesty se zkopíruje jakákoli část příchozí cesty, která odpovídá vlastní cestě v poli **cesty back-end.** Následující tabulka ukazuje, jak tato funkce funguje:

- Pokud je nastavení HTTP připojeno k základnímu pravidlu směrování požadavků:

  | Původní požadavek  | Přepsat zadní cestu | Požadavek předán do back-endu |
  | ----------------- | --------------------- | ---------------------------- |
  | /domů/            | /přepsat/            | /přepsat/domů/              |
  | /home/secondhome/ | /přepsat/            | /přepsat/domů/secondhome/   |

- Pokud je nastavení HTTP připojeno k pravidlu směrování požadavků na základě cesty:

  | Původní požadavek           | Pravidlo cesty       | Přepsat zadní cestu | Požadavek předán do back-endu |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /přepsat/            | /přepsat/domů/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /přepsat/            | /přepsat/domů/secondhome/   |
  | /domů/                     | /pathrule*      | /přepsat/            | /přepsat/domů/              |
  | /home/secondhome/          | /pathrule*      | /přepsat/            | /přepsat/domů/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /přepsat/            | /přepsat/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /přepsat/            | /přepsat/secondhome/        |
  | /pathrule/                 | /pathrule/      | /přepsat/            | /přepsat/                   |

### <a name="use-for-app-service"></a>Použití pro službu aplikace

Toto je pouze zástupce ui, který vybere dvě požadovaná nastavení pro back-end služby Azure App Service. Umožňuje **vybrat název hostitele z back-endové adresy**a vytvoří novou vlastní sondu, pokud ji ještě nemáte. (Další informace naleznete v části [Nastavení názvu hostitele vyberte z adres back-end](#pick) v tomto článku.) Vytvoří se nová sonda a záhlaví sondy je vybráno z adresy back-endového člena.

### <a name="use-custom-probe"></a>Použití vlastní sondy

Toto nastavení přidruží [vlastní sondu](application-gateway-probe-overview.md#custom-health-probe) k nastavení PROTOKOLU HTTP. K nastavení PROTOKOLU HTTP lze přidružit pouze jednu vlastní sondu. Pokud explicitně nepřidružíte vlastní sondu, [výchozí sonda](application-gateway-probe-overview.md#default-health-probe-settings) se používá ke sledování stavu back-endu. Doporučujeme vytvořit vlastní sondu pro větší kontrolu nad monitorování stavu zadních konců.

> [!NOTE]
> Vlastní sonda nesleduje stav back-endového fondu, pokud odpovídající nastavení HTTP není explicitně přidruženo k naslouchací proces.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Vybrat jméno hostitele z adresy back-end

Tato funkce dynamicky nastaví záhlaví *hostitele* v požadavku na název hostitele back-endového fondu. Používá IP adresu nebo vícenežony.

Tato funkce pomáhá, když se název domény back-endu liší od názvu DNS aplikační brány a back-end spoléhá na konkrétní hlavičku hostitele, která se má přeložit na správný koncový bod.

Příkladem případu je víceklientské služby jako back-end. Služba aplikace je služba s více klienty, která používá sdílený prostor s jednou IP adresou. Takže služba aplikace lze přistupovat pouze prostřednictvím názvů hostitelů, které jsou nakonfigurovány v nastavení vlastní domény.

Ve výchozím nastavení je vlastní název domény *example.azurewebsites.net*. Chcete-li získat přístup ke službě aplikace pomocí brány aplikace prostřednictvím názvu hostitele, který není explicitně zaregistrován v aplikaci nebo prostřednictvím hlavního názvu názvu služby aplikace, přepíšete název hostitele v původním požadavku na název hostitele služby aplikace. Chcete-li to provést, povolte **nastavení vyskladnění hostitele z back-endové adresy.**

Pro vlastní doménu, jejíž stávající vlastní název DNS je mapován na službu aplikace, není třeba povolit toto nastavení.

> [!NOTE]
> Toto nastavení není vyžadováno pro prostředí služby App Service Environment, což je vyhrazené nasazení.

### <a name="host-name-override"></a>Přepsání názvu hostitele

Tato funkce nahradí hlavičku *hostitele* v příchozí masce v bráně aplikace zadaným názvem hostitele.

Pokud je například v nastavení **název hostitele** zadán`https://appgw.eastus.cloudapp.azure.com/path1` *www.contoso.com,* původní`https://www.contoso.com/path1` požadavek * se změní na * při předání požadavku serveru back-end.

## <a name="back-end-pool"></a>Back-endový fond

Back-endový fond můžete nasměrovat na čtyři typy back-endových členů: konkrétní virtuální počítač, škálovací sadu virtuálních počítačů, IP adresu/vícenež název souboru souborů nebo službu aplikace. 

Po vytvoření back-endového fondu jej musíte přidružit k jednomu nebo více pravidlům směrování požadavků. Je také nutné nakonfigurovat sondy stavu pro každý back-endový fond na vaší aplikační bráně. Pokud je splněna podmínka pravidla směrování požadavku, brána aplikace předá provoz na servery v pořádku (jak je určeno sondami stavu) v odpovídajícím back-endovém fondu.

## <a name="health-probes"></a>Sondy stavu

Aplikační brána ve výchozím nastavení monitoruje stav všech prostředků v back-endu. Ale důrazně doporučujeme vytvořit vlastní sondu pro každé nastavení http back-end získat větší kontrolu nad monitorování stavu. Informace o konfiguraci vlastní sondy naleznete v [tématu Vlastní nastavení sondy stavu](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Po vytvoření vlastní sondy stavu je třeba ji přidružit k nastavení http back-endu. Vlastní sonda nebude sledovat stav back-endového fondu, pokud odpovídající nastavení HTTP není explicitně přidruženo k naslouchací proces pomocí pravidla.

## <a name="next-steps"></a>Další kroky

Nyní, když víte o součástech aplikační brány, můžete:

- [Vytvoření aplikační brány na webu Azure Portal](quick-create-portal.md)
- [Vytvoření aplikační brány pomocí PowerShellu](quick-create-powershell.md)
- [Vytvoření aplikační brány pomocí nastavení příkazového příkazu Azure](quick-create-cli.md)
