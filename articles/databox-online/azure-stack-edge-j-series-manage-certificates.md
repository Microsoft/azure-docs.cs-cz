---
title: Použití certifikátů s grafickým procesorem Azure Stack Edge pro | Microsoft Docs
description: V této části najdete popis použití certifikátů s GRAFICKÝm zařízením Azure Stack Edge pro, včetně toho, jaké typy a jak na zařízení nahrávat certifikáty.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: e5c8a496c60d3bba81040716c74bca7b5cb6095e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569424"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Použití certifikátů se zařízením Azure Stack Edge pro GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje typy certifikátů, které je možné nainstalovat na zařízení Azure Stack Edge pro. Článek obsahuje také podrobnosti o jednotlivých typech certifikátů spolu s postupem instalace a určení data vypršení platnosti. 

## <a name="about-certificates"></a>Informace o certifikátech

Certifikát poskytuje propojení mezi **veřejným klíčem** a entitou (jako je název domény), která byla **podepsána** (ověřena) důvěryhodnou třetí stranou (například **certifikační autorita**).  Certifikát nabízí pohodlný způsob distribuce důvěryhodných veřejných šifrovacích klíčů. Certifikáty zajistí, že vaše komunikace je důvěryhodná a že posíláte šifrované informace na správný server. 

Při počáteční konfiguraci zařízení Azure Stack Edge pro se automaticky vygenerují certifikáty podepsané svým držitelem. Volitelně můžete uvést vlastní certifikáty. Pokud plánujete zahrnout vlastní certifikáty, je třeba postupovat podle pokynů.

## <a name="types-of-certificates"></a>Typy certifikátů

Mezi různé typy certifikátů, které se používají na Azure Stack hraničních zařízeních pro, patří následující: 
- Podpisové certifikáty
    - Kořenová CA
    - Středně pokročilý

- Certifikáty koncového bodu
    - Certifikát uzlu
    - Místní certifikáty uživatelského rozhraní
    - Azure Resource Manager certifikátů
    - Certifikáty BLOB Storage
    - Certifikáty zařízení IoT
    <!--- WiFi certificates
    - VPN certificates-->

- Certifikáty šifrování
    - Podpora certifikátů relací

Každý z těchto certifikátů je podrobně popsaný v následujících oddílech.

## <a name="signing-chain-certificates"></a>Certifikáty podpisového řetězce

Jedná se o certifikáty pro autoritu, které podepisují certifikáty nebo podpisovou certifikační autoritu. 

### <a name="types"></a>Typy

Tyto certifikáty by mohly představovat kořenové certifikáty nebo zprostředkující certifikáty. Kořenové certifikáty jsou vždy podepsané svým držitelem (nebo samotné podepsané). Zprostředkující certifikáty nejsou podepsané svým držitelem a podepisují se podpisovou autoritou.

### <a name="caveats"></a>Upozornění

- Kořenové certifikáty by měly být podepisování certifikátů řetězu.
- Kořenové certifikáty je možné nahrát na vaše zařízení v následujícím formátu: 
    - **Der** – tyto soubory jsou k dispozici jako `.cer` Přípona souboru.
    - **Kódování Base-64ed** – jsou k dispozici jako `.cer` Přípona souboru.
    - **P7b** – tento formát se používá jenom pro certifikáty podpisového řetězce, které obsahují kořenové a zprostředkující certifikáty.
- Certifikáty podpisového řetězce se nahrají vždycky před nahráním jakýchkoli dalších certifikátů.


## <a name="node-certificates"></a>Certifikáty uzlů

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Všechny uzly v zařízení neustále vzájemně komunikují, a proto musí mít vztah důvěryhodnosti. Certifikáty uzlů poskytují způsob, jak vytvořit tento vztah důvěryhodnosti. Certifikáty uzlů také přicházejí do hry, pokud se připojujete k uzlu zařízení pomocí vzdálené relace PowerShellu přes protokol HTTPS.

### <a name="caveats"></a>Upozornění

