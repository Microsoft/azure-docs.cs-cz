---
title: Správa individuálních senzorů
description: Naučte se spravovat jednotlivé senzory, včetně správy aktivačních souborů, provádění záloh a aktualizace samostatného senzoru.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/10/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b61bef89d53da280961b818425d11a6f81bbf5ea
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210235"
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

6. Uložte soubor.

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

- Senzory **připojené k cloudu**: senzor se nemůže připojit k Internetu. Ověřte konfiguraci senzoru sítě. Pokud se váš senzor potřebuje připojit prostřednictvím webového proxy serveru pro přístup k Internetu, ověřte, že je váš proxy server správně nakonfigurovaný na obrazovce **Konfigurace sítě senzorů** . Ověřte, že \* v bráně firewall nebo proxy serveru je povolený. Azure-Devices.NET:443. Pokud se zástupné znaky nepodporují nebo chcete mít větší kontrolu, měli byste v bráně firewall nebo proxy serveru otevřít plně kvalifikovaný název domény pro konkrétního Defenderu pro IoT Hub. Podrobnosti najdete v tématu [IoT Hub koncových bodů reference](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints).  

- **Pro senzory připojené k cloudu**: aktivační soubor je platný, ale Defender pro IoT ho zamítl. Pokud tento problém nemůžete vyřešit, můžete si stáhnout další aktivaci ze stránky **Správa senzorů** v Defenderu pro IoT Portal. Pokud to nepomůže, obraťte se na podpora Microsoftu.

## <a name="manage-certificates"></a>Správa certifikátů

Po instalaci senzoru se vygeneruje místní certifikát podepsaný svým držitelem, který se použije pro přístup k webové aplikaci snímače. Při prvním přihlášení ke senzoru se uživatelům zobrazí výzva k zadání certifikátu protokolu SSL/TLS.  Další informace o tom, jak nastavit první nastavení, najdete v tématu [přihlášení a aktivace snímače](how-to-activate-and-set-up-your-sensor.md).

Tento článek poskytuje informace o aktualizaci certifikátů, práci s příkazy certifikátu CLI a podporovaných certifikátech a parametrech certifikátů.

### <a name="about-certificates"></a>Informace o certifikátech

Azure Defender pro IoT používá certifikáty SSL/TLS k těmto účelům:

1. Nahrajte certifikát podepsaný certifikační autoritou a vyžádejte si konkrétní požadavky na certifikát a šifrování, které vaše organizace požaduje.

1. Umožňuje ověření mezi konzolou pro správu a připojenými senzory a mezi konzolou pro správu a konzolou pro správu s vysokou dostupností. Ověřování se vyhodnocuje na seznam odvolaných certifikátů a datum vypršení platnosti certifikátu. **Pokud se ověření nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření. Tato možnost je po instalaci povolena ve výchozím nastavení.**

 Pravidla předávání třetích stran, například informace o výstrahách odeslaných SYSLOG, Splunk nebo ServiceNow; nebo komunikace se službou Active Directory není ověřena.

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

- Infrastruktura privátního/podnikového klíče (privátní infrastruktura veřejných klíčů) 
- Infrastruktura veřejných klíčů (veřejná infrastruktura veřejných klíčů) 
- Místně generované na zařízení (místně podepsané svým držitelem). **Použití certifikátů podepsaných svým držitelem se nedoporučuje.** Toto připojení je *nezabezpečené* a mělo by se používat jenom pro testovací prostředí. Vlastníka certifikátu nelze ověřit a zabezpečení systému nelze udržovat. Certifikáty podepsané svým držitelem by se nikdy neměly používat pro produkční sítě.  

Podporovány jsou následující parametry. CRT certifikátu

- Primární soubor certifikátu pro název domény
- Signature – algoritmus = SHA256RSA
- Signatura algoritmu hash = SHA256
- Platné od = platné datum posledního
- Platný až = platné budoucí datum
- Veřejný klíč = RSA 2048bits (minimum) nebo 4096bits
- Distribuční bod seznamu CRL = adresa URL souboru. CRL
- Subject CN = URL může být certifikát se zástupnými znaky, např. example.contoso.com nebo  *. contoso.com**
- Subject (C) ountry = defined, např. US
- Subjekt (OU) organizační jednotka = definovaná, např. contoso Labs
- Subject (O) organizace = defined, např. contoso Inc.

Soubor klíče

