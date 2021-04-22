---
title: Přidání a Správa certifikátů TLS/SSL
description: Vytvořte si bezplatný certifikát, importujte certifikát App Service, importujte certifikát Key Vault nebo si kupte certifikát App Service v Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1e05435f364cc30b351275439a04caff47c35512
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871790"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Přidání certifikátu TLS nebo SSL ve službě Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto článku se dozvíte, jak vytvořit, nahrát nebo importovat privátní certifikát nebo veřejný certifikát do App Service. 

Po přidání certifikátu do aplikace App Service aplikace nebo [aplikace Function App](../azure-functions/index.yml)můžete [ZABEZPEČIT vlastní název DNS](configure-ssl-bindings.md) nebo ho [použít ve svém kódu aplikace](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Certifikát nahraný do aplikace je uložený v jednotce nasazení, která je vázaná na kombinaci skupiny prostředků aplikace a oblasti (interně se nazývá *webový prostor*). Tím se certifikát zpřístupní ostatním aplikacím ve stejné kombinaci skupiny prostředků a oblasti. 

V následující tabulce jsou uvedeny možnosti pro přidávání certifikátů v App Service:

|Možnost|Popis|
|-|-|
| Vytvoření bezplatného App Service spravovaného certifikátu (Preview) | Soukromý certifikát, který je zdarma a který je snadno použitelný, pokud potřebujete zabezpečit [vlastní doménu](app-service-web-tutorial-custom-domain.md) v App Service. |
| Koupit certifikát App Service | Privátní certifikát, který spravuje Azure. Kombinuje jednoduchost automatizované správy certifikátů a flexibility možností obnovení a exportu. |
| Importovat certifikát z Key Vault | Užitečné v případě, že ke správě [certifikátů PKCS12](https://wikipedia.org/wiki/PKCS_12)používáte [Azure Key Vault](../key-vault/index.yml) . Viz [požadavky na privátní certifikát](#private-certificate-requirements). |
| Nahrání privátního certifikátu | Pokud už privátní certifikát máte od jiného poskytovatele, můžete ho nahrát. Viz [požadavky na privátní certifikát](#private-certificate-requirements). |
| Nahrajte veřejný certifikát. | Veřejné certifikáty se nepoužívají k zabezpečení vlastních domén, ale můžete je načíst do kódu, pokud je potřebujete pro přístup ke vzdáleným prostředkům. |

## <a name="prerequisites"></a>Požadavky

- [Vytvořte aplikaci App Service](./index.yml).
- U privátního certifikátu se ujistěte, že splňuje všechny [požadavky od App Service](#private-certificate-requirements).
- **Jenom bezplatný certifikát**:
    - Namapujte doménu, pro kterou chcete vyApp Service certifikát. Informace najdete v tématu [kurz: Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).
    - V případě kořenové domény (jako je contoso.com) se ujistěte, že vaše aplikace nemá nakonfigurovaná žádná [omezení IP adres](app-service-ip-restrictions.md) . Vytvoření certifikátu i jeho pravidelné obnovení pro kořenovou doménu závisí na vaší aplikaci, která je dostupná z Internetu.

## <a name="private-certificate-requirements"></a>Požadavky na privátní certifikát

[Bezplatný App Service spravovaný certifikát](#create-a-free-managed-certificate-preview) a [certifikát App Service](#import-an-app-service-certificate) již splňují požadavky App Service. Pokud se rozhodnete odeslat nebo importovat privátní certifikát do App Service, musí váš certifikát splňovat následující požadavky:

* Exportováno jako [soubor PFX chráněný heslem](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)zašifrovaný pomocí algoritmu Triple DES.
* Obsahuje privátní klíč dlouhý alespoň 2 048 bitů
* Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

K zabezpečení vlastní domény ve vazbě TLS má certifikát další požadavky:

* Obsahuje [rozšířené použití klíče](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) pro ověřování serveru (OID = 1.3.6.1.5.5.7.3.1).
* Podepsaný důvěryhodnou certifikační autoritou

> [!NOTE]
> **Certifikáty ECC (elliptic curve cryptography)** můžou fungovat se službou App Service, ale tento článek se jimi nezabývá. Konkrétní pokyny k vytvoření certifikátů ECC vám sdělí vaše certifikační autorita.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Vytvoření bezplatného spravovaného certifikátu (Preview)

> [!NOTE]
> Před vytvořením bezplatného spravovaného certifikátu ověřte, že jste [splnili požadavky](#prerequisites) na aplikaci.

Bezplatný App Service spravovaný certifikát představuje řešení pro zabezpečení vlastního názvu DNS v App Service. Jedná se o plně funkční certifikát TLS/SSL, který se spravuje App Service a automaticky se obnoví. Bezplatný certifikát přináší následující omezení:

- Nepodporuje certifikáty se zástupnými znaky.
- Nelze exportovat.
- Není podporován v App Service Environment (pomocného mechanismu).
- Není podporován u kořenových domén, které jsou integrovány s Traffic Manager.

> [!NOTE]
> Bezplatný certifikát vystaví DigiCert. U některých domén nejvyšší úrovně musíte explicitně dovolit DigiCert jako vystavitele certifikátu vytvořením [záznamu domény CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) s hodnotou: `0 issue digicert.com` .
> 

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace vyberte **Nastavení TLS/SSL**  >  **certifikáty privátních klíčů (. pfx)**  >  **vytvořit App Service spravovaný certifikát**.

![Vytvoření bezplatného certifikátu v App Service](./media/configure-ssl-certificate/create-free-cert.png)

Vyberte vlastní doménu, pro kterou chcete vytvořit bezplatný certifikát, a vyberte **vytvořit**. Pro každou podporovanou vlastní doménu můžete vytvořit jenom jeden certifikát.

Po dokončení operace se certifikát zobrazí v seznamu **certifikáty privátního klíče** .

![Vytvoření bezplatného certifikátu se dokončilo.](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v části [vytvoření vazby](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Import App Service Certificate

Pokud si koupíte App Service Certificate z Azure, Azure spravuje následující úlohy:

- Zabere se v potaz proces nákupu od GoDaddy.
- Provede ověření domény certifikátu.
- Udržuje certifikát v [Azure Key Vault](../key-vault/general/overview.md).
- Spravuje obnovování certifikátů (viz [obnovení certifikátu](#renew-certificate)).
- Synchronizujte certifikát automaticky pomocí importovaných kopií v aplikacích App Service.

Pokud si chcete koupit certifikát App Service, pokračujte na [začátek pořadí certifikátů](#start-certificate-order).

Pokud již máte funkční App Service certifikát, můžete:

- [Importujte certifikát do App Service](#import-certificate-into-app-service).
- [Spravujte certifikát](#manage-app-service-certificates), jako je například obnovení, opětovné vytvoření klíče a jeho export.
> [!NOTE]
> V současné době se certifikáty App Service v národních cloudech Azure nepodporují.

### <a name="start-certificate-order"></a>Spustit pořadí certifikátů

Na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stránce vytvoření App Service Certificate</a>spusťte App Service pořadí certifikátů.

![Spustit App Service nákup certifikátu](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Pomocí následující tabulky můžete nakonfigurovat certifikát. Jakmile budete hotovi, klikněte na **Vytvořit**.

| Nastavení | Popis |
|-|-|
| Název | Popisný název certifikátu App Service. |
| Název hostitele holé domény | Sem zadejte kořenovou doménu. Vydaný certifikát zabezpečuje kořenovou *doménu i* `www` subdoménu. V vystaveném certifikátu obsahuje pole běžný název kořenovou doménu a pole Alternativní název subjektu obsahuje `www` doménu. Chcete-li zabezpečit všechny subdomény, zadejte plně kvalifikovaný název domény (například `mysubdomain.contoso.com` ).|
| Předplatné | Odběr, který bude obsahovat certifikát. |
| Skupina prostředků | Skupina prostředků, která bude obsahovat certifikát. Můžete použít novou skupinu prostředků nebo vybrat stejnou skupinu prostředků jako aplikace App Service, například. |
| SKU certifikátu | Určuje typ certifikátu, který se má vytvořit, zda se jedná o standardní certifikát nebo [certifikát se zástupným znakem](https://wikipedia.org/wiki/Wildcard_certificate). |
| Právní výrazy | Kliknutím potvrďte, že souhlasíte s právními podmínkami. Certifikáty se získávají z GoDaddy. |

> [!NOTE]
> Certifikáty App Service zakoupené v Azure vystavuje GoDaddy. U některých domén nejvyšší úrovně musíte explicitně dovolit GoDaddy jako vystavitele certifikátu vytvořením [záznamu domény CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) s hodnotou: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Uložit v Azure Key Vault

Po dokončení procesu nákupu certifikátů je potřeba provést několik dalších kroků, než budete moct tento certifikát začít používat. 

Vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a pak klikněte na **Konfigurace certifikátu**  >  **Krok 1: Store**.

![Konfigurace Key Vaultho úložiště certifikátu App Service](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Je to úložiště, které je vhodné pro App Service certifikátů.

Na stránce **stav Key Vault** klikněte na **úložiště Key Vault** a vytvořte nový trezor nebo vyberte existující trezor. Pokud se rozhodnete vytvořit nový trezor, použijte následující tabulku, která vám pomůžete nakonfigurovat trezor a kliknout na vytvořit. Vytvoří novou Key Vault v rámci stejného předplatného a skupiny prostředků jako aplikace App Service.

| Nastavení | Popis |
|-|-|
| Název | Jedinečný název, který se skládá pro alfanumerické znaky a pomlčky. |
| Skupina prostředků | Jako doporučení vyberte stejnou skupinu prostředků jako certifikát App Service. |
| Umístění | Vyberte stejné umístění jako aplikace App Service. |
| Cenová úroveň | Informace najdete v tématu informace [o cenách Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zásady přístupu| Definuje aplikace a povolený přístup k prostředkům trezoru. Později ji můžete nakonfigurovat podle kroků v části [přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md). |
| Přístup k Virtual Network | Omezte přístup k trezoru na určité virtuální sítě Azure. Později ji můžete nakonfigurovat podle kroků v části [konfigurace Azure Key Vault brány firewall a virtuální sítě](../key-vault/general/network-security.md) . |

Po výběru trezoru zavřete stránku **Key Vault úložiště** . V části **Krok 1: úložiště** by se měla zobrazit zelená značka zaškrtnutí u možnosti úspěch. Nechejte stránku otevřenou pro další krok.

### <a name="verify-domain-ownership"></a>Ověření vlastnictví domény

Ze stejné stránky **Konfigurace certifikátu** , kterou jste použili v posledním kroku, klikněte na **Krok 2: ověření**.

![Ověřit doménu pro App Service certifikát](./media/configure-ssl-certificate/verify-domain.png)

Vyberte **ověřování App Service**. Vzhledem k tomu, že jste už namapovali doménu na webovou aplikaci (viz [požadavky](#prerequisites)), je už ověřená. Stačí kliknout na **ověřit** , aby se tento krok dokončil. Klikněte na tlačítko **aktualizovat** , dokud se nezobrazí **certifikát zprávy ověřená doména** .

> [!NOTE]
> Podporovány jsou čtyři typy metod ověřování domény: 
> 
> - **App Service** – nejpohodlnější možnost, pokud je již doména namapována na App Service aplikace ve stejném předplatném. Využívá skutečnost, že aplikace App Service již ověřila vlastnictví domény.
> - **Doména** – ověřte [App Service doménu, kterou jste si koupili v Azure](manage-custom-dns-buy-domain.md). Azure pro vás automaticky přidá záznam ověřovacího TXT a proces se dokončí.
> - **E-mail** – ověřte doménu odesláním e-mailu správci domény. Pokyny jsou k dispozici, když vyberete možnost.
> - **Ručně** – ověřte doménu pomocí stránky HTML (jenom **standardní** certifikát) nebo záznamu TXT DNS. Pokyny jsou k dispozici, když vyberete možnost.

### <a name="import-certificate-into-app-service"></a>Importovat certifikát do App Service

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace vyberte možnost **Nastavení TLS/SSL**  >  importovat **certifikát privátního klíče (. pfx)**  >  **App Service Certificate**.

![Importovat certifikát App Service v App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Vyberte certifikát, který jste právě koupili, a vyberte **OK**.

Po dokončení operace se certifikát zobrazí v seznamu **certifikáty privátního klíče** .

![Import certifikátu App Service byl dokončen.](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v části [vytvoření vazby](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importovat certifikát z Key Vault

Pokud používáte Azure Key Vault ke správě certifikátů, můžete importovat certifikát PKCS12 z Key Vault do App Service, pokud [splňuje požadavky](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autorizovat App Service ke čtení z trezoru
Ve výchozím nastavení poskytovatel prostředků App Service nemá přístup k Key Vault. Aby bylo možné použít Key Vault pro nasazení certifikátu, je nutné [autorizovat poskytovatele prostředků na přístup k trezoru](../key-vault/general/assign-access-policy-cli.md)klíčů. 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  je hlavní název služby poskytovatele prostředků pro App Service a je stejný pro všechna předplatná Azure. V případě Azure Government cloudového prostředí použijte `6a02c803-dafd-4136-b4c3-5a6f318b4714` místo toho jako hlavní název služby poskytovatele prostředků.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Import certifikátu z vašeho trezoru do aplikace

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace vyberte možnost **Nastavení TLS/SSL**  >  importovat **certifikáty privátního klíče (. pfx)**  >  **Key Vault certifikát**.

![Importovat certifikát Key Vault v App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Pomocí následující tabulky můžete vybrat certifikát.

| Nastavení | Popis |
|-|-|
| Předplatné | Předplatné, ke kterému Key Vault patří. |
| Key Vault | Trezor s certifikátem, který chcete naimportovat. |
| Certifikát | V trezoru vyberte ze seznamu PKCS12 certifikátů. Všechny certifikáty PKCS12 v trezoru jsou uvedené s jejich kryptografickými otisky, ale ne všechny jsou podporované v App Service. |

Po dokončení operace se certifikát zobrazí v seznamu **certifikáty privátního klíče** . Pokud import selhává s chybou, certifikát nesplňuje [požadavky pro App Service](#private-certificate-requirements).

![Import certifikátu Key Vault byl dokončen.](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Pokud certifikát aktualizujete v Key Vault pomocí nového certifikátu, App Service certifikát automaticky synchronizuje během 24 hodin.

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v části [vytvoření vazby](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Nahrání privátního certifikátu

Jakmile obdržíte certifikát od poskytovatele certifikátů, postupujte podle kroků v této části, aby byl připravený na App Service.

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

Exportujte sloučený certifikát TLS/SSL s privátním klíčem, se kterým se vygenerovala vaše žádost o certifikát.

Pokud jste žádost o certifikát vygenerovali pomocí OpenSSL, vytvořili jste i soubor privátního klíče. Exportujte certifikát do formátu PFX spuštěním následujícího příkazu. Nahraďte zástupné symboly _&lt; Private-Key-File>_ a _&lt; merge-Certificate-File>_ cestami k vašemu privátnímu klíči a souboru sloučeného certifikátu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po zobrazení výzvy definujte heslo pro export. Toto heslo budete používat při nahrávání certifikátu TLS/SSL do App Service později.

Pokud jste k vygenerování žádosti o certifikát použili službu IIS nebo nástroj _Certreq.exe_, nainstalujte certifikát na místním počítači a pak [exportujte certifikát do formátu PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Nahrát certifikát do App Service

Nyní jste si certifikát nahráli do App Service.

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace vyberte **Nastavení TLS/SSL**  >  certifikát pro odeslání certifikátu **privátního klíče (. pfx)**  >  .

![Nahrání privátního certifikátu v App Service](./media/configure-ssl-certificate/upload-private-cert.png)

V části **Soubor certifikátu PFX** vyberte svůj soubor PFX. Do pole **Heslo certifikátu** zadejte heslo, které jste vytvořili při exportování souboru PFX. Po dokončení klikněte na **Odeslat**. 

Po dokončení operace se certifikát zobrazí v seznamu **certifikáty privátního klíče** .

![Nahrávání certifikátu bylo dokončeno.](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je stále nutné vytvořit vazbu certifikátu. Postupujte podle kroků v části [vytvoření vazby](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Nahrajte veřejný certifikát.

Veřejné certifikáty jsou podporovány ve formátu *. cer* . 

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services**  >  **\<app-name>** .

V levém navigačním panelu aplikace klikněte na **Nastavení TLS/SSL**  >  **veřejné certifikáty (. cer)**  >  **nahrát certifikát veřejného klíče**.

Do pole **název** zadejte název certifikátu. V **souboru certifikátu CER** vyberte svůj soubor CER.

Klikněte na **Odeslat**.

![Nahrání veřejného certifikátu v App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Po nahrání certifikátu zkopírujte kryptografický otisk certifikátu a podívejte [se na zpřístupněný certifikát](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Správa certifikátů App Service

V této části se dozvíte, jak spravovat certifikát App Service, který jste zakoupili v tématu [Import certifikátu App Service](#import-an-app-service-certificate).

- [Certifikát opětovného navýšení](#rekey-certificate)
- [Prodloužit platnost certifikátu](#renew-certificate)
- [Export certifikátu](#export-certificate)
- [Odstranit certifikát](#delete-certificate)

### <a name="rekey-certificate"></a>Certifikát opětovného navýšení

Pokud se domníváte, že dojde k ohrožení bezpečnosti privátního klíče certifikátu, můžete svůj certifikát znovu zařadit do klíčů. Vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a pak v levém navigačním panelu vyberte obnovení **klíčů a synchronizaci** .

Kliknutím na obnovit **klíč** zahajte proces. Dokončení tohoto procesu může trvat 1-10 minut.

![Opětovné vytvoření klíče App Service certifikátu](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Po opětovném vytvoření klíče se certifikát zahrne do nového certifikátu vydaného certifikační autoritou.

Jakmile je operace obnovení klíčů dokončena, klikněte na tlačítko **synchronizovat**. Operace synchronizace automaticky aktualizuje vazby hostitelů pro certifikát v App Service, aniž by to mělo za následek jakékoliv výpadky aplikací.

> [!NOTE]
> Pokud nekliknete na **synchronizovat**, App Service se certifikát automaticky synchronizuje během 24 hodin.

### <a name="renew-certificate"></a>Prodloužit platnost certifikátu

Pokud chcete kdykoli zapnout automatické obnovování certifikátu, vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a potom klikněte na tlačítko **automaticky obnovit nastavení** v levém navigačním panelu. Ve výchozím nastavení mají certifikáty App Service jednoletou dobu platnosti.

Vyberte **zapnuto** a klikněte na **Uložit**. Certifikáty se můžou po uplynutí 30 dnů před vypršením platnosti automaticky prodloužit, pokud je zapnuté automatické obnovení.

![Obnovit certifikát App Service automaticky](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Pokud chcete certifikát místo toho ručně obnovit, klikněte na **Ruční obnovení**. Můžete požádat o ruční obnovení certifikátu 60 dní před vypršením platnosti.

Po dokončení operace obnovení klikněte na **synchronizovat**. Operace synchronizace automaticky aktualizuje vazby hostitelů pro certifikát v App Service, aniž by to mělo za následek jakékoliv výpadky aplikací.

> [!NOTE]
> Pokud nekliknete na **synchronizovat**, App Service se certifikát automaticky synchronizuje během 24 hodin.

### <a name="export-certificate"></a>Export certifikátu

Vzhledem k tomu, že App Service Certificate je [Key Vault tajný klíč](../key-vault/general/about-keys-secrets-certificates.md), můžete exportovat kopii PFX a použít ji pro jiné služby Azure nebo mimo Azure.

Chcete-li exportovat App Service Certificate jako soubor PFX, spusťte v [Cloud Shell](https://shell.azure.com)následující příkazy. Pokud jste [nainstalovali Azure CLI](/cli/azure/install-azure-cli), můžete ho také spustit místně. Zástupné symboly nahraďte názvy, které jste použili při [vytváření certifikátu App Service](#start-certificate-order).

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

Stažený soubor *appservicecertificate. pfx* je nezpracovaný soubor PKCS12, který obsahuje jak veřejné, tak privátní certifikáty. V každém řádku použijte prázdný řetězec pro heslo importu a frázi PEM Pass.

### <a name="delete-certificate"></a>Odstranit certifikát 

Odstranění App Serviceho certifikátu je finální a nevratné. Odstranění App Service Certificateho prostředku vede k odvolání certifikátu. Jakékoli vazby v App Service s tímto certifikátem budou neplatné. Aby se zabránilo nechtěnému odstranění, Azure vloží do certifikátu zámek. Chcete-li odstranit certifikát App Service, je nutné nejprve odebrat zámek proti odstranění na certifikátu.

Vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a pak v levém navigačním panelu vyberte **zámky** .

Najděte zámek certifikátu pomocí typu zámku **Delete**. Napravo od něj vyberte **Odstranit**.

![Odstranit zámek pro App Service certifikát](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nyní můžete certifikát App Service odstranit. V levém navigačním panelu vyberte **Přehled**  >  **Odstranit**. V potvrzovacím dialogovém okně zadejte název certifikátu a vyberte **OK**.

## <a name="automate-with-scripts&quot;></a>Automatizace pomocí skriptů

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Další zdroje informací

* [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md)
* [Vynucení protokolu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Použití certifikátu TLS nebo SSL v kódu ve službě Azure App Service](configure-ssl-certificate-in-code.md)
* [Nejčastější dotazy: App Service certifikátů](./faq-configuration-and-management.md)
