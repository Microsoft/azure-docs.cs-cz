---
title: Samoobslužný hostování portálu pro vývojáře
titleSuffix: Azure API Management
description: Naučte se, jak samoobslužně hostovat portál pro vývojáře API Management.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741629"
---
# <a name="self-host-the-api-management-developer-portal"></a>Samoobslužná hostování portálu pro vývojáře API Management

V tomto kurzu se dozvíte, jak můžete samoobslužně hostovat [portál pro vývojáře API Management](api-management-howto-developer-portal.md). Samoobslužné hostování nabízí flexibilitu pro rozšiřování portálu pro vývojáře vlastní logikou a widgety, které dynamicky přidávají stránky za běhu. Můžete sami hostovat více portálů pro vaši instanci API Management s různými funkcemi. Po samoobslužném hostování portálu se stanete jeho údržbou a zodpovídáte za jeho upgrady. 

Následující kroky ukazují, jak nastavit místní vývojové prostředí, provést změny na portálu pro vývojáře a publikovat a nasazovat je do účtu služby Azure Storage.

Pokud jste již nahráli nebo upravili mediální soubory na spravovaném portálu, přečtěte si téma [Přesunutí ze spravovaného do](#move-from-managed-to-self-hosted-developer-portal)místního prostředí dále v tomto článku.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

K nastavení místního vývojového prostředí potřebujete:

- Instance služby API Management. Pokud ho nemáte, přečtěte si téma [rychlý Start – vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
- Účet služby Azure Storage se zapnutou [funkcí static websites](../storage/blobs/storage-blob-static-website.md) . Viz [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
- Git na vašem počítači. Nainstalujte ji podle [tohoto kurzu pro Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Node.js (verze LTS `v10.15.0` nebo novější) a npm na vašem počítači. Viz [Stažení a instalace Node.js a npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- Azure CLI Postupujte podle [kroků pro instalaci Azure CLI](/cli/azure/install-azure-cli-windows).

## <a name="step-1-set-up-local-environment"></a>Krok 1: nastavení místního prostředí

K nastavení místního prostředí budete muset klonovat úložiště, přepnout na nejnovější verzi portálu pro vývojáře a nainstalovat balíčky npm.

1. Naklonujte úložiště [API-Management-Developer-Portal](https://github.com/Azure/api-management-developer-portal.git) z GitHubu:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Přejít na místní kopii úložiště:

    ```console
    cd api-management-developer-portal
    ```

1. Podívejte se na nejnovější verzi portálu.

    Před spuštěním následujícího kódu, podívejte se na značku aktuální verze v [oddílu releases úložiště](https://github.com/Azure/api-management-developer-portal/releases) a nahraďte `<current-release-tag>` hodnotu nejnovější značkou verze.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Nainstalujte všechny dostupné balíčky npm:

    ```console
    npm install
    ```

> [!TIP]
> Vždy používejte [nejnovější verzi portálu](https://github.com/Azure/api-management-developer-portal/releases) a udržujte svůj rozvětvený portál v aktuálním stavu. Software technici `master` pro každodenní vývoj používá větev tohoto úložiště. Má nestabilní verze softwaru.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Krok 2: Konfigurace souborů JSON, statického webu a nastavení CORS

Portál pro vývojáře vyžaduje REST API API Management pro správu obsahu.

### <a name="configdesignjson-file"></a>config.design.jsv souboru

Přejít do `src` složky a otevřít `config.design.json` soubor.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Nakonfigurujte soubor:

1. V `managementApiUrl` hodnotě nahraďte `<service-name>` názvem vaší instance API Management. Pokud jste nakonfigurovali [vlastní doménu](configure-custom-domain.md), použijte ji místo toho (například `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Ručním vytvořením tokenu SAS](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) povolte přímý REST API přístup k instanci API Management.

1. Zkopírujte vygenerovaný token a vložte ho jako `managementApiAccessToken` hodnotu.

1. V `backendUrl` hodnotě nahraďte `<service-name>` názvem vaší instance API Management. Pokud jste nakonfigurovali [vlastní doménu](configure-custom-domain.md), použijte ji místo toho (například `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Pokud byste chtěli povolit CAPTCHA na portálu pro vývojáře, přečtěte si téma [Povolení CAPTCHA](#enable-captcha).

### <a name="configpublishjson-file"></a>config.publish.jsv souboru

Přejít do `src` složky a otevřít `config.publish.json` soubor.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Nakonfigurujte soubor:

1. Zkopírujte a vložte `managementApiUrl` hodnoty a `managementApiAccessToken` z předchozího konfiguračního souboru.

1. Pokud byste chtěli povolit CAPTCHA na portálu pro vývojáře, přečtěte si téma [Povolení CAPTCHA](#enable-captcha).

### <a name="configruntimejson-file"></a>config.runtime.jsv souboru

Přejít do `src` složky a otevřít `config.runtime.json` soubor.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Nakonfigurujte soubor:

1. Zkopírujte a vložte `managementApiUrl` hodnotu z předchozího konfiguračního souboru.

1. V `backendUrl` hodnotě nahraďte `<service-name>` názvem vaší instance API Management. Pokud jste nakonfigurovali [vlastní doménu](configure-custom-domain.md), použijte ji místo toho (například. `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Konfigurace statického webu

Nakonfigurujte funkci **statického webu** v účtu úložiště tím, že zadáte trasy na rejstřík a chybové stránky:

1. Přejděte na účet úložiště v Azure Portal a v nabídce na levé straně vyberte možnost **statický web** .

1. Na stránce **statická webová** stránka Vyberte možnost **povoleno**.

1. Do pole **název dokumentu indexu** zadejte *index.html*.

1. Do pole **cesta k chybovému dokumentu** zadejte *404/index.html*.

1. Vyberte **Uložit**.

### <a name="configure-the-cors-settings"></a>Konfigurace nastavení CORS

Nakonfigurujte nastavení pro sdílení prostředků mezi zdroji (CORS):

1. Přejděte na účet úložiště v Azure Portal a v nabídce na levé straně vyberte **CORS** .

1. Na kartě **BLOB Service** nakonfigurujte následující pravidla:

    | Pravidlo | Hodnota |
    | ---- | ----- |
    | Povolené zdroje | * |
    | Povolené metody | Vyberte všechny operace HTTP. |
    | Povolené hlavičky | * |
    | Zveřejněné hlavičky | * |
    | Maximální stáří | 0 |

1. Vyberte **Uložit**.

## <a name="step-3-run-the-portal"></a>Krok 3: spuštění portálu

Nyní můžete vytvořit a spustit místní instanci portálu v režimu vývoje. V režimu pro vývoj jsou všechny optimalizace vypnuté a zdrojové mapy jsou zapnuté.

Spusťte následující příkaz:

```console
npm start
```

Po krátké době se výchozí prohlížeč automaticky otevře s vaší místní instancí portálu pro vývojáře. Výchozí adresa je `http://localhost:8080` , ale port se může změnit, pokud `8080` už je obsazený. Jakékoli změny základu kódu projektu aktivují opětovné sestavení a aktualizuje okno prohlížeče.

## <a name="step-4-edit-through-the-visual-editor"></a>Krok 4: úpravy pomocí vizuálního editoru

Pomocí vizuálního editoru proveďte tyto úkoly:

- Přizpůsobení portálu
- Vytvořit obsah
- Uspořádání struktury webu
- Vzhled stylizace

Viz [kurz: přístup a přizpůsobení portálu pro vývojáře](api-management-howto-developer-portal-customize.md). Obsahuje základní informace o uživatelském rozhraní pro správu a uvádí doporučené změny výchozího obsahu. Uložte všechny změny v místním prostředí a stisknutím **klávesové zkratky CTRL + C** ho zavřete.

## <a name="step-5-publish-locally"></a>Krok 5: místní publikování

Data portálu pocházejí z formuláře objektů se silnými typy. Následující příkaz je převede do statických souborů a umístí výstup do `./dist/website` adresáře:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Krok 6: nahrání statických souborů do objektu BLOB

Pomocí Azure CLI nahrajte místně generované statické soubory do objektu BLOB a ujistěte se, že se k nim návštěvníci můžou dostat:

1. Otevřete příkazový řádek systému Windows, PowerShell nebo jiné příkazové prostředí.

1. Spusťte následující příkaz rozhraní příkazového řádku Azure.
   
    Nahraďte `<account-connection-string>` připojovacím řetězcem vašeho účtu úložiště. Můžete ji získat z oddílu **přístupové klíče** účtu úložiště.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Krok 7: Přejít na web

Váš web je teď živý pod názvem hostitele zadaným ve vlastnostech Azure Storage (**primární koncový bod** ve **statických websites**).

## <a name="step-8-change-api-management-notification-templates"></a>Krok 8: změna šablon oznámení API Management

Nahraďte adresu URL portálu pro vývojáře v šablonách oznámení API Management, abyste odkazovali na váš Samoobslužný portál. Přečtěte si téma [Konfigurace oznámení a e-mailových šablon v Azure API Management](api-management-howto-configure-notifications.md).

Konkrétně proveďte následující změny výchozích šablon:

> [!NOTE]
> Hodnoty v následujících **aktualizovaných** oddílech předpokládají, že budete hostovat portál na adrese **https: \/ /Portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Potvrzení změny e-mailu

Aktualizujte adresu URL portálu pro vývojáře v šabloně oznámení o **potvrzení změny e-mailu** :

**Původní obsah**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualizuj**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Potvrzení nového účtu vývojáře

Aktualizujte adresu URL portálu pro vývojáře v nové šabloně oznámení o **potvrzení účtu vývojáře** :

**Původní obsah**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualizuj**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Pozvat uživatele

Aktualizujte adresu URL portálu pro vývojáře v šabloně oznámení **uživateli pro pozvání** :

**Původní obsah**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Aktualizuj**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Aktivované nové předplatné

Aktualizujte adresu URL portálu pro vývojáře v šabloně oznámení **Aktivace nového předplatného** :

**Původní obsah**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Aktualizuj**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Původní obsah**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Aktualizuj**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Původní obsah**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Aktualizuj**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Původní obsah**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Aktualizuj**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Potvrzení změny hesla

Aktualizujte adresu URL portálu pro vývojáře v šabloně oznámení o **potvrzení změny hesla** :

**Původní obsah**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualizuj**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Všechny šablony

Aktualizujte adresu URL portálu pro vývojáře v jakékoli šabloně, která má odkaz v zápatí:

**Původní obsah**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualizuj**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Přesunout ze spravovaného na Samoobslužný portál pro vývojáře

V průběhu času se můžou vaše obchodní požadavky změnit. Můžete skončit v situaci, kdy spravovaná verze portálu API Management Developer Portal už nevyhovuje vašim potřebám. Nový požadavek může například vynutit vytvoření vlastní pomůcky, která se integruje s poskytovatelem dat třetí strany. Na rozdíl od verze spravovaných vám samoobslužná verze portálu nabízí plnou flexibilitu a rozšiřitelnost.

### <a name="transition-process"></a>Proces přechodu

Z spravované verze můžete přejít na verzi v místním prostředí v rámci stejné instance služby API Management. Proces zachovává změny, které jste provedli ve spravované verzi portálu. Ujistěte se, že jste předem zálohovali obsah portálu. Zálohovací skript najdete ve `scripts` složce [úložiště GitHub](https://github.com/Azure/api-management-developer-portal)portálu pro vývojáře API Management.

Proces převodu je skoro shodný s nastavením obecného samoobslužného portálu, jak je znázorněno v předchozích krocích v tomto článku. Konfigurační krok obsahuje jednu výjimku. Účet úložiště v `config.design.json` souboru musí být stejný jako účet úložiště spravované verze portálu. Pokyny k načtení adresy URL SAS najdete v tématu [kurz: použití identity přiřazené systémem Linux VM pro přístup k Azure Storage prostřednictvím přihlašovacích údajů SAS](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) .

> [!TIP]
> V souboru doporučujeme použít samostatný účet úložiště `config.publish.json` . Tento přístup poskytuje větší kontrolu a zjednodušuje správu hostingové služby portálu.

## <a name="enable-captcha"></a>Povolit CAPTCHA

Při nastavování samoobslužného portálu jste pravděpodobně zakázali CAPTCHA prostřednictvím tohoto `useHipCaptcha` nastavení. K komunikaci s CAPTCHA dochází prostřednictvím koncového bodu, který umožňuje sdílení prostředků mezi zdroji (CORS) pouze pro název hostitele spravovaného vývojářského portálu. Pokud je váš portál pro vývojáře v místním prostředí, používá jiný název hostitele a CAPTCHA nebude umožňovat komunikaci.

### <a name="update-the-json-config-files"></a>Aktualizace konfiguračních souborů JSON

Povolení CAPTCHA na samoobslužném portálu:

1. Přiřaďte vlastní doménu (například `api.contoso.com` ) na koncový bod **portálu pro vývojáře** služby API Management.

    Tato doména se vztahuje na spravovanou verzi vašeho portálu a koncový bod CAPTCHA. Postup najdete v tématu [Konfigurace vlastního názvu domény pro instanci Azure API Management](configure-custom-domain.md).

1. `src`Pro váš Samoobslužný portál přejdete do složky v [místním prostředí](#step-1-set-up-local-environment) .

1. Aktualizujte konfigurační `json` soubory:

    | Soubor | Nová hodnota | Poznámka |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Nahraďte `<custom-domain>` vlastní doménou, kterou jste nastavili v prvním kroku. |
    |  | `"useHipCaptcha": true` | Změňte hodnotu na `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Nahraďte `<custom-domain>` vlastní doménou, kterou jste nastavili v prvním kroku. |
    |  | `"useHipCaptcha": true` | Změňte hodnotu na `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Nahraďte `<custom-domain>` vlastní doménou, kterou jste nastavili v prvním kroku. |

1. [Publikujte](#step-5-publish-locally) portál.

1. [Nahrajte](#step-6-upload-static-files-to-a-blob) a hostovat nově publikovaný portál.

1. Zveřejněte Samoobslužný portál prostřednictvím vlastní domény.

První a druhá úroveň domény portálu se musí shodovat s doménou nastavenou v prvním kroku. Například, `portal.contoso.com`. Přesný postup závisí na vaší hostující platformě, kterou si zvolíte. Pokud jste použili účet úložiště Azure, můžete pro příslušné pokyny použít odkaz k [Mapování vlastní domény na koncový bod Azure Blob Storage](../storage/blobs/storage-custom-domain-name.md) .

## <a name="next-steps"></a>Další kroky

- Další informace o [alternativních přístupech k samoobslužnému hostování](developer-portal-alternative-processes-self-host.md)
