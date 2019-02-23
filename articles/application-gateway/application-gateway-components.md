---
title: Azure Application Gateway komponenty
description: Tento článek obsahuje informace o různých komponent ve službě Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 14f400a1b85e213496ac98996d6c2378cf559026
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675784"
---
# <a name="application-gateway-components"></a>Součásti aplikace brány

 Službu Application Gateway slouží jako jediný bod kontaktu pro klienty. U více fondů back-end, jako jsou virtuální počítače Azure, VMSS, App Services nebo na místní nebo externí servery ji distribuuje příchozí aplikační provoz. K tomu využívá několik komponent, které jsou popsané níže:

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip"></a>IP front-endu

Přední stěnou IP adresa je adresa IP (Internet Protocol) přidružená k bráně aplikace. Můžete nakonfigurovat Application Gateway buď mít veřejnou IP adresu, privátní IP Adresou nebo obojí. Ve službě application gateway se podporuje jenom jednu veřejnou IP adresu. Virtuální síť a veřejnou IP adresu musí být ve stejném umístění jako vaše brána Application Gateway.

### <a name="static-vs-dynamic-public-ip"></a>Porovnání statické a dynamické veřejné IP

V1 SKU podporuje statické interní IP adresy, ale nepodporuje statické veřejné IP adresy. Virtuální IP adresu můžete změnit, pokud application gateway je zastavit a spustit. Název DNS, který je přidružený k službě application gateway nemění přes životní cyklus brány. Z tohoto důvodu se doporučuje použít CNAME alias a přejděte na adresu DNS služby application gateway.

SKU v2 Application Gateway podporuje statické veřejné IP adresy a také jako interní IP adresy. Ve službě application gateway se podporuje jenom jednu veřejnou IP adresu.

Po vytvoření front-endová IP, je přidružen k *naslouchacích procesů* kontrolovat příchozí žádosti na IP adresu front-endu.

## <a name="listeners"></a>Naslouchací procesy

Než začnete používat vaše brána Application Gateway, musíte přidat jednu nebo víc naslouchacích procesů. Naslouchací proces je proces, pomocí které Application Gateway kontroluje příchozí požadavky na připojení použití protokol a port, který jste nakonfigurovali. Po naslouchací proces detekuje příchozí žádosti od klientů, službu Application Gateway směrovat tyto žádosti do back-end serverů ve fondu back-end pomocí pravidel směrování žádosti, které definujete pro naslouchací proces, který obdržel příchozího požadavku.

Naslouchací procesy podporují následující porty a protokoly:

### <a name="ports"></a>Porty

Toto je port, na kterém naslouchá naslouchací proces na žádosti klientů. Konfigurace portu je povolen následující oblasti: 1–65535

### <a name="protocols"></a>Protokoly

Služba Application Gateway podporuje následující protokoly pro 4: HTTP, HTTPS, HTTP/2, Websocket

Při konfiguraci naslouchacího procesu, musíte zvolit protokol HTTP a HTTPS. Služba Application Gateway poskytuje nativní podporu pro protokoly Websocket a HTTP/2. S naslouchacích procesů HTTP a HTTPS můžete použít objekty Websocket. 

Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. Ve výchozím nastavení je zakázána podpora HTTP/2. Následující příklad fragmentu kódu prostředí Azure PowerShell ukazuje, jak můžete zajistit:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Ve výchozím nastavení je povolena podpora protokolu Websocket. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

