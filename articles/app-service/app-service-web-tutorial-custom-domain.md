---
title: Mapování existujícího vlastního názvu DNS na Azure Web Apps | Microsoft Docs
description: Zjistěte, jak přidat existující vlastní název domény DNS (individuální doména) k webové aplikaci, back-endu mobilní aplikace nebo aplikaci API ve službě Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a9f1e66a4c55d866d9f174528eb4912c3b9391c0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714511"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-web-apps"></a>Kurz: Mapování existujícího vlastního názvu DNS na Azure Web Apps

[Azure Web Apps](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak mapovat existující vlastní název DNS na Azure Web Apps.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Mapovat subdoménu (například `www.contoso.com`) pomocí záznamu CNAME
> * Mapovat kořenovou doménu (například `contoso.com`) pomocí záznamu A
> * Mapovat zástupnou doménu (například `*.contoso.com`) pomocí záznamu CNAME
> * Automatizovat mapování domén pomocí skriptů

K mapování vlastního názvu DNS na službu App Service můžete použít **záznam CNAME** nebo **záznam A**. 

> [!NOTE]
> Pro všechny vlastní názvy DNS kromě kořenové domény (například `contoso.com`) doporučujeme použít záznam CNAME.

Pokud chcete do služby App Service migrovat živý web a jeho název domény DNS, přečtěte si téma [Migrace aktivního názvu DNS do služby Azure App Service](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* Zakoupit název domény a ověřit, že máte přístup k registru DNS pro vašeho poskytovatele domény (například GoDaddy).

  Abyste například mohli přidat záznamy DNS pro `contoso.com` a `www.contoso.com`, musíte mít možnost konfigurovat nastavení DNS pro kořenovou doménu `contoso.com`.

  > [!NOTE]
  > Pokud nemáte žádný existující název domény, zvažte [zakoupení domény pomocí webu Azure Portal](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Příprava aplikace

Abyste mohli mapovat vlastní název DNS na webovou aplikaci, [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) příslušné webové aplikace musí být na placené úrovni (**Shared**, **Basic**, **Standard** nebo **Premium**). V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Přechod do aplikace na webu Azure Portal

V levé nabídce vyberte **App Services** a pak vyberte název aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zobrazí se stránka pro správu aplikace App Service.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, že aplikace není na úrovni **F1**. Vlastní DNS se na úrovni **F1** nepodporuje. 

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Pokud plán služby App Service není úrovně **F1**, zavřete stránku **Vertikálně navýšit kapacitu** a přejděte k části [Mapování záznamu CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na tlačítko **Použít**.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mapování záznamu CNAME

V tomto příkladu přidáte záznam CNAME pro subdoménu `www` (například `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME pro mapování subdomény na výchozí název hostitele aplikace (`<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace).

Pro příklad domény `www.contoso.com` přidejte záznam CNAME, který mapuje název `www` na `<app_name>.azurewebsites.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Povolení mapování záznamu CNAME v Azure

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** aplikace přidejte do seznamu plně kvalifikovaný vlastní název DNS (`www.contoso.com`).

Vyberte ikonu **+** vedle možnosti **Přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například `www.contoso.com`. 

Vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **CNAME (www.example.com nebo jakákoli subdoména)**.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Pokud jste nějaký krok vynechali nebo jste někde udělali překlep, ve spodní části stránky se zobrazí chyba ověření.

![Chyba ověření](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapování záznamu A

V tomto příkladu přidáte záznam A pro kořenovou doménu (například `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Zkopírování IP adresy aplikace

K mapování záznamu A potřebujete externí IP adresu aplikace. Tuto IP adresu najdete na stránce **Vlastní domény** aplikace na webu Azure Portal.

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Vytvoření záznamu A

K mapování záznamu A na aplikaci vyžaduje služba App Service **dva** záznamy DNS:

- Záznam **A** pro mapování na IP adresu aplikace.
- Záznam **TXT** pro mapování na výchozí název hostitele aplikace `<app_name>.azurewebsites.net`. Služba App Service používá tento záznam pouze během konfigurace k ověření, že jste vlastníkem vlastní domény. Po ověření a konfiguraci vlastní domény ve službě App Service můžete tento záznam TXT odstranit. 

Pro příklad domény `contoso.com` vytvořte záznamy A a TXT podle následující tabulky (`@` obvykle představuje kořenovou doménu). 

| Typ záznamu | Hostitel | Hodnota |
| - | - | - |
| A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Po přidání záznamů bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Stránka záznamů DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Povolení mapování záznamu A v aplikaci

Zpět na stránce **Vlastní domény** aplikace na webu Azure Portal přidejte do seznamu plně kvalifikovaný vlastní název DNS (například `contoso.com`).

Vyberte ikonu **+** vedle možnosti **Přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Zadejte plně kvalifikovaný název domény, pro který jste nakonfigurovali záznam A, například `contoso.com`.

Vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **Záznam A (www.example.com)**.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Pokud jste nějaký krok vynechali nebo jste někde udělali překlep, ve spodní části stránky se zobrazí chyba ověření.

![Chyba ověření](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapování zástupné domény

V tomto příkladu namapujete na aplikaci App Service [zástupný název DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (například `*.contoso.com`) přidáním záznamu CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME mapující zástupný název na výchozí název hostitele aplikace (`<app_name>.azurewebsites.net`).

Pro příklad domény `*.contoso.com` bude záznam CNAME mapovat název `*` na `<app_name>.azurewebsites.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Povolení mapování záznamu CNAME v aplikaci

Teď můžete do aplikace přidat jakoukoli subdoménu, která odpovídá zástupnému názvu (například `sub1.contoso.com` a `sub2.contoso.com` odpovídají `*.contoso.com`). 

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Vyberte ikonu **+** vedle možnosti **Přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, který odpovídá zástupné doméně (například `sub1.contoso.com`), a pak vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **Záznam CNAME (www.example.com nebo jakákoli subdoména)**.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

Znovu vyberte ikonu **+** a přidejte další název hostitele, který odpovídá zástupné doméně. Přidejte například `sub2.contoso.com`.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Test v prohlížeči

Přejděte na názvy DNS, které jste předtím nakonfigurovali (například `contoso.com`, `www.contoso.com`, `sub1.contoso.com` a `sub2.contoso.com`).

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Řešení chyby 404: Web se nenašel

Pokud se při přechodu na adresu URL vaší vlastní domény zobrazí chyba HTTP 404 (Nenalezeno), pomocí webu <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> ověřte, že se doména překládá na IP adresu vaší aplikace. Pokud ne, může to být způsobené jedním z následujících důvodů:

- V konfiguraci vlastní domény chybí záznam A nebo záznam CNAME.
- Prohlížeč uložil do mezipaměti starou IP adresu vaší domény. Vymažte mezipaměť a znovu otestujte překlad DNS. Na počítači s Windows můžete mezipaměť vymazat příkazem `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Některá webová rozhraní však nezačínají v kořenovém adresáři. Například [Laravel](https://laravel.com/) začíná v podadresáři `public`. Když budeme pokračovat v příkladu DNS `contoso.com`, taková aplikace by byla přístupná na adrese `http://contoso.com/public`. Místo toho byste však měli chtít směrovat `http://contoso.com` do adresáře `public`. Tento krok nezahrnuje překlad DNS, ale přizpůsobení virtuálního adresáře.

Uděláte to tak, že v levém navigačním panelu na stránce vaší webové aplikace vyberete **Nastavení aplikace**. 

V dolní části stránky kořenový virtuální adresář `/` odkazuje ve výchozím nastavení na adresář `site\wwwroot`, což je kořenový adresář kódu vaší aplikace. Změňte ho tak, aby místo toho odkazoval například na adresář `site\wwwroot\public`, a uložte provedené změny. 

![Přizpůsobení virtuálního adresáře](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po dokončení této operace by vaše aplikace měla na kořenové cestě (například http://contoso.com)) vracet správnou stránku.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu vlastních domén můžete automatizovat pomocí skriptů s využitím [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShellu](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Další informace najdete v tématu [Mapování vlastní domény na webovou aplikaci](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Další informace najdete v tématu [Přiřazení vlastní domény k webové aplikaci](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Mapovat subdoménu pomocí záznamu CNAME
> * Mapovat kořenovou doménu pomocí záznamu A
> * Mapovat zástupnou doménu pomocí záznamu CNAME
> * Automatizovat mapování domén pomocí skriptů

V dalším kurzu se dozvíte, jak vytvořit vazbu vlastního certifikátu SSL k webové aplikaci.

> [!div class="nextstepaction"]
> [Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
