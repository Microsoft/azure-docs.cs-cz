---
title: Konfigurace služby api management pomocí Gitu – Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak uložit a nakonfigurovat konfiguraci služby API Management pomocí Gitu.
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
ms.openlocfilehash: 9bbd62bc05e03641c2abe9308d9238bef23877c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71104970"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Uložení a konfigurace nastavení služby API Management pomocí Gitu

Každá instance služby API Management udržuje konfigurační databázi, která obsahuje informace o konfiguraci a metadata pro instanci služby. Změny lze provést v instanci služby změnou nastavení na webu Azure Portal, pomocí rutiny prostředí PowerShell nebo volání rozhraní REST API. Kromě těchto metod můžete také spravovat konfiguraci instance služby pomocí Gitu a povolit scénáře správy služeb, jako jsou:

* Správa verzí konfigurace – stažení a uložení různých verzí konfigurace služby
* Hromadné změny konfigurace – proveďte změny ve více částech konfigurace služby v místním úložišti a integrujte je zpět na server pomocí jediné operace
* Známý řetězec nástrojů a pracovní postup Gitu – použijte nástroje Git a pracovní postupy, které již znáte

Následující diagram znázorňuje přehled různých způsobů konfigurace instance služby Api Management.

![Konfigurace Gitu][api-management-git-configure]

Když provedete změny ve službě pomocí portálu Azure, rutin prostředí PowerShell nebo rozhraní REST API, spravujete konfigurační databázi služeb pomocí koncového `https://{name}.management.azure-api.net` bodu, jak je znázorněno na pravé straně diagramu. Na levé straně diagramu je znázorněno, jak můžete spravovat konfiguraci služby `https://{name}.scm.azure-api.net`pomocí Git a Git úložiště pro vaši službu umístěnou na adrese .

Následující kroky poskytují přehled správy instance služby API Management pomocí Gitu.

1. Přístup ke konfiguraci Gitu ve vaší službě
2. Uložení konfigurační databáze služby do úložiště Git
3. Klonování úložiště Git do místního počítače
4. Stáhněte nejnovější repo dolů do místního počítače a potvrdíte a zasuňte změny zpět do svého repo
5. Nasazení změn z repo do konfigurační databáze služby

Tento článek popisuje, jak povolit a používat Git ke správě konfigurace služby a poskytuje odkaz na soubory a složky v úložišti Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Přístup ke konfiguraci Gitu ve vaší službě

Chcete-li zobrazit a nakonfigurovat nastavení konfigurace Gitu, můžete kliknout na nabídku **Zabezpečení** a přejít na kartu **Úložiště konfigurace.**

![Povolit GIT][api-management-enable-git]

> [!IMPORTANT]
> Všechny tajné klíče, které nejsou definovány jako pojmenované hodnoty, budou uloženy v úložišti a zůstanou v jeho historii, dokud nezakážete a znovu nepovolíte přístup gitu. Pojmenované hodnoty poskytují bezpečné místo pro správu konstantní řetězcové hodnoty, včetně tajných kódů, napříč všemi konfigurace rozhraní API a zásady, takže není třeba ukládat přímo v příkazech zásad. Další informace najdete [v tématu Jak používat pojmenované hodnoty v zásadách správy rozhraní API Azure](api-management-howto-properties.md).
>
>