- Certifikát uzlu by měl být uveden ve `.pfx` formátu s privátním klíčem, který lze exportovat. 
- Můžete vytvořit a nahrát 1 certifikát uzlu se zástupnými znaky nebo 4 certifikáty jednotlivých uzlů. 
- Certifikát uzlu se musí změnit, pokud se změní doména DNS, ale název zařízení se nezmění. Pokud přidáváte vlastní certifikát uzlu, nemůžete změnit sériové číslo zařízení, ale můžete změnit jenom název domény.
- Následující tabulku vám pomůže při vytváření certifikátu uzlu.
   
    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certifikáty koncového bodu

U všech koncových bodů, které zařízení zpřístupňuje, se vyžaduje certifikát pro důvěryhodnou komunikaci. Certifikáty koncového bodu zahrnují ty, které se vyžadují při přístupu k Azure Resource Manager a BLOB Storage prostřednictvím rozhraní REST API. 

Když přiřadíte podepsanému certifikátu vlastní, budete také potřebovat odpovídající podpisový řetězec certifikátu. Pro podpisový řetěz, Azure Resource Manager a certifikáty objektů BLOB v zařízení budete potřebovat odpovídající certifikáty na klientském počítači, aby bylo možné ověřit a komunikovat se zařízením.

### <a name="caveats"></a>Upozornění

- Certifikáty koncového bodu musí být ve `.pfx` formátu s privátním klíčem. Podpisový řetězec by měl být ve formátu DER ( `.cer` Přípona souboru). 
- Když přenesete vlastní certifikáty koncového bodu, může to být individuální certifikáty nebo více doménových certifikátů. 
- Pokud přidáváte do řetězce podepisování, je nutné před nahráním certifikátu koncového bodu odeslat certifikát podpisového řetězce.
- Tyto certifikáty musí být změněny, pokud se změní název zařízení nebo domény DNS.
- Můžete použít certifikát koncového bodu se zástupnými znaky.
- Vlastnosti certifikátů koncového bodu jsou podobné těm, které jsou typické pro certifikát SSL. 
- Při vytváření certifikátu koncového bodu použijte následující tabulku:

    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Jeden certifikát pro více sítí SAN pro oba koncové body|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Místní certifikáty uživatelského rozhraní

K místnímu webovému uživatelskému rozhraní vašeho zařízení můžete přistupovat přes prohlížeč. Aby bylo zajištěno zabezpečení této komunikace, můžete nahrát vlastní certifikát. 

### <a name="caveats"></a>Upozornění

- Místní certifikát uživatelského rozhraní se také nahraje ve `.pfx` formátu se soukromým klíčem, který se dá exportovat.
- Po nahrání místního certifikátu uživatelského rozhraní bude nutné restartovat prohlížeč a vymazat mezipaměť. Podívejte se na konkrétní pokyny pro váš prohlížeč.

    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Místní uživatelské rozhraní| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge certifikátů zařízení

Vaše zařízení Azure Stack Edge pro je také zařízení IoT s výpočetním prostředím, které je k němu připojené IoT Edge zařízení. Pro případnou zabezpečenou komunikaci mezi tímto zařízením IoT Edge a zařízeními pro příjem dat, která se k němu mohou připojit, můžete také odeslat IoT Edge certifikátů. 

Zařízení má certifikáty podepsané svým držitelem, které se dají použít, pokud chcete použít jenom výpočetní scénář se zařízením. Pokud je ale zařízení Azure Stack Edge pro připojené k zařízením pro příjem dat, budete muset uvést vlastní certifikáty.

Existují tři IoT Edge certifikátů, které je třeba nainstalovat, aby bylo možné tento vztah důvěryhodnosti povolit:

- **Kořenová certifikační autorita nebo certifikační autorita vlastníka**
- **Autorita pro certifikáty zařízení** 
- **Certifikát klíče zařízení**

### <a name="caveats"></a>Upozornění

- Certifikáty IoT Edge jsou nahrány ve `.pem` formátu. 


