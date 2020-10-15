---
title: Kurz – mapování stávající vlastní domény na jarní cloud Azure
description: Jak namapovat stávající vlastní název DNS (Distributed Name Service) na Azure jaře Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ea0887dd1d28bb958b27813df7f4c7a221470bac
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088750"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapování stávající vlastní domény na jarní cloud Azure

**Tento článek se týká:** ✔️ Java ✔️ C #

Služba DNS (Domain Name Service) je technika pro ukládání názvů síťových uzlů v síti. Tento kurz namapuje doménu, například www.contoso.com, pomocí záznamu CNAME. Zabezpečuje vlastní doménu s certifikátem a ukazuje, jak vymáhat protokol TLS (Transport Layer Security), označovaný také jako SSL (Secure Sockets Layer) (SSL). 

Certifikáty šifrují webový provoz. Tyto certifikáty TLS/SSL můžou být uložené v Azure Key Vault. 

## <a name="prerequisites"></a>Předpoklady
* Aplikace nasazená do jarního cloudu Azure (Další informace najdete v tématu [rychlý Start: spuštění stávající aplikace pro jarní Cloud v Azure pomocí Azure Portal](spring-cloud-quickstart.md), nebo použití existující aplikace).
* Název domény s přístupem k registru DNS pro poskytovatele domény, jako je GoDaddy.
* Privátní certifikát (tedy certifikát podepsaný svým držitelem) od poskytovatele třetí strany. Certifikát se musí shodovat s doménou.
* Nasazená instance [Azure Key Vault](../key-vault/general/overview.md)

