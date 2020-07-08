---
title: Přehled portálu pro vývojáře Azure API Management
titleSuffix: Azure API Management
description: Seznamte se s portálem pro vývojáře v API Management.
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
ms.openlocfilehash: ecbd4d97bb092ccbdb286e4865bf04e770ca9473
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207885"
---
# <a name="azure-api-management-developer-portal-overview"></a>Přehled portálu pro vývojáře Azure API Management

Portál pro vývojáře je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjišťovat vaše rozhraní API, učit se, jak je používat, žádat o přístup a vyzkoušet si je.

Tento článek popisuje rozdíly mezi místně hostovanými a spravovanými verzemi portálu pro vývojáře v API Management. Vysvětluje také její architekturu a poskytuje odpovědi na nejčastější dotazy.

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Spravované a samostatně hostované verze

Portál pro vývojáře můžete vytvořit dvěma způsoby:

- **Spravovaná verze** – úpravou a přizpůsobením portálu, který je součástí vaší instance API Management a je přístupný prostřednictvím adresy URL `<your-api-management-instance-name>.developer.azure-api.net` . Informace o přístupu a přizpůsobení spravovaného portálu najdete v [tomto článku v dokumentaci](api-management-howto-developer-portal-customize.md) .
- **Samoobslužná verze** – nasazením a vlastním hostováním portálu mimo instanci API Management. Tento přístup umožňuje upravit základ kódu na portálu a zvětšit poskytované základní funkce – například implementovat vlastní widgety pro integraci se systémy třetích stran. V tomto scénáři jste portálem pro správu a zodpovídáte za upgrade portálu na nejnovější verzi. Podrobnosti a pokyny najdete v [úložišti GitHub se zdrojovým kódem portálu][1] a v [kurzu implementace widgetu][3]. [Kurz spravované verze](api-management-howto-developer-portal-customize.md) se provede pomocí panelu pro správu portálu, který je společný pro spravované a samoobslužné verze.

## <a name="portal-architectural-concepts"></a>Koncepce architektury portálu

Komponenty portálu mohou být logicky rozděleny do dvou kategorií: *kód* a *obsah*.

*Kód* je udržován v [úložišti GitHub][1] a zahrnuje:

- Widgety, které reprezentují vizuální prvky a kombinují HTML, JavaScript, styl možností, nastavení a mapování obsahu. Příkladem je obrázek, textový odstavec, formulář, seznam rozhraní API atd.
- Definice stylů – určení způsobu, jakým mohou být widgety ve stylu
- Modul, který generuje statické webové stránky z obsahu portálu a je napsán v JavaScriptu
- Vizuální Editor – umožňuje přizpůsobení v prohlížeči a možnosti vytváření obsahu.

*Obsah* je rozdělen do dvou podkategorií: *obsah portálu* a *API Management obsah*.

*Obsah portálu* je specifický pro portál a zahrnuje:

- Stránky – například úvodní stránka, kurzy k rozhraní API, příspěvky na blogu
- Média – obrázky, animace a další obsah založený na souborech
- Rozložení – šablony, které jsou porovnány s adresou URL a definují způsob zobrazení stránek
- Styly – hodnoty pro definice stylu, například písma, barvy, ohraničení
- Nastavení – konfigurace, například favicon, metadata webu

*Obsah portálu*, s výjimkou médií, se vyjadřuje jako dokumenty JSON.

*API Management obsah* zahrnuje entity, jako jsou rozhraní API, operace, produkty a odběry.

