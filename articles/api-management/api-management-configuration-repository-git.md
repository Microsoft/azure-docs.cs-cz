---
title: Konfigurace služby API Management pomocí Gitu – Azure | Microsoft Docs
description: Naučte se, jak uložit a nakonfigurovat konfiguraci služby API Management pomocí Gitu.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400347"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Uložení a konfigurace nastavení služby API Management pomocí Gitu

Každá instance služby API Management udržuje konfigurační databázi, která obsahuje informace o konfiguraci a metadatech instance služby. Změny instance služby lze provést změnou nastavení v Azure Portal, pomocí rutiny prostředí PowerShell nebo provedením REST API volání. Kromě těchto metod můžete také spravovat konfiguraci instancí služby pomocí Gitu a povolit scénáře správy služeb, jako například:

* Správa verzí konfigurace – stažení a uložení různých verzí konfigurace služby
* Hromadné změny konfigurace – proveďte změny ve více částech konfigurace služby v místním úložišti a integrujte změny zpátky na server s jedinou operací.
* Známý sada nástrojů a pracovní postup pro Git – použití nástrojů Git a pracovních postupů, které už znáte

Následující diagram znázorňuje přehled různých způsobů konfigurace instance služby API Management.

![Konfigurace Git][api-management-git-configure]

Když ve službě provedete změny pomocí Azure Portal, rutin prostředí PowerShell nebo REST API spravujete databázi konfigurace služby pomocí `https://{name}.management.azure-api.net` koncového bodu, jak je znázorněno na pravé straně diagramu. Levá strana diagramu znázorňuje, jak můžete spravovat konfiguraci služby pomocí úložiště Git a Git pro vaši službu, která se nachází v `https://{name}.scm.azure-api.net` .

Následující kroky poskytují přehled správy instance služby API Management pomocí Gitu.

1. Přístup ke konfiguraci Gitu ve vaší službě
2. Uložení konfigurační databáze služby do úložiště Git
3. Naklonujte úložiště Git na svůj místní počítač.
4. Stáhněte si nejnovější úložiště do místního počítače a potvrďte a dosaďte změny zpátky do svého úložiště.
5. Nasazení změn z úložiště do konfigurační databáze služby

Tento článek popisuje, jak povolit a použít Git ke správě konfigurace služby a poskytuje odkaz na soubory a složky v úložišti Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Přístup ke konfiguraci Gitu ve vaší službě

Pokud chcete zobrazit a nakonfigurovat nastavení konfigurace Gitu, můžete kliknout na nabídku **nasazení a infrastruktura** a přejít na kartu **úložiště** .

![Povolit GIT][api-management-enable-git]

> [!IMPORTANT]
> Všechny tajné kódy, které nejsou definované jako pojmenované hodnoty, budou uloženy v úložišti a zůstanou ve své historii, dokud nezakážete a znovu nepovolíte přístup k Gitu. Pojmenované hodnoty představují bezpečné místo pro správu hodnot konstantních řetězců, včetně tajných kódů napříč všemi konfiguracemi a zásadami rozhraní API, takže je nemusíte ukládat přímo do příkazů zásad. Další informace najdete v tématu [použití pojmenovaných hodnot v zásadách Azure API Management](api-management-howto-properties.md).
>
>