Naslouchací proces HTTPS můžete použít pro ukončení protokolu SSL, který bude snížení zatížení využitím šifrování a dešifrování pro vaši bránu Application Gateway tak, aby vaše webové servery může dešifrováním nákladné režie šifrování a dešifrování a vaše aplikace můžou zaměřit na svoje obchodní logiku. Je nutné nasadit alespoň jeden certifikát serveru SSL pro naslouchací proces HTTPS. Další informace najdete v tématu [konfiguraci ukončení protokolu SSL](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení. Služba Application gateway můžou zobrazovat vlastní chybovou stránku, když požadavek nemá přístup na back-endu. Další informace najdete v tématu [vlastní chybové stránky pro vaši bránu Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error)

### <a name="types-of-listeners"></a>Typy naslouchacích procesů

Existují dva typy naslouchacích procesů:

- **Základní**: Tento typ naslouchací proces naslouchá na jednu doménu web, kde má jednoho mapování DNS na IP adresu služby Application Gateway. Tato konfigurace naslouchacího procesu je nutná, pokud jsou hostiteli jedné lokality za službou Application gateway
- **Multi-Site**: Tato konfigurace naslouchacího procesu je nutná, pokud konfigurujete víc než jednu webovou aplikaci na stejné instance služby application gateway. To umožňuje nakonfigurovat efektivnější topologii vašich nasazení tak, že přidáte až 100 webů do jedné služby application gateway. Každou stránku lze přesměrovat na vlastní back-endový fond. Příklad:  Pro tři subdomény – abc.alpha.com, xyz.alpha.com a pqr.alpha.com přejděte na adresu IP služby Application Gateway. Vytvořte 3 naslouchacích procesů typu "více lokalit" a nakonfigurujte každý naslouchací proces pro příslušný port a nastavení protokolu. 

Po vytvoření naslouchacího procesu, přiřaďte ji k žádosti o pravidlo směrování, která určuje, jak se žádost o přijetí na naslouchací proces má být směrována na back-endu.

Naslouchací procesy jsou zpracovávány v pořadí, ve kterém jsou zobrazeny. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí žádosti zpracovávat jej nejprve. Naslouchací procesy Multi-Site by měl být nakonfigurovaný před základní naslouchací proces chcete zajistit, aby provoz se směruje do správného back endu.

## <a name="request-routing-rule"></a>Požádat o pravidlo směrování

Toto je nejdůležitější komponenta služby Application Gateway a určuje, jak se směrovat provoz na naslouchací proces spojený s tímto pravidlem. Pravidlo váže naslouchací proces a fond back endového serveru nastavení HTTP back-end a definuje, kterému fondu back endového serveru provoz směrovat při volání příslušného naslouchacího procesu. Jeden naslouchací proces lze připojit k pouze jedno pravidlo.

Může existovat dva typy pravidel směrování žádosti:

- **Basic:** Všechny žádosti na přidružený naslouchací proces (např: blog.contoso.com/*) se předávají do přidružené back-endový fond, pomocí přidružené nastavení protokolu HTTP.
- **Na základě cest:** Tento typ pravidla poskytuje možnost směrování žádostí na přidružený naslouchací proces pro konkrétní back-end fondu na základě adresy URL v požadavku. Pokud cesta adresy URL v požadavku odpovídá vzor cesty v pravidle na základě cest, požadavek se směruje pomocí tohoto pravidla. Vzor cesty platí pouze pro cestu adresy URL, aby jeho parametry dotazu. 

Pokud cesta adresy URL požadavku na naslouchací proces neodpovídá žádné z pravidel na základě cest, pak požadavek se přesměruje do *výchozí* back-endového fondu a *výchozí* nastavení HTTP.

Pravidla se zpracovávají v pořadí, ve kterém jsou nakonfigurované. Doporučuje se, že jsou před základních pravidel, snížíte pravděpodobnost, že provoz se směruje na nevhodný back-end jako základní pravidlo by odpovídala provoz na portu před Multi-Site pravidlo se vyhodnotí na základě nakonfigurovaná pravidla více lokalit.

### <a name="redirection-support"></a>Podpora přesměrování

Pravidlo směrování požadavku také umožňuje přesměrování provozu ve službě Application Gateway. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. To vám umožní automatické HTTP do HTTPS přesměrování zajistit, že se že veškerá komunikace mezi aplikací a vaši uživatelé probíhá přes šifrované cestu. Můžete určit cílový naslouchací proces nebo externí web, ke kterému je žádoucí přesměrování. Element konfigurace také podporuje možnosti, jak povolit připojení řetězec identifikátoru URI cestu a dotaz na přesměrované adresu URL. Můžete také zvolit, zda přesměrování je dočasný (stavový kód HTTP 302) nebo trvalé přesměrování (kód stavu protokolu HTTP 301). Při použití základního pravidla, konfigurace přesměrování je přidružený zdroj naslouchací proces a je globální přesměrování. Při použití pravidla na základě cest, přesměrování konfigurace je definována v mapování cestu adresy URL. To platí jen pro konkrétní cesty oblasti lokality. Další informace najdete v tématu [přesměrování provozu ve službě Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Služba Application Gateway vloží záhlaví x předané u, x-forwarded-proto a x předané port do žádosti předané back-endu. Formát pro x předané – pro hlavičky je čárkou oddělený seznam IP: port. Platné hodnoty pro x-forwarded-proto jsou http nebo https. X předané port Určuje port, ve kterém bylo dosaženo žádost ve službě application gateway. Služba Application Gateway také vloží hlavičku X-původní-hostitele, která obsahuje hlavičku původního hostitele, pomocí kterého Přišla žádost. Tato hlavička se užitečné v situacích, jako jsou integrace webu Azure, ve kterém se upraví hlavičku hostitele příchozího před provoz směruje do back-endu.

#### <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Pomocí pravidel směrování žádosti můžete přidat, odebrat nebo aktualizovat hlavičky požadavku a odpovědi HTTP (S), zatímco pakety žádostí a odpovědí přesunutí mezi klientem a back-endových fondů, přes Application Gateway. Záhlaví nejde nastavit pouze na statické hodnoty, ale také k jiné záhlaví a důležitých serverových proměnných. To vám pomůže provádět několik případů použití důležité, jako je například extrahování IP adresu klienti odebrání citlivých informací o back-endu, přidat další bezpečnostní opatření, atd. Další informace najdete v tématu[hlavičky HTTP přepsat na vaše brána Application Gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)

## <a name="http-settings"></a>Nastavení HTTP

Application Gateway směruje provoz do back-end serverů pomocí číslo portu, protokolu a další nastavení zadané v tomto prostředku. 

Back-endové fondy podporují následující porty a protokoly:

### <a name="ports"></a>Porty

Toto je port, na kterém jsou servery back-end naslouchání provoz přicházející z aplikační brány. Konfigurace portu je povolen následující oblasti: 1–65535

### <a name="protocols"></a>Protokoly

Služba Application Gateway podporuje protokoly HTTP a HTTPS pro směrování žádostí na back-end serverů.

Pokud je vybrán protokol HTTP, provoz pak toky nešifrován na back-end serverů. 

V těchto případech nešifrovaná komunikace s back-end servery nepřijatelnou možností měli byste zvolit protokol HTTPS. Toto nastavení kombinované s výběrem v naslouchací proces protokolu HTTPS vám umožní povolit [kompletního protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Který vám umožní bezpečně přenášet citlivá data do back-endu v zašifrované. Každý back-endový server v back-endovém fondu s povoleným koncovým šifrováním protokolu SSL musí být pro umožnění bezpečné komunikace nakonfigurován s certifikátem.

Při použití nastavení protokolu HTTP můžou využívat několik možností:

### <a name="cookie-based-session-affinity"></a>Spřažení relace na základě souborů cookie

Tato funkce je užitečná, pokud chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, pak nebudete moct používat tuto funkci

### <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení lze použít u všech členů fondu back-endu během vytváření pravidla. Je-li služba Application Gateway povolena, zajistí, aby instance back-endového fondu, u nichž se ruší registrace, nedostávaly žádné nové žádosti, a zároveň umožní dokončit stávající žádosti v nakonfigurovaném časovém limitu. To platí jak pro instance back-endu, které jsou výslovně odebrány z back-endového fondu na základě volání rozhraní API, tak pro takové, které jsou podle sond stavu hlášeny jako poškozené.

### <a name="override-backend-path"></a>Přepsat cestu na back-end

Tato funkce umožňuje přepsání cesty v adrese URL, takže je možné směrovat požadavky pro konkrétní cestu, do jiné cesty. Například, pokud chcete směrovat požadavky pro contoso.com/images výchozí a pak zadejte "/" do tohoto textového pole a následně připojit k přidruženo contoso.com/images pravidlo tomuto nastavení HTTP.

### <a name="pick-host-name-from-a-backend-address"></a>Vybrat název hostitele z adres back-endu

Tato možnost nastaví *hostitele* hlavičky v požadavku na název hostitele back-endového fondu (IP nebo plně kvalifikovaný název domény). To je užitečné ve scénářích, kde název domény pro back-end se liší od názvu DNS aplikace, jako je například scénáře použití služby Azure App Service jako back-end. Je to proto, že služby Azure App Service je prostředí s více tenanty pomocí sdílené místo jednu IP adresu, službu App Service je přístupný pouze s názvy hostitelů nakonfigurované v nastavení vlastní domény. Ve výchozím nastavení je "example.azurewebsites.net" a pokud budete chtít přístup ke službě App Service pomocí Application Gateway s názvem hostitele není zaregistrovaný ve službě App Service nebo Application Gateway plně kvalifikovaný název domény, musíte přepsat jako název hostitele v původní požadavek na aplikaci Název hostitele služby.

### <a name="host-override"></a>**Přepsání hostitele**

Tato novinka nahrazuje *hostitele* záhlaví v příchozím požadavku ve službě Application Gateway na název hostitele, který zde určíte. 

Jakmile vytvoříte nastavení HTTP, budete se muset spojit s jedním nebo více pravidel směrování žádostí.

## <a name="backend-pool"></a>Back-endový fond

Back-endového fondu se používá k směrovat požadavky do back-end serverů, které budou sloužit pro požadavek. Back-endové fondy se může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure App Service. Členy fondu back-end brány aplikace nejsou vázané na skupinu dostupnosti. Členů fondu back-end může být napříč clustery, datová centra, Azure nebo mimo něj, tak dlouho, dokud mají připojení pomocí IP adresy. Můžete vytvořit jiný back-endové fondy pro různé typy požadavků. Například vytvořte jeden back-endového fondu pro obecné požadavky a druhý fond back-end pro požadavky na mikroslužby pro vaši aplikaci.

Po vytvoření back-endový fond je nutné přidružit jednu nebo více směrování pravidla požadavku. Budete potřebovat ke konfiguraci sondy stavu pro každý fond back-endu na vaše brána Application Gateway. Při splnění podmínkou pravidla směrování žádostí na službu Application Gateway předává provoz na v pořádku servery (jako sond stavu) v odpovídající back-endový fond.

## <a name="health-probes"></a>Sondy stavu

Ve výchozím nastavení služba Azure Application Gateway monitoruje stav všech prostředků v jeho back endovém fondu a automaticky odstraní všechny prostředky z fondu považoval za poškozený. Služba Application Gateway pokračuje v monitorování instance v nedobrém stavu a přidá je zpět do fondu back-end v pořádku, jakmile budou k dispozici a reagují na sondy stavu. Služba Application gateway odesílá že sondy stavu se stejný port, který je definován v nastavení HTTP back-end.

Kromě používání výchozího stavu testu monitorování, můžete také přizpůsobit sondu stavu, aby odpovídala požadavkům vaší aplikace. Vlastní sondy vám umožní mít podrobnější kontrolu nad monitorování stavu. Pokud používáte vlastní sondy, můžete nakonfigurovat interval testu, adresu URL a cesty k testování a počet neúspěšných odpovědí tak, aby přijímal před označením instance back endovém fondu jako není v pořádku. Důrazně doporučujeme, abyste nakonfigurovali vlastní sondy pro sledování stavu jednotlivých back-endového fondu. Další informace najdete v tématu [monitorovat stav služby Application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)

## <a name="next-steps"></a>Další postup

Po získání informací o službě Application Gateway součásti, můžete [služby Application Gateway můžete vytvořit na webu Azure Portal](quick-create-portal.md) nebo [vytvoření služby Application Gateway pomocí Powershellu](quick-create-powershell.md) nebo [vytvoření Application Gateway pomocí Azure CLI](quick-create-cli.md).