## <a name="import-certificate"></a>Import certifikátu
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Příprava souboru certifikátu v souboru PFX (volitelné)
Azure Key Vault podporuje import privátního certifikátu ve formátu PEM a PFX. Pokud soubor PEM, který jste získali od poskytovatele certifikátů, nefunguje v části níže: [Uložit certifikát v Key Vault](#save-certificate-in-key-vault), postupujte podle kroků uvedených tady a vygenerujte PFX pro Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Sloučení zprostředkujících certifikátů

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

#### <a name="export-certificate-to-pfx"></a>Export certifikátu do formátu PFX

Exportujte sloučený certifikát TLS/SSL s privátním klíčem, se kterým se vygenerovala vaše žádost o certifikát.

Pokud jste žádost o certifikát vygenerovali pomocí OpenSSL, vytvořili jste i soubor privátního klíče. Exportujte certifikát do formátu PFX spuštěním následujícího příkazu. Nahraďte zástupné symboly _ &lt; Private-Key-File>_ a _ &lt; merge-Certificate-File>_ cestami k vašemu privátnímu klíči a souboru sloučeného certifikátu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Po zobrazení výzvy definujte heslo pro export. Toto heslo budete používat při nahrávání certifikátu TLS/SSL do Azure Key Vault později.

Pokud jste k vygenerování žádosti o certifikát použili službu IIS nebo nástroj _Certreq.exe_, nainstalujte certifikát na místním počítači a pak [exportujte certifikát do formátu PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Uložit certifikát v Key Vault
Postup importu certifikátu vyžaduje, aby soubor s kódováním PEM nebo PFX byl na disku, a musíte mít privátní klíč. 
#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
Postup nahrání certifikátu do trezoru klíčů:
1. Přejít do instance trezoru klíčů.
1. V levém navigačním podokně klikněte na **certifikáty**.
1. V horní nabídce klikněte na možnost **Generovat/importovat**.
1. V dialogovém okně **vytvořit certifikát** v části **Metoda vytvoření certifikátu**vyberte `Import` .
1. V části **nahrát soubor certifikátu**přejděte do umístění certifikátu a vyberte ho.
1. V části **heslo**zadejte privátní klíč certifikátu.
1. Klikněte na **Vytvořit**.

    ![Importovat certifikát 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů v Azure jaře cloudu

Než naimportujete certifikát, musíte pro svůj Trezor klíčů udělit přístup k Azure jaře cloudu:
#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
1. Přejít do instance trezoru klíčů.
1. V levém navigačním podokně klikněte na **přístup k zásadám**.
1. V horní nabídce klikněte na **Přidat zásady přístupu**.
1. Vyplňte informace a klikněte na tlačítko **Přidat** a pak na **Uložit** zásady přístupu.

| Oprávnění tajného kódu | Oprávnění certifikátu | Vybrat objekt zabezpečení |
|--|--|--|
| Získat, seznam | Získat, seznam | Domain-Management Azure jaře Cloud |

![Importovat certifikát 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Udělte Azure jaře přístup pro čtení k trezoru klíčů, nahraďte `<key vault resource group>` a `<key vault name>` v následujícím příkazu.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importovat certifikát do jarního cloudu Azure
#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
1. Přejít na instanci služby. 
1. V levém navigačním podokně aplikace vyberte **Nastavení TLS/SSL**.
1. Pak klikněte na **importovat Key Vault certifikát**.

    ![Import certifikátu](./media/custom-dns-tutorial/import-certificate.png)

1. Po úspěšném Importování certifikátu se zobrazí seznam **certifikátů privátních klíčů**.

    ![Certifikát privátního klíče](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Zobrazení seznamu importovaných certifikátů:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je nutné vytvořit navázání certifikátu na konkrétní doménu. Postupujte podle kroků v této části: [Přidání vazby SSL](#add-ssl-binding).

## <a name="add-custom-domain"></a>Přidat vlastní doménu
Záznam CNAME můžete použít k mapování vlastního názvu DNS na jarní cloud Azure. 

> [!NOTE] 
> Záznam A se nepodporuje. 

### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME
Přejít na poskytovatele DNS a přidat záznam CNAME pro mapování domény na <service_name>. azuremicroservices.io. Tady <service_name> je název vaší instance Azure jaře cloudu. Podporujeme doménu se zástupnými znaky a podřízenou doménu. Po přidání CNAME bude stránka záznamů DNS vypadat podobně jako v následujícím příkladu: 

![Stránka záznamů DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapování vlastní domény na jarní cloudovou aplikaci Azure
Pokud nemáte aplikaci v Azure jaře cloudu, postupujte podle pokynů v části [rychlý Start: spuštění stávající aplikace Azure jaře cloudu pomocí Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
Přejít na stránku aplikace

1. Vyberte **vlastní doména**.
2. Pak **přidejte vlastní doménu**. 

    ![Vlastní doména](./media/custom-dns-tutorial/custom-domain.png)

3. Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například www.contoso.com. Ujistěte se, že typ záznamu názvu hostitele je nastavený na CNAME (<service_name>. azuremicroservices.io).
4. Kliknutím na tlačítko **ověřit** povolte tlačítko **Přidat** .
5. Klikněte na **Přidat**.

    ![Přidat vlastní doménu](./media/custom-dns-tutorial/add-custom-domain.png)

Jedna aplikace může mít víc domén, ale jedna doména se dá mapovat jenom na jednu aplikaci. Po úspěšném namapování vlastní domény k aplikaci se zobrazí v tabulce vlastní doména.

![Vlastní doménová tabulka](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Zobrazení seznamu vlastních domén:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> **Nezabezpečený** popisek pro vaši vlastní doménu znamená, že ještě není vázaný na certifikát SSL. Jakékoli žádosti HTTPS z prohlížeče do vaší vlastní domény zobrazí chybu nebo varování.

## <a name="add-ssl-binding"></a>Přidat vazbu SSL

#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
V tabulce vlastní doména vyberte **Přidat vazbu SSL** , jak je znázorněno na předchozím obrázku.  
1. Vyberte svůj **certifikát** nebo ho importujte.
1. Klikněte na **Uložit**.

    ![Přidat vazbu SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Po úspěšném přidání vazby SSL bude stav domény zabezpečený: **v pořádku**. 

![Přidat vazbu SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Vynucení protokolu HTTPS
Ve výchozím nastavení může kdokoli k vaší aplikaci přistupovat pomocí protokolu HTTP, ale můžete přesměrovat všechny požadavky HTTP na port HTTPS.
#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)
Na stránce aplikace v levém navigačním panelu vyberte **vlastní doména**. Potom nastavte **pouze https**na *hodnotu true*.

![Přidat vazbu SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Po dokončení operace přejděte na libovolnou adresu URL HTTPS, která odkazuje na vaši aplikaci. Upozorňujeme, že adresy URL HTTP nefungují.

## <a name="see-also"></a>Viz také
* [Co je Azure Key Vault?](../key-vault/general/overview.md)
* [Import certifikátu](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Spusťte svoji jarní cloudovou aplikaci pomocí Azure CLI.](./spring-cloud-quickstart.md)