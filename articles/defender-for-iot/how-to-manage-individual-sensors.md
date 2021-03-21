---
title: Správa individuálních senzorů
description: Naučte se spravovat jednotlivé senzory, včetně správy aktivačních souborů, provádění záloh a aktualizace samostatného senzoru.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/02/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c8bb44d323574f6815aa570b271ed4c0df1fc6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526914"
---
# <a name="manage-individual-sensors"></a>Správa individuálních senzorů

Tento článek popisuje, jak spravovat jednotlivé senzory. Mezi úlohy patří Správa aktivačních souborů, provádění záloh a aktualizace samostatného senzoru.

Můžete také provádět určité úlohy správy senzorů z místní konzoly pro správu, kde je možné spravovat více senzorů současně.

Použijete Azure Portal k registraci senzoru a registraci.

## <a name="manage-sensor-activation-files"></a>Spravovat aktivační soubory senzorů

Váš senzor byl připojen pomocí Azure Defenderu pro IoT z Azure Portal. Jednotlivé snímače se připojily buď k místně připojenému senzoru, nebo ke senzoru připojenému ke cloudu.

Do každého snímače, který nasadíte, se nahraje jedinečný aktivační soubor. Další informace o tom, kdy a jak používat nový soubor, najdete v tématu [nahrání nových aktivačních souborů](#upload-new-activation-files). Pokud soubor nemůžete nahrát, přečtěte si téma [řešení potíží s aktivací souboru pro odeslání](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>O aktivačních souborech pro místně připojené senzory

Místně připojené senzory jsou přidružené k předplatnému Azure. Aktivační soubor pro vaše místně připojené senzory obsahuje datum vypršení platnosti. Jeden měsíc před tímto datem se v horní části konzole senzorů zobrazí varovná zpráva. Upozornění zůstane až po aktualizaci aktivačního souboru.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Snímek obrazovky s nastavením systému":::

V programu Defender pro funkce IoT můžete pokračovat v práci i v případě, že vypršela platnost aktivačního souboru. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>O aktivačních souborech pro senzory připojené ke cloudu

Senzory, které jsou připojeny ke cloudu, jsou přidruženy k programu Defender pro IoT Hub. Tyto senzory nejsou omezeny časovými obdobími pro aktivační soubor. Aktivační soubor pro senzory připojené k cloudu se používá k zajištění připojení k programu Defender pro IoT Hub.

### <a name="upload-new-activation-files"></a>Nahrání nových aktivačních souborů

Je možné, že budete muset nahrát nový aktivační soubor pro integrovaný senzor v těchto případech:

- Aktivační soubor vyprší na místně připojeném senzoru. 

- Chcete pracovat v jiném režimu správy senzorů. 

- Chcete přidružit nový Defender pro IoT Hub ke senzoru připojenému ke cloudu.

Přidání nového aktivačního souboru:

1. Přejít na stránku **správy senzorů** .

2. Vyberte senzor, pro který chcete odeslat nový aktivační soubor.

3. Odstraňte ji.

4. Znovu zaveďte senzor ze stránky **připojování** v novém režimu nebo pomocí nového programu Defender pro IoT Hub.

5. Stáhněte si aktivační soubor ze stránky **soubor pro aktivaci stahování** .

6. Soubor uložte.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Stáhněte si aktivační soubor z programu Defender pro IoT Hub.":::

7. Přihlaste se ke konzole služby Defender pro IoT snímač.

8. V konzole senzorů vyberte možnost   >  **Opětovná aktivace** nastavení systému.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Výběr opětovné aktivace na obrazovce nastavení systému.":::

9. Vyberte **nahrát** a vyberte soubor, který jste uložili.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Nahrajte soubor, který jste uložili.":::

10. Vyberte **aktivovat**.

### <a name="troubleshoot-activation-file-upload"></a>Řešení potíží s aktivací souboru

Pokud se aktivační soubor nepovedlo nahrát, zobrazí se chybová zpráva. Mohlo dojít k následujícím událostem:

- **Pro místně připojené senzory**: aktivační soubor není platný. Pokud soubor není platný, přejdete na portál Defender for IoT. Na stránce **Správa senzorů** vyberte senzor s neplatným souborem a stáhněte nový aktivační soubor.

- Senzory **připojené k cloudu**: senzor se nemůže připojit k Internetu. Ověřte konfiguraci senzoru sítě. Pokud se váš senzor potřebuje připojit prostřednictvím webového proxy serveru pro přístup k Internetu, ověřte, že je váš proxy server správně nakonfigurovaný na obrazovce **Konfigurace sítě senzorů** . Ověřte, že \* v bráně firewall nebo proxy serveru je povolený. Azure-Devices.NET:443. Pokud se zástupné znaky nepodporují nebo chcete mít větší kontrolu, měli byste v bráně firewall nebo proxy serveru otevřít plně kvalifikovaný název domény pro konkrétního Defenderu pro IoT Hub. Podrobnosti najdete v tématu [IoT Hub koncových bodů reference](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Pro senzory připojené k cloudu**: aktivační soubor je platný, ale Defender pro IoT ho zamítl. Pokud tento problém nemůžete vyřešit, můžete si stáhnout další aktivaci ze stránky lokality a senzory portálu Defender pro IoT Portal. Pokud to nepomůže, obraťte se na podpora Microsoftu.

## <a name="manage-certificates"></a>Správa certifikátů

Po instalaci senzoru se vygeneruje místní certifikát podepsaný svým držitelem, který se použije pro přístup k webové aplikaci snímače. Při prvním přihlášení ke senzoru se uživatelům zobrazí výzva k zadání certifikátu protokolu SSL/TLS.  Další informace o nastavení prvního času najdete v tématu [přihlášení a aktivace snímače](how-to-activate-and-set-up-your-sensor.md).

Tento článek poskytuje informace o aktualizaci certifikátů, práci s příkazy certifikátu CLI a podporovaných certifikátech a parametrech certifikátů.

### <a name="about-certificates"></a>Informace o certifikátech

Azure Defender pro IoT používá certifikáty SSL/TLS k těmto účelům:

- Nahrajte certifikát podepsaný certifikační autoritou a vyžádejte si konkrétní požadavky na certifikát a šifrování, které vaše organizace požaduje.

- Umožňuje ověření mezi konzolou pro správu a připojenými senzory a mezi konzolou pro správu a konzolou pro správu s vysokou dostupností. Ověřování se vyhodnocuje na seznam odvolaných certifikátů a datum vypršení platnosti certifikátu. *Pokud se ověření nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření*. Tato možnost je po instalaci povolena ve výchozím nastavení.

 Pravidla předávání třetích stran, například informace o výstrahách odeslaných SYSLOG, Splunk nebo ServiceNow; nebo komunikace se službou Active Directory není ověřena.

#### <a name="ssl-certificates"></a>Certifikáty SSL

Defender pro IoT snímač a místní Konzola pro správu používají SSL a certifikáty TLS pro tyto funkce: 

 - Zabezpečte komunikaci mezi uživateli a webovou konzolou zařízení. 
 
 - Zabezpečená komunikace s REST API na senzoru a místní konzole pro správu.
 
 - Zabezpečte komunikaci mezi senzory a místní konzolou pro správu. 

Po nainstalování zařízení vygeneruje místní certifikát podepsaný svým držitelem, který umožňuje předběžný přístup k webové konzole. Certifikáty Enterprise SSL a TLS se můžou nainstalovat pomocí [`cyberx-xsense-certificate-import`](#cli-commands) nástroje příkazového řádku.

 > [!NOTE]
 > Pro integrační a předávací pravidla, kde je zařízení klientem a iniciátorem relace, se používají konkrétní certifikáty a nevztahují se k systémovým certifikátům.  
 >
 >V těchto případech jsou certifikáty většinou přijímány ze serveru, nebo používají asymetrické šifrování, kde bude k dispozici konkrétní certifikát pro nastavení integrace.

Zařízení můžou používat jedinečné soubory certifikátů. Pokud potřebujete certifikát, který jste nahráli, nahradit;

- Z verze 10,0 se certifikát dá nahradit z nabídky nastavení systému.

- U verzí předcházejících 10,0 se certifikát SSL dá nahradit pomocí nástroje příkazového řádku.

### <a name="update-certificates"></a>Aktualizace certifikátů

Uživatelé Správce senzorů můžou aktualizovat certifikáty.

Aktualizace certifikátu:  

1. Vyberte **nastavení systému**.

1. Vyberte **certifikáty SSL/TLS.**
1. Odstraňte certifikát nebo ho upravte a přidejte nový.

    - Přidejte název certifikátu.
    
    - Nahrajte soubor CRT a soubor klíče a zadejte přístupové heslo.
    - V případě potřeby nahrajte soubor PEM.

Změna nastavení ověřování:

1. Povolí nebo zakáže přepínač **Povolit ověření certifikátu** .

1. Vyberte **Uložit**.

Pokud je tato možnost povolená a ověření se nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření.

### <a name="certificate-support"></a>Podpora certifikátů

Podporují se tyto certifikáty:

- Privátní a podniková infrastruktura klíčů (privátní infrastruktura veřejných klíčů)

- Infrastruktura veřejných klíčů (veřejná infrastruktura veřejných klíčů) 

- Místně generované na zařízení (místně podepsané svým držitelem). 

> [!IMPORTANT]
> Nedoporučujeme používat certifikáty podepsané svým držitelem. Tento typ připojení není zabezpečený a měl by se používat jenom pro testovací prostředí. Vzhledem k tomu, že vlastník certifikátu nelze ověřit a zabezpečení systému nelze udržovat, certifikáty podepsané svým držitelem by se nikdy neměly používat pro produkční sítě.

### <a name="supported-ssl-certificates"></a>Podporované certifikáty SSL 

Podporovány jsou následující parametry. 

**CRT certifikátu**

- Primární soubor certifikátu pro název domény

- Signature – algoritmus = SHA256RSA
- Signatura algoritmu hash = SHA256
- Platné od = platné datum posledního
- Platný až = platné budoucí datum
- Veřejný klíč = RSA 2048 bity (minimum) nebo 4096 bitů
- Distribuční bod seznamu CRL = adresa URL souboru. CRL
- Subject CN = URL může být certifikát se zástupnými znaky; například senzor. contoso. <span> com nebo *. contoso. <span> Doplňky
- Subject (C) ountry = defined, například US
- Subjekt (OU) organizační jednotka = definovaná, například contoso Labs
- Subject (O) organizace = defined, například Contoso Inc.

**Soubor klíče**

- Soubor klíče generovaný při vytvoření CSR

- RSA 2048 bity (minimální) nebo 4096 bitů.

 > [!Note]
 > Použití klíče s délkou 4096bits:
 > - Metoda handshake SSL na začátku každého připojení bude pomalejší.  
 > - Během ověřování metodou handshake dojde ke zvýšení využití procesoru. 

**Řetěz certifikátů**

- Soubor zprostředkujícího certifikátu (pokud existuje), který byl dodán vaší certifikační autoritou

- Certifikát certifikační autority, který vystavil certifikát serveru, by měl být nejprve v souboru, následovaný všemi ostatními až do kořenového adresáře. 
- Může obsahovat atributy kontejneru.

**Hesel**

- Jeden klíč je podporován.

- Nastavte při importu certifikátu.

Certifikáty s jinými parametry mohou fungovat, ale společnost Microsoft je nepodporuje.

#### <a name="encryption-key-artifacts"></a>Artefakty šifrovacího klíče

**. pem – soubor kontejneru certifikátů**

Soubory PEM (Privacy Enhanced mail) byly obecný typ souboru používaný k zabezpečení e-mailů. Současné době, soubory PEM se používají s certifikáty a používají klíče ASN1 x509.  

Soubor kontejneru je definovaný v části RFC 1421 až 1424, což je formát kontejneru, který může obsahovat jenom veřejný certifikát. Například nainstaluje Apache, certifikát certifikační autority, soubory atd., SSL nebo certifikáty. To může zahrnovat celý řetěz certifikátů, včetně veřejného klíče, privátního klíče a kořenových certifikátů.  

Může také kódovat CSR jako formát PKCS10, který se dá přeložit na PEM.

**. CERT. cer. CRT – soubor kontejneru certifikátů**

`.pem`Nebo `.der` formátovaný soubor s jinou příponou. Tento soubor rozpozná Průzkumník Windows jako certifikát. `.pem`   Průzkumník Windows nerozpoznal soubor.

**. Key – soubor privátního klíče**

Soubor klíče má stejný formát jako soubor PEM, ale má jinou příponu.

#### <a name="additional-commonly-available-key-artifacts"></a>Další běžně dostupné klíčové artefakty

**. CSR – žádost o podepsání certifikátu**  

Tento soubor se používá k odesílání certifikačním autoritám. Skutečný formát je PKCS10, který je definován v dokumentu RFC 2986 a může obsahovat některé nebo všechny klíčové podrobnosti požadovaného certifikátu. Například předmět, organizace a stav. Je to veřejný klíč certifikátu, který podepisuje certifikační autorita, a při návratu obdrží certifikát.  

Vrácený certifikát je veřejný certifikát, který obsahuje veřejný klíč, ale ne privátní klíč. 

**. pkcs12. pfx. p12 – kontejner hesel**. 

Společnost Microsoft původně definovala RSA v standardech šifrování Public-Key (PKCS), a proto ji původně zvýšila společnost Microsoft a později byla odeslána jako RFC 7292.  

Tento formát kontejneru vyžaduje heslo, které obsahuje dvojice veřejného i privátního certifikátu. Na rozdíl od `.pem`   souborů je tento kontejner plně zašifrovaný.  

OpenSSL můžete použít k tomu, abyste ho zapnuli do `.pem`   souboru s veřejným i privátním klíčem: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – binární KÓDOVANÝ PEM**

Způsob, jak kódovat číslo ASN. 1 v binárním souboru, je `.pem`   souborem, který je pouze soubor kódovaný v kódování Base64 `.der` . 

OpenSSL může tyto soubory převést na `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Systém Windows bude tyto soubory rozpoznat jako soubory certifikátů. Ve výchozím nastavení bude systém Windows exportovat certifikáty jako `.der` formátované soubory s jinou příponou.  

**. CRL – seznam odvolaných certifikátů**.  
Certifikační autority tyto soubory vytváří jako způsob, jak zrušit autorizaci certifikátů před jejich vypršením platnosti.
 
##### <a name="cli-commands"></a>Příkazy rozhraní CLI

`cyberx-xsense-certificate-import`K importu certifikátů použijte příkaz CLI. Chcete-li tento nástroj použít, je třeba do zařízení odeslat soubory certifikátů pomocí nástrojů, jako je WinSCP nebo wget.

Příkaz podporuje následující vstupní příznaky:

- `-h`: Zobrazí syntaxi Help příkazového řádku.

- `--crt`: Cesta k souboru certifikátu (přípona CRT).

- `--key`:  \* . Key – soubor. Délka klíče by měla být minimálně 2 048 bitů.

- `--chain`: Cesta k souboru řetězu certifikátů (volitelné).

- `--pass`: Heslo použité k šifrování certifikátu (volitelné).

- `--passphrase-set`: Výchozí = `False` , Nepoužito. Nastavte na `True` použití předchozího hesla zadaného pro předchozí certifikát (volitelné).

Při použití příkazu CLI:

- Ověřte, jestli jsou na zařízení čitelné soubory certifikátů.

- Ověřte, že se název domény a IP adresa v certifikátu shodují s konfigurací, kterou IT oddělení plánuje.

### <a name="use-openssl-to-manage-certificates"></a>Použití OpenSSL ke správě certifikátů

Spravujte certifikáty pomocí následujících příkazů:

| Description | CLI – příkaz |
|--|--|
| Vygenerovat nový privátní klíč a žádost o podepsání certifikátu | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Vygenerování certifikátu podepsaného svým držitelem | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Vygenerovat žádost o podepsání certifikátu (CSR) pro existující privátní klíč | `openssl req -out CSR.csr -key privateKey.key -new` |
| Vygenerovat žádost o podepsání certifikátu na základě existujícího certifikátu | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Odebrání přístupového hesla z privátního klíče | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Pokud potřebujete ověřit informace v rámci certifikátu, CSR nebo privátního klíče, použijte tyto příkazy;

| Description | CLI – příkaz |
|--|--|
| Ověření žádosti o podepsání certifikátu (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Ověřit privátní klíč | `openssl rsa -in privateKey.key -check` |
| Ověřit certifikát | `openssl x509 -in certificate.crt -text -noout`  |

Pokud se zobrazí chyba, že se soukromý klíč neshoduje s certifikátem, nebo pokud certifikát, který jste nainstalovali do lokality, není důvěryhodný, použijte k opravě chyby tyto příkazy.

| Description | CLI – příkaz |
|--|--|
| Zkontrolujte hodnotu hash MD5 veřejného klíče a ujistěte se, že se shoduje s tím, co je v rámci zástupce nebo privátního klíče. | první. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> odst. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 1. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Chcete-li převést certifikáty a klíče do různých formátů, aby byly kompatibilní s konkrétními typy serverů nebo softwaru, použijte tyto příkazy;

| Description | CLI – příkaz |
|--|--|
| Převod souboru DER (. CRT. cer. der) na PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Převod souboru PEM na DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Převod souboru PKCS # 12 (. pfx. P12), který obsahuje privátní klíč a certifikáty, na PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Můžete přidat `-nocerts` pouze do výstupu privátního klíče nebo přidat `-nokeys` pouze do výstupu certifikátů. |
| Převod souboru certifikátu PEM a soukromého klíče na PKCS # 12 (. pfx. P12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Připojit senzor ke konzole pro správu

Tato část popisuje, jak zajistit připojení mezi senzorem a místní konzolou pro správu. To je potřeba udělat, když pracujete v gapped síti a chcete odesílat informace o zařízení a výstrahách do konzoly pro správu ze senzoru. Toto připojení také umožňuje, aby konzola pro správu nabízela nastavení systému snímači a prováděla další úlohy správy na senzoru.

Pro připojení:

1. Přihlaste se k místní konzole pro správu.

2. Vyberte **nastavení systému**.

3. V části **Nastavení senzoru – připojovací řetězec** zkopírujte automaticky generovaný připojovací řetězec.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Zkopírujte připojovací řetězec z této obrazovky."::: 

4. Přihlaste se ke konzole senzorů.

5. V levém podokně vyberte **nastavení systému**.

6. Vyberte **připojení konzoly pro správu**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Snímek obrazovky dialogového okna připojení konzoly pro správu":::

7. Vložte připojovací řetězec do pole **připojovací řetězec** a vyberte **připojit**.

8. V místní konzole pro správu, v okně **Správa lokality** , přiřaďte senzor k zóně.

## <a name="change-the-name-of-a-sensor"></a>Změna názvu senzoru

Můžete změnit název vaší konzole senzorů. Nový název se zobrazí ve webovém prohlížeči konzoly, v různých oknech konzoly a v protokolech řešení potíží.

Proces změny názvů senzorů se liší u místně propojených senzorů a senzorů připojených k cloudu. Výchozí název je **senzor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Změna názvu místně připojeného senzoru

Změna názvu:

1. V dolní části levého podokna konzoly vyberte aktuální popisek senzoru.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Snímek obrazovky, který zobrazuje popisek snímače.":::

1. V dialogovém okně **Upravit název senzoru** zadejte název.

1. Vyberte **Uložit**. Použije se nový název.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Změna názvu senzoru připojeného ke cloudu

Pokud byl senzor zaregistrován jako senzor připojený k cloudu, název snímače je definován názvem přiřazeným během registrace. Název je obsažen v aktivačním souboru, který jste nahráli při prvním přihlášení. Chcete-li změnit název snímače, je třeba nahrát nový aktivační soubor.

Změna názvu:

1. Na portálu Azure Defender pro IoT Portal navštivte stránku lokality a senzory.

1. Odstraňte senzor ze stránky weby a senzory.

1. Zaregistrujte se novým názvem výběrem možnosti připojit **senzor** ze stránky Začínáme.

1. Stáhněte si nový aktivační soubor.

1. Přihlaste se ke konzole služby Defender pro IoT snímač.

1. V konzole senzorů vyberte **nastavení systému** a pak vyberte **Opětovná aktivace**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Nahrajte aktivační soubor a znovu aktivujte senzor.":::

1. Vyberte **nahrát** a vyberte soubor, který jste uložili.

1. Vyberte **aktivovat**.

## <a name="update-the-sensor-network-configuration"></a>Aktualizace konfigurace sítě senzorů

Konfigurace sítě snímače byla definovaná při instalaci senzoru. Můžete změnit parametry konfigurace. Můžete také nastavit konfiguraci proxy serveru.

Pokud vytvoříte novou IP adresu, možná se budete muset přihlásit znovu.

Změna konfigurace:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte **síť**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Nakonfigurujte nastavení sítě.":::

3. Nastavte parametry:

    | Parametr | Popis |
    |--|--|
    | IP adresa | IP adresa snímače |
    | Maska podsítě | Adresa masky |
    | Výchozí brána | Adresa výchozí brány |
    | DNS | Adresa serveru DNS |
    | Název hostitele | Název hostitele snímače |
    | Proxy server | Hostitel proxy a název portu |

4. Vyberte **Uložit**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synchronizovat časová pásma na senzoru

Čas a oblast senzoru můžete nakonfigurovat tak, aby všichni uživatelé viděli stejnou dobu a oblast.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Nakonfigurujte čas a oblast.":::

| Parametr | Popis |
|--|--|
| Časové pásmo | Definice časového pásma pro:<br />– Výstrahy<br />– Trendy a statistiky pomůcky<br />– Sestavy dolování dat<br />   -Sestavy posouzení rizik<br />– Vektory útoku |
| Formát data | Vyberte jednu z následujících možností formátu:<br />-DD/MM/RRRR HH: mm: SS<br />-MM/DD/RRRR HH: mm: SS<br />-RRRR/MM/DD HH: mm: SS |
| Datum a čas | Zobrazí aktuální datum a místní čas ve vybraném formátu.<br />Pokud je vaše skutečné umístění například America a Praha, ale časové pásmo je nastavené na Evropa a Berlín, zobrazí se čas v závislosti na místním čase v oblasti Berlín. |

Postup konfigurace času senzoru:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte možnost **čas & oblast**.

3. Nastavte parametry a vyberte **Uložit**.

## <a name="set-up-backup-and-restore-files"></a>Nastavení zálohování a obnovení souborů

Zálohování systému se provádí automaticky v 3:00 denně. Data se ukládají na jiný disk ve snímači. Výchozí umístění je `/var/cyberx/backups` .

Tento soubor můžete automaticky přenést do interní sítě.

> [!NOTE]
> - Postup zálohování a obnovení lze provést pouze mezi stejnými verzemi.
> - V některých architekturách je zálohování zakázané. Můžete ho povolit v `/var/cyberx/properties/backup.properties` souboru.

Pokud ovládáte senzor pomocí místní konzoly pro správu, můžete použít plán zálohování senzoru ke shromáždění těchto záloh a jejich uložení v konzole pro správu nebo na externím záložním serveru.

**Co je zálohovaná:** Konfigurace a data.

**Co není zálohováno:** PCAP soubory a protokoly. Můžete ručně zálohovat a obnovovat PCAPs a protokoly.

Záložní soubory senzorů se automaticky pojmenují v následujícím formátu: `<sensor name>-backup-version-<version>-<date>.tar` . Příklad: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Konfigurace zálohování:

- Přihlaste se k účtu správce a zadejte `$ sudo cyberx-xsense-system-backup` .

Postup obnovení nejnovějšího záložního souboru:

- Přihlaste se k účtu správce a zadejte `$ sudo cyberx-xsense-system-restore` .

Uložení zálohy na externí server SMB:

1. Vytvořte sdílenou složku na externím serveru SMB.

    Získejte cestu ke složce, uživatelské jméno a heslo potřebné pro přístup k serveru SMB.

2. Ve snímači vytvořte adresář pro zálohování:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Upravit `fstab` :

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Upravit a vytvořit přihlašovací údaje pro sdílení serveru SMB:

    `sudo nano /etc/samba/user` 

5. Přidejte:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Připojte adresář:

    `sudo mount -a`

7. Nakonfigurujte záložní adresář do sdílené složky v programu Defender pro IoT snímač:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Obnovit senzory

Zálohy můžete obnovit z konzoly senzorů a pomocí rozhraní příkazového řádku.

**Postup obnovení z konzoly:**

- V okně **nastavení systému** senzorů vyberte **Obnovit obrázek** .

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Obnovte image výběrem tlačítka.":::

V konzole se zobrazí chyby obnovení.

**Obnovení pomocí rozhraní příkazového řádku:**

- Přihlaste se k účtu správce a zadejte `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Aktualizace verze samostatného senzoru

Následující postup popisuje aktualizaci samostatného senzoru pomocí konzoly senzorů. Proces aktualizace trvá přibližně 30 minut.

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Přejít na Defender pro IoT.

3. Přejít na stránku **aktualizace** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Snímek obrazovky se stránkou aktualizací v programu Defender pro IoT":::

4. V části **senzory** vyberte **Stáhnout** a soubor uložte.

5. Na bočním panelu konzoly senzorů vyberte **nastavení systému**.

6. V podokně **Upgrade verze** vyberte možnost **upgradovat**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Snímek obrazovky s podoknem upgrade":::

7. Vyberte soubor, který jste stáhli ze stránky **aktualizace** Defender pro IoT.

8. Spustí se proces aktualizace, během kterého se systém dvakrát restartuje. Po prvním restartování (před dokončením procesu aktualizace) se systém otevře s přihlašovacím oknem. Po přihlášení se verze upgradu objeví v levém dolním rohu bočního panelu.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Snímek obrazovky verze upgradu, která se zobrazí po přihlášení":::

## <a name="forward-sensor-failure-alerts"></a>Výstrahy selhání snímače pro dopředný senzor

Zasílání výstrah třetím stranám vám umožní poskytnout podrobnosti o těchto tématech:

- Odpojené senzory

- Selhání vzdálené zálohy

Tyto informace se odesílají, když vytvoříte předávací pravidlo pro systémová oznámení.

> [!NOTE]
> Správci mohou odesílat systémová oznámení.

Odeslání oznámení:

1. Přihlaste se k místní konzole pro správu.
1. V postranní nabídce vyberte **předat dál** .
1. Vytvořte pravidlo pro předávání.
1. Vyberte možnost **oznamovat systémová oznámení**.

Další informace o pravidlech předávání najdete v tématu [předávat informace o výstrahách](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Upravit vlastnosti systému

Vlastnosti systému řídí různé operace a nastavení ve snímači. Úpravy nebo úpravy můžou poškodit činnost konzoly senzorů.

Než změníte nastavení, Projděte si [Podpora Microsoftu](https://support.microsoft.com/) .

Přístup k vlastnostem systému:

1. Přihlaste se k místní konzole pro správu nebo senzoru.

2. Vyberte **nastavení systému**.

3. V části **Obecné** vyberte **Vlastnosti systému** .

## <a name="see-also"></a>Viz také

[Analýzy a balíčky pro analýzu hrozeb](how-to-work-with-threat-intelligence-packages.md)

[Správa senzorů z konzoly pro správu](how-to-manage-sensors-from-the-on-premises-management-console.md)