Portál je založen na upraveném rozvětvení [architektury Paperbits](https://paperbits.io/). Původní funkce Paperbits se rozšířily tak, aby poskytovaly widgety specifické pro API Management (například seznam rozhraní API, seznam produktů) a konektor pro API Management službu pro ukládání a načítání obsahu.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Nejčastější dotazy

V této části odpovíme na běžné otázky k portálu pro vývojáře, které jsou obecné povahy. Otázky specifické pro samostatnou verzi najdete [v části wiki v úložišti GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a>Jak se dá migrovat z verze Preview portálu?

Pomocí verze Preview portálu pro vývojáře jste v rámci služby API Management zřídili obsah verze Preview. Výchozí obsah byl významně upraven v všeobecně dostupné verzi pro lepší uživatelské prostředí. Obsahuje také nové pomůcky.

Pokud používáte spravovanou verzi, obnovte obsah portálu kliknutím na **obnovit obsah** v části nabídka **operace** . Potvrzením této operace dojde k odebrání veškerého obsahu portálu a zřízení nového výchozího obsahu. V API Management službě se automaticky aktualizoval modul portálu.

![Resetování obsahu portálu](media/api-management-howto-developer-portal/reset-content.png)

Pokud používáte samoobslužnou verzi, pomocí nástroje `scripts/cleanup.bat` a `scripts/generate.bat` z úložiště GitHub odeberte existující obsah a zřiďte nový obsah. Ujistěte se, že upgradujete kód vašeho portálu na nejnovější verzi z úložiště GitHub předem.

Pokud nechcete obnovit obsah portálu, můžete zvážit použití nově dostupných pomůcek na všech stránkách. Stávající pomůcky se automaticky aktualizovaly na nejnovější verze.

Pokud byl portál zřízen po oznámení obecné dostupnosti, měl by již být součástí nového výchozího obsahu. Z vaší strany se nevyžaduje žádná akce.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Jak se dá migrovat z původního portálu pro vývojáře na portál pro vývojáře?

Portály nejsou kompatibilní a je potřeba migrovat obsah ručně.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Má portál všechny funkce starého portálu?

Portál pro vývojáře už nepodporuje *aplikace* a *problémy*.

Ověřování pomocí OAuth v interaktivní konzole pro vývojáře ještě není podporované. Průběh můžete sledovat prostřednictvím [problému na GitHubu](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Byl starý portál zastaralý?

Starý portál pro vývojáře a vydavatele teď představují *starší* funkce – budou dostávat jenom aktualizace zabezpečení. Nové funkce se implementují jenom na novém portálu pro vývojáře.

Vyřazení starší verze portálů bude oznámeno samostatně. Pokud máte dotazy, obavy nebo komentáře, vyvolejte je [ve vyhrazeném problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funkce, které potřebuji, se na portálu nepodporují.

Můžete otevřít [žádost o funkci](https://aka.ms/apimwish) nebo [implementovat chybějící funkce sami][3]. Pokud implementujete funkci sami, můžete portál pro vývojáře sami hostovat nebo otevřít žádost o přijetí změn na GitHubu a zahrnout změny ve spravované verzi.

### <a name="how-can-i-automate-portal-deployments"></a>Jak můžu automatizovat nasazení portálu?

Můžete programově přistupovat k obsahu portálu pro vývojáře a spravovat ho prostřednictvím REST API bez ohledu na to, jestli používáte spravovanou nebo místně hostovanou verzi.

Rozhraní API je popsané v [části wiki úložiště GitHubu][2]. Dá se použít k automatizaci migrace obsahu portálu mezi prostředími – například z testovacího prostředí do produkčního prostředí. Další informace o tomto procesu najdete [v tomto článku v dokumentaci](https://aka.ms/apimdocs/migrateportal) na GitHubu.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Podporuje portál Azure Resource Manager šablony nebo je kompatibilní se sadou API Management DevOps Resource Kit?

Ne.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Musím pro závislosti spravovaného portálu povolit další připojení VNet?

Ve většině případů – ne.

Pokud je vaše služba API Management v interní virtuální síti, portál pro vývojáře je přístupný jenom v rámci sítě. Název hostitele koncového bodu správy se musí překládat na interní virtuální IP adresu služby z počítače, který používáte pro přístup k rozhraní pro správu portálu. Ujistěte se, že je koncový bod správy zaregistrován v DNS. V případě chybné konfigurace se zobrazí chyba: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Pokud je vaše služba API Management interní virtuální síť a k ní přistupujete prostřednictvím Application Gateway z Internetu, Nezapomeňte povolit připojení k portálu pro vývojáře a koncovým bodům správy API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Přiřadil (a) jsem vlastní doménu API Management a publikovaný portál nefunguje

Po aktualizaci domény je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Přidal (a) jsem poskytovatele identity a nevidím ho na portálu

Po nakonfigurování zprostředkovatele identity (například AAD, AAD B2C) je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Nastavil (a) delegování a portál ho nepoužívá

Po nastavení delegování je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Změny konfigurace mých API Management nebyly rozšířeny na portál pro vývojáře.

Většina změn konfigurace (například virtuální síť, přihlášení a podmínek produktu) vyžaduje [Opětovné publikování portálu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Při použití interaktivní konzoly se mi zobrazuje chyba CORS

Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Přidejte [zásadu CORS](api-management-cross-domain-policies.md#CORS) do vašich rozhraní API a vyřešte problém s CORS.

Stav zásad CORS můžete zjistit v části **Přehled portálu** služby API Management v Azure Portal. Výstražné pole indikuje chybějící nebo nesprávně nakonfigurovanou zásadu.

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automaticky použít zásadu CORS kliknutím na tlačítko **Povolit CORS**

Můžete také povolit CORS ručně.

1. Pokud chcete zobrazit generovaný kód zásad, klikněte na odkaz ručně na odkaz na **globální úrovni** .
2. V Azure Portal přejděte ke **všem rozhraním API** v části **rozhraní API** služby API Management.
3. **</>** V části **příchozí zpracování** klikněte na ikonu.
4. Vložte zásadu do **<inbound>** části souboru XML. Ujistěte se, že **<origin>** hodnota odpovídá vaší doméně portálu pro vývojáře.

> [!NOTE]
> 
> Pokud použijete zásadu CORS v oboru produktu místo rozhraní API (s) a vaše rozhraní API použije ověřování pomocí klíče předplatného v hlavičce, nebude vaše konzola fungovat.
>
> Prohlížeč automaticky vydá požadavek na možnost HTTP, který neobsahuje hlavičku s klíčem předplatného. Z důvodu chybějícího klíče předplatného API Management nemůže přidružit volání možností k produktu, takže nemůže uplatnit zásadu CORS.
>
> Jako alternativní řešení můžete klíč předplatného předat v parametru dotazu.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jaká oprávnění potřebuji k úpravám portálu pro vývojáře?

Pokud se vám při `Oops. Something went wrong. Please try again later.` otevření portálu v režimu správy zobrazuje chyba, možná nemáte požadovaná oprávnění (RBAC).

Starší portály vyžadovaly oprávnění `Microsoft.ApiManagement/service/getssotoken/action` v oboru služby ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ), aby správce uživatelů mohl mít přístup k portálům. Nový portál vyžaduje oprávnění `Microsoft.ApiManagement/service/users/token/action` v oboru `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

K vytvoření role s požadovaným oprávněním můžete použít následující skript prostředí PowerShell. Nezapomeňte změnit `<subscription-id>` parametr. 

```powershell
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
 
Jakmile je role vytvořená, dá se jim udělit libovolný uživatel z části **Access Control (IAM)** v Azure Portal. Přiřazení této role uživateli přiřadí oprávnění v oboru služby. Uživatel bude moci generovat tokeny SAS jménem *libovolného* uživatele ve službě. Minimálně tuto roli je potřeba přiřadit Správci služby. Následující příkaz prostředí PowerShell ukazuje, jak přiřadit roli uživateli `user1` na nejnižší úrovni, aby se zabránilo udělení zbytečných oprávnění uživateli: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po udělení oprávnění uživateli se uživatel musí odhlásit a znovu přihlásit k Azure Portal, aby se nová oprávnění projevila.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Zobrazuje se `Unable to start the portal. See if settings are specified correctly (...)` Chyba

Tato chyba se zobrazí, když `GET` volání `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` neproběhne úspěšně. Volání je vystaveno z prohlížeče pomocí rozhraní pro správu portálu.

Pokud je vaše služba API Management ve virtuální síti, přečtěte si výše uvedenou otázku připojení k virtuální síti.

Selhání volání může být způsobeno také certifikátem TLS/SSL, který je přiřazen vlastní doméně a není důvěryhodný pro prohlížeč. Jako zmírnění můžete odebrat vlastní doménu koncového bodu správy – API Management se vrátí k výchozímu koncovému bodu s důvěryhodným certifikátem.

### <a name="whats-the-browser-support-for-the-portal"></a>Jaká je podpora prohlížeče pro portál?

| Prohlížeč                     | Podporuje se       |
|-----------------------------|-----------------|
| Apple Safari                | Ano<sup>1</sup> |
| Google Chrome               | Ano<sup>1</sup> |
| Microsoft Edge              | Ano<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ano<sup>1</sup> |

 <small><sup>1</sup> podporuje se v obou nejnovějších produkčních verzích.</small>

## <a name="next-steps"></a>Další kroky

Další informace o novém portálu pro vývojáře:

- [Přístup k portálu spravovaného vývojáře a jeho přizpůsobení](api-management-howto-developer-portal-customize.md)
- [Nastavení samoobslužné verze portálu][2]
- [Implementace vlastního widgetu][3]

Procházet Další prostředky:

- [Úložiště GitHub se zdrojovým kódem][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