Další informace o IoT Edgech certifikátů najdete v tématu [Azure IoT Edge o certifikátech](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Podpora certifikátů relací

Pokud se v zařízení Azure Stack Edge pro vyskytují nějaké problémy, pak při řešení těchto problémů můžete na zařízení otevřít relaci podpory vzdáleného prostředí PowerShell. Pokud chcete v rámci této relace podpory povolit zabezpečenou šifrovanou komunikaci, můžete nahrát certifikát.

### <a name="caveats"></a>Upozornění

- Zajistěte, aby byl `.pfx` na klientském počítači nainstalován odpovídající certifikát s privátním klíčem pomocí nástroje pro dešifrování.
- Ověřte, že pole **použití klíče** pro certifikát není **podepsáno certifikátem**. Pokud to chcete ověřit, klikněte pravým tlačítkem na certifikát, vyberte **otevřít** a na kartě **Podrobnosti** Najděte **použití klíče**. 


### <a name="caveats"></a>Upozornění

- Certifikát relace podpory musí být zadaný jako Formát DER s `.cer` příponou.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Vytvořit certifikáty (volitelné)

Následující část popisuje postup vytvoření podpisového řetězce a certifikátů koncového bodu.

### <a name="certificate-workflow"></a>Pracovní postup certifikátu

Budete mít definovaný způsob, jak vytvořit certifikáty pro zařízení provozovaná ve vašem prostředí. Můžete použít certifikáty, které jste dostali od správce IT. 

**Pro účely vývoje nebo testování můžete k vytváření certifikátů v místním systému použít taky Windows PowerShell.** Při vytváření certifikátů pro klienta postupujte podle těchto pokynů:

1. Můžete vytvořit některý z následujících typů certifikátů:

    - Vytvořte jeden certifikát platný pro použití s jedním plně kvalifikovaným názvem domény (FQDN). Například *mydomain.com*.
    - Vytvořte také certifikát se zástupným znakem pro zabezpečení hlavního názvu domény a několika dílčích domén. Například **. mydomain.com*.
    - Vytvořte certifikát pro alternativní název subjektu (SAN), který bude pokrývat více názvů domén v jednom certifikátu. 

2. Pokud přidáváte vlastní certifikát, budete potřebovat kořenový certifikát pro podpisový řetězec. V tématu Postup [Vytvoření certifikátů Podepisování řetězů](#create-signing-chain-certificate).

3. Můžete dále vytvořit certifikáty koncového bodu pro místní uživatelské rozhraní zařízení, objekt BLOB a Azure Resource Manager. Pro zařízení, objekt BLOB a Azure Resource Manager můžete vytvořit 3 samostatné certifikáty, případně můžete vytvořit jeden certifikát pro všechny 3 koncové body. Podrobný postup najdete v tématu [Vytvoření podpisových a koncových certifikátů](#create-signed-endpoint-certificates).

4. Bez ohledu na to, jestli vytváříte 3 samostatné certifikáty nebo jeden certifikát, zadejte názvy subjektů (SN) a alternativní názvy subjektu (SAN) podle pokynů uvedených pro každý typ certifikátu. 

### <a name="create-signing-chain-certificate"></a>Vytvořit certifikát podpisového řetězu

Tyto certifikáty vytvoříte prostřednictvím prostředí Windows PowerShell spuštěného v režimu správce. **Certifikáty vytvořené tímto způsobem by měly být použity pouze pro účely vývoje nebo testování.**

Certifikát podpisového řetězce se musí vytvořit jenom jednou. Ostatní certifikáty koncového bodu budou odkazovat na tento certifikát pro podepisování.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Vytváření podepsaných certifikátů koncového bodu

Tyto certifikáty vytvoříte prostřednictvím prostředí Windows PowerShell spuštěného v režimu správce.

V těchto příkladech se pro zařízení vytvoří koncové body certifikátů s:
    - Název zařízení: `DBE-HWDC1T2`
    - Doména DNS: `microsoftdatabox.com`

Nahraďte názvem a doménou DNS pro vaše zařízení, abyste mohli vytvářet certifikáty pro vaše zařízení.
 
**Certifikát koncového bodu objektu BLOB**

Vytvořte certifikát pro koncový bod objektu BLOB v osobním úložišti.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager certifikát koncového bodu**

Vytvořte certifikát pro koncové body Azure Resource Manager v osobním úložišti.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certifikát místního webového uživatelského rozhraní pro zařízení**

Vytvořte certifikát pro místní webové uživatelské rozhraní zařízení v osobním úložišti.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Jeden certifikát pro více sítí SAN pro všechny koncové body**

Vytvořte jeden certifikát pro všechny koncové body v osobním úložišti.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Po vytvoření certifikátů je dalším krokem nahrání certifikátů na zařízení Azure Stack Edge pro.


## <a name="upload-certificates"></a>Nahrávání certifikátů 

Certifikáty, které jste ve výchozím nastavení vytvořili pro vaše zařízení, se nacházejí v **osobním úložišti** klienta. Tyto certifikáty je nutné exportovat v klientovi do odpovídajících souborů formátu, které je možné odeslat do zařízení.

1. Kořenový certifikát musí být exportován jako Formát DER s `.cer` příponou. Podrobný postup najdete v tématu [export certifikátů ve formátu der](#export-certificates-as-der-format).
2. Certifikáty koncového bodu musí být exportovány jako soubory *. pfx* s privátními klíči. Podrobný postup najdete v tématu [export certifikátů jako soubor *. pfx* s privátními klíči](#export-certificates-as-pfx-format-with-private-key). 
3. Kořenové a koncové certifikáty se pak nahrají na zařízení pomocí možnosti **+ Přidat certifikát** na stránce certifikáty v místním webovém uživatelském rozhraní. 

    1. Nejdřív nahrajte kořenové certifikáty. V místním webovém uživatelském rozhraní přejdete na **certifikáty > + přidat certifikát**.

        ![Přidat certifikát podpisového řetězce 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Následně nahrajte certifikáty koncového bodu. 

        ![Přidat certifikát podpisového řetězu 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Vyberte soubory certifikátů ve formátu *. pfx* a zadejte heslo, které jste zadali při exportu certifikátu. Použití certifikátu Azure Resource Manager může trvat několik minut.

        Pokud se podpisový řetězec nejdříve neaktualizuje a pokusíte se nahrát certifikáty koncového bodu, zobrazí se chyba.

        ![Chyba použití certifikátu](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Vraťte se zpátky a nahrajte certifikát podpisového řetězce a pak nahrajte a použijte certifikáty koncového bodu.

> [!IMPORTANT]
> Pokud se změní název zařízení nebo doména DNS, musí se vytvořit nové certifikáty. Certifikáty klienta a certifikáty zařízení by se měly aktualizovat pomocí nového názvu zařízení a domény DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Import certifikátů na klienta, který přistupuje k zařízení

Certifikáty, které jste vytvořili a nahráli do svého zařízení, musí být naimportovány do příslušného úložiště certifikátů na vašem klientovi Windows (přístup k zařízení).

1. Kořenový certifikát, který jste exportovali jako DER, by se teď měl importovat do **důvěryhodných kořenových certifikačních autorit** v klientském systému. Podrobný postup najdete v tématu [Import certifikátů do úložiště Důvěryhodné kořenové certifikační autority](#import-certificates-as-der-format).

2. Certifikáty koncového bodu, které jste exportovali jako, `.pfx` musí být exportovány jako der s `.cer` příponou. To `.cer` se pak naimportuje do **osobního úložiště certifikátů** ve vašem systému. Podrobný postup najdete v tématu [Import certifikátů do osobního úložiště certifikátů](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importovat certifikáty jako Formát DER

Pokud chcete importovat certifikáty na klienta Windows, proveďte následující kroky:

1. Klikněte na soubor pravým tlačítkem a vyberte **nainstalovat certifikát**. Tato akce spustí Průvodce importem certifikátu.

    ![Importovat certifikát 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. V poli **umístění úložiště**vyberte **místní počítač**a pak vyberte **Další**.

    ![Importovat certifikát 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**a pak vyberte **Procházet**. 

    - Pokud chcete importovat do osobního úložiště, přejděte do osobního úložiště svého vzdáleného hostitele a pak vyberte **Další**.

        ![Import certifikátu 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Pokud chcete importovat do důvěryhodného úložiště, přejděte do části Důvěryhodné kořenové certifikační autority a pak vyberte **Další**.

        ![Importovat certifikát 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Vyberte **Dokončit**. Zobrazí se zpráva o tom, že import proběhl úspěšně.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportovat certifikáty jako formát. pfx s privátním klíčem

Provedením následujících kroků exportujte certifikát SSL s privátním klíčem na počítači s Windows. 

> [!IMPORTANT]
> Proveďte tyto kroky ve stejném počítači, který jste použili k vytvoření certifikátu. 

1. Spusťte *Certlm. msc* a spusťte úložiště certifikátů místního počítače.

1. Dvakrát klikněte na složku **osobní** a pak na **certifikáty**.

    ![Export certifikátu 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Klikněte pravým tlačítkem na certifikát, který chcete zálohovat, a vyberte **všechny úlohy > exportovat...**

    ![Export certifikátu 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Postupujte podle pokynů Průvodce exportem certifikátu a zazálohujte certifikát do souboru. pfx.

    ![Exportovat certifikát 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Vyberte **Ano, exportovat privátní klíč**.

    ![Export certifikátu 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Pokud je to možné, vyberte možnost **Zahrnout všechny certifikáty do cesty certifikátu**, **exportovat všechny rozšířené vlastnosti** a **Povolit ochranu osobních údajů certifikátu**. 

    > [!IMPORTANT]
    > **Pokud export proběhl úspěšně, nevybírejte možnost Odstranit privátní klíč**.

    ![Export certifikátu 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Zadejte heslo, které si budete pamatovat. Potvrďte heslo. Heslo chrání privátní klíč.

    ![Exportovat certifikát 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Vyberte možnost Uložit soubor do umístění sady.

    ![Export certifikátu 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Vyberte **Dokončit**.

    ![Export certifikátu 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Zobrazí se zpráva o úspěšném exportu. Vyberte **OK**.

    ![Exportovat certifikát 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Záloha souboru. pfx se teď uloží do umístění, které jste vybrali a která je připravená k přesunutí nebo uložení pro zajištění bezpečného uchování.


### <a name="export-certificates-as-der-format"></a>Exportovat certifikáty jako Formát DER

1. Spusťte *Certlm. msc* a spusťte úložiště certifikátů místního počítače.

1. V osobním úložišti certifikátů vyberte kořenový certifikát. Klikněte pravým tlačítkem a vyberte **všechny úlohy > exportovat...**

    ![Exportovat certifikát DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Otevře se Průvodce certifikátem. Vyberte formát **binární X. 509 kódovaný jako der (CER)**. Vyberte **Další**.

    ![Exportovat certifikát DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Vyhledejte a vyberte umístění, kam chcete exportovat soubor formátu. cer.

    ![Exportovat certifikát DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)


4. Vyberte **Dokončit**.

    ![Exportovat certifikát DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Podporované algoritmy certifikátů

 U zařízení Azure Stack Edge pro se podporují jenom certifikáty Rivest – Shamir – Adleman (RSA). Pokud se používají certifikáty ECDSA (s algoritmem eliptické křivky digitálního podpisu), je chování zařízení neurčité.

 Certifikáty, které obsahují veřejný klíč RSA, se označují jako certifikáty RSA. Certifikáty, které obsahují veřejný klíč kryptografických rozhraní s eliptickou křivkou (ECC), jsou označovány jako ECDSA (s algoritmem eliptické křivky digitálního podpisu). 


## <a name="view-certificate-expiry"></a>Zobrazit vypršení platnosti certifikátu

Pokud přenesete do vlastních certifikátů, vyprší platnost certifikátů obvykle za 1 rok nebo 6 měsíců. Chcete-li zobrazit datum vypršení platnosti vašeho certifikátu, klikněte na stránce **certifikáty** v místním webovém uživatelském rozhraní vašeho zařízení. Pokud vyberete konkrétní certifikát, můžete si prohlédnout datum vypršení platnosti certifikátu.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates](azure-stack-edge-j-series-manage-certificates.md).-->

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md)
