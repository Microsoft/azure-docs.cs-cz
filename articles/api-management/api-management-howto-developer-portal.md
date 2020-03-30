---
title: Přehled portálu pro vývojáře Azure API Management
titleSuffix: Azure API Management
description: Další informace o portálu pro vývojáře ve správě rozhraní API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335908"
---
# <a name="azure-api-management-developer-portal-overview"></a>Přehled portálu pro vývojáře správy rozhraní Azure API

Vývojářský portál je automaticky generovaný, plně přizpůsobitelný web s dokumentací vašich API. Je to místo, kde mohou spotřebitelé rozhraní API zjistit vaše rozhraní API, naučit se je používat, požádat o přístup a vyzkoušet si je.

Tento článek popisuje rozdíly mezi samoobslužnými a spravovanými verzemi portálu pro vývojáře ve správě rozhraní API. Vysvětluje také svou architekturu a poskytuje odpovědi na často kladené otázky.

![Portál pro vývojáře pro správu rozhraní API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Spravované a samoobslužné verze

Portál pro vývojáře můžete vytvořit dvěma způsoby:

- **Spravovaná verze** – úpravou a přizpůsobením portálu, který je integrován `<your-api-management-instance-name>.developer.azure-api.net`do instance správy rozhraní API a je přístupný prostřednictvím adresy URL . V [tomto článku s dokumentací](api-management-howto-developer-portal-customize.md) se dozvíte, jak získat přístup ke spravovanému portálu a jak jej přizpůsobit.
- **Verze hostovaná vlastním hostitelem** – nasazením a vlastním hostováním portálu mimo instanci správy rozhraní API. Tento přístup umožňuje upravit základ kódu portálu a rozšířit zajišťovnou základní funkcionalitu – například implementovat vlastní widgety pro integraci se systémy třetích stran. V tomto scénáři jste správce portálu a jste zodpovědní za upgrade portálu na nejnovější verzi. Podrobnosti a pokyny najdete v [úložišti GitHub se zdrojovým kódem portálu][1] a [v kurzu implementace widgetu][3]. [Kurz pro spravovanou verzi](api-management-howto-developer-portal-customize.md) prochází panel správy portálu, který je společný pro spravované a vlastní hostované verze.

## <a name="portal-architectural-concepts"></a>Architektonické koncepty portálu

Komponenty portálu lze logicky rozdělit do dvou kategorií: *kód* a *obsah*.

*Kód* je udržován v [úložišti GitHub][1] a obsahuje:

- Widgety - které představují vizuální prvky a kombinují HTML, JavaScript, stylingschopnost, nastavení a mapování obsahu. Příkladem je obrázek, textový odstavec, formulář, seznam api atd.
- Definice stylů - které určují, jak lze widgety stylovat
- Engine - který generuje statické webové stránky z obsahu portálu a je napsán v JavaScriptu
- Visual editor - který umožňuje přizpůsobení a vytváření v prohlížeči

*Obsah* je rozdělen do dvou podkategorií: *obsah portálu* a *obsah pro správu rozhraní API*.

*Obsah portálu* je specifický pro portál a zahrnuje:

- Stránky – například vstupní stránka, kurzy API, blogové příspěvky
- Média – obrázky, animace a další obsah založený na souborech
- Rozložení - šablony, které jsou porovnány s ADRESOu URL a definují způsob zobrazení stránek
- Styly - hodnoty pro definice stylů, např.
- Nastavení - konfigurace, např.

*Obsah portálu*, s výjimkou médií, je vyjádřen jako dokumenty JSON.

*Obsah správy rozhraní API* zahrnuje entity, jako jsou rozhraní API, operace, produkty, předplatná.

Portál je založen na upravené vidlice [frameworku Paperbits](https://paperbits.io/). Původní funkce Paperbits byla rozšířena o widgety specifické pro správu rozhraní API (například seznam rozhraní API, seznam produktů) a konektor ke službě API Management pro ukládání a načítání obsahu.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Nejčastější dotazy

V této části odpovídáme na běžné otázky týkající se portálu pro vývojáře, které jsou obecné povahy. Otázky specifické pro samoobslužnou verzi naleznete [v části wiki úložiště GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Jak mohu migrovat z verze náhledu portálu?

Pomocí verze preview portálu pro vývojáře jste zřídit obsah náhledu ve službě api management. Výchozí obsah byl výrazně upraven v obecně dostupné verzi pro lepší uživatelské prostředí. Obsahuje také nové widgety.

Pokud používáte spravovanou verzi, resetujte obsah portálu kliknutím na **Obnovit obsah** v části Nabídka **Operace.** Potvrzením této operace odeberete veškerý obsah portálu a zřídíte nový výchozí obsah. Modul portálu byl automaticky aktualizován ve službě api management.

![Obnovit obsah portálu](media/api-management-howto-developer-portal/reset-content.png)

Pokud používáte verzi s vlastním hostitelem, `scripts/cleanup.bat` `scripts/generate.bat` použijte úložiště GitHub a z úložiště GitHub odeberte existující obsah a zřazujte nový obsah. Ujistěte se, že jste předem upgradovali kód portálu na nejnovější verzi z úložiště GitHub.

Pokud nechcete resetovat obsah portálu, můžete zvážit použití nově dostupných widgetů na všech stránkách. Stávající widgety byly automaticky aktualizovány na nejnovější verze.

Pokud byl váš portál zřízen po oznámení o obecné dostupnosti, měl by již obsahovat nový výchozí obsah. Z vaší strany není nutná žádná akce.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Jak můžu migrovat ze starého portálu pro vývojáře na portál pro vývojáře?

Portály jsou nekompatibilní a je třeba migrovat obsah ručně.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Má portál všechny funkce starého portálu?

Portál pro vývojáře již nepodporuje *aplikace* a *problémy*.

Ověřování pomocí oauthu v interaktivní vývojářské konzole ještě není podporováno. Průběh můžete sledovat prostřednictvím [problému GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Byl starý portál zastaral?

Staré portály pro vývojáře a vydavatele jsou nyní *staršími* funkcemi – budou dostávat pouze aktualizace zabezpečení. Nové funkce budou implementovány pouze v novém portálu pro vývojáře.

Vyřazení starších portálů bude oznámeno samostatně. Pokud máte dotazy, obavy nebo komentáře, vznesete je [ve vyhrazeném problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funkce, které potřebuji, nejsou na portálu podporovány

Požadavek [na funkci](https://aka.ms/apimwish) můžete otevřít nebo [implementovat chybějící funkce sami][3]. Pokud implementujete funkce sami, můžete buď vlastní hostování portálu pro vývojáře nebo otevřít žádost o přijetí změn na GitHubu a zahrnout změny ve spravované verzi.

### <a name="how-can-i-automate-portal-deployments"></a>Jak lze automatizovat nasazení portálu?

Můžete programově přistupovat a spravovat obsah portálu pro vývojáře prostřednictvím rozhraní REST API, bez ohledu na to, zda používáte spravovanou nebo vlastní hostovanou verzi.

API je zdokumentováno v [sekci wiki úložiště GitHub][2]. Lze jej použít pro automatizaci migrace obsahu portálu mezi prostředími – například z testovacího prostředí do produkčního prostředí. Další informace o tomto procesu najdete [v tomto článku s dokumentací](https://aka.ms/apimdocs/migrateportal) na GitHubu.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Podporuje portál šablony Azure Resource Manageru nebo je kompatibilní se sadou API Management DevOps Resource Kit?

Ne.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Musím povolit další připojení virtuální sítě pro závislosti spravovaného portálu?

Ve většině případů - ne.

Pokud je vaše služba api management v interní virtuální síti, váš portál pro vývojáře je přístupný jenom v rámci sítě. Název hostitele koncového bodu správy musí přeložit na interní VIP služby z počítače, který používáte pro přístup k rozhraní pro správu portálu. Ujistěte se, že koncový bod správy je registrován ve službě DNS. V případě chybné konfigurace se zobrazí chyba: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Pokud je vaše služba api Management v interní virtuální síti a přistupujete k ní prostřednictvím aplikační brány z Internetu, nezapomeňte povolit připojení k portálu pro vývojáře a koncovým bodům správy správy rozhraní API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Přiřadila jsem vlastní doménu správy rozhraní API a publikovaný portál nefunguje

Po aktualizaci domény je třeba [znovu publikovat portál,](api-management-howto-developer-portal-customize.md#publish) aby se změny projevily.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Přidal(a) jsem zprostředkovatele identity a nevidím ho na portálu

Po konfiguraci zprostředkovatele identity (například AAD, AAD B2C) je třeba [znovu publikovat portál,](api-management-howto-developer-portal-customize.md#publish) aby se změny projevily.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Nastavil(a) jsem delegování a portál ji nepoužívá

Po nastavení delegování je třeba [znovu publikovat portál,](api-management-howto-developer-portal-customize.md#publish) aby se změny projevily.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Moje další změny konfigurace správy rozhraní API nebyly rozšířeny na portálu pro vývojáře

Většina změn konfigurace (například virtuální síť, přihlášení a podmínky produktu) vyžaduje [opětovné publikování portálu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Při používání interaktivní konzole se zobrazuje chyba CORS

Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Vyřešte problém CORS [přidáním zásady CORS](api-management-cross-domain-policies.md#CORS) na vaše rozhraní API.

Stav zásad CORS můžete zkontrolovat v části **Přehled portálu** ve službě Api Management na webu Azure Portal. Varovné pole označuje chybějící nebo nesprávně nakonfigurovanou zásadu.

![Portál pro vývojáře pro správu rozhraní API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automaticky použijte zásady CORS kliknutím na **tlačítko Povolit CORS.**

Cors můžete také povolit ručně.

1. Kliknutím na **tlačítko Ručně použít na** propojení globální úrovně zobrazíte vygenerovaný kód zásad.
2. Přejděte na **všechna rozhraní API** v části rozhraní **API** služby Api Management na webu Azure Portal.
3. Klikněte **</>** na ikonu v části **Příchozí zpracování.**
4. Vložte zásadu **<inbound>** do části souboru XML. Ujistěte **<origin>** se, že hodnota odpovídá doméně portálu pro vývojáře.

> [!NOTE]
> 
> Pokud použijete zásadu CORS v oboru produktu, namísto oboru rozhraní API a vaše rozhraní API používá ověřování pomocí klíče předplatného prostřednictvím záhlaví, konzole nebude fungovat.
>
> Prohlížeč automaticky vydá požadavek HTTP OPTIONS, který neobsahuje záhlaví s klíčem předplatného. Z důvodu chybějící klíč předplatného, api management nelze přidružit volání MOŽNOSTI s produktem, takže nelze použít zásady CORS.
>
> Jako řešení můžete předat klíč předplatného v parametru dotazu.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jaká oprávnění potřebuji k úpravám portálu pro vývojáře?

Pokud se při `Oops. Something went wrong. Please try again later.` otevření portálu v režimu správy zobrazuje chyba, může vám chybět požadovaná oprávnění (RBAC).

Starší portály vyžadovaly `Microsoft.ApiManagement/service/getssotoken/action` oprávnění v`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`oboru služby ( ) k povolení přístupu správce uživatele k portálům. Nový portál vyžaduje `Microsoft.ApiManagement/service/users/token/action` oprávnění v `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`oboru .

Pomocí následujícího skriptu prostředí PowerShell můžete vytvořit roli s požadovaným oprávněním. Nezapomeňte změnit `<subscription-id>` parametr. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Po vytvoření role, může být udělena libovolnému uživateli z oddílu **řízení přístupu (IAM)** na webu Azure Portal. Přiřazení této role uživateli přiřadí oprávnění k oboru služby. Uživatel bude moci generovat tokeny SAS jménem *libovolného* uživatele ve službě. Tato role musí být minimálně přiřazena správci služby. Následující příkaz prostředí PowerShell ukazuje, jak přiřadit `user1` roli uživateli s nejnižším rozsahem, aby se zabránilo udělení zbytečných oprávnění uživateli: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po udělení oprávnění uživateli se uživatel musí odhlásit a znovu přihlásit na portál Azure, aby se nová oprávnění projevila.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Zobrazuje se `Unable to start the portal. See if settings are specified correctly (...)` chyba

Tato chyba se `GET` zobrazí `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` při volání nezdaří. Volání je vydáváno z prohlížeče administrativním rozhraním portálu.

Pokud vaše služba správy rozhraní API je ve virtuální síti – podívejte se na výše uvedenou otázku připojení virtuální sítě.

Selhání volání může být také způsobeno certifikátem TLS/SSL, který je přiřazen k vlastní doméně a prohlížeč mu nedůvěřuje. Jako zmírnění můžete odebrat vlastní doménu koncového bodu pro správu – správa rozhraní API se vrátí zpět na výchozí koncový bod s důvěryhodným certifikátem.

### <a name="whats-the-browser-support-for-the-portal"></a>Jaká je podpora prohlížeče pro portál?

| Prohlížeč                     | Podporuje se       |
|-----------------------------|-----------------|
| Apple Safari                | Ano<sup>1</sup> |
| Google Chrome               | Ano<sup>1</sup> |
| Microsoft Edge              | Ano<sup>1</sup> |
| Microsoft Internet Explorer | Ne              |
| Mozilla Firefox             | Ano<sup>1</sup> |

 <small><sup>1</sup> Podporováno ve dvou nejnovějších produkčních verzích.</small>

## <a name="next-steps"></a>Další kroky

Další informace o novém portálu pro vývojáře:

- [Přístup k portálu pro spravované vývojáře a jeho přizpůsobení](api-management-howto-developer-portal-customize.md)
- [Nastavení samoobslužné verze portálu][2]
- [Implementujte svůj vlastní widget][3]

Procházet další zdroje informací:

- [Úložiště GitHub se zdrojovým kódem][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend