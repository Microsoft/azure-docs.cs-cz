---
title: Správa místní konzoly pro správu
description: Přečtěte si o možnostech místní konzoly pro správu, jako je zálohování a obnovení, definování názvu hostitele a nastavení proxy serveru na senzory.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 871c74eee4b74538a8a09188953916ff7376bc8d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781717"
---
# <a name="manage-the-on-premises-management-console"></a>Správa místní konzoly pro správu

Tento článek se zabývá místními možnostmi konzoly pro správu, jako je zálohování a obnovení, stahování souboru aktivace zařízení výboru, aktualizace certifikátů a nastavení proxy serveru na senzory.

Místní konzolu pro správu se připojujete z Azure Portal.

## <a name="upload-an-activation-file"></a>Nahrát aktivační soubor

Při prvním přihlášení se stáhne aktivační soubor pro místní konzolu pro správu. Tento soubor obsahuje agregovaná potvrzená zařízení, která jsou definována během procesu připojování. Seznam obsahuje senzory přidružené k několika předplatným.

Po počáteční aktivaci může počet monitorovaných zařízení překročit počet potvrzených zařízení, která jsou definovaná během připojování. K této události může dojít například v případě, že se k konzole pro správu připojíte více senzorů. Pokud dojde k nesouladu mezi počtem monitorovaných zařízení a počtem potvrzených zařízení, zobrazí se v konzole pro správu upozornění. Pokud k této události dojde, měli byste nahrát nový soubor aktivace.

Postup nahrání aktivačního souboru:

1. Přejít na stránku **ceny** Azure Defenderu pro IoT.
1. Vyberte možnost **Stáhnout aktivační soubor pro kartu Konzola pro správu** . Aktivační soubor se stáhne.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Stáhněte si aktivační soubor.":::

1. V konzole pro správu vyberte **nastavení systému** .
1. Vyberte **Aktivace**.
1. Vyberte možnost **zvolit soubor** a vyberte soubor, který jste uložili.

## <a name="manage-certificates"></a>Správa certifikátů

Po instalaci místní konzoly pro správu se vygeneruje místní certifikát podepsaný svým držitelem, který se použije pro přístup k webové aplikaci konzoly pro správu. Při prvním přihlášení uživatele správce do konzoly pro správu se zobrazí výzva k zadání certifikátu protokolu SSL/TLS. Další informace o nastavení prvního času najdete v tématu [Aktivace a nastavení místní konzoly pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md).

V následujících částech najdete informace o aktualizaci certifikátů, práci s příkazy certifikátu CLI a podporovaných certifikátech a parametrech certifikátů.

### <a name="about-certificates"></a>Informace o certifikátech

Azure Defender pro IoT používá certifikáty SSL a TLS k těmto akcím:

- Nahrajte certifikát podepsaný certifikační autoritou a vyžádejte si konkrétní požadavky na certifikát a šifrování, které vaše organizace požaduje.

- Umožňuje ověření mezi konzolou pro správu a připojenými senzory a mezi konzolou pro správu a konzolou pro správu s vysokou dostupností. Ověření se vyhodnocuje na základě seznamu odvolaných certifikátů a data vypršení platnosti certifikátu. *Pokud se ověření nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření*. Tato možnost je po instalaci povolena ve výchozím nastavení.

Pravidla předávání třetích stran nejsou ověřena. Příklady jsou informace o výstrahách odeslaných protokolu SYSLOG, Splunk nebo ServiceNow. a komunikace se službou Active Directory.

#### <a name="ssl-certificates"></a>Certifikáty SSL

Defender pro IoT snímač a místní Konzola pro správu používají SSL a certifikáty TLS pro tyto funkce: 

 - Zabezpečte komunikaci mezi uživateli a webovou konzolou zařízení. 
 
 - Zabezpečená komunikace s REST API na senzoru a místní konzole pro správu.
 
 - Zabezpečte komunikaci mezi senzory a místní konzolou pro správu. 

Po nainstalování zařízení vygeneruje místní certifikát podepsaný svým držitelem, který umožňuje předběžný přístup k webové konzole. Pomocí nástroje příkazového řádku je možné nainstalovat certifikáty Enterprise SSL a TLS [`cyberx-xsense-certificate-import`](#cli-commands) . 

 > [!NOTE]
 > Pro integrační a předávací pravidla, kde je zařízení klientem a iniciátorem relace, se používají konkrétní certifikáty a nevztahují se k systémovým certifikátům.  
 >
 >V těchto případech jsou certifikáty většinou přijímány ze serveru, nebo používají asymetrické šifrování, kde bude k dispozici konkrétní certifikát pro nastavení integrace. 

### <a name="update-certificates"></a>Aktualizace certifikátů

Správci místní konzoly pro správu mohou aktualizovat certifikáty.

Aktualizace certifikátu:  

1. Vyberte **nastavení systému**.

1. Vyberte **certifikáty SSL/TLS**.
1. Odstraňte certifikát nebo ho upravte a přidejte nový.
   
   - Přidejte název certifikátu.
   
   - Nahrajte soubor CRT a soubor klíče a zadejte přístupové heslo.
   - V případě potřeby nahrajte soubor PEM.

Změna nastavení ověřování:

1. Zapněte nebo vypněte přepínač **Povolit ověření certifikátu** .

1. Vyberte **Uložit**.

Pokud je možnost povolena a ověření se nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření.

### <a name="certificate-support"></a>Podpora certifikátů

Podporují se tyto certifikáty:

- Privátní a podniková infrastruktura klíčů (privátní infrastruktura veřejných klíčů)
 
- Infrastruktura veřejných klíčů (veřejná infrastruktura veřejných klíčů) 

- Místně generované na zařízení (místně podepsané svým držitelem) 

  > [!IMPORTANT]
  > Nedoporučujeme používat certifikáty podepsané svým držitelem. Tento typ připojení není zabezpečený a měl by se používat jenom pro testovací prostředí. Vzhledem k tomu, že vlastník certifikátu nelze ověřit a zabezpečení systému nelze udržovat, certifikáty podepsané svým držitelem by se nikdy neměly používat pro produkční sítě.

### <a name="supported-ssl-certificates"></a>Podporované certifikáty SSL 

Podporovány jsou následující parametry: 

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
- Subjekt (OU) organizační jednotka = definovaná; například contoso Labs
- Subject (O) organizace = Defined; například Contoso Inc

**Soubor klíče**

- Soubor klíče generovaný při vytváření CSR

- RSA 2048 bity (minimálně) nebo 4096 bitů

 > [!Note]
 > Použití klíče s délkou 4096bits:
 > - Metoda handshake SSL na začátku každého připojení bude pomalejší.  
 > - Během ověřování metodou handshake dojde ke zvýšení využití procesoru. 

**Řetěz certifikátů**

- Soubor zprostředkujícího certifikátu (pokud existuje), který byl dodán vaší certifikační autoritou.

- Certifikát certifikační autority, který vystavil certifikát serveru, by měl být nejprve v souboru, následovaný všemi ostatními až do kořenového adresáře. 
- Řetězec může obsahovat atributy kontejneru.

**Hesel**

- Podporuje se jeden klíč.

- Nastavte při importu certifikátu.

Certifikáty s jinými parametry mohou fungovat, ale společnost Microsoft je nepodporuje.

#### <a name="encryption-key-artifacts"></a>Artefakty šifrovacího klíče

**. pem: soubor kontejneru certifikátů**

Soubory PEM (Privacy Enhanced mail) byly obecný typ souboru používaný k zabezpečení e-mailů. Současné době, soubory PEM se používají s certifikáty a používají klíče ASN1 x509.  

Soubor kontejneru je definovaný v části RFC 1421 až 1424, což je formát kontejneru, který může obsahovat jenom veřejný certifikát. Například nainstaluje Apache, certifikát certifikační autority, soubory atd., SSL nebo certifikáty. To může zahrnovat celý řetěz certifikátů, včetně veřejného klíče, privátního klíče a kořenových certifikátů.  

Může také kódovat CSR jako formát PKCS10, který se dá přeložit na PEM.

**. CERT. cer. CRT: soubor kontejneru certifikátů**

`.pem`Nebo `.der` formátovaný soubor s jinou příponou. Tento soubor rozpozná Průzkumník Windows jako certifikát. `.pem`   Průzkumník Windows nerozpoznal soubor.

**. Key: soubor privátního klíče**

Soubor klíče má stejný formát jako soubor PEM, ale má jinou příponu. 

#### <a name="additional-commonly-available-key-artifacts"></a>Další běžně dostupné klíčové artefakty

**. CSR – žádost o podepsání certifikátu**  

Tento soubor se používá k odesílání certifikačním autoritám. Skutečný formát je PKCS10, který je definován v dokumentu RFC 2986 a může obsahovat některé nebo všechny klíčové podrobnosti požadovaného certifikátu. Například předmět, organizace a stav. Je to veřejný klíč certifikátu, který podepisuje certifikační autorita, a při návratu obdrží certifikát.  

Vrácený certifikát je veřejný certifikát, který obsahuje veřejný klíč, ale ne privátní klíč. 

**. pkcs12. pfx. p12 – kontejner hesel**. 

Společnost Microsoft původně definovala RSA v standardech šifrování Public-Key (PKCS), a proto ji původně zvýšila společnost Microsoft a později byla odeslána jako RFC 7292.  

Tento formát kontejneru vyžaduje heslo, které obsahuje dvojice veřejného i privátního certifikátu. Na rozdíl od `.pem`   souborů je tento kontejner plně zašifrovaný.  

Pomocí OpenSSL můžete soubor přepínat do `.pem`   souboru s veřejným i privátním klíčem: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – binární KÓDOVANÝ PEM**

Způsob, jak kódovat číslo ASN. 1 v binárním souboru, je `.pem`   souborem, který je pouze soubor kódovaný v kódování Base64 `.der` . 

OpenSSL může tyto soubory převést na `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Systém Windows bude tyto soubory rozpoznat jako soubory certifikátů. Ve výchozím nastavení bude systém Windows exportovat certifikáty jako `.der` formátované soubory s jinou příponou.

**. CRL – seznam odvolaných certifikátů**.  

Certifikační autority je poskytují jako způsob, jak zrušit autorizaci certifikátů před jejich vypršením platnosti. 

#### <a name="cli-commands"></a>Příkazy rozhraní CLI

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

| Popis | CLI – příkaz |
|--|--|
| Vygenerovat nový privátní klíč a žádost o podepsání certifikátu | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Vygenerování certifikátu podepsaného svým držitelem | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Vygenerovat žádost o podepsání certifikátu (CSR) pro existující privátní klíč | `openssl req -out CSR.csr -key privateKey.key -new` |
| Vygenerovat žádost o podepsání certifikátu na základě existujícího certifikátu | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Odebrání přístupového hesla z privátního klíče | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Pokud potřebujete ověřit informace v rámci certifikátu, CSR nebo privátního klíče, použijte tyto příkazy;

| Popis | CLI – příkaz |
|--|--|
| Ověření žádosti o podepsání certifikátu (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Ověřit privátní klíč | `openssl rsa -in privateKey.key -check` |
| Ověřit certifikát | `openssl x509 -in certificate.crt -text -noout`  |

Pokud se zobrazí chyba, že se soukromý klíč neshoduje s certifikátem, nebo pokud certifikát, který jste nainstalovali do lokality, není důvěryhodný, použijte k opravě chyby tyto příkazy.

| Popis | CLI – příkaz |
|--|--|
| Zkontrolujte hodnotu hash MD5 veřejného klíče a ujistěte se, že se shoduje s tím, co je v rámci zástupce nebo privátního klíče. | první. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> odst. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 1. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Chcete-li převést certifikáty a klíče do různých formátů, aby byly kompatibilní s konkrétními typy serverů nebo softwaru, použijte tyto příkazy;

| Popis | CLI – příkaz |
|--|--|
| Převod souboru DER (. CRT. cer. der) na PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Převod souboru PEM na DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Převod souboru PKCS # 12 (. pfx. P12), který obsahuje privátní klíč a certifikáty, na PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Můžete přidat `-nocerts` pouze do výstupu privátního klíče nebo přidat `-nokeys` pouze do výstupu certifikátů. |
| Převod souboru certifikátu PEM a soukromého klíče na PKCS # 12 (. pfx. P12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Definování nastavení zálohování a obnovení

Místní zálohování systému konzoly pro správu se provádí automaticky denně. Data se ukládají na jiný disk. Výchozí umístění je `/var/cyberx/backups` . 

Tento soubor můžete automaticky přenést do interní sítě. 

> [!NOTE]
> Postup zálohování a obnovení můžete provést jenom na stejné verzi. 

Zálohování místního počítače konzoly pro správu: 

- Přihlaste se k účtu správce a zadejte `sudo cyberx-management-backup -full` .

Postup obnovení nejnovějšího záložního souboru:

- Přihlaste se k účtu správce a zadejte `$ sudo cyberx-management-system-restore` .

Uložení zálohy na externí server SMB:

1. Vytvořte sdílenou složku na externím serveru SMB.  

   Získejte cestu ke složce, uživatelské jméno a heslo potřebné pro přístup k serveru SMB. 

2. V programu Defender pro IoT vytvořte adresář pro zálohování:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Upravit fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Upravit nebo vytvořit přihlašovací údaje pro server SMB ke sdílení: 

   - `sudo nano /etc/samba/user` 

5. Přidejte: 

   - `username=<user name>`

   - `password=<password>` 

6. Připojte adresář: 

   - `sudo mount -a` 

7. Nakonfigurujte záložní adresář na sdílenou složku v konzole Defender pro místní správu IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Upravit název hostitele

Postup úpravy názvu hostitele konzoly pro správu nakonfigurovaného na serveru DNS organizace:

1. V levém podokně konzoly pro správu vyberte **nastavení systému**.  

2. V části síť konzoly vyberte **síť**. 

3. Zadejte název hostitele nakonfigurovaný na serveru DNS organizace. 

4. Vyberte **Uložit**.

## <a name="define-vlan-names"></a>Definování názvů sítí VLAN

Názvy sítí VLAN nejsou synchronizovány mezi senzorem a konzolou pro správu. V součástech byste měli definovat stejné názvy.

V oblasti sítě vyberte **síť VLAN** a přidejte názvy zjištěných ID sítí VLAN. Pak vyberte **Uložit**.

## <a name="define-a-proxy-to-sensors"></a>Definice proxy serveru pro senzory

Zvyšte zabezpečení systému tím, že zabráníte tomu, aby se uživatel přihlásil přímo ke senzoru. Místo toho použijte tunelové propojení proxy serveru, které uživatelům umožní přístup ke senzoru z místní konzoly pro správu pomocí jediného pravidla brány firewall. Toto vylepšení omezí možnost neoprávněného přístupu k síťovému prostředí mimo senzor.

Použijte proxy v prostředích, kde není k dispozici žádné přímé připojení k senzorům.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Uživatelský.":::

Následující postup připojí senzor k místní konzole pro správu a povolí tunelování v této konzole:

1. Přihlaste se k místnímu zařízení CLI konzoly pro správu s přihlašovacími údaji správce.

2. Zadejte `sudo cyberx-management-tunnel-enable` a vyberte **ENTER**.

4. Zadejte `--port 10000` a vyberte **ENTER**.

## <a name="adjust-system-properties"></a>Upravit vlastnosti systému

Vlastnosti systému řídí různé operace a nastavení v konzole pro správu. Úpravou nebo úpravou může dojít k poškození operace konzoly pro správu. Před změnou nastavení se poraďte se [Podpora Microsoftu](https://support.microsoft.com) .

Přístup k vlastnostem systému: 

1. Přihlaste se k místní konzole pro správu nebo senzoru.

2. Vyberte **nastavení systému**.

3. V části **Obecné** vyberte **Vlastnosti systému** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>Změna názvu místní konzoly pro správu

Můžete změnit název místní konzoly pro správu. Nové názvy se zobrazí ve webovém prohlížeči v konzole, v různých oknech konzoly a v protokolech řešení potíží. Výchozí název je **Konzola pro správu**.

Změna názvu:

1. V dolní části levého podokna vyberte aktuální název.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Snímek obrazovky s verzí místní konzoly pro správu":::

2. V dialogovém okně **Upravit konfiguraci konzoly pro správu** zadejte nový název. Název nemůže být delší než 25 znaků.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Snímek obrazovky s úpravou konfigurace aplikace Defender pro IoT":::

3. Vyberte **Uložit**. Použije se nový název.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Snímek obrazovky, který zobrazuje změněný název konzoly.":::

## <a name="password-recovery"></a>Obnovení hesla

Obnovení hesla pro místní konzolu pro správu je vázané na předplatné, ke kterému je zařízení připojené. Heslo nemůžete obnovit, pokud si nejste jisti, ke kterému předplatnému je zařízení připojené.

Resetování hesla:

1. Přejít na přihlašovací stránku místní konzoly pro správu.
1. Vyberte **obnovení hesla**.
1. Zkopírujte jedinečný identifikátor.
1. Navštivte stránku Defender pro **weby a senzory** a vyberte kartu **obnovit heslo** .
1. Zadejte jedinečný identifikátor a vyberte **obnovit**. Aktivační soubor se stáhne.
1. Přejdete na stránku **pro obnovení hesla** a nahrajte aktivační soubor.
1. Vyberte **Další**.
 
   Teď jste dali uživatelské jméno a nové heslo generované systémem.

> [!NOTE]
> Senzor je propojený s předplatným, ke kterému byl původně připojený. Heslo můžete obnovit jenom pomocí stejného předplatného, ke kterému je připojené.

## <a name="update-the-software-version"></a>Aktualizace verze softwaru

Následující postup popisuje, jak aktualizovat místní verzi softwaru konzoly pro správu. Proces aktualizace trvá přibližně 30 minut.

1. Přejděte na [Azure Portal](https://portal.azure.com/).

1. Přejít na Defender pro IoT.

1. Přejít na stránku **aktualizace** .

1. V části místní konzole pro správu vyberte verzi.

1. Vyberte **Stáhnout** a uložte soubor.

1. Přihlaste se k místní konzole pro správu a v postranní nabídce vyberte **nastavení systému** .

1. V podokně **aktualizace verze** vyberte **aktualizovat**.

1. Vyberte soubor, který jste stáhli ze stránky **aktualizace** Defender pro IoT.

## <a name="mail-server-settings"></a>Nastavení poštovního serveru

Definujte nastavení poštovního serveru SMTP pro místní konzolu pro správu.

Pro definování:

1. Přihlaste se k rozhraní příkazového řádku pro místní správu s přihlašovacími údaji správce.
1. Zadejte ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Stiskněte klávesu ENTER. Zobrazí se následující výzvy.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Zadejte název serveru SMTP a odesílatele a vyberte zadat.

## <a name="see-also"></a>Viz také

[Správa senzorů z konzoly pro správu](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Správa individuálních senzorů](how-to-manage-individual-sensors.md)
