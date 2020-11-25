---
title: Řešení problémů s připojením Point-to-site k Azure
titleSuffix: Azure VPN Gateway
description: Naučte se řešit běžné problémy s připojením Point-to-site a další chyby a problémy s virtuálními privátními sítěmi.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994855"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Řešení potíží: problémy s připojením k bodům Azure Point-to-site

V tomto článku jsou uvedené běžné problémy s připojením Point-to-site, se kterými se můžete setkat. Popisuje také možné příčiny a řešení těchto problémů.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Chyba klienta VPN: certifikát se nepovedlo najít.

### <a name="symptom"></a>Příznak

Když se pokusíte připojit ke službě Azure Virtual Network pomocí klienta VPN, zobrazí se tato chybová zpráva:

**Nebyl nalezen certifikát, který lze použít s tímto protokolem EAP (Extensible Authentication Protocol). (Chyba 798)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud certifikát klienta chybí v části **Certifikáty – aktuální User\Personal\Certificates**.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte podle následujících kroků:

1. Otevřete Správce certifikátů: klikněte na **Start**, zadejte **Správa certifikátů počítačů** a pak ve výsledcích hledání klikněte na **spravovat certifikáty počítače** .

2. Ujistěte se, že tyto certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuální User\Personal\Certificates |
    | AzureRoot. cer    | Kořenové certifikační autority místních Computer\Trusted|

3. \<UserName>V části C:\Users \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , ručně nainstalujte certifikát (soubor *. cer) do úložiště uživatele a počítače.

