---
title: Poradce při potížích Azure připojení point-to-site | Dokumentace Microsoftu
description: Zjistěte, jak řešit problémy s připojením point-to-site.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: cd89c41b43be1da339ca7dcc64110e7145a93903
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857326"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Poradce při potížích: Problémy s připojením Azure point-to-site

Tento článek uvádí běžné problémy s připojením point-to-site, které se mohou vyskytnout. Popisuje také možné příčiny a řešení těchto problémů.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Chyba klienta VPN: certifikát nebyl nalezen.

### <a name="symptom"></a>Příznak

Při pokusu o připojení ke službě Azure virtual network pomocí klienta VPN, zobrazí se následující chybová zpráva:

**Certifikát se nenašel, který lze použít s této Extensible Authentication Protocol. (Chyba 798)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud chybí klientský certifikát z **Certificates - Current User\Personal\Certificates**.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte podle těchto kroků:

1. Klikněte na tlačítko Otevřít správce certifikátů: **Start**, typ **spravovat certifikáty počítače**a potom klikněte na tlačítko **spravovat certifikáty počítače** ve výsledcích hledání.

2. Ujistěte se, že tyto certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuální uživatel\osobní\certifikáty |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuální User\Trusted kořenové certifikační autority|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Místní počítač\Důvěryhodné kořenové certifikační autority|

3. Přejděte na uživatelé\<uživatelské jméno > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, je nutné ručně nainstalovat certifikát (soubor *.cer) v úložišti počítače a uživatele.

