---
title: 'Kurz: mapování stávajícího vlastního názvu DNS'
description: Naučte se, jak přidat existující vlastní název domény DNS (individuální Domain) do webové aplikace, back-endu mobilní aplikace nebo aplikace API v Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 79599ce04b93409c67342be73cf88d5e20621c1d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182566"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Kurz: mapování stávajícího vlastního názvu DNS na Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak namapovat stávající vlastní název DNS (Domain Name System) na App Service.

![Snímek obrazovky zobrazující Azure Portal navigaci do aplikace Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Namapujte subdoménu (například `www.contoso.com` ) pomocí záznamu CNAME.
> * Namapujte kořenovou doménu (například `contoso.com` ) pomocí záznamu a.
> * Mapování domény se zástupnými znaky (například `*.contoso.com` ) pomocí záznamu CNAME.
> * Přesměrujte výchozí adresu URL na vlastní adresář.
> * Automatizace mapování domén pomocí skriptů.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](./index.yml) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* Zakupte název domény a ujistěte se, že máte přístup k registru DNS vašeho poskytovatele domény (například GoDaddy).

  Abyste například mohli přidat záznamy DNS pro `contoso.com` a `www.contoso.com`, musíte mít možnost konfigurovat nastavení DNS pro kořenovou doménu `contoso.com`.

  > [!NOTE]
  > Pokud nemáte existující název domény, zvažte [Nákup domény pomocí Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Příprava aplikace

Pokud chcete namapovat vlastní název DNS na webovou aplikaci, [plán App Service](https://azure.microsoft.com/pricing/details/app-service/) webové aplikace musí být placená úroveň (Shared, Basic, Standard, Premium nebo spotřeba pro Azure Functions). V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com)a přihlaste se pomocí svého účtu Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Výběr aplikace v Azure Portal

1. Vyhledejte a vyberte **App Services**.

   ![Snímek obrazovky, který zobrazuje výběr App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na stránce **App Services** vyberte název aplikace Azure.

   ![Snímek obrazovky znázorňující navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zobrazí se stránka pro správu aplikace App Service.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

1. V levém podokně stránky aplikace přejděte do části **Nastavení** a vyberte **horizontální navýšení kapacity (App Service plán)**.

   ![Snímek obrazovky s nabídkou pro horizontální navýšení kapacity (App Service plán)](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Ujistěte se, že aplikace není ve vrstvě **F1** . Vlastní DNS se nepodporuje ve vrstvě **F1** .

   ![Snímek obrazovky, který ukazuje Doporučené cenové úrovně.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Pokud plán App Service není ve vrstvě **F1** , zavřete stránku **horizontálního navýšení kapacity** a přejděte k [mapě záznamu CNAME](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

1. Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti **zobrazíte výběrem možnosti Zobrazit další možnosti**.

1. Vyberte **Použít**.

   ![Snímek obrazovky, který ukazuje kontrolu cenové úrovně.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Až se zobrazí následující oznámení, operace škálování je dokončená.

   ![Snímek obrazovky, který ukazuje potvrzení operace škálování.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Získat ID ověření domény

Pokud chcete do aplikace přidat vlastní doménu, musíte ověřit vlastnictví domény tím, že přidáte ID ověřování jako záznam TXT pro vašeho poskytovatele domény. V levém podokně stránky aplikace vyberte **vlastní domény**. Zkopírujte ID do pole **ID ověření vlastní domény** na stránce **vlastní domény** pro další krok.

![Snímek obrazovky zobrazující ID v poli ID ověření vlastní domény](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Přidání identifikátorů ověřování domény do vlastní domény může zabránit tomu, aby se dangling záznamy DNS a usnadnily převzetí subdomény. U vlastních domén, které jste dříve nakonfigurovali bez tohoto ID ověření, byste je měli chránit před stejným rizikem přidáním ID ověření do záznamu DNS. Další informace o této společné hrozbě s vysokou závažností najdete v tématu [převzetí subdomény](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Mapování domény

K mapování vlastního názvu DNS na službu App Service můžete použít záznam CNAME nebo záznam A. Postupujte podle příslušných kroků:

- [Mapování záznamu CNAME](#map-a-cname-record)
- [Mapování záznamu A](#map-an-a-record)
- [Mapování zástupné domény (pomocí záznamu CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Záznamy CNAME byste měli použít pro všechny vlastní názvy DNS s výjimkou kořenových domén (například `contoso.com`). Pro kořenové domény použijte záznamy A.

### <a name="map-a-cname-record"></a>Mapování záznamu CNAME

V tomto příkladu přidáte záznam CNAME pro subdoménu `www` (například `www.contoso.com`).

Pokud máte subdoménu jinou než `www` , nahraďte ji `www` subdoménou (například `sub` Pokud je vaše vlastní doména `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>Přístup k záznamům DNS pomocí poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Namapujte subdoménu na název výchozí domény aplikace ( `<app-name>.azurewebsites.net` , kde `<app-name>` je název vaší aplikace). Chcete-li vytvořit mapování CNAME pro `www` subdoménu, vytvořte dva záznamy:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Samotné mapování domény. |
| TXT | `asuid.www` | [ID ověření, které jste získali dříve](#get-a-domain-verification-id) | App Service přistupuje k `asuid.<subdomain>` záznamu TXT a ověří vlastnictví vlastní domény. |

Po přidání záznamů CNAME a TXT bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Snímek obrazovky zobrazující navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Povolení mapování záznamu CNAME v Azure

1. V levém podokně stránky aplikace v Azure Portal vyberte **vlastní domény**.

    ![Snímek obrazovky, který zobrazuje nabídku vlastní domény.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stránce **vlastní domény** aplikace přidejte do seznamu plně kvalifikovaný název DNS ( `www.contoso.com` ).

1. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje položku pro přidání názvu hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například `www.contoso.com`.

1. Vyberte **Ověřit**. Zobrazí se stránka **Přidat vlastní doménu** .

1. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na **CNAME (webová \. example.com nebo libovolná subdoména)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje tlačítko Přidat vlastní doménu](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    ![Snímek obrazovky, který ukazuje přidání záznamu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).

    Pokud jste nějaký krok neudělali nebo jste ho provedli dříve, zobrazí se v dolní části stránky chyba ověřování.

    ![Snímek obrazovky, který zobrazuje chybu ověřování.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapování záznamu A

V tomto příkladu přidáte záznam A pro kořenovou doménu (například `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Zkopírování IP adresy aplikace

K mapování záznamu A potřebujete externí IP adresu aplikace. Tuto IP adresu najdete na stránce **vlastní domény** aplikace v Azure Portal.

1. V levém podokně stránky aplikace v Azure Portal vyberte **vlastní domény**.

   ![Snímek obrazovky, který zobrazuje nabídku vlastní domény.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stránce **vlastní domény** zkopírujte IP adresu aplikace.

   ![Snímek obrazovky, který zobrazuje navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Přístup k záznamům DNS pomocí poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Vytvoření záznamu A

Pokud chcete namapovat záznam A na aplikaci, obvykle pro kořenovou doménu, vytvořte dva záznamy:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) | Samotné mapování domény ( `@` obvykle představuje kořenovou doménu). |
| TXT | `asuid` | [ID ověření, které jste získali dříve](#get-a-domain-verification-id) | App Service přistupuje k `asuid.<subdomain>` záznamu TXT a ověří vlastnictví vlastní domény. Pro kořenovou doménu použijte `asuid` . |

> [!NOTE]
> Chcete-li přidat subdoménu (například `www.contoso.com` ) pomocí záznamu a místo doporučeného [záznamu CNAME](#map-a-cname-record), měl by váš záznam a záznam TXT vypadat jako v následující tabulce:
>
> | Typ záznamu | Hostitel | Hodnota |
> | - | - | - |
> | A | `www` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
> | TXT | `asuid.www` | [ID ověření, které jste získali dříve](#get-a-domain-verification-id) |
>

Po přidání záznamů bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Snímek obrazovky zobrazující stránku záznamů DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Povolení mapování záznamu A v aplikaci

Zpátky na stránce **vlastní domény** aplikace v Azure Portal přidejte do seznamu plně kvalifikovaný vlastní název DNS (například `contoso.com` ).

1. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který ukazuje přidání názvu hostitele.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Zadejte plně kvalifikovaný název domény, pro který jste nakonfigurovali záznam A, například `contoso.com`. 

1. Vyberte **Ověřit**. Zobrazí se stránka **Přidat vlastní doménu** .

1. Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **Záznam A (www.example.com)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který ukazuje přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    ![Snímek obrazovky, který ukazuje přidání záznamu A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).
    
    Pokud jste nějaký krok neudělali nebo jste ho provedli dříve, zobrazí se v dolní části stránky chyba ověřování.
    
    ![Snímek obrazovky znázorňující chybu ověřování](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapování zástupné domény

V tomto příkladu namapujete na aplikaci App Service [zástupný název DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (například `*.contoso.com`) přidáním záznamu CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Přístup k záznamům DNS pomocí poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Namapujte zástupný název `*` na výchozí název domény aplikace ( `<app-name>.azurewebsites.net` , kde `<app-name>` je název vaší aplikace). Chcete-li namapovat zástupný název, vytvořte dva záznamy:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Samotné mapování domény. |
| TXT | `asuid` | [ID ověření, které jste získali dříve](#get-a-domain-verification-id) | App Service přistupuje k `asuid` záznamu TXT a ověří vlastnictví vlastní domény. |

Pro příklad domény `*.contoso.com` bude záznam CNAME mapovat název `*` na `<app-name>.azurewebsites.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Snímek obrazovky, který zobrazuje navigaci na aplikaci Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Povolení mapování záznamu CNAME v aplikaci

Nyní můžete do aplikace přidat jakoukoli subdoménu, která odpovídá zástupnému názvu (například `sub1.contoso.com` , `sub2.contoso.com` a `*.contoso.com` obě shody `*.contoso.com` ).

1. V levém podokně stránky aplikace v Azure Portal vyberte **vlastní domény**.

    ![Snímek obrazovky, který zobrazuje nabídku vlastní domény.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který ukazuje přidání názvu hostitele.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Zadejte plně kvalifikovaný název domény, který odpovídá zástupné doméně (například `sub1.contoso.com`), a pak vyberte **Ověřit**.

    Aktivuje se tlačítko **Přidat vlastní doménu** .

1. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na **záznam CNAME (webová \. example.com nebo libovolná subdoména)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje přidání názvu DNS do aplikace.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

1. **+** Opětovným výběrem této ikony přidejte další vlastní doménu, která odpovídá zástupné doméně. Přidejte například `sub2.contoso.com`.

    ![Snímek obrazovky, který ukazuje přidání záznamu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Testování v prohlížeči

Přejděte na názvy DNS, které jste nakonfigurovali dříve (například,, a `contoso.com` `www.contoso.com` `sub1.contoso.com` `sub2.contoso.com` ).

![Snímek obrazovky, který ukazuje navigaci na aplikaci Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Vyřešit 404 "Nenalezeno"

Pokud se zobrazí chyba HTTP 404 (Nenalezeno), když přejdete na adresu URL vlastní domény, ověřte, že se vaše doména přeloží na IP adresu vaší aplikace pomocí <a href="https://www.nslookup.io/" target="_blank">nslookup.IO</a>. V takovém případě Ověřte, zda jsou záznamy A a CNAME správně nakonfigurovány pomocí stejné lokality. Pokud se IP adresa správně vyřeší, ale pořád se vám zobrazí 404, může váš prohlížeč Uložit starou IP adresu vaší domény do mezipaměti. Vymažte mezipaměť a znovu otestujte překlad DNS. Na počítači s Windows můžete mezipaměť vymazat příkazem `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrace aktivní domény

Pokud chcete do služby App Service migrovat živý web a jeho název domény DNS bez výpadku, přečtěte si článek o [migraci aktivního názvu DNS do služby Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Přesměrování do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Některé webové architektury se ale nespouštějí v kořenovém adresáři. Například [Laravel](https://laravel.com/) začíná v podadresáři `public`. Pokud chcete v `contoso.com` příkladu DNS pokračovat, taková aplikace je přístupná v `http://contoso.com/public` , ale `http://contoso.com` místo toho chcete směrovat do `public` adresáře. Tento krok nezahrnuje rozlišení DNS, ale je o přizpůsobení virtuálního adresáře.

Pokud chcete přizpůsobit virtuální adresář pro aplikace pro Windows, v levém podokně stránky vaší webové aplikace vyberte **nastavení aplikace** . 

> [!NOTE]
> Aplikace pro Linux nemají tuto stránku. Pokud chcete změnit kořenový adresář pro aplikace pro Linux, přečtěte si téma konfigurační příručky pro konkrétní jazyk (například[php](configure-language-php.md?pivots=platform-linux#change-site-root)).

V dolní části stránky kořenový virtuální adresář `/` odkazuje ve výchozím nastavení na adresář `site\wwwroot`, což je kořenový adresář kódu vaší aplikace. Změňte ho tak, aby místo toho odkazoval například na adresář `site\wwwroot\public`, a uložte provedené změny.

![Snímek obrazovky, který ukazuje přizpůsobení virtuálního adresáře.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po dokončení operace by vaše aplikace měla vrátit pravou stránku na kořenové cestě (například `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/)můžete automatizovat správu vlastních domén pomocí skriptů.

### <a name="azure-cli"></a>Azure CLI

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Další informace najdete v tématu [Mapování vlastní domény na webovou aplikaci](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Další informace najdete v tématu [Přiřazení vlastní domény k webové aplikaci](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Namapujte subdoménu pomocí záznamu CNAME.
> * Namapujte kořenovou doménu pomocí záznamu A.
> * Mapování domény se zástupnými znaky pomocí záznamu CNAME.
> * Přesměrujte výchozí adresu URL na vlastní adresář.
> * Automatizace mapování domén pomocí skriptů.

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit vazby vlastního certifikátu TLS/SSL k webové aplikaci.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md)
