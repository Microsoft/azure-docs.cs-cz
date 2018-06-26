---
title: Konfigurace služby API Management pomocí Git - Azure | Microsoft Docs
description: Zjistěte, jak uložit a nakonfigurovat konfigurace služby API Management pomocí Git.
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
ms.openlocfilehash: 0165de82850c0c80052564c5f31a5e5cf5effb11
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938304"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Uložte a konfiguraci konfigurace služby API Management pomocí Git

Každá instance služby API Management udržuje databázi konfigurace, která obsahuje informace o konfiguraci a metadat pro instanci služby. Změna nastavení na portálu Azure, pomocí rutiny prostředí PowerShell nebo volání rozhraní REST API, můžete provedeny změny v instanci služby. Kromě těchto metod můžete také spravovat konfigurace instance služby pomocí Git, například povolení scénářů správy služby:

* Konfigurace správy verzí - stažení a uložení různých verzích konfigurace služby
* Hromadné změny konfigurace – provádět změny více částí konfigurace služby v místním úložišti a integrovat změny zpět na server s jedinou operací.
* Známých nástrojů Git a pracovní postup - používat Git nástrojů a pracovní postupy, které jste již obeznámeni s

Následující diagram ukazuje přehled různé způsoby, jak nakonfigurovat instanci služby API Management.

![Konfigurace Git][api-management-git-configure]

Když provedete změny služby pomocí portálu Azure, rutiny prostředí PowerShell nebo rozhraní REST API, kterou spravujete vaší služby konfigurace databáze pomocí `https://{name}.management.azure-api.net` koncový bod, jak je znázorněno na pravé straně diagramu. Levé straně diagram znázorňuje, jak můžete spravovat konfiguraci služby pomocí Git a úložiště Git pro vaši službu na `https://{name}.scm.azure-api.net`.

Následující kroky poskytují přehled správy pomocí Git instanci služby API Management.

1. Konfigurace přístupu Git ve službě
2. Uložit konfigurační databáze služby služby do úložiště Git
3. Naklonujte úložiště Git do místního počítače
4. Stáhněte nejnovější úložišti na místním počítači a potvrzení a posílejte nabízená oznámení změn zpět do vašeho úložiště
5. Nasazení změny z vašeho úložiště do konfigurační databáze služby service

Tento článek popisuje, jak povolit a používat Git ke správě konfigurace služby a poskytuje odkaz pro soubory a složky do úložiště Git.

## <a name="access-git-configuration-in-your-service"></a>Konfigurace přístupu Git ve službě

Pokud chcete zobrazit a konfigurovat nastavení konfigurace Git, můžete kliknout na **zabezpečení** nabídky a přejděte do **konfigurace úložiště** kartě.

![Povolit GIT][api-management-enable-git]

> [!IMPORTANT]
> Všechny tajné klíče, které nejsou definovány vlastnosti bude uložen v úložišti a zůstane v historii dokud zakázat a znovu povolte přístup Git. Vlastnosti poskytují na bezpečné místo pro správu konstantní řetězcové hodnoty, včetně tajných klíčů, ve všech konfigurací rozhraní API a zásady, takže není nutné ukládat je přímo do vaší příkazy zásad. Další informace najdete v tématu [použití vlastností v zásadách Azure API Management](api-management-howto-properties.md).
> 
> 

