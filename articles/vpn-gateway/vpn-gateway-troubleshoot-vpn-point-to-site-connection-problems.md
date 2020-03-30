---
title: Poradce při potížích s připojením azure point-to-site
titleSuffix: Azure VPN Gateway
description: Přečtěte si, jak řešit problémy s připojením z bodu na web.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297630"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Řešení potíží: Problémy s připojením z bodu na pracoviště Azure

Tento článek uvádí běžné problémy s připojením bodu k webu, ke kterým může dojít. Zabývá se také možnými příčinami a řešeními těchto problémů.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Chyba klienta VPN: Certifikát nebyl nalezen.

### <a name="symptom"></a>Příznak

Při pokusu o připojení k virtuální síti Azure pomocí klienta VPN se zobrazí následující chybová zpráva:

**Nebyl nalezen certifikát, který lze použít s tímto protokolem Extensible Authentication Protocol. (Chyba 798)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud klientský certifikát chybí **v certifikáty - aktuální uživatel\Osobní\Certifikáty**.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte takto:

1. Otevřít Správce certifikátů: Klepněte na tlačítko **Start**, zadejte **příkaz Spravovat certifikáty počítače**a ve výsledku hledání klepněte na příkaz Spravovat **certifikáty počítače.**

2. Ujistěte se, že následující certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuální uživatel\Osobní\Certifikáty |
    | AzureRoot.cer    | Místní počítač\Důvěryhodné kořenové certifikační úřady|

3. Přejděte na příkaz\<C:\Users UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>, ručně nainstalujte certifikát (soubor*.cer) do úložiště uživatele a počítače.

Další informace o instalaci klientského certifikátu naleznete v [tématu Generování a export certifikátů pro připojení typu point-to-site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Při importu klientského certifikátu nevybírejte možnost **Povolit ochranu silného soukromého klíče.**

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Nelze navázat síťové připojení mezi počítačem a serverem VPN, protože vzdálený server nereaguje

### <a name="symptom"></a>Příznak

Když se pokusíte připojit k bráně virtuální sítě Azure pomocí IKEv2 v systému Windows, zobrazí se následující chybová zpráva:

**Nelze navázat síťové připojení mezi počítačem a serverem VPN, protože vzdálený server nereaguje**

### <a name="cause"></a>Příčina
 
 K problému dochází, pokud verze systému Windows nemá podporu fragmentace ike
 
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

2. Nastavte hodnotu klíče registru. Vytvořte `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` nebo nastavte REG_DWORD klíč v registru na 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Chyba klienta VPN: Přijatá zpráva byla neočekávaná nebo špatně formátovaná

### <a name="symptom"></a>Příznak

Při pokusu o připojení k virtuální síti Azure pomocí klienta VPN se zobrazí následující chybová zpráva:

**Přijatá zpráva byla neočekávaná nebo špatně formátovaná. (Chyba 0x80090326)**

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud je splněna jedna z následujících podmínek:

- Použití uživatelem definovaných tras (UDR) s výchozí trasou v podsíti brány je nastaveno nesprávně.
- Veřejný klíč kořenového certifikátu se nenahraje do brány Azure VPN. 
- Klíč je poškozen nebo vypršela jeho platnost.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte takto:

1. Odeberte UDR v podsíti brány. Ujistěte se, že UDR předává veškerý provoz správně.
2. Zkontrolujte stav kořenového certifikátu na webu Azure Portal a zjistěte, jestli byl odvolán. Pokud není odvolán, zkuste odstranit kořenový certifikát a znovu nahrát. Další informace naleznete v [tématu Vytváření certifikátů](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Chyba klienta VPN: Řetězec certifikátů zpracován, ale ukončen 

### <a name="symptom"></a>Příznak 

Při pokusu o připojení k virtuální síti Azure pomocí klienta VPN se zobrazí následující chybová zpráva:

**Řetěz certifikátů byl zpracován, ale ukončen v kořenovém certifikátu, který poskytovatel vztahu důvěryhodnosti nedůvěřuje.**

### <a name="solution"></a>Řešení

1. Ujistěte se, že následující certifikáty jsou ve správném umístění:

    | Certifikát | Umístění |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuální uživatel\Osobní\Certifikáty |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuální uživatelské\důvěryhodné kořenové certifikační úřady|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Místní počítač\Důvěryhodné kořenové certifikační úřady|

2. Pokud certifikáty jsou již v umístění, pokuste se odstranit certifikáty a znovu je nainstalovat. Certifikát **azuregateway-*GUID*.cloudapp.net** je v konfiguračním balíčku klienta VPN, který jste stáhli z webu Azure Portal. Archivátory souborů můžete použít k extrahování souborů z balíčku.

## <a name="file-download-error-target-uri-is-not-specified"></a>Chyba stahování souboru: Není zadán cílový identifikátor URI.

### <a name="symptom"></a>Příznak

Zobrazí se následující chybová zpráva:

**Chyba stahování souboru. Cílová identifikátor URI není zadán.**

### <a name="cause"></a>Příčina 

K tomuto problému dochází z důvodu nesprávného typu brány. 

### <a name="solution"></a>Řešení

Typ brány VPN musí být **VPN**a typ VPN musí být **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Chyba klienta VPN: Vlastní skript Azure VPN se nezdařil 

### <a name="symptom"></a>Příznak

Při pokusu o připojení k virtuální síti Azure pomocí klienta VPN se zobrazí následující chybová zpráva:

**Vlastní skript (pro aktualizaci směrovací tabulky) se nezdařil. (Chyba 8007026f)**

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud se pokoušíte otevřít připojení VPN site-to-point pomocí zástupce.

### <a name="solution"></a>Řešení 

Otevřete balíček VPN přímo namísto jeho otevření ze zkratky.

## <a name="cannot-install-the-vpn-client"></a>Nelze nainstalovat klienta VPN.

### <a name="cause"></a>Příčina 

Další certifikát je nutné důvěřovat bráně VPN pro vaši virtuální síť. Certifikát je součástí balíčku konfigurace klienta VPN, který se generuje z portálu Azure.

### <a name="solution"></a>Řešení

Extrahujte konfigurační balíček klienta VPN a vyhledejte soubor CER. Chcete-li certifikát nainstalovat, postupujte takto:

1. Otevřete soubor mmc.exe.
2. Přidejte modul **snap-in Certifikáty.**
3. Vyberte účet **Počítače** pro místní počítač.
4. Klikněte pravým tlačítkem myši na uzel **Důvěryhodné kořenové certifikační autority.** Klikněte na**Import** **všech úloh** > a vyhledejte soubor CER, který jste extrahovali z konfiguračního balíčku klienta VPN.
5. Restartujte počítač. 
6. Pokuste se nainstalovat klienta VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Chyba na portálu Azure: Nepodařilo se uložit bránu VPN a data jsou neplatná.

### <a name="symptom"></a>Příznak

Při pokusu o uložení změn pro bránu VPN na webu Azure Portal se zobrazí následující chybová zpráva:

**Uložení &lt; *názvu*&gt;brány brány virtuální sítě se nezdařilo . Data pro &lt; *ID certifikátu* &gt; jsou neplatná.**

### <a name="cause"></a>Příčina 

K tomuto problému může dojít, pokud veřejný klíč kořenového certifikátu, který jste nahráli, obsahuje neplatný znak, například mezeru.

### <a name="solution"></a>Řešení

Ujistěte se, že data v certifikátu neobsahuje neplatné znaky, například konce řádků (konce řádků). Celá hodnota by měla být jeden dlouhý řádek. Následující text je ukázkou certifikátu:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Chyba na portálu Azure: Nepodařilo se uložit bránu VPN a název prostředku je neplatný.

### <a name="symptom"></a>Příznak

Při pokusu o uložení změn pro bránu VPN na webu Azure Portal se zobrazí následující chybová zpráva: 

**Uložení &lt; *názvu*&gt;brány brány virtuální sítě se nezdařilo . Název &lt; *certifikátu názvu prostředku, který se pokoušíte odeslat,* &gt; je neplatný**.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože název certifikátu obsahuje neplatný znak, například mezeru. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Chyba portálu Azure: Chyba stahování souboru balíčku VPN 503

### <a name="symptom"></a>Příznak

Při pokusu o stažení balíčku konfigurace klienta VPN se zobrazí následující chybová zpráva:

**Stažení souboru se nezdařilo. Podrobnosti o chybě: chyba 503. Server je zaneprázdněn.**
 
### <a name="solution"></a>Řešení

Tato chyba může být způsobena dočasným problémem se sítí. Zkuste stáhnout balíček VPN znovu po několika minutách.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Upgrade brány Azure VPN: Všichni klienti point to site se nemohou připojit

### <a name="cause"></a>Příčina

Pokud je certifikát více než 50 procent po celou dobu jeho životnosti, certifikát je vrácena.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, znovu stáhnout a znovu nasadit point to site balíček na všechny klienty.

## <a name="too-many-vpn-clients-connected-at-once"></a>Příliš mnoho klientů VPN připojených najednou

Je dosaženo maximálního počtu povolených připojení. Celkový počet připojených klientů najdete na webu Azure Portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient VPN nemá přístup ke sdíleným síťovým souborům

### <a name="symptom"></a>Příznak

Klient VPN se připojil k virtuální síti Azure. Klient však nemůže získat přístup ke sdíleným položkovým položky mj.

### <a name="cause"></a>Příčina

Protokol SMB se používá pro přístup ke sdílené složce. Po zahájení připojení klient VPN přidá pověření relace a dojde k selhání. Po navázání připojení je klient nucen použít pověření mezipaměti pro ověřování protokolem Kerberos. Tento proces iniciuje dotazy na Centrum distribuce klíčů (řadič domény) získat token. Vzhledem k tomu, že se klient připojuje z Internetu, nemusí být schopen dosáhnout řadiče domény. Klient proto nemůže přepojit převzetí služeb při selhání z protokolu Kerberos na NTLM. 

Klient je vyzván k zadání pověření pouze v případě, že má platný certifikát (s SAN = UPN) vydaný doménou, ke které je připojen. Klient musí být také fyzicky připojen k síti domény. V takovém případě se klient pokusí použít certifikát a natáhne se k řadiči domény. Potom Centrum distribuce klíčů vrátí chybu "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient je nucen převzetí služeb při selhání do NTLM. 

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, zakažte ukládání pověření domény do mezipaměti z následujícího podklíče registru: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Po přeinstalaci klienta VPN nelze v systému Windows najít připojení VPN bodu k webu.

### <a name="symptom"></a>Příznak

Odeberete připojení VPN z bodu na místo a potom přeinstalujte klienta VPN. V takovém případě není připojení VPN úspěšně nakonfigurováno. Připojení VPN se v systému Windows nezobrazí v nastavení **síťových připojení.**

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, odstraňte staré konfigurační soubory klienta VPN z **aplikace C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** a potom znovu spusťte instalační program klienta VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient VPN s bodem na lokalitu nemůže vyřešit hlavní název domény prostředků v místní doméně.

### <a name="symptom"></a>Příznak

Když se klient připojí k Azure pomocí připojení VPN bodu k webu, nemůže vyřešit hlavní název domény prostředků v místní doméně.

### <a name="cause"></a>Příčina

Klient VPN point-to-site používá servery Azure DNS, které jsou nakonfigurované ve virtuální síti Azure. Servery Azure DNS mají přednost před místními servery DNS, které jsou nakonfigurované v klientovi, takže všechny dotazy DNS se odešlou na servery Azure DNS. Pokud servery Azure DNS nemají záznamy pro místní prostředky, dotaz se nezdaří.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, ujistěte se, že servery Azure DNS, které se používají ve virtuální síti Azure můžete vyřešit záznamy DNS pro místní prostředky. Chcete-li to provést, můžete použít servery pro předávání DNS nebo podmíněné servery pro předávání. Další informace naleznete v tématu [Překlad názvů pomocí vlastního serveru DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Připojení VPN bodu k webu je navázáno, ale stále se nemůžete připojit k prostředkům Azure 

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud klient VPN nezíská trasy z brány Azure VPN.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, [resetujte bránu Azure VPN](vpn-gateway-resetgw-classic.md). Chcete-li se ujistit, že jsou používány nové trasy, musí být klienti VPN typu Point-to-Site znovu staženi po úspěšné konfiguraci partnerského vztahu virtuální sítě.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Chyba: Funkce odvolání nemohla zkontrolovat odvolání, protože server pro odvolání byl offline. (Chyba 0x80092013)"

### <a name="causes"></a>Příčiny
K této chybě dojde, pokud http://crl3.digicert.com/ssca-sha2-g1.crl http://crl4.digicert.com/ssca-sha2-g1.crlklient nemá přístup a .  Kontrola odvolání vyžaduje přístup k těmto dvěma webům.  K tomuto problému obvykle dochází na straně klienta, který má nakonfigurovaný proxy server. V některých prostředích, pokud požadavky neprocházejí proxy serverem, budou odmítnuty v bráně firewall Edge Firewall.

### <a name="solution"></a>Řešení

Zkontrolujte nastavení proxy serveru, ujistěte http://crl3.digicert.com/ssca-sha2-g1.crl se, že klient má přístup a http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Chyba klienta VPN: Připojení bylo zabráněno z důvodu zásady nakonfigurované na serveru RAS/VPN. (Chyba 812)

### <a name="cause"></a>Příčina

K této chybě dochází, pokud server RADIUS, který jste použili k ověřování klienta VPN, má nesprávné nastavení nebo služba Azure Gateway nedosáhne serveru Radius.

### <a name="solution"></a>Řešení

Zkontrolujte, zda je server RADIUS správně nakonfigurován. Další informace naleznete [v tématu Integrace ověřování RADIUS se serverem Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Chyba 405" při stahování kořenového certifikátu z brány VPN Gateway

### <a name="cause"></a>Příčina

Kořenový certifikát nebyl nainstalován. Kořenový certifikát je nainstalován v úložišti **důvěryhodných certifikátů** klienta.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Chyba klienta VPN: Vzdálené připojení nebylo provedeno, protože pokus o tunelové propojení VPN se nezdařil. (Chyba 800) 

### <a name="cause"></a>Příčina

Ovladač NIC je zastaralý.

### <a name="solution"></a>Řešení

Aktualizace ovladače nic:

1. Klepněte na tlačítko **Start**, zadejte **příkaz Správce zařízení**a vyberte jej ze seznamu výsledků. Pokud se zobrazí výzva k zadání hesla nebo potvrzení správce, zadejte heslo nebo poskytněte potvrzení.
2. V kategoriích **Síťové adaptéry** vyhledejte síťovou adaptér, kterou chcete aktualizovat.  
3. Poklepejte na název zařízení, vyberte **Aktualizovat ovladač**, **vyberte automaticky vyhledat aktualizovaný software ovladače**.
4. Pokud systém Windows nenajde nový ovladač, můžete ho zkusit vyhledat na webu výrobce zařízení a postupovat podle jeho pokynů.
5. Restartujte počítač a opakujte připojení.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Chyba klienta VPN: <VPN Connection Name>Vytáčení připojení VPN , Stav = Platforma VPN neaktivovala připojení

V prohlížeči událostí z rasclient se také může <User> zobrazit následující <VPN Connection Name> chyba: "Uživatel vytočil připojení s názvem, které selhalo. Kód chyby vrácené při selhání je 1460."

### <a name="cause"></a>Příčina

Klient Azure VPN nemá v nastavení aplikací pro Windows povolené oprávnění k aplikacím "Aplikace na pozadí".

### <a name="solution"></a>Řešení

1. Ve Windows přejděte na Nastavení -> aplikace pro ochranu osobních údajů >
2. Přepněte "Nechat aplikace běžet na pozadí" na Zapnuto

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Chyba: Cílová určová chyba stahování souborů Není zadána.

### <a name="cause"></a>Příčina

To je způsobeno nesprávným typem brány je nakonfigurován.

### <a name="solution"></a>Řešení

Typ brány Azure VPN musí být VPN a typ VPN musí být **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalační program balíčku VPN není dokončen

### <a name="cause"></a>Příčina

Tento problém může být způsoben předchozími instalacemi klienta VPN. 

### <a name="solution"></a>Řešení

Odstraňte staré konfigurační soubory klienta VPN z **c:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** a znovu spusťte instalační program klienta VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient VPN přezimuje nebo spí po určité době

### <a name="solution"></a>Řešení

Zkontrolujte nastavení režimu spánku a hibernace v počítači, ve které je klient VPN spuštěn.
