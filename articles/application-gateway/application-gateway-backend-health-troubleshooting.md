---
title: Řešení potíží se stavem back-endu v Azure Application Gateway
description: Tento článek vás provede řešením potíží se stavem back-endu pro Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: ce1d0542530c4f190ace52d45e2369d6ecc18772
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384083"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Řešení potíží se stavem back-endu v Application Gateway
==================================================

<a name="overview"></a>Přehled
--------

Application Gateway ve výchozím nastavení sondují back-end servery, aby kontrolovaly stav a, pokud jsou připravené na obsluhu požadavků. Uživatelé můžou vytvářet vlastní sondy i uvést název hostitele, cestu k testování a stavové kódy, které se budou přijímat jako v pořádku. V obou případech, pokud back-end server nereaguje správně, Application Gateway označí Server jako špatný a zastaví předávání požadavku serveru. Jakmile server začne reagovat úspěšně, bude pokračovat v obsluze požadavků.

### <a name="how-to-check-backend-health"></a>Postup kontroly stavu back-endu

Pokud chcete zjistit stav vašeho back-endu, můžete použít stránku "stav back-endu" v Azure Portal. Můžete také použít [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)nebo [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) k získání stavu. Pokud chcete získat další informace, podívejte se na propojený článek na jednotlivé metody.

Stav, který získá kterákoli z výše uvedených metod, může mít tři typy:

1.  V pořádku

2.  Není v pořádku

3.  Neznámé

Pokud je stav back-endu serveru v pořádku, znamená to, že Application Gateway předají požadavky na daný server. Pokud ale stav back-endu pro všechny servery v back-endu není v pořádku nebo není známý, můžete při přístupu k aplikacím čelit nějakým problémům. Tento dokument popisuje příznak, příčinu a řešení jednotlivých chyb zobrazených v případě, že back-end servery můžou být chybné nebo neznámé.

<a name="backend-health-status-unhealthy"></a>Stav back-endu není v pořádku
-------------------------------

Pokud se stav back-endu zobrazuje jako není v pořádku, zobrazí se na portálu jako snímek obrazovky níže:

![Stav back-endu Application Gateway – není v pořádku](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Nebo pokud používáte dotaz Azure PowerShell, CLI nebo Azure REST API, zobrazí se odpověď podobná té následující:
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
Jakmile se zobrazí stav back-end serveru, který není v pořádku pro všechny servery v back-end fondu, požadavky se na ně předají a Application Gateway vrátí 502 chybnou bránu k žádajícímu klientovi. Pokud chcete tento problém vyřešit, podívejte se do sloupce podrobnosti na kartě stav back-endu.

Zpráva zobrazená ve sloupci Podrobnosti na kartě stav back-endu poskytuje podrobnější přehled o problému a na základě těchto informací můžeme začít s řešením problému.

> [!NOTE]
> Výchozí žádost o test paměti je odeslána ve formátu <protocol>://127.0.0.1:<port> <http://127.0.0.1/> /, například pro test http na portu 80 a bere v úvahu pouze odpověď stavových kódů HTTP 200-399 jako v pořádku. Protokol a cílový port jsou zděděné z nastavení HTTP. Pokud chcete, Application Gateway použít test na jiném protokolu, názvu hostitele nebo cestě a přijměte jiný stavový kód jako v pořádku, nakonfigurujte vlastní test a přidružte ho k nastavení HTTP.

### <a name="error-messages"></a>Chybové zprávy

#### <a name="backend-server-timeout"></a>Časový limit back-endu serveru

**Zpráva** Doba, kterou back-end zabere na reakci\'na test stavu služby Application Gateway s, je větší než prahová hodnota časového limitu v nastavení sondy.

**Způsobit** Jakmile Application Gateway odešle back-end serveru požadavek na test HTTP (S), počká na odpověď ze serveru back-end na určitou dobu nakonfigurované. Pokud back-end server neodpovídá v rámci hodnoty časového limitu, označí se jako není v pořádku, dokud znovu nezačne reagovat v rámci časového limitu.

**Řešení:** Ověřte, zda server nebo aplikace back-end nereagují v nakonfigurovaném časovém limitu, a také ověřte závislosti aplikace, například pokud má databáze nějaké problémy, což může vést k prodlevě v reakci. Pokud znáte chování aplikace a chcete odpovědět jenom po hodnotě časového limitu, zvyšte hodnotu časového limitu z nastavení vlastního testu.
Abyste mohli změnit hodnotu časového limitu, musíte mít vlastní test paměti. Chcete-li zjistit, jak nakonfigurovat vlastní test paměti, [Podívejte se na stránku dokumentace](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Chcete-li zvýšit časový limit, postupujte podle následujících kroků:

1.  Přihlaste se k back-end serveru přímo a ověřte čas potřebný k reakci serveru na této stránce. K přístupu k, včetně prohlížeče pomocí vývojářských nástrojů, můžete použít libovolný nástroj.

2.  Jakmile si vyberete čas potřebný k reakci na aplikaci, klikněte na kartu sondy stavu a vyberte sondu, která je přidružená k vašemu nastavení protokolu HTTP.

3.  Zadejte libovolnou vyšší hodnotu časového limitu, než je doba odezvy aplikace v sekundách.

4.  Uložte nastavení vlastního testu a ověřte stav back-endu, pokud se teď zobrazuje v pořádku.

#### <a name="dns-resolution-error"></a>Chyba rozlišení DNS

**Zpráva** Aplikační brána nemohla vytvořit test pro tento back-end. K tomu obvykle dochází v případě, že plně kvalifikovaný název domény back-endu nebyl zadán správně. 

**Způsobit** Pokud je back-end fond typu IP adresa nebo plně kvalifikovaný název domény nebo App Service, Application Gateway se přeloží na IP adresu plně kvalifikovaného názvu domény zadaného pomocí serveru DNS (Custom nebo Azure default) a pokusí se připojit k serveru na portu TCP uvedeném v nastavení HTTP. Pokud se ale zobrazí tato zpráva, je navržena tak, že Application Gateway nebylo možné úspěšně přeložit IP adresu zadaného plně kvalifikovaného názvu domény.

**Řešení:**

1.  Ověřte, jestli je plně kvalifikovaný název domény zadaný v back-end fondu správný, a pokud se jedná o veřejnou doménu, zkuste ho vyřešit z místního počítače.

2.  Pokud IP adresu vyřešíte, v konfiguraci DNS ve virtuální síti může být něco špatné.

3.  Ověřte, jestli je virtuální síť nakonfigurovaná s vlastním serverem DNS. Pokud ano, zaregistrujte se na serveru DNS, na kterém se nebude moct přeložit na IP adresu daného plně kvalifikovaného názvu domény.

4.  Pokud se jedná o výchozí DNS Azure, ověřte u svého registrátora názvu domény, jestli se provedlo správné mapování záznamů nebo záznamu CNAME.

5.  Pokud je doména soukromá/interní, zkuste ji vyřešit z virtuálního počítače ve stejné virtuální síti, a pokud ji můžete vyřešit, restartujte Application Gateway a zkuste to znovu. Chcete-li restartovat Application Gateway, je nutné [zastavit](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) a [Spustit](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) příkazy prostředí PowerShell uvedené v příslušných odkazech k dokumentu.

#### <a name="tcp-connect-error"></a>Chyba připojení TCP

**Zpráva** Aplikační brána se nemohla připojit k back-endu.
Zkontrolujte prosím, že back-end reaguje na port použitý pro test paměti.
Také ověřte, zda jakákoli NSG/UDR/firewall blokuje přístup k IP adrese a portu tohoto back-endu.

**Způsobit** Po fázi překladu DNS se Application Gateway pokusí připojit k back-end serveru na portu TCP, který je nakonfigurovaný v nastavení HTTP. Pokud Application Gateway není možné navázat relaci TCP na zadaném portu, sonda je označena jako poškozená s touto zprávou.

**Řešení:** Pokud se zobrazí tato chyba, zkontrolujte následující:

1.  Ověřte, jestli se můžete připojit k back-end serveru na portu uvedeném v nastavení HTTP pomocí prohlížeče nebo pomocí PowerShellu, například můžete použít příkaz: Test-NetConnection-ComputerName www.bing.com-port 443

2.  Pokud uvedený port není požadovaným portem, zadejte správné číslo portu pro Application Gateway připojení k back-endu serveru.

3.  Pokud se na portu nemůžete připojit i z místního počítače, pak:

    a.  Ověřte nastavení NSG síťové karty a podsítě back-end serveru a ověřte, jestli jsou povolená příchozí připojení k nakonfigurovanému portu. Pokud nejsou povoleny, vytvořte nové pravidlo, které povolí připojení. Pokud se chcete dozvědět, jak vytvořit pravidla NSG, [Podívejte se na stránku dokumentace](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Pokud je povolené odchozí veřejné a soukromé přenosy, ověřte nastavení NSG podsítě Application Gateway, aby bylo možné vytvořit připojení. Pokud chcete získat další informace o vytváření pravidel NSG, podívejte se na dokument propojený v (a).
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Ověřte nastavení UDR podsítě Application Gateway a back-end serveru pro všechny anomálie směrování. Ujistěte se, že UDR nesměruje provoz mimo podsíť back-endu. Můžete třeba vyhledat trasy k síťovým virtuálním zařízením nebo výchozí trasy inzerované do podsítě služby Application Gateway prostřednictvím ExpressRoute/VPN.

    d.  K ověření efektivních tras a pravidel pro síťové rozhraní můžete použít následující příkazy PowerShellu.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Pokud nenajdete žádné problémy s NSG nebo UDR, Projděte si back-end Server pro problémy související s aplikacemi, protože klienti nemůžou navázat relaci TCP na portech nakonfigurovaných. Můžete kontrolovat několik věcí:

    e.  Otevřete příkazový řádek (Win + R-\> cmd) a zadejte netstat a stiskněte klávesu ENTER.

    f.  Ověřte, zda server naslouchá na portu, který je nakonfigurován.
        Příklad:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Pokud tomu tak není, ověřte nastavení webového serveru, například vazby webu ve službě IIS, server Block v NGINX a Virtual Host v Apache.

    h.  Zkontrolujte nastavení brány firewall pro operační systém a ujistěte se, že je příchozí provoz na port povolený.

#### <a name="http-status-code-mismatch"></a>Neshoda stavového kódu HTTP

**Zpráva** Stavový kód odpovědi HTTP\'back-end se neshoduje s nastavením testu paměti. Očekávalo se: {HTTPStatusCode0} přijatých: {HTTPStatusCode1}.

**Způsobit** Po navázání připojení TCP a provedení ověření SSL metodou handshake (Pokud je protokol SSL povolený) Application Gateway odešle test jako požadavek HTTP GET na back-end Server. Jak je uvedeno výše, \<výchozí sonda bude protokol\>://127.0.0.1:\<port\>/a považuje se za stavové kódy odpovědí ve formátu Rage 200-399 jako v pořádku. Pokud server vrátí jakýkoliv jiný stavový kód, bude tato zpráva označena jako poškozená.

**Řešení:** V závislosti na kódu odpovědi back-end serveru můžete provést následující kroky. Tady jsou uvedené některé běžné stavové kódy:

| **Chyba** | **Akce** |
| --- | --- |
| Neshoda stavového kódu testu: Přijato 401 | Ověřte, zda back-end Server vyžaduje ověření. Testy služby Application Gateway nemůžou v tomto okamžiku předat přihlašovací údaje pro ověření. Buď povolte \"protokol HTTP\" 401 ve stavovém kódu sondy, nebo proveďte test na cestu, kde server nevyžaduje ověřování. | |
| Neshoda stavového kódu testu: Přijato 403 | Přístup je zakázán. Ověřte, jestli je na back-end serveru povolený přístup k cestě. | |
| Neshoda stavového kódu testu: Přijato 404 | Stránka se nenašla. Ověřte cestu k názvu hostitele, pokud je k dispozici na back-end serveru. Změňte parametr názvu hostitele nebo cesty na hodnotu, která je k dispozici. | |
| Neshoda stavového kódu testu: Přijato 405 | Požadavky testu na Application Gateway používají metodu HTTP GET. Ověřte, zda server tuto možnost povoluje. | |
| Neshoda stavového kódu testu: Přijato 500 | Došlo k vnitřní chybě serveru. Podívejte se na stav back-end serveru a na spuštěné služby. | |
| Neshoda stavového kódu testu: Přijato 503 | Služba není k dispozici. Podívejte se na stav back-end serveru a na spuštěné služby. | |

Nebo pokud se domníváte, že odpověď je zavedená a že chcete, Application Gateway přijmout další stavové kódy jako v pořádku, můžete vytvořit vlastní test. Tato změna bude užitečná v situacích, kdy back-end web potřebuje ověření a protože požadavky testu nenesou žádné přihlašovací údaje uživatele, selžou a stavový kód HTTP 401 se vrátí back-end serverem.

Pokud chcete vytvořit vlastní test paměti, postupujte podle kroků uvedených [tady](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Neshoda textu odpovědi HTTP

**Zpráva** Tělo odpovědi HTTP back\'-end s neodpovídá nastavení testu paměti. Přijatý text odpovědi neobsahuje {String}.

**Způsobit** Když vytvoříte vlastní test, budete mít možnost označit back-end Server v pořádku porovnáním řetězce z těla odpovědi. Můžete například nakonfigurovat Application Gateway pro přijetí "neautorizovaného" jako řetězce, který se má shodovat. Pokud odpověď serveru back-end pro požadavek sondy obsahuje řetězec "Neautorizováno", bude označena jako v pořádku.
V opačném případě bude označena stav není v pořádku. Tato zpráva bude označena jako chybná.

**Řešení:** Tento problém můžete vyřešit pomocí následujících kroků:

1.  Přihlaste se k serveru back-end místně nebo z klientského počítače v cestě testu a ověřte tělo odpovědi.

2.  Zkontrolujte Application Gateway vlastní konfigurace testu, abyste ověřili, jestli tělo odpovědi odpovídá nakonfigurovanému obsahu.

3.  Pokud se neshodují, změňte konfiguraci sondy pomocí správné řetězcové hodnoty, kterou chcete přijmout.

Další informace o [Application Gateway najdete v](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)článku věnovaném kontrole sondy.

#### <a name="backend-server-certificate-invalid-ca"></a>Neplatná CA certifikátu back-end serveru

**Zpráva** Certifikát serveru používaný back-end není podepsán známou certifikační autoritou (CA). Seznam povolených back-endu služby Application Gateway nahrajte tak, že nahrajete kořenový certifikát certifikátu serveru, který používá back-end.

**Způsobit** Komplexní šifrování SSL s Application Gateway v2 vyžaduje, aby byl certifikát back-end serveru ověřený, aby se mohl považovat za server v pořádku.
Aby byl certifikát SSL důvěryhodný, musí být certifikát serveru back-end vydaný certifikační autoritou, která je součástí důvěryhodného úložiště Application Gateway. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, například certifikáty podepsané svým držitelem, musí uživatelé odeslat certifikát vystavitele do Application Gateway.

**Řešení:** Použijte následující postup k exportu a nahrání důvěryhodného kořenového certifikátu do Application Gateway (níže uvedené kroky jsou pro klienty Windows).

1.  Přihlaste se k počítači, ve kterém je vaše aplikace hostovaná.

2.  Spusťte příkaz Spustit stisknutím Win + R nebo kliknutím pravým tlačítkem myši na tlačítko Start a výběrem možnosti Spustit.

3.  Zadejte certmgr. msc a stiskněte klávesu ENTER. Můžete také vyhledat správce certifikátů v nabídce Start.

4.  Vyhledejte certifikát, \'obvykle v části certifikáty – osobní\\certifikáty\\\'uživatelů a otevřete certifikát.

5.  Ve vlastnostech certifikátu vyberte kartu cesta k certifikátu.

6.  Vyberte kořenový certifikát a vyberte možnost Zobrazit certifikát.

7.  Ve vlastnostech certifikátu přepněte na kartu Podrobnosti.

8.  Na kartě Podrobnosti vyberte možnost Kopírovat do souboru a uložte soubor v souboru X. 509 s kódováním Base-64 (. CER) – formát

9.  Po uložení otevřete stránku Application Gateway nastavení HTTP v Azure Portal

10. Otevřete nastavení HTTP a klikněte na Přidat certifikát a vyhledejte soubor certifikátu, který jsme nedávno uložili.

11. Kliknutím na Uložit uložte nastavení HTTP.

Alternativně můžete exportovat kořenový certifikát z klientského počítače přímým přístupem k serveru (obejít Application Gateway) pomocí prohlížeče a exportovat kořenový certifikát z prohlížeče.

Podrobné pokyny k extrakci a nahrávání důvěryhodných kořenových certifikátů v Application Gateway najdete [tady](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Neshoda důvěryhodného kořenového certifikátu

**Zpráva** Kořenový certifikát serveru, který používá back-end, se neshoduje s důvěryhodným kořenovým certifikátem přidaným do aplikační brány. Ujistěte se, že jste přidali správný kořenový certifikát pro přidání do seznamu povolených back-endu.

**Způsobit** Komplexní šifrování SSL s Application Gateway v2 vyžaduje, aby byl certifikát back-end serveru ověřený, aby se mohl považovat za server v pořádku.
Aby byl certifikát SSL důvěryhodný, musí být certifikát serveru back-end vydaný certifikační autoritou, která je součástí důvěryhodného úložiště Application Gateway. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, například certifikáty podepsané svým držitelem, musí uživatelé odeslat certifikát vystavitele do Application Gateway.

Certifikát, který byl nahrán do Application Gateway nastavení HTTP, se musí shodovat s kořenovým certifikátem certifikátu back-end serveru.

**Řešení:** Pokud se zobrazí výše uvedená chybová zpráva, znamená to, že došlo k neshodě mezi certifikátem, který byl nahrán do Application Gateway a ten se nahrál na back-end Server.

Podle výše uvedeného postupu 1-11 nahrajte do Application Gateway správný důvěryhodný kořenový certifikát.

Podrobné pokyny k extrakci a nahrávání důvěryhodných kořenových certifikátů v Application Gateway najdete [tady](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Uvedená chyba se může objevit i v případě, že back-end Server neměňuje úplný řetěz certifikátů, včetně zprostředkujícího > (Pokud je k dispozici) – > list během metody handshake TLS. K ověření můžete použít příkazy OpenSSL z libovolného klienta a připojit se k back-end serveru pomocí nakonfigurovaných nastavení v Application Gateway PROBE.

Například
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Pokud výstup nezobrazuje úplný řetěz vráceného certifikátu, exportujte certifikát znovu s úplným řetězcem včetně kořenového certifikátu a nakonfigurujte ho na back-end serveru. 

PŘIPOJENO (00000188) \
Hloubka = 0 ou = ověřený ovládací prvek domény, CN \*=. example.com \
ověřit chybu: číslo = 20: nejde získat certifikát místního vystavitele \.
ověření vrácení: 1 \
Hloubka = 0 ou = ověřený ovládací prvek domény, CN \*=. example.com \
ověřit chybu: číslo = 21: nelze ověřit první certifikát \
ověření vrácení: 1 \
\-\-\-\
Řetěz certifikátů \
 0 s:/OU = ověřený ovládací prvek domény/CN = *. example. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----ZAČÍT CERTIFIKÁT-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----UKONČIT-----CERTIFIKÁTU

#### <a name="backend-certificate-invalid-common-name-cn"></a>Neplatný běžný název certifikátu back-endu (CN)

**Zpráva** Běžný název (CN) certifikátu back-endu se neshoduje s hlavičkou hostitele sondy.

**Způsobit** Application Gateway ověří, jestli název hostitele zadaný v nastavení http back-endu odpovídá běžnému názvu (CN), který prezentuje certifikát SSL serveru back-end. Toto je pouze chování Standard_v2 a WAF_v2 SKU. SNI Standard a WAF SKU se nastaví jako plně kvalifikovaný název domény v adrese back-endu fondu.

Pokud je v SKU verze V2 výchozí sonda (není nakonfigurovaná ani přidružená vlastní sonda), SNI se nastaví z názvu hostitele uvedeného v nastavení HTTP, nebo pokud se v nastavení HTTP uvede název hostitele z back-endu adresy, kde se nachází fond adres back-end, který obsahuje platný plně kvalifikovaný název domény.

Pokud je k nastavení HTTP přidružená vlastní sonda, SNI se nastaví z názvu hostitele zmíněného v konfiguraci vlastního testu, nebo pokud se ve vlastní sondě vrátí název hostitele z nastavení HTTP back-endu, nanastaví se z názvu hostitele uvedeného v HTTP. Možnost.

V případě, že je v nastavení HTTP nastavený příkaz "vybrat hostitele z back-endu adresy", musí fond adres back-end obsahovat platný plně kvalifikovaný název domény.

Pokud se zobrazí výše uvedená chybová zpráva, znamená to, že běžný název (CN) certifikátu back-endu se neshoduje s názvem hostitele nakonfigurovaným ve vlastním testu nebo v nastavení HTTP (v případě, že je zvolena možnost vybrat název hostitele z back-endu HTTP nastavení). Pokud používáte výchozí test, název hostitele se nastaví na "127.0.0.1". Pokud to není požadovaná hodnota, měli byste vytvořit vlastní test a přidružit ho k nastavení HTTP.

**Řešení:**

Chcete-li tento problém vyřešit, postupujte podle následujících kroků:

Ve Windows:

1.  Přihlaste se k počítači, ve kterém je vaše aplikace hostovaná.

2.  Spusťte příkaz Spustit stisknutím Win + R nebo kliknutím pravým tlačítkem myši na tlačítko Start a výběrem možnosti Spustit.

3.  Zadejte certmgr. msc a stiskněte klávesu ENTER. Můžete také vyhledat správce certifikátů v nabídce Start.

4.  Vyhledejte certifikát, \'obvykle v části certifikáty – osobní\\certifikáty\\\'uživatelů a otevřete certifikát.

5.  Na kartě Podrobnosti ověřte předmět certifikátu.

6.  Ověřte CN certifikátu z podrobností a zadejte stejný název do pole název hostitele vlastního testu nebo nastavení HTTP (Pokud je zvolená možnost "vybrat název hostitele z back-endu HTTP nastavení"). Pokud se nejedná o požadovaný název hostitele vašeho webu, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele v konfiguraci vlastního testu/nastavení protokolu HTTP.

Pro Linux pomocí OpenSSL

1.  Spustit tento příkaz v OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  V zobrazených vlastnostech vyhledejte CN certifikátu a zadejte stejný název do pole název hostitele nastavení protokolu HTTP. Pokud se nejedná o požadovaný název hostitele vašeho webu, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele v konfiguraci vlastního testu/nastavení protokolu HTTP.

#### <a name="backend-certificate-is-invalid"></a>Certifikát back-endu je neplatný.

**Zpráva** Certifikát back-endu je neplatný. \"Aktuální datum není v rozsahu platné od\" do a \"platnost do\" data v rámci certifikátu.

**Způsobit** Každý certifikát se dodává s platností a připojení HTTPS nebude zabezpečené, pokud není certifikát SSL serveru platný.
Aktuální data musí být v rozmezí od do rozsahu platný od do. V takovém případě se certifikát považuje za neplatný a bude mít obavy z hlediska zabezpečení. V tomto okamžiku bude Application Gateway server back-end označovat jako špatný.

**Řešení:** Pokud vypršela platnost certifikátu protokolu SSL, obnovte certifikát u svého dodavatele a aktualizujte nastavení serveru pomocí nového certifikátu. Pokud se jedná o certifikát podepsaný svým držitelem, musíte vygenerovat platný certifikát a nahrajte kořenový certifikát do nastavení HTTP Application Gateway. Postupujte podle následujících kroků:

1.  Otevřete nastavení Application Gateway HTTP na portálu.

2.  Vyberte nastavení HTTP s certifikátem, který vypršel, vyberte Přidat certifikát a otevřete nový soubor certifikátu.

3.  Odeberte starý certifikát pomocí ikony odstranit vedle certifikátu a klikněte na Uložit.

#### <a name="certificate-verification-failed"></a>Certifikát se nepovedlo ověřit.

**Zpráva** Platnost certifikátu back-endu nelze ověřit. Pokud chcete zjistit příčinu, podívejte se na otevřít diagnostiku SSL pro zprávu přidruženou k kódu chyby {errorCode}.

**Způsobit** K této chybě dochází, když Application Gateway nedokáže ověřit platnost certifikátu.

**Řešení:** Pokud chcete tento problém vyřešit, ověřte, jestli je certifikát na serveru vytvořený správně. Můžete například použít [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) k ověření certifikátu a jeho vlastností a zkuste znovu odeslat certifikát do nastavení http Application Gateway.

<a name="backend-health-status-unknown"></a>Neznámý stav back-endu
-------------------------------
Pokud je stav back-endu zobrazen jako neznámý, zobrazí se na portálu jako snímek obrazovky níže:

![Stav back-endu Application Gateway – neznámý](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

V případě, že je stav back-endu zobrazen jako neznámý, může to být z jednoho nebo více z následujících důvodů:

1.  NSG v podsíti Application Gateway blokuje příchozí přístup k portům 65503-65534 (v1 SKU) nebo 65200-65535 (SKU v2) z Internetu.
2.  UDR v podsíti Application Gateway s výchozí trasou (0.0.0.0/0) s dalším segmentem směrování ne jako s internetem.
3.  Výchozí trasa inzerovaná připojením ExpressRoute/VPN k virtuální síti přes BGP
4.  Ve virtuální síti je nakonfigurovaný vlastní server DNS, který nedokáže přeložit názvy veřejných domén.
5.  Application Gateway je ve stavu není v pořádku.

**Řešení:**

1.  Ověřte, jestli váš NSG blokuje přístup k portům 65503-65534 (SKU V1) nebo 65200-65535 (SKU v2) z Internetu.

    a.  Na kartě Application Gateway Přehled vyberte odkaz Virtual Network/podsíť.

    b.  Na kartě podsítě v Virtual Network vyberte podsíť, ve které je Application Gateway nasazená.

    c.  Ověřte, jestli není nakonfigurovaný žádný NSG.

    d.  Pokud existuje, vyhledejte tento prostředek NSG na kartě Hledat nebo v části všechny prostředky.

    e.  V části příchozí pravidla přidejte příchozí pravidlo, které povoluje rozsah cílových portů 65503-65534 pro SKU v1 nebo 65200-65535 v2 SKU se zdrojem jako libovolným nebo internetem.

    f.  Klikněte na Uložit a ověřte, jestli se dá úspěšně zobrazit server back-end.

    g.  Případně to můžete provést prostřednictvím [PowerShellu nebo](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) rozhraní PŘÍKAZového řádku.

2.  Ověřte, jestli váš UDR má výchozí trasu (0.0.0.0/0) s dalším segmentem směrování, ne jako Internet.
    
    a.  Podle kroků 1. a a 1. b určete podsíť.

    b.  Ověřte, jestli nejsou nakonfigurované žádné UDR. Pokud existuje, vyhledejte prostředek na panelu hledání nebo v části všechny prostředky.

    c.  Ověřte, jestli existují žádné výchozí trasy (0.0.0.0/0) s dalším segmentem směrování, nikoli s internetem. Pokud se jedná o virtuální zařízení nebo Virtual Network bránu, musíte se ujistit, že virtuální zařízení nebo místní zařízení budou moct paket správně směrovat zpátky do internetového cíle bez změny paketu.

    d.  V opačném případě změňte další směrování na Internet, klikněte na Uložit a ověřte stav back-endu.

3.  Výchozí trasa inzerovaná připojením ExpressRoute/VPN k virtuální síti přes BGP

    a.  Pokud máte připojení ExpressRoute/VPN k virtuální síti přes protokol BGP a pokud jste vystavili výchozí trasu, musíte se ujistit, že se paket směruje zpátky do cílového místa na internetu bez změny.

    b.  Pomocí možnosti "řešení potíží s připojením" na portálu Application Gateway Portal můžete ověřit.

    c.  Cíl vyberte ručně jako jakoukoli internetovou IP adresu, jako je například "1.1.1.1" a cílový port jako cokoli a ověřte připojení.

    d.  Pokud je další segment směrování Virtual Network bránou, může se jednat o výchozí trasu inzerovanou přes ExpressRoute nebo VPN.

4.  Pokud je ve virtuální síti nakonfigurovaný vlastní server DNS, ověřte, jestli servery můžou vyřešit veřejné domény. V případech, kdy se Application Gateway musí spojit s externími doménami, jako jsou servery OCSP nebo kontrolovat stav odvolání certifikátu, může být vyžadováno řešení názvů veřejných domén.

5.  Pokud chcete ověřit, jestli je Application Gateway v pořádku a běží, na portálu vyberte možnost Resource Health a ověřte, jestli je v pořádku. Pokud se vám zobrazuje stav není v pořádku nebo je snížený, [obraťte](https://azure.microsoft.com/support/options/)se na podporu.

<a name="next-steps"></a>Další kroky
----------

Další informace o diagnostice a protokolování Application Gateway najdete [tady](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
