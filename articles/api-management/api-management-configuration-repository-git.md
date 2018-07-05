---
title: Konfigurace služby API Management pomocí Git – Azure | Dokumentace Microsoftu
description: Zjistěte, jak uložit a nakonfigurujte konfigurace služby API Management pomocí Gitu.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 87381a27a59d6719f4bfff9ed367598932c1985c
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436399"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Uložit a konfigurace konfigurace služby API Management pomocí Gitu

Každá instance služby API Management udržuje konfigurační databáze, který obsahuje informace o konfiguraci a metadat pro instanci služby. Můžete provést změny k instanci služby změnou nastavení na portálu Azure portal, pomocí rutiny Powershellu nebo volání rozhraní REST API. Kromě těchto metod můžete také spravovat konfigurace instance služby pomocí Gitu, jako například povolení scénářů správy služby:

* Konfigurace správy verzí – stažení a uložení různých verzích konfigurace služby
* Hromadné změny konfigurace – provádět změny více částí konfigurace služby ve vašem místním úložišti a integrovat změny na server v jedné operaci
* Známých nástrojů Git a pracovního postupu – pomocí nástroje Git a pracovní postupy, které jste už obeznámení s

Následující diagram znázorňuje základní informace o různých způsobech konfigurace vaší instance služby API Management.

![Konfigurace Gitu][api-management-git-configure]

Pokud provedete změny do svojí služby pomocí webu Azure portal, rutin prostředí PowerShell nebo rozhraní REST API, které spravujete vaší služby konfigurace databáze pomocí `https://{name}.management.azure-api.net` koncový bod, jak je znázorněno na pravé straně diagramu. Levé straně diagramu znázorňuje, jak můžete spravovat pomocí nástroje Git konfigurace služby a umístění úložiště Git pro vaši službu `https://{name}.scm.azure-api.net`.

Následující kroky obsahují přehled správy vaší instance služby API Management pomocí Gitu.

1. Konfigurace přístupu Git ve službě
2. Uložení konfigurační databáze služby do úložiště Git
3. Naklonujte úložiště Git do místního počítače
4. Stáhněte nejnovější úložiště na místním počítači a potvrzení a nasdílení změn změny zpět do úložiště
5. Změny v úložišti nasadí do konfigurační databáze služby

Tento článek popisuje, jak povolit a používat Git ke správě konfigurace služby a poskytuje odkaz pro soubory a složky v úložišti Git.

## <a name="access-git-configuration-in-your-service"></a>Konfigurace přístupu Git ve službě

Pokud chcete zobrazit a konfigurovat nastavení konfigurace Git, můžete kliknout na **zabezpečení** nabídky a přejděte **konfigurace úložiště** kartu.

![Povolit GIT][api-management-enable-git]

> [!IMPORTANT]
> Všechny tajné klíče, které nejsou definovány vlastnosti bude uložen v úložišti a zůstanou v historii do můžete zakázat a znovu povolit přístup Git. Vlastnosti poskytují zabezpečené místo, kde můžete spravovat konstantní hodnoty řetězce, včetně tajné kódy, přes všechny zásady a konfigurace rozhraní API, takže není nutné pro ukládání přímo v příkazech vaše zásady. Další informace najdete v tématu [jak používat vlastnosti v Azure API Management zásady](api-management-howto-properties.md).
> 
> 

