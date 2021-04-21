---
title: Řešení potíží se stavem back-endu v Azure Application Gateway
description: Popisuje řešení potíží se stavem back-endu pro Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 8664f9327af37345c7104c65b2521212669ae806
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786320"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Řešení potíží se stavem back-endu ve službě Application Gateway
==================================================

<a name="overview"></a>Přehled
--------

Ve výchozím nastavení služba Azure Application Gateway PROBE back-end serverů a kontroluje jejich stav a kontroluje, jestli jsou připravené na poskytování požadavků. Uživatelé také mohou vytvořit vlastní sondy, které by uváděly název hostitele, cestu k testování a stavové kódy, které budou přijaty jako v pořádku. V obou případech, pokud back-end server neodpoví úspěšně, Application Gateway označí Server jako špatný a přestane předávat požadavky na server. Po úspěšném spuštění serveru Application Gateway pokračuje v předávání požadavků.

### <a name="how-to-check-backend-health"></a>Postup kontroly stavu back-endu

Pokud chcete zjistit stav back-endu, můžete na Azure Portal použít stránku **stavu back-endu** . Nebo můžete použít [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), [CLI](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health)nebo [REST API](/rest/api/application-gateway/applicationgateways/backendhealth).

Stav načtený některou z těchto metod může být kterýkoli z následujících:

- V pořádku

- Není v pořádku

- Neznámý

Pokud je stav back-endu serveru pro server v pořádku, znamená to, že Application Gateway přepošle žádosti na tento server. Pokud ale stav back-endu pro všechny servery v back-end fondu není v pořádku nebo není známý, může dojít k problémům při pokusu o přístup k aplikacím. Tento článek popisuje příznaky, příčinu a řešení jednotlivých zobrazených chyb.

<a name="backend-health-status-unhealthy"></a>Stav back-endu: špatný stav
-------------------------------

Pokud stav back-endu není v pořádku, zobrazení portálu bude vypadat jako na následujícím snímku obrazovky:

![Stav back-endu Application Gateway – není v pořádku](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Nebo pokud používáte dotaz REST API Azure PowerShell, CLI nebo Azure, dostanete odpověď, která bude vypadat přibližně takto:
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
Až obdržíte stav back-end serveru pro všechny servery ve fondu back-end, požadavky se nepředávají na servery a Application Gateway v žádajícím klientovi vrátí chybu "502 chybné brány". Pokud chcete tento problém vyřešit, Projděte si sloupec **Podrobnosti** na kartě **stav back-endu** .

Zpráva zobrazená ve sloupci **Podrobnosti** poskytuje podrobnější přehled o problému a na základě těchto informací můžete začít s řešením tohoto problému.

> [!NOTE]
> Výchozí žádost sondy se pošle ve formátu \<protocol\> ://127.0.0.1: \<port\> /. Například http://127.0.0.1:80 pro test paměti http na portu 80. Pouze stavové kódy HTTP 200 až 399 jsou považovány za v pořádku. Protokol a cílový port se dědí z nastavení protokolu HTTP. Pokud chcete, aby Application Gateway PROBE v jiném protokolu, názvu hostitele nebo cestě a rozpoznal jiný stavový kód jako v pořádku, nakonfigurujte vlastní test a přidružte ho k nastavení HTTP.

<a name="error-messages"></a>Chybové zprávy
------------------------
#### <a name="backend-server-timeout"></a>Časový limit back-endu serveru

**Zpráva:** Doba, kterou back-end má reagovat na test stavu služby Application Gateway \' s, je větší než prahová hodnota časového limitu v nastavení sondy.

**Příčina:** Po Application Gateway odešle back-end serveru požadavek na test HTTP (S), čeká na odpověď serveru back-end po nakonfigurované období. Pokud back-end server nereaguje v rámci nakonfigurovaného období (hodnota časového limitu), označí se jako špatný, dokud znovu nezačne reagovat v nakonfigurovaném časovém limitu.

**Řešení:** Ověřte, proč back-end Server nebo aplikace nereaguje v nakonfigurovaném časovém limitu, a také ověřte závislosti aplikací. Například ověřte, zda má databáze nějaké problémy, které mohou aktivovat zpoždění v reakci. Pokud víte o chování aplikace a chcete odpovědět jenom po hodnotě časového limitu, zvyšte hodnotu časového limitu z nastavení vlastního testu paměti. Pokud chcete změnit hodnotu časového limitu, musíte mít vlastní test. Informace o tom, jak nakonfigurovat vlastní test paměti, [najdete na stránce s dokumentací](./application-gateway-create-probe-portal.md).

K zvýšení hodnoty časového limitu použijte následující postup:

1.  Přihlaste se k back-end serveru přímo a ověřte čas potřebný k reakci serveru na této stránce. Pro přístup k back-end serveru, včetně prohlížeče pomocí vývojářských nástrojů, můžete použít libovolný nástroj.

1.  Až zadáte čas potřebný k reakci aplikace, vyberte kartu **sondy stavu** a potom vyberte sondu, která je přidružená k nastavení http.

1.  Zadejte libovolnou hodnotu časového limitu, která je větší než doba odezvy aplikace v sekundách.

1.  Uložte vlastní nastavení sondy a ověřte, jestli stav back-endu je teď v pořádku.

#### <a name="dns-resolution-error"></a>Chyba rozlišení DNS

**Zpráva:** Application Gateway nemohl pro tento back-end vytvořit test paměti. K tomu obvykle dochází v případě nesprávně zadaného plně kvalifikovaného názvu domény back-endu. 

**Příčina:** Pokud je back-end fond typu IP adresa nebo plně kvalifikovaný název domény nebo App Service, Application Gateway se přeloží na IP adresu plně kvalifikovaného názvu domény zadaného pomocí DNS (Domain Name System) (vlastní nebo výchozí Azure) a pokusí se připojit k serveru na portu TCP uvedeném v nastavení HTTP. Pokud se ale zobrazí tato zpráva, je navržena tak, že Application Gateway nedokázala úspěšně přeložit IP adresu zadaného plně kvalifikovaného názvu domény.

**Řešení:**

1.  Ověřte, že plně kvalifikovaný název domény zadaný ve fondu back-end je správný a že se jedná o veřejnou doménu, a zkuste ho vyřešit z místního počítače.

1.  Pokud IP adresu vyřešíte, může se stát, že v konfiguraci DNS ve virtuální síti dojde k nějakému problému.

1.  Ověřte, jestli je virtuální síť nakonfigurovaná s vlastním serverem DNS. Pokud je, podívejte se na server DNS o tom, proč se nedá přeložit na IP adresu zadaného plně kvalifikovaného názvu domény.

1.  Pokud používáte výchozí DNS Azure, zaregistrujte se od svého registrátora názvu domény, jestli je dokončený záznam nebo mapování záznamů CNAME.

1.  Pokud je doména soukromá nebo interní, zkuste ji vyřešit z virtuálního počítače ve stejné virtuální síti. Pokud je můžete vyřešit, restartujte Application Gateway a znovu se vraťte. Pokud chcete restartovat Application Gateway, musíte [zastavit](/powershell/module/azurerm.network/stop-azurermapplicationgateway) a [Spustit](/powershell/module/azurerm.network/start-azurermapplicationgateway) pomocí příkazů PowerShellu popsaných v těchto propojených prostředcích.

#### <a name="tcp-connect-error"></a>Chyba připojení TCP

**Zpráva:** Application Gateway se nepovedlo připojit k back-endu.
Zkontrolujte prosím, že back-end reaguje na port použitý pro test paměti.
Také ověřte, zda jakákoli NSG/UDR/firewall blokuje přístup k IP adrese a portu tohoto back-endu.

**Příčina:** Po fázi překladu DNS se Application Gateway pokusí připojit k back-end serveru na portu TCP, který je nakonfigurovaný v nastavení HTTP. Pokud Application Gateway nemůže na zadaném portu vytvořit relaci TCP, sonda je označena jako poškozená s touto zprávou.

**Řešení:** Pokud se zobrazí tato chyba, postupujte následovně:

1.  Ověřte, jestli se můžete připojit k back-end serveru na portu uvedeném v nastavení HTTP pomocí prohlížeče nebo PowerShellu. Například spusťte následující příkaz: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Pokud uvedený port není požadovaným portem, zadejte správné číslo portu pro Application Gateway připojení k back-endu serveru.

1.  Pokud se na portu nemůžete připojit i z místního počítače, pak:

    a.  Ověřte nastavení skupiny zabezpečení sítě (NSG) síťového adaptéru a podsítě back-end serveru a zda jsou povolená příchozí připojení k nakonfigurovanému portu. Pokud ne, vytvořte nové pravidlo, které povolí připojení. Informace o tom, jak vytvořit pravidla NSG, [najdete na stránce s dokumentací](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Ověřte, zda nastavení NSG podsítě Application Gateway umožňují odchozí veřejné a soukromé přenosy, aby bylo možné vytvořit připojení. Další informace o vytváření pravidel NSG najdete na stránce dokumentu, která je k dispozici v kroku 3a.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Ověřte nastavení trasy definované uživatelem (UDR) Application Gateway a podsíť back-end serveru pro všechny anomálie směrování. Ujistěte se, že UDR nesměruje provoz mimo podsíť back-endu. Můžete třeba vyhledat trasy k síťovým virtuálním zařízením nebo výchozí trasy inzerované do Application Gateway podsítě prostřednictvím Azure ExpressRoute a/nebo VPN.

    d.  K ověření efektivních tras a pravidel pro síťový adaptér můžete použít následující příkazy PowerShellu:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Pokud nenajdete žádné problémy s NSG nebo UDR, Projděte si back-end Server pro problémy související s aplikacemi, které brání klientům v navázání relace TCP na portech nakonfigurovaných. Můžete kontrolovat několik věcí:

    a.  Otevřete příkazový řádek (Win + R- \> cmd), zadejte `netstat` a vyberte Enter.

    b.  Ověřte, zda server naslouchá na portu, který je nakonfigurován. Například:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Pokud nenaslouchá na konfigurovaném portu, ověřte nastavení svého webového serveru. Například: vazby webu ve službě IIS, server Block v NGINX a Virtual Host v Apache.

    d.  Zkontrolujte nastavení brány firewall pro operační systém a ujistěte se, že je příchozí provoz na port povolen.

#### <a name="http-status-code-mismatch"></a>Neshoda stavového kódu HTTP

**Zpráva:** Stavový kód odpovědi HTTP back-end se \' neshoduje s nastavením testu paměti. Očekávalo se: {HTTPStatusCode0} přijatých: {HTTPStatusCode1}.

**Příčina:** Po navázání připojení TCP a provedení TLS handshake (Pokud je povolený protokol TLS) Application Gateway odešle test jako požadavek HTTP GET na back-end Server. Jak je popsáno výše, výchozí sonda bude \<protocol\> ://127.0.0.1: \<port\> /a považuje se za stavové kódy odpovědí ve formátu Rage 200 až 399 jako v pořádku. Pokud server vrátí jakýkoliv jiný stavový kód, bude tato zpráva označena jako poškozená.

**Řešení:** V závislosti na kódu odpovědi back-end serveru můžete provést následující kroky. Tady jsou uvedené některé běžné stavové kódy:

| **Chyba** | **Akce** |
| --- | --- |
| Neshoda stavového kódu testu: přijata 401 | Ověřte, zda back-end Server vyžaduje ověření. Application Gateway PROBE nemůže předat přihlašovací údaje pro ověření. Buď povolte \" protokol HTTP 401 \" ve stavovém kódu sondy nebo proveďte test na cestu, kde server nevyžaduje ověření. |
| Neshoda stavového kódu testu: přijata 403 | Přístup je zakázán. Ověřte, jestli je na serveru back-end povolený přístup k cestě. |
| Neshoda stavového kódu testu: přijata 404 | Stránka se nenašla. Ověřte, zda je na serveru back-end přístupná cesta k názvu hostitele. Změňte název hostitele nebo parametr cesty na hodnotu, která je k dispozici. |
| Neshoda stavového kódu testu: přijata 405 | Požadavky testu na Application Gateway používají metodu HTTP GET. Ověřte, zda server tuto metodu povoluje. |
| Neshoda stavového kódu testu: přijata 500 | Vnitřní chyba serveru. Zkontrolujte stav back-endového serveru a jestli jsou služby spuštěné. |
| Neshoda stavového kódu testu: přijata 503 | Nedostupná služba. Zkontrolujte stav back-endového serveru a jestli jsou služby spuštěné. |

Pokud si myslíte, že odpověď je legitimní a chcete, aby Application Gateway přijímala další stavové kódy jako v pořádku, můžete vytvořit vlastní test. Tento přístup je užitečný v situacích, kdy back-end web potřebuje ověřování. Vzhledem k tomu, že požadavky sondy neobsahují žádné přihlašovací údaje uživatele, selžou, že back-end server vrátí stavový kód HTTP 401.

Pokud chcete vytvořit vlastní test paměti, postupujte podle [těchto kroků](./application-gateway-create-probe-portal.md).

#### <a name="http-response-body-mismatch"></a>Neshoda textu odpovědi HTTP

**Zpráva:** Tělo odpovědi HTTP back-end \' s neodpovídá nastavení testu paměti. Přijatý text odpovědi neobsahuje {String}.

**Příčina:** Když vytvoříte vlastní test, budete mít možnost označit back-end Server jako zdravý v závislosti na řetězci z těla odpovědi. Můžete například nakonfigurovat Application Gateway pro přijetí "neautorizovaného" jako řetězce, který se má shodovat. Pokud odpověď serveru back-end pro požadavek sondy obsahuje **neoprávněný** řetězec, bude označena jako v pořádku. V opačném případě bude tato zpráva označena jako poškozená.

**Řešení:** Chcete-li tento problém vyřešit, postupujte podle následujících kroků:

1.  Přihlaste se k serveru back-end místně nebo z klientského počítače v cestě testu a ověřte tělo odpovědi.

1.  Ověřte, že tělo odpovědi v Application Gateway konfiguraci vlastního testu paměti se shoduje s nakonfigurovaným nastavením.

1.  Pokud se neshodují, změňte konfiguraci sondy tak, aby bylo možné přijmout správnou hodnotu řetězce.

Přečtěte si další informace o [Application Gateway shodě](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> U všech chybových zpráv souvisejících s protokolem TLS si můžete přečíst další informace o chování SNI a rozdílech mezi SKU V1 a v2. Podívejte se na stránku [Přehled protokolu TLS](ssl-overview.md) .


#### <a name="backend-server-certificate-invalid-ca"></a>Neplatná CA certifikátu back-end serveru

**Zpráva:** Certifikát serveru používaný back-end není podepsán známou certifikační autoritou (CA). Povolí back-end na Application Gateway tím, že se nahraje kořenový certifikát certifikátu serveru používaného back-endu.

**Příčina:** Komplexní protokol SSL s Application Gateway v2 vyžaduje ověření certifikátu back-end serveru, aby bylo možné považovat Server za v pořádku.
Aby byl certifikát TLS/SSL důvěryhodný, musí být certifikát serveru back-end vydaný certifikační autoritou, která je součástí důvěryhodného úložiště Application Gateway. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou (například při použití certifikátu podepsaného svým držitelem), musí uživatelé odeslat certifikát vystavitele do Application Gateway.

**Řešení:** Pomocí těchto kroků exportujte důvěryhodný kořenový certifikát a nahrajte ho do Application Gateway. (Tyto kroky jsou pro klienty Windows.)

1.  Přihlaste se k počítači, ve kterém je vaše aplikace hostovaná.

1.  Vyberte Win + R nebo klikněte pravým tlačítkem na tlačítko **Start** a pak vyberte **Spustit**.

1.  Zadejte `certmgr.msc` a vyberte Enter. Správce certifikátů můžete také vyhledat v nabídce **Start** .

1.  Vyhledejte certifikát, obvykle v `\Certificates - Current User\\Personal\\Certificates\` a otevřete ho.

1.  Vyberte kořenový certifikát a pak vyberte **Zobrazit certifikát**.

1.  Ve vlastnostech certifikátu vyberte kartu **Podrobnosti** .

1.  Na kartě **Podrobnosti** vyberte možnost **Kopírovat do souboru** a uložte soubor v souboru X. 509 s kódováním Base-64 (. CER) formátu.

1.  Otevřete stránku Application Gateway **Nastavení** protokolu HTTP v Azure Portal.

1. Otevřete nastavení HTTP, vyberte **Přidat certifikát** a vyhledejte soubor certifikátu, který jste právě uložili.

1. Vyberte **Uložit** a uložte nastavení http.

Alternativně můžete exportovat kořenový certifikát z klientského počítače přímým přístupem k serveru (obejít Application Gateway) prostřednictvím prohlížeče a exportem kořenového certifikátu z prohlížeče.

Další informace o extrakci a nahrání důvěryhodných kořenových certifikátů v Application Gateway najdete v tématu [Export důvěryhodného kořenového certifikátu (pro SKU verze 2)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Neshoda důvěryhodného kořenového certifikátu

**Zpráva:** Kořenový certifikát serveru, který používá back-end, se neshoduje s důvěryhodným kořenovým certifikátem přidaným do aplikační brány. Ujistěte se, že jste přidali správný kořenový certifikát pro povolenýchí back-endu.

**Příčina:** Komplexní protokol SSL s Application Gateway v2 vyžaduje ověření certifikátu back-end serveru, aby bylo možné považovat Server za v pořádku.
Aby byl certifikát TLS/SSL důvěryhodný, musí být certifikát serveru back-end vydaný certifikační autoritou, která je součástí důvěryhodného úložiště Application Gateway. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou (například byl použit certifikát podepsaný svým držitelem), musí uživatelé odeslat certifikát vystavitele do Application Gateway.

Certifikát, který byl nahrán do Application Gateway nastavení HTTP, se musí shodovat s kořenovým certifikátem certifikátu back-end serveru.

**Řešení:** Pokud se zobrazí tato chybová zpráva, dojde k neshodě mezi certifikátem, který byl nahrán do Application Gateway a ten, který byl nahrán na back-end Server.

Při nahrávání správného důvěryhodného kořenového certifikátu do Application Gateway postupujte podle kroků 1-11 v předchozí metodě.

Další informace o extrakci a nahrání důvěryhodných kořenových certifikátů v Application Gateway najdete v tématu [Export důvěryhodného kořenového certifikátu (pro SKU verze 2)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> K této chybě může dojít také v případě, že back-end Server nemění úplný řetěz certifikátu, včetně kořenového > zprostředkujícího (Pokud je k dispozici) > list během metody handshake TLS. K ověření můžete použít příkazy OpenSSL z libovolného klienta a připojit se k back-end serveru pomocí nakonfigurovaných nastavení v Application Gateway PROBE.

Například:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Pokud výstup nezobrazuje úplný řetěz vráceného certifikátu, exportujte certifikát znovu s úplným řetězcem, včetně kořenového certifikátu. Nakonfigurujte tento certifikát na back-end serveru. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Neplatný běžný název certifikátu back-endu (CN)

**Zpráva:** Běžný název (CN) certifikátu back-endu se neshoduje s hlavičkou hostitele sondy.

**Příčina:** Application Gateway ověří, jestli název hostitele zadaný v nastavení HTTP back-endu odpovídá hodnotě CN, kterou prezentuje certifikát TLS/SSL serveru back-end. Toto je Standard_v2 a chování WAF_v2 SKU (v2). Standard a WAF SKU (V1) Indikace názvu serveru (SNI) se nastaví jako plně kvalifikovaný název domény v adrese back-endu fondu. Další informace o chování SNI a rozdílech mezi systémem V1 a 2 SKU najdete v tématu [Přehled ukončení protokolu TLS a koncového protokolu TLS s Application Gateway](ssl-overview.md).

Pokud ve skladové položce v2 existuje výchozí sonda (není nakonfigurovaný a přidružený žádný vlastní test paměti), SNI se nastaví z názvu hostitele uvedeného v nastavení HTTP. Nebo, pokud je v nastavení HTTP uveden příkaz "vybrat název hostitele z back-endové adresy", bude použito toto nastavení.

Pokud je k nastavení HTTP přidružená vlastní sonda, SNI se nastaví z názvu hostitele uvedeného v konfiguraci vlastního testu. Nebo, pokud se ve vlastním testu vybere možnost **Vybrat název hostitele z back-endu http** , sni se nastaví z názvu hostitele uvedeného v nastavení http.

Pokud je v nastavení HTTP nastavená možnost **Vybrat název hostitele z back-endu** , musí fond adres pro back-end obsahovat platný plně kvalifikovaný název domény.

Pokud se zobrazí tato chybová zpráva, CN certifikátu back-end se neshoduje s názvem hostitele nakonfigurovaným ve vlastním testu nebo v nastavení HTTP (Pokud je vybraná možnost **Vybrat název hostitele z back-endu http** ). Pokud používáte výchozí test, název hostitele se nastaví jako **127.0.0.1**. Pokud to není požadovaná hodnota, měli byste vytvořit vlastní test a přidružit ho k nastavení HTTP.

**Řešení:**

Pokud chcete tento problém vyřešit, použijte následující postup.

Ve Windows:

1.  Přihlaste se k počítači, ve kterém je vaše aplikace hostovaná.

1.  Vyberte Win + R nebo klikněte pravým tlačítkem na tlačítko **Start** a vyberte **Spustit**.

1.  Zadejte **certmgr. msc** a vyberte Enter. Správce certifikátů můžete také vyhledat v nabídce **Start** .

1.  Vyhledejte certifikát (obvykle v části `\Certificates - Current User\\Personal\\Certificates` ) a otevřete certifikát.

1.  Na kartě **Podrobnosti** se podívejte na **Předmět** certifikátu.

1.  Ověřte CN certifikátu z podrobností a zadejte stejný název do pole název hostitele vlastního testu nebo v nastavení HTTP (Pokud je vybraná **možnost vybrat název hostitele z back-endu http** ). Pokud se nejedná o požadovaný název hostitele vašeho webu, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele v konfiguraci vlastního testu nebo nastavení protokolu HTTP.

Pro Linux pomocí OpenSSL:

1.  Spusťte tento příkaz v OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  V zobrazených vlastnostech vyhledejte CN certifikátu a zadejte ho do pole název hostitele v nastavení http. Pokud se nejedná o požadovaný název hostitele vašeho webu, musíte získat certifikát pro tuto doménu nebo zadat správný název hostitele v konfiguraci vlastního testu nebo nastavení protokolu HTTP.

#### <a name="backend-certificate-is-invalid"></a>Certifikát back-endu je neplatný.

**Zpráva:** Certifikát back-endu je neplatný. Aktuální datum není v \" rozsahu platné od do \" a \" platnost do \" data v rámci certifikátu.

**Příčina:** Každý certifikát se dodává s rozsahem platnosti a připojení HTTPS nebude zabezpečené, pokud není certifikát TLS/SSL serveru platný. Aktuální data musí být v rozmezí od do rozsahu **platný od** **do** . Pokud není, certifikát se považuje za neplatný a vytvoří problémy se zabezpečením, které Application Gateway označí back-end Server jako špatný.

**Řešení:** Pokud vypršela platnost certifikátu TLS/SSL, obnovte certifikát u svého dodavatele a aktualizujte nastavení serveru pomocí nového certifikátu. Pokud se jedná o certifikát podepsaný svým držitelem, musíte vygenerovat platný certifikát a nahrajte kořenový certifikát do nastavení Application Gateway HTTP. Provedete to podle těchto kroků:

1.  Otevřete nastavení Application Gateway HTTP na portálu.

1.  Vyberte nastavení, které má certifikát s vypršenou platností, vyberte **Přidat certifikát** a otevřete nový soubor certifikátu.

1.  Odeberte starý certifikát pomocí ikony **Odstranit** vedle certifikátu a pak vyberte **Uložit**.

#### <a name="certificate-verification-failed"></a>Certifikát se nepovedlo ověřit.

**Zpráva:** Platnost certifikátu back-endu nelze ověřit. Pokud chcete zjistit příčinu, podívejte se na diagnostiku OpenSSL pro zprávu spojenou s kódem chyby {errorCode}.

**Příčina:** K této chybě dochází, když Application Gateway nemůže ověřit platnost certifikátu.

**Řešení:** Chcete-li tento problém vyřešit, ověřte, zda byl certifikát na serveru správně vytvořen. Můžete například použít [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) k ověření certifikátu a jeho vlastností a pak zkuste znovu odeslat certifikát do nastavení Application Gateway http.

<a name="backend-health-status-unknown"></a>Stav back-endu: neznámý
-------------------------------
Pokud je stav back-endu zobrazen jako neznámý, zobrazení portálu bude vypadat jako na následujícím snímku obrazovky:

![Stav back-endu Application Gateway – neznámý](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

K tomuto chování může dojít z některého z následujících důvodů:

1.  NSG v podsíti Application Gateway blokuje příchozí přístup k portům 65503-65534 (v1 SKU) nebo 65200-65535 (SKU v2) z Internetu.
1.  UDR v podsíti Application Gateway je nastavená na výchozí trasu (0.0.0.0/0) a další segment směrování není zadaný jako Internet.
1.  Výchozí trasa se inzeruje připojením ExpressRoute/VPN k virtuální síti přes protokol BGP.
1.  Vlastní server DNS je nakonfigurovaný ve virtuální síti, která nemůže překládat názvy veřejných domén.
1.  Application Gateway je ve stavu Není v pořádku.

**Řešení:**

1.  Ověřte, jestli váš NSG blokuje přístup k portům 65503-65534 (v1 SKU) nebo 65200-65535 (SKU v2) z **Internetu**:

    a.  Na kartě **přehled** Application Gateway vyberte odkaz **Virtual Network/podsíť** .

    b.  Na kartě **podsítě** ve vaší virtuální síti vyberte podsíť, ve které Application Gateway nasazená.

    c.  Ověřte, jestli je nakonfigurované nějaké NSG.

    d.  Pokud je NSG nakonfigurovaný, vyhledejte tento prostředek NSG na kartě **Hledat** nebo v části **všechny prostředky**.

    e.  V části **příchozí pravidla** přidejte příchozí pravidlo, které povoluje rozsah cílových portů 65503-65534 pro SKU v1 nebo 65200-65535 v2 SKU se **zdrojovou** sadou jako **libovolný** nebo **internetový**.

    f.  Vyberte **Uložit** a ověřte, zda můžete zobrazit back-end jako v pořádku. Případně to můžete provést prostřednictvím [PowerShellu nebo](../virtual-network/manage-network-security-group.md)rozhraní příkazového řádku.

1.  Ověřte, jestli váš UDR má výchozí trasu (0.0.0.0/0) s dalším segmentem směrování, který není nastavený jako **Internet**:
    
    a.  K určení vaší podsítě postupujte podle kroků 1a a 1b.

    b.  Ověřte, jestli je nakonfigurované nějaké UDR. Pokud je, vyhledejte prostředek na panelu hledání nebo v části **všechny prostředky**.

    c.  Ověřte, jestli existují žádné výchozí trasy (0.0.0.0/0) s dalším segmentem směrování, který není nastavený jako **Internet**. Pokud je toto nastavení buď **virtuální zařízení** , nebo **Brána Virtual Network**, musíte zajistit, aby virtuální zařízení nebo místní zařízení správně směrovala paket zpět do cílového umístění v Internetu bez změny paketu.

    d.  V opačném případě změňte další směrování na **Internet**, vyberte **Uložit** a ověřte stav back-endu.

1.  Výchozí trasa inzerovaná připojením ExpressRoute/VPN k virtuální síti prostřednictvím protokolu BGP:

    a.  Pokud máte připojení ExpressRoute/VPN k virtuální síti přes protokol BGP a při inzerování výchozí trasy, je nutné zajistit, aby byl paket směrován zpět do cílového umístění v Internetu beze změny. Můžete ověřit pomocí možnosti **řešení potíží s připojením** na portálu Application Gateway.

    b.  Cíl vyberte ručně jako libovolnou IP adresu pro směrování přes Internet, jako je 1.1.1.1. Nastavte cílový port jako cokoli a ověřte připojení.

    c.  Pokud je další segment směrování bránou virtuální sítě, může se jednat o výchozí trasu inzerovanou přes ExpressRoute nebo VPN.

1.  Pokud je ve virtuální síti nakonfigurovaný vlastní server DNS, ověřte, že server (nebo servery) dokáže vyřešit veřejné domény. Ve scénářích, kdy se Application Gateway musí dostat k externím doménám, jako jsou servery OCSP, nebo zjistit stav odvolání certifikátu, může být vyžadováno řešení názvů veřejných domén.

1.  Pokud chcete ověřit, jestli je Application Gateway v pořádku a běží, na portálu vyberte možnost **Resource Health** a ověřte, jestli je stav v **pořádku**. Pokud se zobrazí stav není v **pořádku** nebo je **snížený** , obraťte se na [podporu](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Další kroky
----------

Přečtěte si další informace o [diagnostice a protokolování Application Gateway](./application-gateway-diagnostics.md).