Informace o povolení nebo zakázání přístupu Gitpomocí rozhraní REST API najdete v tématu [Povolení nebo zakázání přístupu gitu pomocí rozhraní REST API](/rest/api/apimanagement/2019-01-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Uložení konfigurace služby do úložiště Git

Prvním krokem před klonováním úložiště je uložení aktuálního stavu konfigurace služby do úložiště. Klepněte **na tlačítko Uložit do úložiště**.

Proveďte požadované změny na potvrzovací obrazovce a klepnutím na **tlačítko Ok** je uložte.

Po několika okamžicích je konfigurace uložena a zobrazí se stav konfigurace úložiště, včetně data a času poslední změny konfigurace a poslední synchronizace mezi konfigurací služby a úložištěm.

Jakmile je konfigurace uložena do úložiště, může být klonována.

Informace o provádění této operace pomocí rozhraní REST API naleznete v tématu [Potvrzení snímku konfigurace pomocí rozhraní REST API](/rest/api/apimanagement/2019-01-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Klonování úložiště do místního počítače

Chcete-li naklonovat úložiště, potřebujete adresu URL úložiště, uživatelské jméno a heslo. Chcete-li získat uživatelské jméno a další pověření, klikněte na **přihlašovací údaje aplikace Access** v horní části stránky.

Chcete-li vygenerovat heslo, **nejprve zkontrolujte,** zda je doba platnosti nastavena na požadované datum a čas vypršení platnosti, a klepněte na tlačítko **Generovat**.

> [!IMPORTANT]
> Poznamenejte si toto heslo. Jakmile opustíte tuto stránku, heslo se znovu nezobrazí.
>

Následující příklady používají nástroj Git Bash z [Gitu pro Windows,](https://www.git-scm.com/downloads) ale můžete použít libovolný nástroj Git, který znáte.

Otevřete nástroj Git v požadované složce a spusťte následující příkaz pro klonování úložiště git do místního počítače pomocí příkazu poskytnutého portálem Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Po zobrazení výzvy zadejte uživatelské jméno a heslo.

Pokud se zobrazí chyby, `git clone` zkuste upravit příkaz tak, aby obsahoval uživatelské jméno a heslo, jak je znázorněno v následujícím příkladu.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Pokud to poskytuje chybu, zkuste URL kódování heslo část příkazu. Jedním z rychlých způsobů, jak to provést, je otevřít visual studio a vydat následující příkaz v **okně okamžité**. Chcete-li otevřít **okamžité okno**, otevřete libovolné řešení nebo projekt v sadě Visual Studio (nebo vytvořte novou prázdnou konzolovou aplikaci) a z nabídky **Ladění** zvolte **Windows**, **Okamžité.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

K vytvoření příkazu git použijte kódované heslo spolu s vaším uživatelským jménem a umístěním úložiště.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Jakmile je úložiště klonováno, můžete jej zobrazit a pracovat s ním v místním systému souborů. Další informace naleznete v [tématu Odkaz na strukturu souborů a složek místního úložiště Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aktualizace místního úložiště pomocí nejaktuálnější konfigurace instance služby

Pokud provedete změny instance služby API Management na webu Azure Portal nebo pomocí rozhraní REST API, musíte tyto změny uložit do úložiště, než budete moci aktualizovat místní úložiště nejnovějšími změnami. Chcete-li to provést, klikněte na tlačítko **Uložit konfiguraci do úložiště** na kartě Úložiště **konfigurace** na webu Azure portal a pak vmístním úložišti vydejte následující příkaz.

```
git pull
```

Před `git pull` spuštěním se ujistěte, že jste ve složce pro místní úložiště. Pokud jste právě `git clone` dokončili příkaz, potom je nutné změnit adresář na úložiště spuštěním příkazu, jako je následující.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Chcete-li na server uložit změny z místního repo,
Chcete-li vysunout změny z místního úložiště do úložiště serveru, je nutné potvrdit změny a potom je zasunou do úložiště serveru. Chcete-li potvrdit změny, otevřete příkazový nástroj Git, přepněte do adresáře místního úložiště a vydejte následující příkazy.

```
git add --all
git commit -m "Description of your changes"
```

Chcete-li všechny revize převést na server, spusťte následující příkaz.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Nasazení změn konfigurace služby do instance služby API Management

Jakmile jsou místní změny potvrzeny a zatlačeny do úložiště serveru, můžete je nasadit do instance služby API Management.

Informace o provádění této operace pomocí rozhraní REST API naleznete v tématu [Nasazení změn Gitu do konfigurační databáze pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odkaz na strukturu souborů a složek místního úložiště Git

Soubory a složky v místním úložišti git obsahují informace o konfiguraci instance služby.

| Položka | Popis |
| --- | --- |
| kořenová složka api-management |Obsahuje konfiguraci nejvyšší úrovně pro instanci služby. |
| složka apis |Obsahuje konfiguraci pro apis v instanci služby. |
| složka skupiny |Obsahuje konfiguraci pro skupiny v instanci služby. |
| složka zásad |Obsahuje zásady v instanci služby. |
| složka portalStyles |Obsahuje konfiguraci pro vlastní nastavení portálu pro vývojáře v instanci služby. |
| složka produktů |Obsahuje konfiguraci produktů v instanci služby. |
| složka šablony |Obsahuje konfiguraci pro šablony e-mailů v instanci služby. |

Každá složka může obsahovat jeden nebo více souborů a v některých případech jednu nebo více složek, například složku pro každé rozhraní API, produkt nebo skupinu. Soubory v každé složce jsou specifické pro typ entity popsaný názvem složky.

| Typ souboru | Účel |
| --- | --- |
| json |Informace o konfiguraci příslušné entity |
| html |Popisy entity, často zobrazené na portálu pro vývojáře |
| xml |Příkazy zásad |
| css |Šablony stylů pro přizpůsobení portálu pro vývojáře |

Tyto soubory lze vytvářet, odstraňovat, upravovat a spravovat v místním systému souborů a změny nasazené zpět do instance služby Správa rozhraní API.

> [!NOTE]
> Následující entity nejsou obsaženy v úložišti Git a nelze je konfigurovat pomocí Gitu.
>
> * [Uživatelé](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user)
> * [Předplatná](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription)
> * [Pojmenované hodnoty](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/property)
> * Entity portálu pro vývojáře jiné než styly
>

### <a name="root-api-management-folder"></a>Kořenová složka pro správu rozhraní API
Kořenová `api-management` složka `configuration.json` obsahuje soubor, který obsahuje informace nejvyšší úrovně o instanci služby v následujícím formátu.

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

První čtyři nastavení`RegistrationEnabled` `UserRegistrationTerms`( `UserRegistrationTermsEnabled`, `UserRegistrationTermsConsentRequired`, , a ) mapovat na následující nastavení na kartě **Identity** v části **Zabezpečení.**

| Nastavení identity | Mapy na |
| --- | --- |
| RegistrationEnabled |Přítomnost poskytovatele **uživatelského jména a** identity hesla |
| UserRegistrationTerms |**Podmínky použití v textovém** poli registrace uživatele |
| UserRegistrationTermsEnabled |**Zaškrtávací políčko Zobrazit podmínky použití na přihlašovací stránce** |
| UserRegistrationTermsConsentVyžadováno |**Zaškrtávací** políčko Vyžadovat souhlas |
| RequireUserSigninEnabled |**Zaškrtávací** políčko Přesměrovat anonymní uživatele na přihlašovací stránku |

Další čtyři nastavení`DelegationEnabled` `DelegationUrl`( `DelegatedSubscriptionEnabled`, `DelegationValidationKey`, , a ) jsou na mapěna na následující nastavení na kartě **Delegování** v části **Zabezpečení.**

| Nastavení delegování | Mapy na |
| --- | --- |
| Povoleno delegování |**Zaškrtávací** políčko & přihlášení delegáta |
| Adresa DelegaceUrl |Textové pole **URL koncového bodu delegování** |
| DelegatedSubscriptionEnabled |**Zaškrtávací** políčko Delegovat předplatné produktu |
| Ověřovací klíč delegationlegování |Textové pole **ověřovacího klíče delegáta** |

Konečné nastavení `$ref-policy`, mapuje na soubor globálních příkazů zásad pro instanci služby.

### <a name="apis-folder"></a>složka apis
Složka `apis` obsahuje složku pro každé rozhraní API v instanci služby, která obsahuje následující položky.

* `apis\<api name>\configuration.json`- toto je konfigurace rozhraní API a obsahuje informace o adrese URL back-endové služby a operacích. Jedná se o stejné informace, které by byly vráceny, `application/json` pokud byste měli volat Získat konkrétní rozhraní [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apis/get) s `export=true` ve formátu.
* `apis\<api name>\api.description.html`- toto je popis ROZHRANÍ API `description` a odpovídá vlastnosti [entity API](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\`- tato `<operation name>.description.html` složka obsahuje soubory, které mapují na operace v API. Každý soubor obsahuje popis jedné operace v rozhraní API, která se mapuje na `description` vlastnost entity [operace](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) v rozhraní REST API.

### <a name="groups-folder"></a>složka skupiny
Složka `groups` obsahuje složku pro každou skupinu definovanou v instanci služby.

* `groups\<group name>\configuration.json`- toto je konfigurace skupiny. Jedná se o stejné informace, které by byly vráceny, pokud byste měli volat [získat konkrétní skupiny](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/group/get) operace.
* `groups\<group name>\description.html`- toto je popis skupiny a `description` odpovídá vlastnosti [subjektu skupiny](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>složka zásad
Složka `policies` obsahuje příkazy zásad pro instanci služby.

* `policies\global.xml`- obsahuje zásady definované v globálním oboru pro instanci služby.
* `policies\apis\<api name>\`- Pokud máte nějaké zásady definované v oboru rozhraní API, jsou obsaženy v této složce.
* `policies\apis\<api name>\<operation name>\`složka - pokud máte nějaké zásady definované v oboru operace, jsou obsaženy v této složce v `<operation name>.xml` souborech, které jsou mapovány na příkazy zásad pro každou operaci.
* `policies\products\`- Pokud máte nějaké zásady definované v oboru produktu, jsou `<product name>.xml` obsaženy v této složce, která obsahuje soubory, které jsou mapovány na příkazy zásad pro každý produkt.

### <a name="portalstyles-folder"></a>složka portalStyles
Složka `portalStyles` obsahuje konfigurace a šablony stylů pro vlastní nastavení portálu pro vývojáře pro instanci služby.

* `portalStyles\configuration.json`- obsahuje názvy šablon stylů používaných portálem pro vývojáře
* `portalStyles\<style name>.css`- `<style name>.css` každý soubor obsahuje styly`Preview.css` `Production.css` pro vývojářský portál ( a ve výchozím nastavení).

### <a name="products-folder"></a>složka produktů
Složka `products` obsahuje složku pro každý produkt definovaný v instanci služby.

* `products\<product name>\configuration.json`- toto je konfigurace produktu. Jedná se o stejné informace, které by byly vráceny, pokud byste měli volat [získat konkrétní](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/product/get) produkt operace.
* `products\<product name>\product.description.html`- toto je popis produktu a `description` odpovídá vlastnosti [entity produktu](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) v rozhraní REST API.

### <a name="templates"></a>šablony
Složka `templates` obsahuje konfiguraci pro [e-mailové šablony instance služby.](api-management-howto-configure-notifications.md)

* `<template name>\configuration.json`- toto je konfigurace pro šablonu e-mailu.
* `<template name>\body.html`- toto je tělo šablony e-mailu.

## <a name="next-steps"></a>Další kroky
Informace o dalších způsobech správy instance služby najdete v těchto tématech:

* Správa instance služby pomocí následujících rutin prostředí PowerShell
  * [Reference k rutinám prostředí PowerShell pro nasazení služeb](https://docs.microsoft.com/powershell/module/wds)
  * [Reference k rutinám prostředí PowerShell pro správu služeb](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Správa instance služby pomocí rozhraní REST API
  * [Odkaz na rozhraní REST API pro správu rozhraní API rozhraní API rozhraní API rozhraní API](/rest/api/apimanagement/)


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




