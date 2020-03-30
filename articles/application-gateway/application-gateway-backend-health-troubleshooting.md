---
title: Řešení problémů se stavem back-endu v Azure Application Gateway
description: Popisuje, jak řešit problémy se stavem back-endu pro Bránu aplikací Azure.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72001666"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Poradce při potížích se stavem back-endu v bráně aplikace
==================================================

<a name="overview"></a>Přehled
--------

Ve výchozím nastavení Azure Application Gateway sonduje back-endové servery, aby zkontrolovala jejich stav a zkontrolovala, jestli jsou připravené k zobrazování požadavků. Uživatelé mohou také vytvořit vlastní sondy pro uvedení názvu hostitele, cesty, která má být sondována, a stavových kódů, které mají být přijaty jako v pořádku. V každém případě pokud back-endový server nereaguje úspěšně, aplikační brána označí server jako není v pořádku a zastaví předávání požadavků na server. Poté, co server začne úspěšně reagovat, aplikace gateway obnoví předávání požadavků.

### <a name="how-to-check-backend-health"></a>Jak zkontrolovat stav back-endu

Chcete-li zkontrolovat stav back-endového fondu, můžete použít stránku **Back-end ové stavna** webu Azure Portal. Nebo můžete použít [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)nebo [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

Stav načtený některou z těchto metod může být některá z následujících možností:

- V pořádku

- Není v pořádku

- Není známo

Pokud je stav back-endu pro server v pořádku, znamená to, že aplikační brána předá požadavky na tento server. Ale pokud stav back-endu pro všechny servery v back-endu fondu není v pořádku nebo neznámý, může dojít k problémům při pokusu o přístup k aplikacím. Tento článek popisuje příznaky, příčinu a řešení pro každou ze zobrazených chyb.

<a name="backend-health-status-unhealthy"></a>Stav back-endu: Není v pořádku
-------------------------------

Pokud je stav back-endu Není v pořádku, zobrazení portálu se bude podobat následujícímu snímku obrazovky:

![Stav back-endové brány aplikace – není v pořádku](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Nebo pokud používáte dotaz azure prostředí, rozhraní příkazového příkazu nebo rozhraní AZURE REST API, dostanete odpověď, která se podobá následující:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Poté, co obdržíte stav back-endového serveru Není v pořádku pro všechny servery v back-endovém fondu, požadavky nejsou předány na servery a application gateway vrátí chybu "502 Bad Gateway" žádajícímu klientovi. Chcete-li tento problém vyřešit, zkontrolujte sloupec **Podrobnosti** na kartě **Stav back-endu.**

Zpráva zobrazená ve **sloupci Podrobnosti** poskytuje podrobnější přehledy o problému a na základě těchto informací můžete začít řešit problém.

> [!NOTE]
> Výchozí požadavek sondy je \<\>odeslán ve formátu protokolu ://127.0.0.1:\<port\>/. Například http://127.0.0.1:80 pro sondu http na portu 80. Pouze stavové kódy HTTP 200 až 399 jsou považovány za v pořádku. Protokol a cílový port jsou zděděny z nastavení protokolu HTTP. Pokud chcete, aby aplikační brána zkoumala jiný protokol, název hostitele nebo cestu a rozpoznala jiný stavový kód jako V pořádku, nakonfigurujte vlastní sondu a přidružte ji k nastavení protokolu HTTP.

<a name="error-messages"></a>Chybové zprávy
------------------------
#### <a name="backend-server-timeout"></a>Časový čas serveru back-endu

**Zpráva:** Čas přijatý back-endreagovat na\'sondu stavu brány aplikace je více než prahová hodnota časového limitu v nastavení sondy.

**Příčina:** Poté, co brána aplikace odešle požadavek sondy HTTP(S) na server back-end, čeká na odpověď z back-endového serveru po nakonfigurovanou dobu. Pokud back-endový server nereaguje v rámci nakonfigurované ho období (hodnota časového času), je označen jako Není v pořádku, dokud začne reagovat v rámci nakonfigurované ho časového období znovu.

**Rozlišení:** Zkontrolujte, proč back-endový server nebo aplikace nereaguje v rámci nakonfigurovaného časového období a také zkontrolujte závislosti aplikací. Zkontrolujte například, zda databáze obsahuje nějaké problémy, které by mohly vyvolat zpoždění v odpovědi. Pokud jste si vědomi chování aplikace a měla by reagovat pouze po hodnotě časového času, zvyšte hodnotu časového času z nastavení vlastní sondy. Chcete-li změnit hodnotu časového času, musíte mít vlastní sondu. Informace o konfiguraci vlastní sondy [naleznete na stránce dokumentace](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Chcete-li zvýšit hodnotu časového času, postupujte takto:

1.  Přímý přístup k serveru back-end a zkontrolujte čas, který server na této stránce zareagoval. Pro přístup k back-endovému serveru, včetně prohlížeče pomocí vývojářských nástrojů, můžete použít libovolný nástroj.

1.  Po zjištění doby, kterou aplikace odebrala, vyberte kartu **Sondy stavu** a pak vyberte sondu, která je přidružená k nastavení protokolu HTTP.

1.  Zadejte libovolnou hodnotu časového času, která je větší než doba odezvy aplikace, v sekundách.

1.  Uložte vlastní nastavení sondy a zkontrolujte, zda se stav back-endu nyní zobrazuje jako V pořádku.

#### <a name="dns-resolution-error"></a>Chyba překladu DNS

**Zpráva:** Aplikační brána nemohla vytvořit sondu pro tento back-end. K tomu obvykle dochází v případě nesprávně zadaného plně kvalifikovaného názvu domény back-endu. 

**Příčina:** Pokud je back-endový fond typu IP adresa/fqdn nebo služba app, aplikační brána se překládá na IP adresu hlavního názvu domény zadanéprostřednictvím dns (vlastní nebo Azure default) a pokusí se připojit k serveru na portu TCP uvedeném v nastavení protokolu HTTP. Pokud se však zobrazí tato zpráva, naznačuje, že aplikační brána nemohla úspěšně vyřešit ip adresu zadaného vícenežového názvového práva.

**Rozlišení:**

1.  Ověřte, zda je hlavní název domény zadaný v back-endovém fondu správný a zda se jedná o veřejnou doménu, a zkuste jej vyřešit z místního počítače.

1.  Pokud můžete přeložit IP adresu, může být něco v nepořádku s konfigurací DNS ve virtuální síti.

1.  Zkontrolujte, zda je virtuální síť nakonfigurována s vlastním serverem DNS. Pokud ano, zkontrolujte server DNS o tom, proč nemůže přeložit na IP adresu zadaného vícenežnatého názvového práva.

1.  Pokud používáte Azure default DNS, zkontrolujte u svého registrátora názvů domén, zda bylo dokončeno správné mapování záznamů A nebo CNAME.

1.  Pokud je doména soukromá nebo interní, zkuste ji vyřešit z virtuálního počítače ve stejné virtuální síti. Pokud ji můžete vyřešit, restartujte aplikační bránu a zkontrolujte to znovu. Chcete-li restartovat aplikační bránu, musíte [zastavit](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) a [spustit](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) pomocí příkazů prostředí PowerShell popsaných v těchto propojených prostředcích.

#### <a name="tcp-connect-error"></a>Chyba připojení protokolu TCP

**Zpráva:** Brána aplikace se nemůže připojit k back-endu.
Zkontrolujte, zda back-end reaguje na portu použitém pro sondu.
Zkontrolujte také, zda některý nsg / UDR / firewall blokuje přístup k Ip a portu tohoto back-endu

**Příčina:** Po fázi překladu DNS se aplikace Gateway pokusí připojit k back-endovému serveru na portu TCP, který je nakonfigurován v nastavení protokolu HTTP. Pokud aplikační brána nemůže vytvořit relaci TCP na zadaném portu, sonda je označena jako Není v pořádku s touto zprávou.

**Řešení:** Pokud se zobrazí tato chyba, postupujte takto:

1.  Zkontrolujte, zda se můžete připojit k back-endovému serveru na portu uvedeném v nastavení protokolu HTTP pomocí prohlížeče nebo prostředí PowerShell. Spusťte například následující příkaz:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Pokud uvedený port není požadovaným portem, zadejte správné číslo portu pro aplikaci Gateway pro připojení k back-endovému serveru

1.  Pokud se nemůžete připojit na portu také z místního počítače, pak:

    a.  Zkontrolujte nastavení skupiny zabezpečení sítě (NSG) síťového adaptéru a podsítě serveru back-end a zda jsou povolena příchozí připojení k nakonfigurovanému portu. Pokud tomu tak není, vytvořte nové pravidlo, které umožní připojení. Informace o tom, jak vytvořit pravidla souboru nsg, [naleznete na stránce dokumentace](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Zkontrolujte, zda nastavení skupiny zabezpečení sítě podsítě Aplikační brána povoluje odchozí veřejný a soukromý provoz, aby bylo možné vytvořit připojení. Další informace o vytváření pravidel souboru nSG najdete na stránce dokumentu, která je k dispozici v kroku 3a.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Zkontrolujte uživatelem definované nastavení tras (UDR) aplikační brány a podsítě back-endového serveru, zda nenajdete všechny anomálie směrování. Ujistěte se, že UDR nesměruje provoz mimo podsíť back-endu. Můžete například vyhledat trasy do síťových virtuálních zařízení nebo výchozí trasy inzerované do podsítě Aplikační brána prostřednictvím Azure ExpressRoute nebo VPN.

    d.  Chcete-li zkontrolovat efektivní trasy a pravidla pro síťový adaptér, můžete použít následující příkazy prostředí PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Pokud nenajdete žádné problémy s nsg nebo UDR, zkontrolujte back-endový server pro problémy související s aplikací, které brání klientům v vytvoření relace TCP na nakonfigurovaných portech. Několik věcí, které je třeba zkontrolovat:

    a.  Otevřete příkazový řádek (Win+R\> `netstat`- cmd), zadejte a vyberte Enter.

    b.  Zkontrolujte, zda server naslouchá na nakonfigurovaném portu. Například:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Pokud na konfigurovaném portu neposlouchá, zkontrolujte nastavení webového serveru. Například: vazby webu ve službách IIS, blok serveru v NGINX a virtuální hostitel v Apache.

    d.  Zkontrolujte nastavení brány firewall operačního systému a ujistěte se, že je povolen příchozí provoz na port.

#### <a name="http-status-code-mismatch"></a>Neshoda stavového kódu PROTOKOLU HTTP

**Zpráva:** Stavový kód odpovědi\'HTTP back-endu s neodpovídá nastavení sondy. Bylo přijato:{HTTPStatusCode0} :{HTTPStatusCode1}.

**Příčina:** Po navázání připojení TCP a dokončení protokolu SSL handshake (pokud je povoleno ssl), aplikace gateway odešle sondu jako požadavek HTTP GET na back-endový server. Jak je popsáno výše, výchozí \<sonda\>bude protokol ://127.0.0.1:\<port\>/, a považuje kódy stavu odezvy ve vzteku 200 až 399 jako v pořádku. Pokud server vrátí jiný stavový kód, bude označen jako Není v pořádku s touto zprávou.

**Řešení:** V závislosti na kódu odpovědi back-endového serveru můžete provést následující kroky. Zde je uvedeno několik běžných stavových kódů:

| **Chyba** | **Akce** |
| --- | --- |
| Neshoda stavového kódu sondy: Přijato 401 | Zkontrolujte, zda back-endový server vyžaduje ověření. Sondy aplikační brány nemohou v tomto okamžiku předat pověření pro ověřování. Povolte \"protokol HTTP\" 401 ve shodě shody stavového kódu sondy nebo sondu na cestu, kde server nevyžaduje ověření. | |
| Neshoda stavového kódu sondy: Přijato 403 | Přístup zakázán. Zkontrolujte, zda je na back-endovém serveru povolen přístup k cestě. | |
| Neshoda stavového kódu sondy: Přijato 404 | Stránka nebyla nalezena. Zkontrolujte, zda je cesta názvu hostitele přístupná na serveru back-end. Změňte název hostitele nebo parametr cesty na přístupnou hodnotu. | |
| Neshoda stavového kódu sondy: Přijato 405 | Požadavky na sondu pro aplikační bránu používají metodu HTTP GET. Zkontrolujte, zda server tuto metodu umožňuje. | |
| Neshoda stavového kódu sondy: Přijato 500 | Vnitřní chyba serveru. Zkontrolujte stav back-endového serveru a jestli jsou služby spuštěné. | |
| Neshoda stavového kódu sondy: Přijato 503 | Nedostupná služba. Zkontrolujte stav back-endového serveru a jestli jsou služby spuštěné. | |

Nebo pokud si myslíte, že odpověď je legitimní a chcete, aby aplikační brána přijímat jiné stavové kódy jako v pořádku, můžete vytvořit vlastní sondu. Tento přístup je užitečný v situacích, kdy web back-endu potřebuje ověření. Vzhledem k tomu, že požadavky probe nenesou žádná pověření uživatele, se nezdaří a stavový kód HTTP 401 bude vrácen serverem back-end.

Chcete-li vytvořit vlastní sondu, postupujte [takto](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Neshoda těla odpovědi HTTP

**Zpráva:** Tělo odpovědi HTTP\'back-endu s neodpovídá nastavení sondy. Tělo přijaté odpovědi neobsahuje {string}.

**Příčina:** Při vytváření vlastní sonda, máte možnost označit back-endový server jako v pořádku odpovídající řetězec z těla odpovědi. Můžete například nakonfigurovat aplikační bránu tak, aby přijímala "neautorizováno" jako řetězec, který odpovídá. Pokud odpověď serveru back-end pro požadavek na sondu obsahuje řetězec **neoprávněný**, bude označen jako V pořádku. V opačném případě bude označenjako není v pořádku s touto zprávou.

**Řešení:** Chcete-li tento problém vyřešit, postupujte takto:

1.  Získejte přístup k back-endovému serveru místně nebo z klientského počítače na cestě sondy a zkontrolujte tělo odpovědi.

1.  Ověřte, zda se tělo odpovědi ve vlastní konfiguraci aplikace gateway shoduje s konfigurací nakonfigurované.

1.  Pokud se neshodují, změňte konfiguraci sondy tak, aby má správnou hodnotu řetězce přijmout.

Další informace o [párování sondy aplikační brány](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Certifikát zálohovaového serveru je neplatný certifikační autorita

**Zpráva:** Certifikát serveru používaný back-endem není podepsán známou certifikační autoritou (CA). Whitelist back-end u brány aplikace nahráním kořenového certifikátu certifikátu serveru používaného back-endem.

**Příčina:** End-to-end SSL s aplikační bránou v2 vyžaduje, aby byl certifikát back-endového serveru ověřen, aby bylo možné považovat server za v pořádku.
Aby byl certifikát SSL důvěryhodný, musí být tento certifikát back-endového serveru vydán certifikační autoritou, která je součástí důvěryhodného úložiště aplikační brány. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou (například pokud byl použit certifikát podepsaný svým držitelem), uživatelé by měli nahrát certifikát vystavittele do brány aplikace.

**Řešení:** Následujícím postupem exportujte a nahrajte důvěryhodný kořenový certifikát do aplikační brány. (Tyto kroky jsou určeny pro klienty systému Windows.)

1.  Přihlaste se k počítači, kde je aplikace hostována.

1.  Vyberte Win+R nebo klepněte pravým tlačítkem na tlačítko **Start** a pak vyberte **Spustit**.

1.  Zadejte `certmgr.msc` a vyberte Enter. Správce certifikátů můžete také vyhledat v nabídce **Start.**

1.  Vyhledejte certifikát, obvykle `\Certificates - Current User\\Personal\\Certificates\`v písmenu a), a otevřete jej.

1.  Vyberte kořenový certifikát a potom vyberte **zobrazit certifikát**.

1.  Ve vlastnostech certifikátu vyberte kartu **Podrobnosti.**

1.  Na kartě **Podrobnosti** vyberte možnost **Kopírovat do souboru** a uložte soubor do kódu Base-64 X.509 (. CER).

1.  Otevřete stránku **Nastavení** HTTP aplikační brány na webu Azure Portal.

1. Otevřete nastavení protokolu HTTP, vyberte **Přidat certifikát**a vyhledejte právě uložený soubor certifikátu.

1. Vyberte **Uložit,** chcete-li uložit nastavení protokolu HTTP.

Případně můžete exportovat kořenový certifikát z klientského počítače přímým přístupem k serveru (vynecháním aplikační brány) prostřednictvím prohlížeče a exportem kořenového certifikátu z prohlížeče.

Další informace o tom, jak extrahovat a nahrát důvěryhodné kořenové certifikáty v application gateway, naleznete v [tématu Export důvěryhodného kořenového certifikátu (pro skladovou položku v2).](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>Neshoda důvěryhodných kořenových certifikátů

**Zpráva:** Kořenový certifikát certifikátu serveru používaný back-endem neodpovídá důvěryhodnému kořenovému certifikátu přidanému do aplikační brány. Ujistěte se, že přidáte správný kořenový certifikát do seznamu povolených back-endů

**Příčina:** End-to-end SSL s aplikační bránou v2 vyžaduje, aby byl certifikát back-endového serveru ověřen, aby bylo možné považovat server za v pořádku.
Aby byl certifikát SSL důvěryhodný, musí být certifikát back-endového serveru vydán certifikační autoritou, která je součástí důvěryhodného úložiště aplikační brány. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou (například byl použit certifikát podepsaný svým držitelem), uživatelé by měli nahrát certifikát vystavittele do brány aplikace.

Certifikát, který byl odeslán do nastavení HTTP brány aplikace, se musí shodovat se kořenovým certifikátem certifikátu back-endového serveru.

**Řešení:** Pokud se zobrazí tato chybová zpráva, dojde k neshodě mezi certifikátem, který byl odeslán do brány aplikace, a certifikátem, který byl odeslán na server back-end.

Postupujte podle pokynů 1-11 v předchozí metodě k nahrání správného důvěryhodného kořenového certifikátu do aplikační brány.

Další informace o tom, jak extrahovat a nahrát důvěryhodné kořenové certifikáty v application gateway, naleznete v [tématu Export důvěryhodného kořenového certifikátu (pro skladovou položku v2).](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> K této chybě může dojít také v případě, že back-endový server nevyměňuje úplný řetězec certifikátu, včetně Kořenového > Intermediate (pokud je to možné) > Leaf během tls handshake. Chcete-li ověřit, můžete použít příkazy OpenSSL z libovolného klienta a připojit se k back-endovému serveru pomocí nakonfigurovaných nastavení v sondě Application Gateway.

Například:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Pokud výstup nezobrazuje úplný řetězec vráceného certifikátu, exportujte certifikát znovu s úplným řetězcem, včetně kořenového certifikátu. Nakonfigurujte tento certifikát na back-endovém serveru. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Neplatný běžný název back-endový certifikát (CN)

**Zpráva:** Obecný název (CN) certifikátu back-endu neodpovídá záhlaví hostitele sondy.

**Příčina:** Aplikační brána zkontroluje, zda název hostitele zadaný v nastavení http back-endu odpovídá názvu propojené sítě, který je prezentován certifikátem SSL back-endového serveru. Jedná se o Standard_v2 a WAF_v2 chování skladové položky. Označení názvu serveru (SNI) standardní a wafové sku je nastaveno jako hlavní název v adrese back-endového fondu.

Ve sku v2, pokud je výchozí sonda (žádná vlastní sonda byla nakonfigurována a přidružena), SNI se nastaví z názvu hostitele uvedeného v nastavení HTTP. Nebo pokud "Vyskladnění názvu hostitele z back-endové adresy" je uvedeno v nastavení HTTP, kde fond adres back-endobsahuje platný souhrnný název, bude toto nastavení použito.

Pokud je vlastní sonda přidružená k nastavení PROTOKOLU HTTP, sni bude nastavena z názvu hostitele uvedeného v konfiguraci vlastní sonda. Nebo pokud **Pick hostname z nastavení http back-endu** je vybrána ve vlastní probační, SNI bude nastavena z názvu hostitele uvedeného v nastavení HTTP.

Pokud je v nastavení http **nastavennázev hostitele Pick z back-endové adresy,** musí back-endový fond adres obsahovat platný souhrnný název souboru.

Pokud se zobrazí tato chybová zpráva, cn certifikát back-endu neodpovídá názvu hostitele nakonfigurovanému ve vlastní sondě nebo nastavení protokolu HTTP (pokud je vybrána možnost **Vybrat název hostitele z nastavení http back-endu).** Pokud používáte výchozí sondu, bude název hostitele nastaven na **127.0.0.1**. Pokud to není požadovaná hodnota, měli byste vytvořit vlastní sondu a přidružit ji k nastavení protokolu HTTP.

**Řešení:**

Pokud chcete tento problém vyřešit, použijte následující postup.

Ve Windows:

1.  Přihlaste se k počítači, kde je aplikace hostována.

1.  Vyberte Win+R nebo klepněte pravým tlačítkem myši na tlačítko **Start** a vyberte **Spustit**.

1.  Zadejte **certmgr.msc** a vyberte Enter. Správce certifikátů můžete také vyhledat v nabídce **Start.**

1.  Vyhledejte certifikát (obvykle `\Certificates - Current User\\Personal\\Certificates`v ) a otevřete certifikát.

1.  Na kartě **Podrobnosti** zkontrolujte **předmět certifikátu**.

1.  Ověřte cn certifikátu z podrobností a zadejte totéž v poli název hostitele vlastní sondy nebo v nastavení HTTP (pokud je vybraná volba **Vyberte název hostitele z nastavení http back-endu).** Pokud to není požadovaný název hostitele pro váš web, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele do konfigurace vlastního sondy nebo nastavení HTTP.

Pro Linux pomocí OpenSSL:

1.  Spusťte tento příkaz v openssl:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Ze zobrazených vlastností vyhledejte cn certifikátu a zadejte totéž do pole název hostitele nastavení http. Pokud to není požadovaný název hostitele pro váš web, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele do konfigurace vlastního sondy nebo nastavení HTTP.

#### <a name="backend-certificate-is-invalid"></a>Back-endový certifikát je neplatný.

**Zpráva:** Back-endový certifikát je neplatný. Aktuální datum není \"v\" rozsahu \"Platné\" od a Platné do data na certifikátu.

**Příčina:** Každý certifikát je dodáván s rozsahem platnosti a připojení HTTPS nebude zabezpečené, dokud není certifikát SSL serveru platný. Aktuální data musí být v rámci **platné ho rozsahu od** a platné **do** rozsahu. Pokud tomu tak není, certifikát je považován za neplatný a vytvoří problém se zabezpečením, ve kterém aplikační brána označí back-endový server jako nefunkční.

**Řešení:** Pokud platnost certifikátu SSL vypršela, obnovte jej u dodavatele a aktualizujte nastavení serveru novým certifikátem. Pokud se jedná o certifikát podepsaný svým držitelem, musíte vygenerovat platný certifikát a nahrát kořenový certifikát do nastavení HTTP aplikační brány. Provedete to podle těchto kroků:

1.  Otevřete nastavení HTTP aplikační brány na portálu.

1.  Vyberte nastavení s certifikátem, jehož platnost vypršela, vyberte **Přidat certifikát**a otevřete nový soubor certifikátu.

1.  Odeberte starý certifikát pomocí ikony **Odstranit** vedle certifikátu a pak vyberte **Uložit**.

#### <a name="certificate-verification-failed"></a>Ověření certifikátu se nezdařilo.

**Zpráva:** Platnost certifikátu back-end nelze ověřit. Chcete-li zjistit důvod, zkontrolujte, zda je zpráva spojená s kódem chyby {errorCode} otevřena diagnostika SSL.

**Příčina:** K této chybě dochází, když brána aplikace nemůže ověřit platnost certifikátu.

**Řešení:** Chcete-li tento problém vyřešit, ověřte, zda byl certifikát na serveru vytvořen správně. Můžete například použít [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) k ověření certifikátu a jeho vlastností a potom zkuste certifikát znovu nahrát do nastavení HTTP aplikační brány.

<a name="backend-health-status-unknown"></a>Stav back-endu: neznámý
-------------------------------
Pokud je stav back-endu zobrazen jako Neznámý, zobrazení portálu se bude podobat následujícímu snímku obrazovky:

![Stav back-endové brány aplikace – neznámý](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

K tomuto chování může dojít z jednoho nebo více z následujících důvodů:

1.  Podsíť NSG v podsíti Aplikační brána blokuje příchozí přístup k portům 65503-65534 (v1 SKU) nebo 65200-65535 (v2 SKU) z "Internet".
1.  UDR v podsíti Aplikační brána je nastavena na výchozí trasu (0.0.0.0/0) a další směrování není zadáno jako "Internet".
1.  Výchozí trasa je inzerována připojením ExpressRoute/VPN k virtuální síti přes protokol BGP.
1.  Vlastní server DNS je nakonfigurován ve virtuální síti, která nemůže přeložit názvy veřejných domén.
1.  Aplikační brána je ve stavu Není v pořádku.

**Řešení:**

1.  Zkontrolujte, zda váš nsg blokuje přístup k portům 65503-65534 (v1 SKU) nebo 65200-65535 (v2 SKU) z **Internetu**:

    a.  Na kartě **Přehled** aplikační brány vyberte propojení **Virtuální síť nebo podsíť.**

    b.  Na kartě **Podsítě** ve virtuální síti vyberte podsíť, do které byla nasazena aplikační brána.

    c.  Zkontrolujte, zda je nakonfigurován nějaký soubor nsg.

    d.  Pokud je nakonfigurován soubor zabezpečení sítě, vyhledejte tento prostředek skupiny zabezpečení sítě na kartě **Hledání** nebo v části **Všechny prostředky**.

    e.  V části **Příchozí pravidla** přidejte příchozí pravidlo, které povolí rozsah cílového portu 65503-65534 pro skladovou položku v1 nebo 65200-65535 v2 skladovou položku s **nastaveným zdrojem** jako **libovolný** nebo **Internet**.

    f.  Vyberte **Uložit** a ověřte, zda můžete back-end zobrazit jako v pořádku. Případně to můžete udělat prostřednictvím [prostředí PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Zkontrolujte, zda má udr. **Internet**
    
    a.  Postupujte podle kroků 1a a 1b k určení podsítě.

    b.  Zkontrolujte, zda je nějaká Konfigurace UDR. Pokud existuje, vyhledejte zdroj na vyhledávacím panelu nebo v části **Všechny zdroje**.

    c.  Zkontrolujte, zda existují výchozí trasy (0.0.0.0/0) s dalším směrováním, které není nastaveno jako **Internet**. Pokud je toto nastavení **virtual appliance** nebo **brána virtuální sítě**, musíte se ujistit, že vaše virtuální zařízení nebo místní zařízení může správně směrovat paket zpět do cíle internetu bez úpravy paketu.

    d.  V opačném případě změňte další směrování na **Internet**, vyberte **uložit**a ověřte stav back-endu.

1.  Výchozí trasa inzerovaná připojením ExpressRoute/VPN k virtuální síti přes protokol BGP:

    a.  Pokud máte expressroute/VPN připojení k virtuální síti přes BGP a pokud inzerujete výchozí trasu, musíte se ujistit, že paket je směrován zpět do cíle Internetu bez úpravy. Ověření můžete ověřit pomocí možnosti **Poradce při potížích s připojením** na portálu Aplikační brány.

    b.  Zvolte cíl ručně jako libovolnou internetovou IP adresu, jako je 1.1.1.1. Nastavte cílový port jako cokoli v a ověřte připojení.

    c.  Pokud je další směrování bránou virtuální sítě, může existovat výchozí trasa inzerovaná přes ExpressRoute nebo VPN.

1.  Pokud je ve virtuální síti nakonfigurován vlastní server DNS, ověřte, zda server (nebo servery) dokáže vyřešit veřejné domény. Překlad názvů veřejné domény může být vyžadován v situacích, kdy aplikace gateway musí oslovit externí domény, jako jsou servery OCSP nebo zkontrolovat stav odvolání certifikátu.

1.  Chcete-li ověřit, zda je brána aplikace v pořádku a spuštěna, přejděte na portálu na možnost **Stav prostředků** a ověřte, zda je stav **V pořádku**. Pokud se zobrazí **stav Nev pořádku** nebo **Snížený** stav, [obraťte se na podporu](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Další kroky
----------

Další informace o [diagnostice a protokolování aplikací .](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