- Soubor klíče generovaný při vytvoření CSR
- RSA 2048bits (minimum) nebo 4096bits

Řetěz certifikátů

- Soubor zprostředkujícího certifikátu (pokud existuje), který byl dodán vaší certifikační autoritou
- Certifikát certifikační autority, který vystavil certifikát serveru, by měl být nejprve v souboru, následovaný všemi ostatními až do kořenového adresáře. 
- Může obsahovat atributy kontejneru.

Hesel

- 1 podporovaný klíč
- Nastavení při importu certifikátu

Certifikáty s jinými parametry mohou fungovat, ale společnost Microsoft je nepodporuje.

#### <a name="encryption-key-artifacts"></a>Artefakty šifrovacího klíče

**. pem – soubor kontejneru certifikátů**

Název pochází z PEM (Privacy Enhanced mail), historické metody pro zabezpečení e-mailu, ale formátu kontejneru, který se používá, a jedná se o překlad Base64 pro klíče x509 ASN. 1.  

Definováno v části RFC 1421 až 1424: formát kontejneru, který může obsahovat jenom veřejný certifikát (například s instalacemi Apache, soubory certifikátů CA/etc/SSL/certs) nebo může zahrnovat celý řetěz certifikátů včetně veřejného klíče, privátního klíče a kořenových certifikátů.  

Může také kódovat CSR, protože formát PKCS10 lze přeložit na PEM.

**. CERT. cer. CRT – soubor kontejneru certifikátů**

Soubor s příponou. pem (nebo zřídka. der) s jinou příponou. Průzkumník Windows ho rozpozná jako certifikát. Průzkumník Windows nerozpoznal soubor. pem.

**. Key – soubor privátního klíče**

Soubor klíče je stejný "formát" jako soubor PEM, ale má jinou příponu.
##### <a name="use-cli-commands-to-deploy-certificates"></a>Nasazení certifikátů pomocí příkazů rozhraní příkazového řádku

K importu certifikátů použijte příkaz *CyberX-xsense-Certificate-import* CLI. Chcete-li tento nástroj použít, je třeba odeslat soubory certifikátů do zařízení (pomocí nástrojů, jako je WinSCP nebo wget).

Příkaz podporuje následující vstupní příznaky:

-h zobrazení syntaxe Help příkazového řádku

--Cesta CRT k souboru certifikátu (rozšíření CRT)

– klíč *. soubor klíče, délka klíče by měla být minimálně 2048 bitů.

--Cesta řetězu k souboru řetězení certifikátů (volitelné)

--pass – heslo použité k šifrování certifikátu (volitelné)

--přístupové heslo – nastaví výchozí nastavení = false, nepoužívá se. Nastavte na hodnotu TRUE, pokud chcete použít předchozí přístupové heslo dodávané s předchozím certifikátem (volitelné).

Při použití příkazu CLI:

- Ověřte, že jsou soubory certifikátů na zařízení čitelné.

- Ověřte, že se název domény a IP adresa v certifikátu shodují s konfigurací plánovanou IT oddělením.


## <a name="connect-a-sensor-to-the-management-console"></a>Připojit senzor ke konzole pro správu

Tato část popisuje, jak zajistit připojení mezi senzorem a místní konzolou pro správu. To je potřeba udělat, když pracujete v gapped síti a chcete odesílat informace o prostředcích a výstrahách do konzoly pro správu ze senzoru. Toto připojení také umožňuje, aby konzola pro správu nabízela nastavení systému snímači a prováděla další úlohy správy na senzoru.

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

1. Na portálu Azure Defender pro IoT Portal navštivte stránku **Správa senzorů** .

1. Odstraňte senzor z okna **pro správu senzorů** .

1. Zaregistrujte se s novým názvem.

1. Stáhnout a nový soubor aktivace.

1. Přihlaste se ke senzoru a nahrajte nový aktivační soubor.

## <a name="update-the-sensor-network-configuration"></a>Aktualizace konfigurace sítě senzorů

Konfigurace sítě snímače byla definovaná při instalaci senzoru. Můžete změnit parametry konfigurace. Můžete také nastavit konfiguraci proxy serveru.

Pokud vytvoříte novou IP adresu, možná se budete muset přihlásit znovu.

Změna konfigurace:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte **síť**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Nakonfigurujte nastavení sítě.":::

3. Parametry nastavte takto:

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

1. Přejděte na web [Azure Portal](https://portal.azure.com/).

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