Informace o povolení nebo zakázání přístupu Git pomocí rozhraní REST API najdete v tématu [povolit nebo zakázat Git přístup pomocí rozhraní REST API](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Chcete-li uložit konfiguraci služby do úložiště Git

Prvním krokem před klonováním úložiště je uložit aktuální stav konfigurace služby úložiště. Klikněte na tlačítko **uložit do úložiště**.

Proveďte požadované změny na potvrzovací obrazovce a klikněte na tlačítko **Ok** uložte.

Po chvíli se uložit konfiguraci a stavu konfigurace příslušného úložiště se zobrazí, včetně datum a čas poslední změny konfigurace a poslední synchronizace mezi konfiguraci služby a úložiště.

Po uložení konfigurace do úložiště můžete klonovat.

Informace o provedení této operace pomocí rozhraní REST API najdete v tématu [potvrzení konfigurace snímku pomocí rozhraní REST API](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Naklonujte úložiště do svého místního počítače

Klonování úložiště, musíte adresu URL úložiště, uživatelské jméno a heslo. Pokud chcete získat uživatelské jméno a jiné přihlašovací údaje, klikněte na **přístup k přihlašovacím údajům** v horní části stránky.  
 
Vytvořit heslo, nejprve zkontrolujte, zda **vypršení platnosti** je nastavena na vypršení platnosti požadované datum a čas a potom klikněte na **generovat**.

> [!IMPORTANT]
> Toto heslo si poznamenejte. Po opuštění této stránky heslo znovu nezobrazí.
> 

Následující příklady používají nástroje Git Bash [Git pro Windows](http://www.git-scm.com/downloads) ale můžete použít libovolný nástroj Gitu, který jste obeznámeni s.

Otevřete nástroj Gitu do požadované složky a spusťte následující příkaz a naklonujte úložiště git do svého místního počítače pomocí příkazu poskytované na webu Azure portal.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Zadejte uživatelské jméno a heslo po zobrazení výzvy.

Pokud se zobrazí nějaké chyby, zkuste upravit vaše `git clone` příkazu zahrnovat uživatelské jméno a heslo, jak je znázorněno v následujícím příkladu.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Pokud to poskytuje k chybě, zkuste použít kódování heslo část příkazu. Jeden rychlý způsob, jak to udělat je spuštění sady Visual Studio a vydejte následující příkaz v **podokna**. Chcete-li otevřít **podokna**otevřete libovolné řešení nebo projektu v sadě Visual Studio (nebo vytvořte novou prázdnou aplikaci) a zvolte možnost **Windows**, **okamžité** z **Ladění** nabídky.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Použijte zakódované heslo spolu s uživatelské jméno a úložiště umístění k vytvoření příkazu git.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Po naklonování úložiště můžete zobrazit a pracovat v místním systému souborů. Další informace najdete v tématu [souborům a složkám struktury odkaz na místní úložiště Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>K aktualizaci vašeho místního úložiště s nejaktuálnější konfiguraci instance služby

Pokud provedete změny instance služby API Management na webu Azure portal nebo pomocí rozhraní REST API, musíte tyto změny uložit do úložiště než budete moct s nejnovějšími změnami aktualizovat místní úložiště. Chcete-li to provést, klikněte na tlačítko **uložit konfiguraci do úložiště** na **konfigurace úložiště** kartu na webu Azure Portal a potom vydejte následující příkaz v místním úložišti.

```
git pull
```

Před spuštěním `git pull` Ujistěte se, že jste ve složce pro vaše místní úložiště. Pokud právě jste dokončili `git clone` příkaz, pak musí změňte adresář na úložišti můžete přecházet pomocí příkazu následujícím postupem.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>K replikaci změn z místního úložiště na server úložiště
Pokud chcete nasdílet změny ze svého místního úložiště do úložiště serveru, musíte potvrzení změn a jejich následnému doručování do serverové úložiště. Potvrďte provedené změny, otevřete nástroj příkaz Git, přejděte do adresáře vašeho místního úložiště a tyto příkazy.

```
git add --all
git commit -m "Description of your changes"
```

Pro všechna potvrzení nasdílet na server, spusťte následující příkaz.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Nasazení změny konfigurace služby do instance služby API Management

Jakmile jsou vaše místní změny potvrdíte a nabídnete do úložiště serveru, je můžete nasadit do vaší instance služby API Management.

Informace o provedení této operace pomocí rozhraní REST API najdete v tématu [nasazení Git změn konfigurace databáze pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odkaz na strukturu souborů a složek z místního úložiště Git

Soubory a složky v úložišti místního gitu obsahují informace o konfiguraci o instanci služby.

| Položka | Popis |
| --- | --- |
| Kořenová složka api management |Obsahuje konfiguraci nejvyšší úrovně pro instance služby |
| rozhraní API složky |Obsahuje konfiguraci pro rozhraní API v instanci služby |
| složka skupiny |Obsahuje konfiguraci pro skupiny v instanci služby |
| Složka zásad |Obsahuje zásady v instanci služby |
| portalStyles složky |Obsahuje konfiguraci pro přizpůsobení portálu pro vývojáře v instanci služby |
| Složka produkty |Obsahuje konfiguraci pro produkty v instanci služby |
| Složka šablony |Obsahuje konfiguraci pro e-mailové šablony v instanci služby |

Všechny složky, může obsahovat jeden nebo více souborů a v některých případech jedné nebo více složek, například do složky pro každé rozhraní API, produktů nebo skupinu. Soubory v rámci každé složky jsou specifická pro typ entity popsány podle názvu složky.

| Typ souboru | Účel |
| --- | --- |
| json |Informace o konfiguraci o příslušné entity |
| HTML |Popis entity, často zobrazují na portálu pro vývojáře |
| xml |Příkazy zásad |
| šablony stylů CSS |Šablony stylů pro přizpůsobení portálu pro vývojáře |

Tyto soubory lze vytvořit, odstranit, upravit a spravovat na vašem místním systému souborů a nasazení změn zpět vaší instance služby API Management.

> [!NOTE]
> Tyto entity nejsou obsaženy v úložišti Git a nedá se konfigurovat pomocí Gitu.
> 
> * Uživatelé
> * Předplatná
> * Vlastnosti
> * Entity portálu pro vývojáře než styly
> 

### <a name="root-api-management-folder"></a>Kořenová složka api management
Kořen `api-management` obsahuje složku `configuration.json` soubor, který obsahuje informace na nejvyšší úrovni o instanci služby v následujícím formátu.

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
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

První čtyři nastavení (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, a `UserRegistrationTermsConsentRequired`) mapovat na následující nastavení na **identit** kartu **zabezpečení** oddílu.

| Nastavení identity | Mapuje se na |
| --- | --- |
| RegistrationEnabled |**Přesměrovat anonymní uživatele na přihlašovací stránce** zaškrtávací políčko |
| UserRegistrationTerms |**Podmínky použití při registraci uživatele** textové pole |
| UserRegistrationTermsEnabled |**Zobrazit podmínky použití na registrační stránce** zaškrtávací políčko |
| UserRegistrationTermsConsentRequired |**Vyžadovat souhlas** zaškrtávací políčko |

Další čtyři nastavení (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, a `DelegationValidationKey`) mapovat na následující nastavení na **delegování** kartu **zabezpečení** oddílu.

| Nastavení delegování | Mapuje se na |
| --- | --- |
| DelegationEnabled |**Delegovat přihlášení a registraci** zaškrtávací políčko |
| DelegationUrl |**Adresa URL koncového bodu delegace** textové pole |
| DelegatedSubscriptionEnabled |**Delegovat předplatné produktu** zaškrtávací políčko |
| DelegationValidationKey |**Delegovat ověřovací klíč** textové pole |

Nastavení konečného `$ref-policy`, mapuje příkazy soubor globálních zásad pro instance služby.

### <a name="apis-folder"></a>rozhraní API složky
`apis` Složka obsahuje složku pro každé rozhraní API v instanci služby, který obsahuje následující položky.

* `apis\<api name>\configuration.json` – To je uvedena konfigurace pro rozhraní API a obsahuje informace o adresu URL back-endové služby a operace. Toto je stejné informace, které by byly vráceny, pokud byste chtěli volání [konkrétní rozhraní API](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/get) s `export=true` v `application/json` formátu.
* `apis\<api name>\api.description.html` – Toto je popis rozhraní API a odpovídá `description` vlastnost [rozhraní API entity](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` – Tato složka obsahuje `<operation name>.description.html` soubory, které se mapují na operace v rozhraní API. Každý soubor obsahuje popis jedné operace v rozhraní API, která se mapuje `description` vlastnost [entity operace](https://docs.microsoft.com/en-us/rest/api/visualstudio/operations/list#operationproperties) v rozhraní REST API.

### <a name="groups-folder"></a>složka skupiny
`groups` Složka obsahuje složku pro jednotlivé skupiny definované v instanci služby.

* `groups\<group name>\configuration.json` – Jedná se o konfiguraci pro skupinu. Toto je stejné informace, které by byly vráceny, pokud byste chtěli volání [získání konkrétní skupiny](https://docs.microsoft.com/en-us/rest/api/apimanagement/group/get) operace.
* `groups\<group name>\description.html` – Toto je popis skupiny a odpovídá `description` vlastnost [skupiny entit](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>Složka zásad
`policies` Složka obsahuje příkazy zásad vaší instance služby.

* `policies\global.xml` -obsahuje zásady definované v globálním oboru vaší instance služby.
* `policies\apis\<api name>\` – Pokud máte jakékoli zásady definované v oboru rozhraní API, jsou obsaženy v této složce.
* `policies\apis\<api name>\<operation name>\` Složka – Pokud máte jakékoli zásady definované v oboru operace jsou obsaženy v této složce v `<operation name>.xml` soubory, které se mapují příkazům zásady pro každou operaci.
* `policies\products\` – Pokud máte jakékoli zásady definované na obor produktu jsou obsaženy v této složce, která obsahuje `<product name>.xml` soubory, které se mapují příkazům zásady pro jednotlivé produkty.

### <a name="portalstyles-folder"></a>portalStyles složky
`portalStyles` Složka obsahuje konfiguraci a styl tabulky pro přizpůsobení portálu pro vývojáře pro instanci služby.

* `portalStyles\configuration.json` -obsahuje názvy šablony stylů používaná na portálu pro vývojáře
* `portalStyles\<style name>.css` -Každá `<style name>.css` soubor obsahuje styly pro portál pro vývojáře (`Preview.css` a `Production.css` ve výchozím nastavení).

### <a name="products-folder"></a>Složka produkty
`products` Složka obsahuje složku pro každý produkt definovaný v instanci služby.

* `products\<product name>\configuration.json` – Jedná se o konfiguraci produktu. Toto je stejné informace, které by byly vráceny, pokud byste chtěli volání [získat konkrétní produkt](https://docs.microsoft.com/en-us/rest/api/apimanagement/product/get) operace.
* `products\<product name>\product.description.html` – Toto je popis produktu a odpovídá `description` vlastnost [entitou produkt](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) v rozhraní REST API.

### <a name="templates"></a>šablony
`templates` Složka obsahuje konfiguraci [e-mailových šablon](api-management-howto-configure-notifications.md) instance služby.

* `<template name>\configuration.json` – Jedná se o konfiguraci pro e-mailové šablony.
* `<template name>\body.html` -tělo e-mailové šablony.

## <a name="next-steps"></a>Další postup
Informace o jiných způsobech ke správě vaší instance služby najdete tady:

* Spravovat pomocí následujících rutin Powershellu pro instance služby
  * [Referenční informace k rutinám PowerShellu pro nasazení služeb](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Reference k rutinám Powershellu pro správu služeb](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Správa vaší instance služby pomocí rozhraní REST API
  * [Reference k REST API služby API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