Informace o povolení nebo zakázání Git přístup pomocí rozhraní REST API najdete v tématu [povolit nebo zakázat Git přístup pomocí rozhraní REST API](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Chcete-li uložit konfiguraci služby do úložiště Git

Prvním krokem před klonováním úložiště je uložit aktuální stav konfigurace služby do úložiště. Klikněte na tlačítko **uložit do úložiště**.

Všechny požadované změny provádějte na potvrzovací obrazovce a klepněte na tlačítko **Ok** uložit.

Po chvíli konfiguraci uložit, a stav konfigurace úložiště, ve kterém se zobrazí, včetně datum a čas poslední změny konfigurace a poslední synchronizace mezi konfiguraci služby a úložiště.

Po konfiguraci je uložen do úložiště, můžete klonovat.

Informace o provedení této operace pomocí rozhraní REST API najdete v tématu [potvrzení konfigurace snímek pomocí rozhraní REST API](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Klonování úložiště do místního počítače

Klonovat úložiště, musíte adresu URL úložiště, uživatelské jméno a heslo. Chcete-li získat uživatelské jméno a další přihlašovací údaje, klikněte na **přístupu k pověřením** v horní části stránky.  
 
Vygenerovat heslo, nejdřív ověřte, že **vypršení platnosti** je nastavená na požadované vypršení platnosti datum a čas a potom klikněte na **generování**.

> [!IMPORTANT]
> Toto heslo si poznamenejte. Jakmile tuto stránku opustíte heslo se znovu nezobrazí.
> 

Následující příklady použít nástroj Git Bash z [Git pro Windows](http://www.git-scm.com/downloads) ale můžete použít jakýkoli Git nástroj, který jste se seznámili s.

Otevřete svůj nástroj Git do požadované složky a spusťte následující příkaz klonovat úložiště git do místního počítače, pomocí příkazu poskytované portálu Azure.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Zadejte uživatelské jméno a heslo po zobrazení výzvy.

Pokud se zobrazí všechny chyby, zkuste upravit vaše `git clone` příkazu zahrnovat uživatelské jméno a heslo, jak je znázorněno v následujícím příkladu.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Pokud to poskytuje k chybě, zkuste URL kódování heslo část příkazu. Jeden rychlý způsob, jak to udělat je otevřete Visual Studio a vydejte následující příkaz v **hodnot proměnných**. Otevřete **hodnot proměnných**, otevřete jakékoli řešení nebo produktu project v sadě Visual Studio (nebo vytvořte novou prázdnou konzolovou aplikaci) a zvolte **Windows**, **Immediate** z **Ladění** nabídky.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Použijte zakódované heslo společně s vaše uživatelské jméno a úložiště umístění k vytvoření příkazu git.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Jakmile je klonovat úložiště můžete zobrazit a pracovat v místním systému souborů. Další informace najdete v tématu [souborů a složek struktury odkaz místní úložiště Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Chcete-li aktualizovat místní úložiště s nejnovější konfigurací instance služby

Pokud provedete změny instanci služby API Management v portálu Azure nebo pomocí rozhraní REST API, musíte uložit tyto změny do úložiště než budete moct aktualizovat místní úložiště s nejnovější změny. Chcete-li to provést, klikněte na tlačítko **uložit konfiguraci úložiště** na **konfigurace úložiště** v portálu Azure a potom vydat příkaz v místním úložišti.

```
git pull
```

Dřív, než spustíte `git pull` Ujistěte se, že jste ve složce pro místní úložiště. Pokud jste právě dokončili `git clone` příkaz, pak musí změňte adresář na vašem úložišti spuštěním příkazu jako následující.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>K replikaci změn z vaší místní úložiště na server úložiště
K replikaci změn z místního úložiště do úložiště serveru, musí potvrdit změny a vložit je na serveru úložiště. Potvrzení změny, otevřete svůj nástroj příkazu Git, přejděte do adresáře, ve vašem místním úložišti a vydat následující příkazy.

```
git add --all
git commit -m "Description of your changes"
```

K replikaci všechna potvrzení na serveru, spusťte následující příkaz.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>K nasazení změny konfigurace služby v instanci služby API Management

Jakmile jsou místní změny potvrzeny a instaluje do úložiště serveru, můžete nasadit instanci služby API Management.

Informace o provedení této operace pomocí rozhraní REST API najdete v tématu [nasazení Git změny konfigurační databáze pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odkaz struktury souborů a složek místní úložiště Git

Soubory a složky v úložišti místní git obsahovat informace o konfiguraci o instanci služby.

| Položka | Popis |
| --- | --- |
| Kořenová složka api management |Obsahuje nejvyšší úrovně konfiguraci pro instanci služby |
| rozhraní API složky |Obsahuje konfiguraci pro rozhraní API v instanci služby |
| složka skupiny |Obsahuje konfiguraci skupin v instanci služby |
| Složka zásad |Obsahuje zásady v instanci služby |
| portalStyles složky |Obsahuje konfiguraci pro přizpůsobení portálu vývojáře v instanci služby |
| produkty složky |Obsahuje konfiguraci pro produkty v instanci služby |
| složka šablon |Obsahuje konfiguraci e-mailových šablon v instanci služby |

Každé složky může obsahovat jeden nebo více souborů a v některých případech jeden nebo více složek, například do složky pro každé rozhraní API, produkty nebo skupiny. Soubory v rámci každé složky jsou specifické pro typ entity popsaného název složky.

| Typ souboru | Účel |
| --- | --- |
| json |Informace o konfiguraci o odpovídající entity |
| HTML |Popisy o entitě, často zobrazeny v portálu pro vývojáře |
| xml |Příkazy zásad |
| šablon stylů CSS |Šablony stylů pro přizpůsobení portálu pro vývojáře |

Tyto soubory lze vytvořit, odstranit, upravit a spravovat v místním systému souborů a nasazení změn zpět do instanci služby API Management.

> [!NOTE]
> Následující entity, které nejsou obsaženy v úložišti Git a nelze konfigurovat pomocí Git.
> 
> * Uživatelé
> * Předplatná
> * Vlastnosti
> * Vývojáře portálu entity než styly
> 

### <a name="root-api-management-folder"></a>Kořenová složka api management
Kořenové `api-management` složka obsahuje `configuration.json` soubor, který obsahuje nejvyšší úrovně informace o instanci služby v následujícím formátu.

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

První čtyři nastavení (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, a `UserRegistrationTermsConsentRequired`) mapování na následující nastavení na **identity** ve **zabezpečení** části.

| Nastavení identity | Se mapuje na |
| --- | --- |
| RegistrationEnabled |**Anonymní uživatelé přesměruje na přihlašovací stránce** zaškrtávací políčko |
| UserRegistrationTerms |**Podmínky použití na registraci uživatele** textbox |
| UserRegistrationTermsEnabled |**Zobrazit podmínky použití na přihlašovací stránce** zaškrtávací políčko |
| UserRegistrationTermsConsentRequired |**Vyžadovat souhlas** zaškrtávací políčko |

Nastavení další čtyři (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, a `DelegationValidationKey`) mapování na následující nastavení na **delegování** ve **zabezpečení** části.

| Nastavení delegování | Se mapuje na |
| --- | --- |
| DelegationEnabled |**Delegát přihlášení a registrace** zaškrtávací políčko |
| DelegationUrl |**Adresa URL koncového bodu delegování** textbox |
| DelegatedSubscriptionEnabled |**Delegovat odběru produktů** zaškrtávací políčko |
| DelegationValidationKey |**Delegovat ověřovací klíč** textbox |

Nastavení konečného `$ref-policy`, se mapuje na soubor globální zásady příkazy pro instanci služby.

### <a name="apis-folder"></a>rozhraní API složky
`apis` Složka obsahuje složku pro každé rozhraní API v instanci služby, který obsahuje následující položky.

* `apis\<api name>\configuration.json` -Toto je konfigurace pro rozhraní API a obsahuje informace o adresu URL back-end služby a činnosti. Toto je stejné informace, které by byla vrácena, pokud byste chtěli volání [získat konkrétní rozhraní API](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/get) s `export=true` v `application/json` formátu.
* `apis\<api name>\api.description.html` -Toto je popis rozhraní API a odpovídá `description` vlastnost [rozhraní API entity](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` – Tato složka obsahuje `<operation name>.description.html` soubory, které se mapují na operace v rozhraní API. Každý soubor obsahuje popis jedné operace v rozhraní API, která se mapuje na `description` vlastnost [operaci entity](https://docs.microsoft.com/en-us/rest/api/visualstudio/operations/list#operationproperties) v rozhraní REST API.

### <a name="groups-folder"></a>složka skupiny
`groups` Složka obsahuje složku pro každou skupinu definované v instanci služby.

* `groups\<group name>\configuration.json` -jedná o konfiguraci pro skupinu. Toto je stejné informace, které by byla vrácena, pokud byste chtěli volání [získat určité skupiny](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) operaci.
* `groups\<group name>\description.html` -Toto je popis skupiny a odpovídá `description` vlastnost [skupiny entity](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>Složka zásad
`policies` Složka obsahuje příkazy zásad pro instanci služby.

* `policies\global.xml` -obsahuje zásady definované v globálním oboru pro instanci služby.
* `policies\apis\<api name>\` – Pokud máte jakékoli zásady definované v oboru rozhraní API, jsou obsažené v této složce.
* `policies\apis\<api name>\<operation name>\` Složka – Pokud máte jakékoli zásady definované v oboru operaci, jsou obsažené v této složce v `<operation name>.xml` soubory, které jsou mapovány na příkazy zásad pro každou operaci.
* `policies\products\` – Pokud máte jakékoli zásady definované v produktu oboru, jsou obsažené v této složce, která obsahuje `<product name>.xml` soubory, které jsou mapovány na příkazy zásad pro jednotlivé produkty.

### <a name="portalstyles-folder"></a>portalStyles složky
`portalStyles` Složka obsahuje konfiguraci a stylu stylů pro vývojáře přizpůsobení portálu pro instance služby.

* `portalStyles\configuration.json` -obsahuje názvy stylů používá portál pro vývojáře
* `portalStyles\<style name>.css` -Každý `<style name>.css` soubor obsahuje styly pro portál pro vývojáře (`Preview.css` a `Production.css` ve výchozím nastavení).

### <a name="products-folder"></a>produkty složky
`products` Složka obsahuje složku pro každý produkt definované v instanci služby.

* `products\<product name>\configuration.json` -Toto je konfigurace produktu. Toto je stejné informace, které by byla vrácena, pokud byste chtěli volání [získat určitý produkt](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) operaci.
* `products\<product name>\product.description.html` -Toto je popis produktu a odpovídá `description` vlastnost [entity produktu](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) v rozhraní REST API.

### <a name="templates"></a>šablony
`templates` Složka obsahuje konfiguraci [e-mailových šablon](api-management-howto-configure-notifications.md) instance služby.

* `<template name>\configuration.json` -Toto je konfigurace pro e-mailové šablony.
* `<template name>\body.html` -Toto je text šablony e-mailu.

## <a name="next-steps"></a>Další postup
Informace o jiných způsobech spravovat instanci služby najdete v tématu:

* Spravovat instanci služby pomocí následující rutiny prostředí PowerShell
  * [Referenční informace k rutinám PowerShellu pro nasazení služeb](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Správa služeb referenční informace o rutinách prostředí PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Spravovat instanci služby pomocí rozhraní REST API
  * [Referenční dokumentace rozhraní API REST API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