Další informace o postupu při instalaci klientského certifikátu naleznete v tématu [generování a export certifikátů pro připojení point-to-site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Při importu certifikátu klienta nesmí být zvolen **povolit silnou ochranu privátního klíče** možnost.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Chyba klienta VPN: byla přijata zpráva není očekávaná nebo chybně formátovaná

### <a name="symptom"></a>Příznak

Při pokusu o připojení ke službě Azure virtual network pomocí klienta VPN, zobrazí se následující chybová zpráva:

**Byla přijata zpráva není očekávaná nebo se nesprávně formátovaný. (Chyba 0x80090326)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud platí jedna z následujících podmínek:

- Použití uživatelem definované trasy (UDR) s výchozí trasa v podsíti brány je nastavena správně.
- Veřejný klíč kořenového certifikátu není nahráli do Azure VPN gateway. 
- Klíč je poškozený nebo vypršela platnost.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte podle těchto kroků:

1. Odeberte uživatelem definovaná TRASA v podsíti brány. Ujistěte se, že uživatelem definovaná TRASA předává všechny přenosy správně.
2. Zkontrolujte stav kořenového certifikátu na portálu Azure, zda byl odvolán. Pokud nebude odvolaný, pokusu o odstranění kořenového certifikátu a reupload. Další informace najdete v tématu [vytvářet certifikáty](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Chyba klienta VPN: zpracuje řetěz certifikátů, ale byla ukončena 

### <a name="symptom"></a>Příznak 

Při pokusu o připojení ke službě Azure virtual network pomocí klienta VPN, zobrazí se následující chybová zpráva:

**Řetěz certifikátů zpracuje, ale byla ukončena v kořenovém certifikátu, který není důvěryhodný vztah důvěryhodnosti zprostředkovatele.**

### <a name="solution"></a>Řešení

1. Ujistěte se, že tyto certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuální uživatel\osobní\certifikáty |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuální User\Trusted kořenové certifikační autority|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Místní počítač\Důvěryhodné kořenové certifikační autority|

2. Pokud certifikáty jsou už v umístění, zkuste odstranit certifikáty a znovu nainstalovat. **Azuregateway -*GUID*. cloudapp.net** certifikát se nachází ve balíček konfigurace klienta VPN, který jste stáhli z portálu Azure portal. Můžete použít soubor archivers extrahujte soubory z balíčku.

## <a name="file-download-error-target-uri-is-not-specified"></a>Chyba stahování souborů: není zadaný cílový identifikátor URI

### <a name="symptom"></a>Příznak

Zobrazí se následující chybová zpráva:

**Chyba při stahování souboru. Není zadaný cílový identifikátor URI.**

### <a name="cause"></a>Příčina 

K tomuto problému dochází kvůli typem nesprávné brány. 

### <a name="solution"></a>Řešení

Musí být typ brány VPN **VPN**, a musí být typ sítě VPN **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Chyba klienta VPN: vlastních skriptů Azure VPN se nezdařilo 

### <a name="symptom"></a>Příznak

Při pokusu o připojení ke službě Azure virtual network pomocí klienta VPN, zobrazí se následující chybová zpráva:

**Vlastní skript (pro aktualizaci směrovací tabulky) se nezdařilo. (Chyba 8007026f)**

### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud se pokoušíte otevřít lokality bod připojení k síti VPN pomocí zástupce.

### <a name="solution"></a>Řešení 

Otevřete balíček VPN přímo, bez otevření pomocí zástupce.

## <a name="cannot-install-the-vpn-client"></a>Nelze nainstalovat klienta VPN

### <a name="cause"></a>Příčina 

Další certifikát se vyžaduje, aby důvěřoval VPN gateway pro virtuální síť. Certifikát je součástí balíčku konfigurace klienta VPN, který je generován z portálu Azure portal.

### <a name="solution"></a>Řešení

Extrahovat balíček pro konfiguraci klienta VPN a vyhledejte soubor .cer. Pokud chcete nainstalovat certifikát, postupujte takto:

1. Otevřete mmc.exe.
2. Přidat **certifikáty** modul snap-in.
3. Vyberte **počítače** účtu v místním počítači.
4. Klikněte pravým tlačítkem myši **důvěryhodných kořenových certifikačních autorit** uzlu. Klikněte na tlačítko **všechny úlohy** > **Import**a přejděte do souboru .cer, který jste rozbalili ze konfiguračního balíčku klienta VPN.
5. Restartujte počítač. 
6. Došlo k pokusu o instalaci klienta VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure portal Chyba: nepovedlo se uložit bránu sítě VPN a data nejsou platná

### <a name="symptom"></a>Příznak

Při pokusu o uložení změny pro bránu sítě VPN na webu Azure Portal, zobrazí se následující chybová zpráva:

**Nepovedlo se uložit bránu virtuální sítě &lt; *název brány*&gt;. Data pro certifikát &lt; *certifikátu ID* &gt; je neplatný.**

### <a name="cause"></a>Příčina 

Tomuto problému může dojít, pokud kořenový certifikát veřejný klíč, který jste nahráli obsahuje neplatný znak, například mezera.

### <a name="solution"></a>Řešení

Ujistěte se, že data v certifikátu neobsahuje neplatné znaky, jako je například konce řádků (návrat). Celá hodnota by měla být jeden dlouhý řádek. Následující text je ukázka certifikátu:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure portal Chyba: nepovedlo se uložit bránu VPN gateway a název prostředku je neplatný

### <a name="symptom"></a>Příznak

Při pokusu o uložení změny pro bránu sítě VPN na webu Azure Portal, zobrazí se následující chybová zpráva: 

**Nepovedlo se uložit bránu virtuální sítě &lt; *název brány*&gt;. Název prostředku &lt; *název certifikátu pokusu o odeslání* &gt; je neplatný**.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože název certifikátu obsahuje neplatný znak, například mezera. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal Chyba: Chyba stahování souboru balíčku sítě VPN 503

### <a name="symptom"></a>Příznak

Při pokusu o stažení konfiguračního balíčku klienta VPN, zobrazí se následující chybová zpráva:

**Nepovedlo se stáhnout soubor. Podrobnosti o chybě: chyba 503. Server je zaneprázdněn.**
 
### <a name="solution"></a>Řešení

Tuto chybu může způsobovat k dočasným potížím sítě. Došlo k pokusu o stažení balíčku pro VPN znovu za pár minut.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Upgrade služby Azure VPN Gateway: All, přejděte na klienty lokality není možné se připojit

### <a name="cause"></a>Příčina

Pokud je více než 50 procent certifikát prostřednictvím svého životního cyklu certifikátu jednotlivě.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, znovu nasaďte bod tak, aby lokality balíček ve všech klientech.

## <a name="too-many-vpn-clients-connected-at-once"></a>Příliš mnoho klientů VPN připojený najednou

Maximální počet připojení povolených u každé brány VPN gateway je 128. Zobrazí se celkový počet připojených klientů na webu Azure Portal.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Point-to-site VPN nesprávně přidá trasu pro 10.0.0.0/8 do směrovací tabulky

### <a name="symptom"></a>Příznak

Když jste telefonické připojení sítě VPN na straně klienta point-to-site, klient VPN nutné přidat směrování směrem k virtuální síti Azure. Pomocná služba IP nutné přidat směrování pro podsíť klientů VPN. 

Rozsah klienta VPN patří do menších podsíti 10.0.0.0/8, jako je například 10.0.12.0/24. Místo trasu pro 10.0.12.0/24 přidá se trasa pro 10.0.0.0/8, který má vyšší prioritu. 

Tato trasa nesprávné přestane fungovat připojení s jinými s místními sítěmi, které může patřit do jiné podsítě v rámci rozsahu 10.0.0.0/8, jako je například 10.50.0.0/24, které neobsahují konkrétní trasy definované. 

### <a name="cause"></a>Příčina

Toto chování je záměrné pro klienty Windows. Pokud klient používá protokol PPP IPCP, získá IP adresu pro rozhraní tunelu ze serveru (brána sítě VPN v tomto případě). Ale kvůli omezení v protokolu, klient nemá masku podsítě. Protože neexistuje jiný způsob, jak získat, klient se pokusí odhadnout masku podsítě, které jsou založené na třídě IP adresu rozhraní tunelového propojení. 

Proto přidá se trasa podle následující statické mapování: 

Pokud adresa patří do třídy A aplikaci--> použít /8

Pokud adresa patří do třídy B--> použijte /16

Pokud adresa patří do třídy C--> použijte /24

### <a name="solution"></a>Řešení

Máte trasy pro ostatní sítě vloženy do směrovací tabulky s nejdelší shody předpony nebo metriky (tedy vyšší prioritou) nižší než bod do lokality. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klienta VPN nemůže přistupovat k síťové sdílené složky

### <a name="symptom"></a>Příznak

Klienta VPN je připojená k virtuální síti Azure. Klient nemá přístup k síťové sdílené složky.

### <a name="cause"></a>Příčina

Protokol SMB se používá pro přístup ke sdílené složce souborů. Při zahájení připojení klienta VPN přidá přihlašovací údaje relace a dojde k selhání. Po připojení se naváže, klient je donucen na používání mezipaměti přihlašovacích údajů pro ověřování protokolem Kerberos. Tento proces se spustí dotazy na Key Distribution Center (řadič domény) k získání tokenu. Protože klient připojí z Internetu, nemusí být schopni připojit k řadiči domény. Proto klient nemůže převzetí služeb při selhání z protokolu Kerberos k ověřování NTLM. 

Jediný čas, který obdrží klient výzvu pro přihlašovací údaj je, když má platný certifikát (se sítí SAN = hlavní název uživatele) vydané doménu, ke kterému je připojený. Klient také musí být fyzicky připojen k doménové síti. V tomto případě klient se pokusí použít certifikát a kontaktuje řadič domény. Key Distribution Center vrátí chybu "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient je donucen převzít služby při selhání k ověřování NTLM. 

### <a name="solution"></a>Řešení

Chcete-li tento problém obejít, zakážete ukládání do mezipaměti přihlašovací údaje domény z následující podklíč registru: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nelze najít připojení point-to-site VPN ve Windows po opětovné instalaci klienta VPN

### <a name="symptom"></a>Příznak

Odebrat připojení VPN typu point-to-site a opětovnou instalací klienta VPN. V takovém případě není připojení k síti VPN byl úspěšně nakonfigurován. Nevidíte prostřednictvím připojení VPN **síťová připojení** nastavení ve Windows.

### <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém, odstranit staré soubory konfigurace klienta VPN z **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >**, a poté znovu spusťte instalační program klienta VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient VPN Point-to-site nelze přeložit plně kvalifikovaný název domény zdroje v místní doméně

### <a name="symptom"></a>Příznak

Když se klient připojí k Azure pomocí připojení VPN typu point-to-site, nelze přeložit FQND prostředkům v místní doméně.

### <a name="cause"></a>Příčina

Klient VPN Point-to-site používá servery Azure DNS, které jsou nakonfigurované ve virtuální síti Azure. Servery Azure DNS mají přednost před místní servery DNS, které jsou nakonfigurované v klientovi, aby všechny dotazy DNS se odesílají na servery Azure DNS. Pokud servery Azure DNS záznamy pro místní prostředky, že se dotaz nezdaří.

### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, ujistěte se, že servery Azure DNS používá ve službě Azure virtual network můžete překlad záznamů DNS pro místní prostředky. K tomuto účelu můžete použít servery DNS pro předávání nebo servery pro podmíněné předávání. Další informace najdete v tématu [překladu IP adresy serveru DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Navázání připojení VPN typu point-to-site, ale se pořád nemůžete připojit k prostředkům Azure 

### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud klient VPN nezíská trasy ze služby Azure VPN gateway.

### <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém [resetování brány Azure VPN](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Chyba: "funkce zrušení se nepodařilo zkontrolovat zrušení, protože server ověřování řetězu je offline. (Chyba 0x80092013)"

### <a name="causes"></a>Způsobí, že
Této chybě dochází, pokud klient nemůže získat http://crl3.digicert.com/ssca-sha2-g1.crl a http://crl4.digicert.com/ssca-sha2-g1.crl.  Kontrola odvolání vyžaduje přístup k těmto dvěma lokalitami.  Tento problém nastává obvykle na straně klienta, který má proxy server nakonfigurovaný. V některých prostředích Pokud požadavky nejsou přes proxy server, ji budou odepřeny na hraniční bráně Firewall.

### <a name="solution"></a>Řešení

Zkontrolujte nastavení proxy serveru, ujistěte se, že klient může získat http://crl3.digicert.com/ssca-sha2-g1.crl a http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Chyba klienta VPN: Připojení nebylo možné kvůli zásady nakonfigurované na serveru RAS/virtuální privátní sítě. (Chyba 812)

### <a name="cause"></a>Příčina

K této chybě dochází, pokud má nesprávné nastavení serveru RADIUS, který jste použili pro ověřování klientů VPN nebo Azure Gateway nemůže spojit se serverem Radius.

### <a name="solution"></a>Řešení

Ujistěte se, jestli je správně nakonfigurovaný server protokolu RADIUS. Další informace najdete v tématu [ověřování pomocí protokolu RADIUS integrovat s Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Chyba 405" při stahování kořenového certifikátu ze služby VPN Gateway

### <a name="cause"></a>Příčina

Kdyby byl nainstalován kořenový certifikát. Kořenový certifikát je nainstalovaný v klientovi sady **důvěryhodné certifikáty** ukládat.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Chyba klienta VPN: Připojení ke vzdálené nebyl provést, protože se nezdařil pokus o tunelových propojení VPN. (Chyba 800) 

### <a name="cause"></a>Příčina

Ovladač síťové rozhraní je zastaralé.

### <a name="solution"></a>Řešení

Aktualizace ovladačů síťové karty:

1. Klikněte na tlačítko **Start**, typ **Správce zařízení**a vyberte ho ze seznamu výsledků. Pokud se výzva k zadání hesla správce nebo k potvrzení, zadejte heslo nebo potvrďte akci.
2. V **síťové adaptéry** kategorií, najít síťové karty, kterou chcete aktualizovat.  
3. Dvakrát klikněte na název zařízení, vyberte **aktualizovat ovladač**vyberte **vyhledat automaticky aktualizovaný ovladač**.
4. Pokud Windows nenajde nové ovladače, můžete akci hledání na webu výrobce zařízení a postupujte podle pokynů v.
5. Restartujte počítač a zkuste připojení znovu.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Chyba: soubor chybě stahování, který není zadaný cílový identifikátor URI

### <a name="cause"></a>Příčina

Příčinou je nesprávná bránou je nakonfigurovaný typu.

### <a name="solution"></a>Řešení

Typ brány Azure VPN musí být VPN a musí být typ sítě VPN **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalační program balíčku sítě VPN se nedokončil

### <a name="cause"></a>Příčina

Tento problém může být způsobeno předchozí instalace klienta VPN. 

### <a name="solution"></a>Řešení

Odstranit staré soubory konfigurace klienta VPN z **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >** a znovu spusťte instalační program klienta VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient VPN přejde do režimu spánku nebo režimu spánku po nějaké době

### <a name="solution"></a>Řešení

Zkontrolujte režimu spánku a hibernace nastavení v počítači, který je spuštěný klient VPN na.
