---
title: Přidání a správa certifikátů TLS/SSL
description: Vytvořte bezplatný certifikát, importujte certifikát služby App Service, importujte certifikát trezoru klíčů nebo si kupte certifikát služby App Service ve službě Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 9f9fcc0b3d8dfe19961668e77da91bc9f14ff2d1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453893"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Přidání certifikátu TLS/SSL ve službě Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto článku se ukazuje, jak vytvořit, nahrát nebo importovat soukromý certifikát nebo veřejný certifikát do služby App Service. 

Po přidání certifikátu do aplikace App Service nebo [aplikace funkce](https://docs.microsoft.com/azure/azure-functions/)můžete [zabezpečit vlastní název DNS s ním](configure-ssl-bindings.md) nebo jej použít v kódu [aplikace](configure-ssl-certificate-in-code.md).

V následující tabulce jsou uvedeny možnosti přidávání certifikátů ve službě App Service:

|Možnost|Popis|
|-|-|
| Vytvoření bezplatného certifikátu spravovaného službou App Service (preview) | Soukromý certifikát, který se snadno používá, pokud `www` potřebujete zabezpečit [vlastní doménu](app-service-web-tutorial-custom-domain.md) nebo jakoukoli doménu, která není nahá ve službě App Service. |
| Zakoupení certifikátu služby App Service | Soukromý certifikát, který spravuje Azure. Kombinuje jednoduchost automatizované správy certifikátů a flexibilitu možností obnovy a exportu. |
| Import certifikátu z trezoru klíčů | Užitečné, pokud ke správě [certifikátů PKCS12](https://wikipedia.org/wiki/PKCS_12)používáte [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) . Viz [Požadavky na soukromý certifikát](#private-certificate-requirements). |
| Nahrání soukromého certifikátu | Pokud již máte soukromý certifikát od jiného poskytovatele, můžete jej nahrát. Viz [Požadavky na soukromý certifikát](#private-certificate-requirements). |
| Nahrání veřejného certifikátu | Veřejné certifikáty se nepoužívají k zabezpečení vlastních domén, ale můžete je načíst do kódu, pokud je potřebujete pro přístup ke vzdáleným prostředkům. |

## <a name="prerequisites"></a>Požadavky

Postupujte podle tohoto návodu:

- [Vytvořte aplikaci služby App Service](/azure/app-service/).
- Pouze bezplatný certifikát: namapujte subdoménu `www.contoso.com`(například) na službu App Service se [záznamem CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Požadavky na soukromé certifikáty

> [!NOTE]
> Azure Web Apps **nepodporuje** AES256 a všechny pfx soubory by měly být šifrované pomocí TripleDES.

[Bezplatný certifikát spravovaný službou App Service](#create-a-free-certificate-preview) nebo certifikát [služby App Service](#import-an-app-service-certificate) již splňují požadavky služby App Service. Pokud se rozhodnete nahrát nebo importovat soukromý certifikát do služby App Service, musí váš certifikát splňovat následující požadavky:

* Exportováno jako [soubor PFX chráněný heslem](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Obsahuje privátní klíč dlouhý alespoň 2 048 bitů
* Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

Chcete-li zabezpečit vlastní doménu ve vazbě TLS, má certifikát další požadavky:

* Obsahuje [rozšířené použití klíče](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) pro ověřování serveru (OID = 1.3.6.1.5.5.7.3.1)
* Podepsaný důvěryhodnou certifikační autoritou

> [!NOTE]
> **Certifikáty ECC (elliptic curve cryptography)** můžou fungovat se službou App Service, ale tento článek se jimi nezabývá. Konkrétní pokyny k vytvoření certifikátů ECC vám sdělí vaše certifikační autorita.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Vytvoření bezplatného certifikátu (Náhled)

Bezplatný certifikát spravovaný službou App Service je řešení na klíč pro zabezpečení vlastního názvu DNS ve službě App Service. Jedná se o plně funkční certifikát TLS/SSL, který spravuje služba App Service a obnovuje se automaticky. Bezplatný certifikát je dodáván s následujícími omezeními:

- Nepodporuje certifikáty se zástupnými symboly.
- Nepodporuje nahé domény.
- Nelze exportovat.
- Nepodporuje záznamy DNS A.

> [!NOTE]
> Bezplatný certifikát vydává společnost DigiCert. U některých domén nejvyšší úrovně musíte explicitně povolit funkci vystavitele certifikátů vytvořením `0 issue digicert.com`záznamu domény [CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) s hodnotou: .
> 

Vytvoření bezplatného certifikátu spravovaného službou App Service:

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace vyberte >  **nastavení TLS/SSL****Private Key Certificates (.pfx)** > **Create App Service Managed Certificate**.

![Vytvoření bezplatného certifikátu ve službě App Service](./media/configure-ssl-certificate/create-free-cert.png)

Všechny domény, které nejsou nahé a které jsou správně namapovány na vaši aplikaci se záznamem CNAME, jsou uvedeny v dialogovém okně. Vyberte vlastní doménu, pro kterou chcete vytvořit bezplatný certifikát, a vyberte **Vytvořit**. Pro každou podporovanou vlastní doménu můžete vytvořit pouze jeden certifikát.

Po dokončení operace se zobrazí certifikát v seznamu **Certifikáty soukromého klíče.**

![Vytvoření dokončeného bezplatného certifikátu](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v [části Vytvořit vazbu](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Import certifikátu služby App Service

Pokud si zakoupíte certifikát služby App Service z Azure, Azure spravuje následující úkoly:

- Stará se o proces nákupu od GoDaddy.
- Provede ověření domény certifikátu.
- Udržuje certifikát v [azure key vault](../key-vault/general/overview.md).
- Spravuje obnovení certifikátu (viz [Obnovit certifikát](#renew-certificate)).
- Automaticky synchronizujte certifikát s importovanými kopiemi v aplikacích App Service.

Pokud chcete zakoupit certifikát služby App Service, přejděte na [úvodní objednávku certifikátu](#start-certificate-order).

Pokud už máte funkční certifikát služby App Service, můžete:

- [Importujte certifikát do služby App Service](#import-certificate-into-app-service).
- [Spravujte certifikát](#manage-app-service-certificates), například obnovte, obnovujte a exportujte jej.

### <a name="start-certificate-order"></a>Zahájit objednávku certifikátu

Spusťte objednávku certifikátu služby App Service na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stránce vytvoření certifikátu služby App Service</a>.

![Spuštění nákupu certifikátu služby App Service](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Ke konfiguraci certifikátu použijte následující tabulku. Jakmile budete hotovi, klikněte na **Vytvořit**.

| Nastavení | Popis |
|-|-|
| Název | Popisný název certifikátu služby App Service. |
| Jméno hostitele domény Naked | Zde zadejte kořenovou doménu. Vydaný certifikát zabezpečuje kořenovou doménu *i* subdoménu. `www` V vydaném certifikátu obsahuje pole Běžný název kořenovou doménu a `www` pole Alternativní název subjektu obsahuje doménu. Chcete-li zabezpečit pouze subdoménu, zadejte zde plně kvalifikovaný `mysubdomain.contoso.com`název domény subdomény (například ).|
| Předplatné | Předplatné, které bude obsahovat certifikát. |
| Skupina prostředků | Skupina prostředků, která bude obsahovat certifikát. Můžete použít novou skupinu prostředků nebo vybrat například stejnou skupinu prostředků jako aplikaci App Service. |
| Skladová položka certifikátu | Určuje typ certifikátu, který má být k vytvoření, zda je to standardní certifikát nebo [certifikát se zástupnými symboly](https://wikipedia.org/wiki/Wildcard_certificate). |
| Právní podmínky | Kliknutím potvrďte, že souhlasíte s právními podmínkami. Certifikáty jsou získány od GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Uložení v trezoru klíčů Azure

Po dokončení procesu nákupu certifikátu je třeba provést několik dalších kroků, než začnete tento certifikát používat. 

Vyberte certifikát na stránce [Certifikáty služby App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a klikněte na Krok **konfigurace certifikátu** > **1: Store**.

![Konfigurace úložiště úložiště úložiště klíčů certifikátu služby App Service](./media/configure-ssl-certificate/configure-key-vault.png)

[Trezor klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-overview) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Je to úložiště volby pro certifikáty služby App Service.

Na stránce **Stav úložiště trezoru klíčů** klikněte na Úložiště **trezoru klíčů** a vytvořte nový trezor nebo zvolte existující úschovnu. Pokud se rozhodnete vytvořit nový trezor, použijte následující tabulku, která vám pomůže nakonfigurovat úschovnu, a klepněte na tlačítko Vytvořit. Vytvořte nový trezor klíčů ve stejné skupině předplatného a prostředků jako aplikace App Service.

| Nastavení | Popis |
|-|-|
| Název | Jedinečný název, který se skládá pro alfanumerické znaky a pomlčky. |
| Skupina prostředků | Jako doporučení vyberte stejnou skupinu prostředků jako certifikát služby App Service. |
| Umístění | Vyberte stejné umístění jako aplikace App Service. |
| Cenová úroveň | Další informace naleznete v [tématu Azure Key Vault podrobnosti o cenách](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zásady přístupu| Definuje aplikace a povolený přístup k prostředkům trezoru. Můžete ji nakonfigurovat později podle kroků na [udělení přístupu několika aplikacím k trezoru klíčů](../key-vault/general/group-permissions-for-apps.md). |
| Přístup k virtuální síti | Omezte přístup trezoru k určitým virtuálním sítím Azure. Můžete ji nakonfigurovat později, podle kroků na [konfigurace brány firewall Azure Key Vault a virtuálních sítí](../key-vault/general/network-security.md) |

Po výběru trezoru zavřete stránku **Úložiště úložiště trezoru klíčů.** **Možnost Krok 1: Obchod** by měla zobrazovat zelenou značku zaškrtnutí pro úspěch. Ponechte stránku otevřenou pro další krok.

### <a name="verify-domain-ownership"></a>Ověření vlastnictví domény

Na stejné stránce **Konfigurace certifikátu,** kterou jste použili v posledním kroku, klikněte na **krok 2: Ověřit**.

![Ověření certifikátu domény pro službu App Service](./media/configure-ssl-certificate/verify-domain.png)

Vyberte **Ověření služby App Service**. Vzhledem k tomu, že jste již namapovali doménu na webovou aplikaci (viz [Požadavky),](#prerequisites)je již ověřená. Chcete-li dokončit tento krok, stačí klepnout na **tlačítko Ověřit.** Klepněte na tlačítko **Aktualizovat,** dokud se nezobrazí **zpráva Certifikát je ověřená doména.**

> [!NOTE]
> Podporovány jsou čtyři typy metod ověření domény: 
> 
> - **Služba App Service** – nejvhodnější možnost, když je doména již namapována na aplikaci služby App Service ve stejném předplatném. Využívá skutečnosti, že aplikace App Service už ověřila vlastnictví domény.
> - **Doména** – [ověřte doménu služby App Service, kterou jste zakoupili v Azure](manage-custom-dns-buy-domain.md). Azure automaticky přidá ověřovací txt záznam za vás a dokončí proces.
> - **Pošta** – Ověřte doménu odesláním e-mailu správci domény. Pokyny jsou uvedeny při výběru možnosti.
> - **Ručně** – Ověřte doménu pomocí stránky HTML (pouze**standardní** certifikát) nebo záznamu TXT DNS. Pokyny jsou uvedeny při výběru možnosti.

### <a name="import-certificate-into-app-service"></a>Import certifikátu do služby App Service

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace vyberte >  **nastavení TLS/SSL****Private Key Certificates (.pfx)** > Import App Service**Certificate**.

![Import certifikátu služby App Service ve službě App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Vyberte certifikát, který jste právě zakoupili, a vyberte **OK**.

Po dokončení operace se zobrazí certifikát v seznamu **Certifikáty soukromého klíče.**

![Import certifikátu služby App Service byl dokončen.](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v [části Vytvořit vazbu](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Import certifikátu z trezoru klíčů

Pokud ke správě certifikátů používáte Azure Key Vault, můžete importovat certifikát PKCS12 z trezoru klíčů do [služby](#private-certificate-requirements)App Service, pokud splňuje požadavky .

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace vyberte **možnost TLS/SSL nastavení** > **certifikátů soukromého klíče (.pfx)** > **import ovat certifikát trezoru klíčů**.

![Import certifikátu trezoru klíčů ve službě App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

K výběru certifikátu použijte následující tabulku.

| Nastavení | Popis |
|-|-|
| Předplatné | Předplatné, ke kterému patří trezor klíčů. |
| Key Vault | Trezor s certifikátem, který chcete importovat. |
| Certifikát | Vyberte ze seznamu certifikátů PKCS12 v úschovně. Všechny certifikáty PKCS12 v trezoru jsou uvedeny s jejich kryptografickými otisky, ale ne všechny jsou podporovány ve službě App Service. |

Po dokončení operace se zobrazí certifikát v seznamu **Certifikáty soukromého klíče.** Pokud se import nezdaří s chybou, certifikát nesplňuje [požadavky na službu App Service](#private-certificate-requirements).

![Import certifikátu trezoru klíčů byl dokončen.](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v [části Vytvořit vazbu](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Nahrání soukromého certifikátu

Jakmile získáte certifikát od poskytovatele certifikátu, postupujte podle pokynů v této části, aby byl připraven pro službu App Service.

### <a name="merge-intermediate-certificates"></a>Sloučení zprostředkujících certifikátů

Pokud jste v řetězu certifikátů od své certifikační autority obdrželi více certifikátů, musíte certifikáty sloučit v daném pořadí.

Pokud to chcete provést, otevřete všechny obdržené certifikáty v textovém editoru.

Vytvořte soubor _mergedcertificate.crt_ pro sloučený certifikát. V textovém editoru zkopírujte do tohoto souboru obsah jednotlivých certifikátů. Pořadí certifikátů by mělo odpovídat jejich pořadí v řetězu certifikátů, počínaje vaším certifikátem a konče kořenovým certifikátem. Soubor bude vypadat jako v následujícím příkladu:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Export certifikátu do formátu PFX

Exportujte sloučený certifikát TLS/SSL se soukromým klíčem, se kterým byla vygenerována vaše žádost o certifikát.

Pokud jste žádost o certifikát vygenerovali pomocí OpenSSL, vytvořili jste i soubor privátního klíče. Exportujte certifikát do formátu PFX spuštěním následujícího příkazu. Nahraďte zástupné symboly _ &lt;>souboru soukromého klíče_ a _ &lt;>souboru sloučených certifikátů_ cestami k soukromému klíči a sloučeným souborem certifikátu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po zobrazení výzvy definujte heslo pro export. Toto heslo použijete později při nahrávání certifikátu TLS/SSL do služby App Service.

Pokud jste k vygenerování žádosti o certifikát použili službu IIS nebo nástroj _Certreq.exe_, nainstalujte certifikát na místním počítači a pak [exportujte certifikát do formátu PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Nahrát certifikát do služby App Service

Nyní jste připraveni nahrát certifikát do služby App Service.

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace vyberte **možnost TLS/SSL nastavení** > **certifikátu soukromého klíče (.pfx)** > **Nahrát certifikát**.

![Nahrání soukromého certifikátu ve službě App Service](./media/configure-ssl-certificate/upload-private-cert.png)

V části **Soubor certifikátu PFX** vyberte svůj soubor PFX. Do pole **Heslo certifikátu** zadejte heslo, které jste vytvořili při exportování souboru PFX. Po dokončení klikněte na **nahrát**. 

Po dokončení operace se zobrazí certifikát v seznamu **Certifikáty soukromého klíče.**

![Dokončení certifikátu pro nahrávání](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v [části Vytvořit vazbu](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Nahrání veřejného certifikátu

Veřejné certifikáty jsou podporovány ve formátu *CER.* 

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu > aplikace klikněte na **Nastavení TLS/SSL****Veřejné certifikáty (.cer)** > **Nahrát certifikát veřejného klíče**.

Do **pole Název**zadejte název certifikátu. V **souboru certifikátu CER**vyberte soubor CER.

Klikněte na **Odeslat**.

![Nahrání veřejného certifikátu ve službě App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Po nahrání certifikátu zkopírujte kryptografický otisk certifikátu a [přečtěte si jeho přístup](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Správa certifikátů služby App Service

V této části se ukazuje, jak spravovat certifikát služby App Service zakoupený v [aplikaci Importovat certifikát služby App Service](#import-an-app-service-certificate).

- [Certifikát Rekey](#rekey-certificate)
- [Obnovit certifikát](#renew-certificate)
- [Export certifikátu](#export-certificate)
- [Odstranit certifikát](#delete-certificate)

### <a name="rekey-certificate"></a>Certifikát Rekey

Pokud se domníváte, že je ohrožen soukromý klíč certifikátu, můžete certifikát znovu zakódovat. Vyberte certifikát na stránce [Certifikáty služby App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a v levém navigačním panelu vyberte **Možnost Reklíč a synchronizace.**

Chcete-li zahájit proces, klepněte na tlačítko **Znovu.** Tento proces může trvat 1-10 minut.

![Opětovné vytvoření certifikátu služby App Service](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Opětovné vytvoření klíče certifikátu je přidruženo k novému certifikátu vydanému certifikační autoritou.

Po dokončení operace opětovného klíče klepněte na tlačítko **Synchronizovat**. Operace synchronizace automaticky aktualizuje vazby názvu hostitele pro certifikát ve službě App Service, aniž by došlo k prostojům vašich aplikací.

> [!NOTE]
> Pokud nekliknete na **Synchronizovat**, služba App Service automaticky synchronizuje váš certifikát do 48 hodin.

### <a name="renew-certificate"></a>Obnovit certifikát

Pokud chcete certifikát kdykoli zapnout, vyberte certifikát na stránce [Certifikáty služby App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a v levé navigaci klikněte na **Automaticky obnovit nastavení.** Ve výchozím nastavení mají certifikáty služby App Service dobu platnosti jeden rok.

Vyberte **Zapnuto** a klepněte na **tlačítko Uložit**. Certifikáty se mohou spustit automaticky obnovovat 60 dní před vypršením platnosti, pokud máte zapnuté automatické obnovení.

![Automatické obnovení certifikátu služby App Service](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Chcete-li certifikát místo toho ručně obnovit, klepněte na tlačítko **Ruční obnovení**. Můžete požádat o ruční obnovení certifikátu 60 dní před vypršením platnosti.

Po dokončení operace obnovení klepněte na tlačítko **Synchronizovat**. Operace synchronizace automaticky aktualizuje vazby názvu hostitele pro certifikát ve službě App Service, aniž by došlo k prostojům vašich aplikací.

> [!NOTE]
> Pokud nekliknete na **Synchronizovat**, služba App Service automaticky synchronizuje váš certifikát do 48 hodin.

### <a name="export-certificate"></a>Export certifikátu

Vzhledem k tomu, že certifikát služby App Service je [tajný klíč trezoru klíčů](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets), můžete exportovat jeho kopii PFX a použít ji pro jiné služby Azure nebo mimo Azure.

Chcete-li exportovat certifikát služby App Service jako soubor PFX, spusťte následující příkazy v [prostředí Cloud Shell](https://shell.azure.com). Můžete také spustit místně, pokud jste [nainstalovali Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Nahraďte zástupné symboly názvy, které jste použili při [vytváření certifikátu služby App Service](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Stažený soubor *appservicecertificate.pfx* je nezpracovaný soubor PKCS12, který obsahuje veřejné i soukromé certifikáty. V každé výzvě použijte prázdný řetězec pro heslo pro import a heslo PEM.

### <a name="delete-certificate"></a>Odstranit certifikát 

Odstranění certifikátu služby App Service je konečné a nevratné. Odstranění prostředku certifikátu služby App Service má za následek odvolání certifikátu. Jakákoli vazba ve službě App Service s tímto certifikátem se stane neplatnou. Chcete-li zabránit náhodnému odstranění, Azure umístí zámek na certifikát. Chcete-li odstranit certifikát služby App Service, musíte nejprve odebrat zámek odstranění na certifikátu.

Vyberte certifikát na stránce [Certifikáty služby App Service a](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) v levém navigačním panelu vyberte **Zámky.**

Najděte zámek na certifikátu s typem zámku **Odstranit**. Napravo od něj vyberte **Odstranit**.

![Odstranit zámek pro certifikát služby App Service](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nyní můžete odstranit certifikát služby App Service. V levém navigačním panelu vyberte **možnost Odstranit přehled** > **Delete**. V potvrzovacím dialogovém okně zadejte název certifikátu a vyberte **OK**.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Další zdroje informací

* [Zabezpečení vlastního názvu DNS pomocí vazby TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md)
* [Vynucení HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Použití certifikátu TLS/SSL ve vašem kódu ve službě Azure App Service](configure-ssl-certificate-in-code.md)
* [Časté otázky: Certifikáty služby App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