Informace o povolení nebo zakázání přístupu k Gitu pomocí REST API najdete v tématu [Povolení nebo zakázání přístupu k Gitu pomocí REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Uložení konfigurace služby do úložiště Git

Prvním krokem před klonováním úložiště je uložení aktuálního stavu konfigurace služby do úložiště. Klikněte na **Uložit do úložiště**.

Proveďte požadované změny na obrazovce s potvrzením a kliknutím na **Uložit** uložte.

Po chvíli se konfigurace uloží a zobrazí se stav konfigurace úložiště, včetně data a času poslední změny konfigurace a poslední synchronizace mezi konfigurací služby a úložištěm.

Po uložení konfigurace do úložiště je lze klonovat.

Informace o provedení této operace pomocí REST API najdete v tématu [potvrzení konfigurace snímku pomocí REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Naklonování úložiště do místního počítače

K naklonování úložiště potřebujete adresu URL vašeho úložiště, uživatelské jméno a heslo. Pokud chcete získat uživatelské jméno a další přihlašovací údaje, klikněte na **přihlašovací údaje pro přístup** v horní části stránky.

Pokud chcete vygenerovat heslo, nejdřív zajistěte, aby **konec platnosti** byl nastavený na požadované datum a čas vypršení platnosti, a pak klikněte na **vygenerovat**.

> [!IMPORTANT]
> Toto heslo si poznamenejte. Po opuštění této stránky se heslo znovu nezobrazí.
>

V následujících příkladech se používá nástroj Git bash z [Gitu pro Windows](https://www.git-scm.com/downloads) , ale můžete použít libovolný nástroj Git, který znáte.

Otevřete nástroj Git v požadované složce a spuštěním následujícího příkazu naklonujte úložiště Git do místního počítače pomocí příkazu, který poskytuje Azure Portal.

```
git clone https://{name}.scm.azure-api.net/
```

Po zobrazení výzvy zadejte uživatelské jméno a heslo.

Pokud se zobrazí nějaké chyby, zkuste upravit `git clone` příkaz tak, aby zahrnoval uživatelské jméno a heslo, jak je znázorněno v následujícím příkladu.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Pokud se zobrazí chyba, zkuste použít adresu URL, která bude v příkazu zakódovat heslo. Jedním z rychlých způsobů, jak to provést, je otevřít Visual Studio a vydat následující příkaz v **příkazovém podokně**. Chcete-li otevřít **okno okamžité**, otevřete jakékoli řešení nebo projekt v aplikaci Visual Studio (nebo vytvořte novou prázdnou konzolovou aplikaci) **a v nabídce** **ladění** vyberte možnost **Windows**.

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

K vytvoření příkazu git použijte zakódované heslo spolu s vaším uživatelským jménem a umístěním úložiště.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Po naklonování úložiště můžete v místním systému souborů zobrazit a pracovat s ním. Další informace najdete v tématu [Referenční dokumentace struktury souborů a složek místního úložiště Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aktualizace místního úložiště s nejaktuálnější konfigurací instance služby

Pokud provedete změny v instanci služby API Management v Azure Portal nebo pomocí REST API, musíte tyto změny uložit do úložiště, aby bylo možné aktualizovat místní úložiště s nejnovějšími změnami. Provedete to tak, že na kartě **úložiště** v Azure Portal kliknete na **Uložit na úložiště** a pak na svém místním úložišti vydáte následující příkaz.

```
git pull
```

Před spuštěním se `git pull` ujistěte, že jste ve složce pro vaše místní úložiště. Pokud jste příkaz právě dokončili `git clone` , musíte ho změnit na své úložiště spuštěním příkazu podobného následujícímu.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Vložení změn z místního úložiště do úložiště serveru
Chcete-li odeslat změny z místního úložiště do úložiště serveru, musíte potvrdit změny a pak je odeslat do úložiště serveru. Potvrďte provedené změny tak, že otevřete příkazový nástroj Git, přejdete do adresáře místního úložiště a vydáte následující příkazy.

```
git add --all
git commit -m "Description of your changes"
```

Chcete-li vložit všechna potvrzení na server, spusťte následující příkaz.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Nasazení jakýchkoli změn konfigurace služby API Management instance služby

Po potvrzení místních změn a jejich vložení do úložiště serveru je můžete nasadit do své instance služby API Management.

Informace o tom, jak tuto operaci provést pomocí REST API, najdete v tématu [nasazení změn Git do konfigurační databáze pomocí REST API](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referenční dokumentace struktury souborů a složek místního úložiště Git

Soubory a složky v místním úložišti Git obsahují informace o konfiguraci instance služby.

| Položka | Popis |
| --- | --- |
| Kořenová složka API – Správa |Obsahuje konfiguraci nejvyšší úrovně pro instanci služby. |
| Složka rozhraní API |Obsahuje konfiguraci pro rozhraní API v instanci služby. |
| Složka skupin |Obsahuje konfiguraci pro skupiny v instanci služby. |
| Složka zásad |Obsahuje zásady v instanci služby. |
| portalStyles složka |Obsahuje konfiguraci pro přizpůsobení portálu pro vývojáře v instanci služby. |
| Složka Products |Obsahuje konfiguraci pro produkty v instanci služby. |
| Složka šablon |Obsahuje konfiguraci pro e-mailové šablony v instanci služby. |

Každá složka může obsahovat jeden nebo více souborů, a v některých případech jednu nebo více složek, například složku pro každé rozhraní API, produkt nebo skupinu. Soubory v jednotlivých složkách jsou specifické pro typ entity, který je popsaný v názvu složky.

| Typ souboru | Účel |
| --- | --- |
| json |Konfigurační informace o příslušné entitě |
| html |Popis entity, který se často zobrazuje na portálu pro vývojáře |
| xml |Příkazy zásad |
| css |Šablony stylů pro přizpůsobení portálu pro vývojáře |

Tyto soubory se dají vytvářet, odstraňovat, upravovat a spravovat v místním systému souborů a změny se nasazují zpátky do vaší instance služby API Management.

> [!NOTE]
> Následující entity nejsou obsaženy v úložišti Git a nelze je konfigurovat pomocí Gitu.
>
> * [Uživatelé](/rest/api/apimanagement/2019-12-01/user)
> * [Předplatná](/rest/api/apimanagement/2019-12-01/subscription)
> * Pojmenované hodnoty
> * Jiné entity portálu pro vývojáře než styly
>

### <a name="root-api-management-folder"></a>Kořenová složka API – Správa
Kořenová `api-management` Složka obsahuje `configuration.json` soubor, který obsahuje informace nejvyšší úrovně o instanci služby v následujícím formátu.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

První čtyři nastavení ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` a) se `UserRegistrationTermsConsentRequired` mapují na následující nastavení na kartě **identity** v části **portál pro vývojáře** .

| Nastavení identity | Mapuje na |
| --- | --- |
| RegistrationEnabled |Přítomnost poskytovatele identity **uživatelského jména a hesla** |
| UserRegistrationTerms |**Podmínky použití v uživatelském poli pro registraci uživatele** |
| UserRegistrationTermsEnabled |Zaškrtávací políčko **pro zobrazení podmínek použití na přihlašovací stránce** |
| UserRegistrationTermsConsentRequired |Zaškrtávací políčko **vyžadovat souhlas** |
| RequireUserSigninEnabled |Zaškrtávací políčko **pro přesměrování anonymních uživatelů na přihlašovací stránku** |

Další čtyři nastavení ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` a) se `DelegationValidationKey` mapují na následující nastavení na kartě **delegování** v části **portál pro vývojáře** .

| Nastavení delegování | Mapuje na |
| --- | --- |
| DelegationEnabled |**Delegovat přihlášení &** přihlašovací políčko |
| DelegationUrl |Textové pole **adresy URL koncového bodu delegování** |
| DelegatedSubscriptionEnabled |**Delegovat předplatné produktu** – zaškrtávací políčko |
| DelegationValidationKey |Textové pole pro **ověřovací klíč delegáta** |

Poslední nastavení, `$ref-policy` mapuje na soubor globálních příkazů zásad pro instanci služby.

### <a name="apis-folder"></a>Složka rozhraní API
`apis`Složka obsahuje složku pro každé rozhraní API v instanci služby, která obsahuje následující položky.

* `apis\<api name>\configuration.json` – Jedná se o konfiguraci rozhraní API a obsahuje informace o adrese URL back-end služby a operacích. Jedná se o stejné informace, které by byly vráceny v případě, že jste volali funkci [získat konkrétní rozhraní API](/rest/api/apimanagement/2019-12-01/apis/get) `export=true` ve `application/json` formátu.
* `apis\<api name>\api.description.html` – Toto je popis rozhraní API a odpovídá `description` vlastnosti [entity rozhraní API](/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\` – Tato složka obsahuje `<operation name>.description.html` soubory, které se mapují na operace v rozhraní API. Každý soubor obsahuje popis jedné operace v rozhraní API, která se mapuje na `description` vlastnost [entity operace](/rest/api/visualstudio/operations/list#operationproperties) v REST API.

### <a name="groups-folder"></a>Složka skupin
`groups`Složka obsahuje složku pro každou skupinu definovanou v instanci služby.

* `groups\<group name>\configuration.json` – Jedná se o konfiguraci skupiny. Jedná se o stejné informace, které by se vracely v případě, že jste volali operaci [získat konkrétní skupinu](/rest/api/apimanagement/2019-12-01/group/get) .
* `groups\<group name>\description.html` – Toto je popis skupiny a odpovídá `description` vlastnosti [entity Group](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>Složka zásad
`policies`Složka obsahuje příkazy zásad pro instanci služby.

* `policies\global.xml` -obsahuje zásady definované v globálním oboru pro instanci služby.
* `policies\apis\<api name>\` – Pokud máte v oboru rozhraní API definované nějaké zásady, jsou obsažené v této složce.
* `policies\apis\<api name>\<operation name>\` Složka – Pokud máte v oboru operací definované nějaké zásady, jsou obsažené v této složce v `<operation name>.xml` souborech, které se mapují na příkazy zásad pro jednotlivé operace.
* `policies\products\` – Pokud máte v oboru produktů definované nějaké zásady, jsou obsažené v této složce, která obsahuje `<product name>.xml` soubory, které se mapují na příkazy zásad pro každý produkt.

### <a name="portalstyles-folder"></a>portalStyles složka
`portalStyles`Složka obsahuje konfiguraci a šablony stylů pro přizpůsobení portálu pro vývojáře pro instanci služby.

* `portalStyles\configuration.json` -obsahuje názvy šablon stylů používaných portálem pro vývojáře.
* `portalStyles\<style name>.css` – Každý `<style name>.css` soubor obsahuje styly pro portál pro vývojáře ( `Preview.css` a `Production.css` ve výchozím nastavení).

### <a name="products-folder"></a>Složka Products
`products`Složka obsahuje složku pro každý produkt definovaný v instanci služby.

* `products\<product name>\configuration.json` – Jedná se o konfiguraci produktu. Jedná se o stejné informace, které by se vracely v případě, že jste volali operaci [získat konkrétní produkt](/rest/api/apimanagement/2019-12-01/product/get) .
* `products\<product name>\product.description.html` – Toto je popis produktu a odpovídá `description` vlastnosti [entity produktu](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) v REST API.

### <a name="templates"></a>šablony
`templates`Složka obsahuje konfiguraci pro [e-mailové šablony](api-management-howto-configure-notifications.md) instance služby.

* `<template name>\configuration.json` – Jedná se o konfiguraci e-mailové šablony.
* `<template name>\body.html` – Jedná se o tělo e-mailové šablony.

## <a name="next-steps"></a>Další kroky
Další informace o dalších způsobech správy instance služby najdete v následujících tématech:

* Pomocí následujících rutin PowerShellu spravujte instanci služby.
  * [Reference k rutinám prostředí PowerShell pro nasazení služeb](/powershell/module/wds)
  * [Reference k rutinám prostředí PowerShell pro správu služeb](/powershell/azure/servicemanagement/overview)
* Správa instance služby pomocí REST API
  * [Odkaz na API Management REST API](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