Další informace o instalaci klientského certifikátu najdete v tématu [generování a export certifikátů pro připojení Point-to-site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Při importu klientského certifikátu nevybírejte možnost **Povolit silnou ochranu privátního klíče** .

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Síťové připojení mezi počítačem a serverem VPN nebylo možné navázat, protože vzdálený server neodpovídá.

### <a name="symptom"></a>Příznak

Když se pokusíte připojit k bráně virtuální sítě Azure pomocí IKEv2 ve Windows, zobrazí se tato chybová zpráva:

**Síťové připojení mezi počítačem a serverem VPN nebylo možné navázat, protože vzdálený server neodpovídá.**

### <a name="cause"></a>Příčina
 
 K tomuto problému dochází, pokud verze systému Windows nepodporuje fragmentaci protokolu IKE.
 
### <a name="solution"></a>Řešení

IKEv2 se podporuje v systémech Windows 10 a Server 2016. Pokud ale chcete používat IKEv2, musíte nainstalovat aktualizace a nastavit hodnotu klíče registru v místním prostředí. Verze operačního systému starší než Windows 10 se nepodporují a mohou používat jenom SSTP.

Postup přípravy systému Windows 10 nebo Server 2016 na IKEv2:

1. Nainstalujte aktualizaci.

   | Verze operačního systému | Datum | Číslo/odkaz |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 verze 1607 | 17. ledna 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 verze 1703 | 17. ledna 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 verze 1709 | 22. března 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Nastavte hodnotu klíče registru. Vytvořte nebo nastavte `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` klíč REG_DWORD v registru na 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Chyba klienta VPN: přijatá zpráva nebyla očekávaná nebo má chybný formát.

### <a name="symptom"></a>Příznak

Když se pokusíte připojit ke službě Azure Virtual Network pomocí klienta VPN, zobrazí se tato chybová zpráva:

**Přijatá zpráva byla Neočekávaná nebo chybně formátovaná. (Chyba 0x80090326)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud je splněna jedna z následujících podmínek:

- Je nesprávně nastaveno použití tras definovaných uživatelem (UDR) s výchozí trasou v podsíti brány.
- Veřejný klíč kořenového certifikátu se nenačte do brány Azure VPN. 
- Klíč je poškozený nebo vypršel jeho platnost.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte podle následujících kroků:

1. Odeberte UDR v podsíti brány. Ujistěte se, že UDR předávat veškerý provoz správně.
2. Zkontrolujte stav kořenového certifikátu v Azure Portal a zjistěte, zda byl odvolán. Pokud se neodvolává, pokuste se odstranit kořenový certifikát a znovu ho odeslat. Další informace najdete v tématu [Vytvoření certifikátů](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Chyba klienta VPN: byl zpracován řetěz certifikátů, ale ukončen. 

### <a name="symptom"></a>Příznak 

Když se pokusíte připojit ke službě Azure Virtual Network pomocí klienta VPN, zobrazí se tato chybová zpráva:

**Řetěz certifikátů byl zpracován, ale ukončen v kořenovém certifikátu, který není důvěryhodný pro poskytovatele důvěryhodnosti.**

### <a name="solution"></a>Řešení

1. Ujistěte se, že tyto certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuální User\Personal\Certificates |
    | Azuregateway-*GUID*. cloudapp.NET  | Aktuální kořenové certifikační autority User\Trusted|
    | AzureGateway-*GUID*. cloudapp.NET, AzureRoot. cer    | Kořenové certifikační autority místních Computer\Trusted|

2. Pokud se již certifikáty nacházejí v umístění, zkuste certifikáty odstranit a znovu je nainstalujte. Certifikát **azuregateway-*GUID*. cloudapp.NET** je v balíčku pro konfiguraci klienta VPN, který jste si stáhli z Azure Portal. K extrakci souborů z balíčku můžete použít nástroje pro archivaci souborů.

## <a name="file-download-error-target-uri-is-not-specified"></a>Chyba stahování souboru: není zadaný cílový identifikátor URI.

### <a name="symptom"></a>Příznak

Zobrazí se následující chybová zpráva:

**Chyba stahování souboru Není zadaný cílový identifikátor URI.**

### <a name="cause"></a>Příčina 

K tomuto problému dochází kvůli nesprávnému typu brány. 

### <a name="solution"></a>Řešení

Typ brány VPN musí být **VPN** a typ VPN musí být **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Chyba klienta VPN: nepovedl se vlastní skript Azure VPN. 

### <a name="symptom"></a>Příznak

Když se pokusíte připojit ke službě Azure Virtual Network pomocí klienta VPN, zobrazí se tato chybová zpráva:

**Vlastní skript (pro aktualizaci směrovací tabulky) se nezdařil. (Chyba 8007026f)**

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud se pokoušíte otevřít připojení VPN typu Site-to-Point pomocí zástupce.

### <a name="solution"></a>Řešení 

Otevřete balíček VPN přímo místo jeho otevření z zástupce.

## <a name="cannot-install-the-vpn-client"></a>Nejde nainstalovat klienta VPN.

### <a name="cause"></a>Příčina 

K důvěřování bráně VPN pro virtuální síť se vyžaduje další certifikát. Certifikát je zahrnutý v balíčku pro konfiguraci klienta VPN, který je vygenerovaný z Azure Portal.

### <a name="solution"></a>Řešení

Rozbalte konfigurační balíček klienta VPN a vyhledejte soubor. cer. K instalaci certifikátu použijte následující postup:

1. Otevřete mmc.exe.
2. Přidejte modul snap-in **certifikáty** .
3. Vyberte účet **počítače** pro místní počítač.
4. Klikněte pravým tlačítkem na uzel **Důvěryhodné kořenové certifikační autority** . Klikněte na **vše –**  >  **Import** úlohy a vyhledejte soubor. CER, který jste extrahovali z balíčku pro konfiguraci klienta VPN.
5. Restartujte počítač. 
6. Zkuste nainstalovat klienta VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal Chyba: nepovedlo se uložit bránu VPN a data jsou neplatná.

### <a name="symptom"></a>Příznak

Při pokusu o uložení změn pro bránu VPN v Azure Portal se zobrazí následující chybová zpráva:

**Nepovedlo se uložit &lt; *název brány* brány virtuální sítě &gt; . Data pro ID certifikátu certifikátu &lt; *certificate ID* &gt; nejsou platná.**

### <a name="cause"></a>Příčina 

K tomuto problému může dojít, pokud veřejný klíč kořenového certifikátu, který jste nahráli, obsahuje neplatný znak, například mezeru.

### <a name="solution"></a>Řešení

Ujistěte se, že data v certifikátu neobsahují neplatné znaky, jako je například zalomení řádků (znak návratu na začátek řádku). Celá hodnota by měla být jedna dlouhá čára. Následující text je ukázkou certifikátu:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal Chyba: nepovedlo se uložit bránu VPN a název prostředku je neplatný.

### <a name="symptom"></a>Příznak

Při pokusu o uložení změn pro bránu VPN v Azure Portal se zobrazí následující chybová zpráva: 

**Nepovedlo se uložit &lt; *název brány* brány virtuální sítě &gt; . Název prostředku název &lt; *certifikátu, který se pokoušíte nahrát* , &gt; je neplatný**.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože název certifikátu obsahuje neplatný znak, například mezeru. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Chyba Azure Portal: Chyba při stahování souboru balíčku VPN 503

### <a name="symptom"></a>Příznak

Při pokusu o stažení konfiguračního balíčku klienta VPN se zobrazí následující chybová zpráva:

**Nepovedlo se stáhnout soubor. Podrobnosti o chybě: chyba 503. Server je zaneprázdněný.**
 
### <a name="solution"></a>Řešení

Tato chyba může být způsobena dočasným problémem v síti. Zkuste stáhnout balíček VPN znovu za několik minut.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Upgrade služby Azure VPN Gateway: všechny odkazy na klienty lokality se nemůžou připojit

### <a name="cause"></a>Příčina

Pokud je certifikát v průběhu své životnosti vyšší než 50%, certifikát se převezme.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, znovu stáhněte a znovu nasaďte bod do balíčku lokality na všech klientech.

## <a name="too-many-vpn-clients-connected-at-once"></a>Současně bylo připojeno příliš mnoho klientů VPN.

Dosáhlo se maximálního počtu povolených připojení. Celkový počet připojených klientů můžete zobrazit v Azure Portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient VPN nemá přístup ke sdíleným složkám souborů.

### <a name="symptom"></a>Příznak

Klient VPN se připojil ke službě Azure Virtual Network. Klient ale nemůže získat přístup ke sdíleným složkám v síti.

### <a name="cause"></a>Příčina

Protokol SMB se používá pro přístup ke sdílení souborů. Po zahájení připojení přidá klient sítě VPN přihlašovací údaje relace a dojde k selhání. Po navázání připojení je nutné, aby klient používal přihlašovací údaje mezipaměti pro ověřování protokolem Kerberos. Tento proces inicializuje dotazy do služba KDC (Key Distribution Center) (řadič domény), aby získal token. Vzhledem k tomu, že se klient připojuje z Internetu, nemusí být schopný získat přístup k řadiči domény. Proto klient nemůže převzít služby při selhání z protokolu Kerberos na NTLM. 

Jediným okamžikem, kdy je klient vyzván k zadání přihlašovacích údajů, je v případě, že má platný certifikát (s SAN = UPN) vydaných doménou, ke které je připojen. Klient musí být také fyzicky připojen k doméně sítě. V takovém případě se klient pokusí použít certifikát a narazí na řadič domény. Pak služba KDC (Key Distribution Center) vrátí chybu "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient je nucen převzít služby při selhání do protokolu NTLM. 

### <a name="solution"></a>Řešení

Pokud chcete tento problém obejít, zakažte ukládání přihlašovacích údajů domény do mezipaměti z následujícího podklíče registru: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Po přeinstalaci klienta VPN nejde najít připojení typu Point-to-Site VPN v systému Windows.

### <a name="symptom"></a>Příznak

Odeberete připojení VPN typu Point-to-site a pak znovu nainstalujete klienta VPN. V takovém případě není připojení VPN úspěšně nakonfigurováno. Připojení VPN se nezobrazuje v nastavení **síťových připojení** v systému Windows.

### <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, odstraňte staré konfigurační soubory klienta VPN z **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** a pak znovu spusťte instalační program klienta VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient VPN typu Point-to-site nemůže přeložit plně kvalifikovaný název domény prostředků v místní doméně.

### <a name="symptom"></a>Příznak

Když se klient připojí k Azure pomocí připojení VPN typu Point-to-site, nemůže přeložit plně kvalifikovaný název domény prostředků ve vaší místní doméně.

### <a name="cause"></a>Příčina

Klient VPN typu Point-to-site normálně používá Azure DNS servery, které jsou nakonfigurované ve službě Azure Virtual Network. Servery Azure DNS mají přednost před místními servery DNS, které jsou nakonfigurované v klientovi (Pokud Metrika rozhraní sítě Ethernet není nižší), takže všechny dotazy DNS se odesílají na servery Azure DNS. Pokud servery Azure DNS neobsahují záznamy pro místní prostředky, dotaz se nezdařil.

### <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, ujistěte se, že servery Azure DNS, které se používají ve službě Azure Virtual Network, můžou přeložit záznamy DNS pro místní prostředky. K tomu můžete použít servery pro přeposílání DNS nebo podmíněné servery pro přeposílání. Další informace najdete v tématu [překlad názvů pomocí vlastního serveru DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Připojení VPN typu Point-to-site je navázáno, ale stále se nemůžete připojit k prostředkům Azure. 

### <a name="cause"></a>Příčina

K tomuto problému může dojít, když klient VPN nezíská trasy z Azure VPN Gateway.

### <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, [resetujte službu Azure VPN Gateway](./reset-gateway.md). Pokud chcete zajistit, aby se nové trasy používaly, je potřeba po úspěšné konfiguraci partnerského vztahu virtuálních sítí stáhnout klienty VPN typu Point-to-site znovu.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Chyba: "funkce odvolání nemohla ověřit odvolání, protože server odvolání byl offline. (Error 0x80092013)

### <a name="causes"></a>Příčiny
Tato chybová zpráva se zobrazí, pokud klient nemá přístup http://crl3.digicert.com/ssca-sha2-g1.crl a http://crl4.digicert.com/ssca-sha2-g1.crl .  Tato kontroly odvolání vyžaduje přístup k těmto dvěma webům.  K tomuto problému obvykle dochází v klientovi, který má proxy server nakonfigurovaný. Pokud se v některých prostředích požadavky neprojde proxy server, zamítne se na hraniční bráně firewall.

### <a name="solution"></a>Řešení

Zkontrolujte nastavení proxy server a ujistěte se, že klient má přístup http://crl3.digicert.com/ssca-sha2-g1.crl a http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Chyba klienta VPN: připojení bylo znemožněno kvůli zásadám nakonfigurovaným na serveru RAS/VPN. (Chyba 812)

### <a name="cause"></a>Příčina

K této chybě dochází, pokud server RADIUS, který jste použili pro ověřování klienta VPN, má nesprávná nastavení nebo brána Azure nemůže kontaktovat server RADIUS.

### <a name="solution"></a>Řešení

Ujistěte se, že je server RADIUS správně nakonfigurovaný. Další informace najdete v tématu [integrace ověřování pomocí protokolu RADIUS s Azure AD Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Chyba 405" při stažení kořenového certifikátu z VPN Gateway

### <a name="cause"></a>Příčina

Kořenový certifikát nebyl nainstalován. Kořenový certifikát je nainstalován v úložišti **důvěryhodných certifikátů** klienta.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Chyba klienta VPN: vzdálené připojení nebylo provedeno, protože se nezdařilo pokus o tunely VPN. (Chyba 800) 

### <a name="cause"></a>Příčina

Ovladač síťové karty je zastaralý.

### <a name="solution"></a>Řešení

Aktualizujte ovladač síťových adaptérů:

1. Klikněte na **Start**, zadejte **Device Manager** a vyberte ho ze seznamu výsledků. Pokud se zobrazí výzva k zadání hesla správce nebo k potvrzení, zadejte heslo nebo zadejte potvrzení.
2. V kategorii **síťové adaptéry** Najděte síťovou kartu, kterou chcete aktualizovat.  
3. Dvakrát klikněte na název zařízení, vyberte **Aktualizovat ovladač** a **pro aktualizovaný software ovladače vyberte automaticky hledat**.
4. Pokud systém Windows nenalezne nový ovladač, zkuste ho vyhledat na webu výrobce zařízení a postupujte podle pokynů.
5. Restartujte počítač a zkuste připojení znovu.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Chyba klienta VPN: vytočení připojení VPN <VPN Connection Name> , stav = platforma VPN neaktivovala připojení.

V Prohlížeč událostí se může zobrazit také následující chyba z RasClient: "uživatel <User> vyvolal připojení s názvem, <VPN Connection Name> které selhalo. Kód chyby vrácený při selhání je 1460. "

### <a name="cause"></a>Příčina

Klient Azure VPN nemá povolené oprávnění aplikace na pozadí v nastavení aplikace pro Windows.

### <a name="solution"></a>Řešení

1. V systému Windows přejít na nastavení-> ochrana osobních údajů – > aplikace na pozadí
2. Přepnutí možnosti povolit aplikacím běžet na pozadí

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Chyba: cílový identifikátor URI při stahování souboru není zadaný.

### <a name="cause"></a>Příčina

To je způsobeno tím, že je nakonfigurován nesprávný typ brány.

### <a name="solution"></a>Řešení

Typ brány VPN Azure musí být VPN a typ VPN musí být **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalace balíčku VPN se nedokončí.

### <a name="cause"></a>Příčina

Tento problém může být způsoben předchozími instalacemi klienta VPN. 

### <a name="solution"></a>Řešení

Odstraňte staré konfigurační soubory klienta VPN z **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** a znovu spusťte instalační program klienta VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Po určité době hibernace nebo režim spánku klienta VPN

### <a name="solution"></a>Řešení

Ověřte nastavení režimu spánku a hibernace v počítači, ve kterém je spuštěný klient VPN.