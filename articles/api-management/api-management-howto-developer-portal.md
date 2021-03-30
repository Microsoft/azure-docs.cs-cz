---
title: Přehled portálu pro vývojáře v Azure API Management
titleSuffix: Azure API Management
description: Přečtěte si o portálu pro vývojáře v API Management – přizpůsobitelný web, ve kterém můžou uživatelé rozhraní API prozkoumat vaše rozhraní API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074793"
---
# <a name="overview-of-the-developer-portal"></a>Přehled portálu pro vývojáře

Portál pro vývojáře je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjišťovat vaše rozhraní API, učit se, jak je používat, žádat o přístup a vyzkoušet si je.

Tento článek popisuje rozdíly mezi místně hostovanými a spravovanými verzemi portálu pro vývojáře v API Management. Obsahuje také odpovědi na nejčastější dotazy.

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrace ze starší verze portálu

> [!IMPORTANT]
> Starší verze portálu pro vývojáře je nyní zastaralá a bude dostávat pouze aktualizace zabezpečení. Můžete ho i nadále používat, jak je obvyklé, do svého vyřazení v říjnu 2023, když se odebere ze všech API Management Services.

Migrace na nový portál pro vývojáře je popsaná v [článku věnovaném vyhrazené dokumentaci](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Přizpůsobení a stylování

Portál pro vývojáře se dá přizpůsobit a vytvořit jeho stylem prostřednictvím integrovaného a přetahování pomocí vizuálního editoru. Další podrobnosti najdete v [tomto kurzu](api-management-howto-developer-portal-customize.md) .

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Rozšiřitelnost

Vaše služba API Management obsahuje integrovaný, stále aktuální, **spravovaný** portál pro vývojáře. K němu můžete přistupovat z rozhraní Azure Portal.

Pokud ho potřebujete zvětšit pomocí vlastní logiky, která není předem podporovaná, můžete změnit jeho základ kódu. Základ kódu na portálu je [k dispozici v úložišti GitHubu][1]. Můžete například implementovat nový widget, který se integruje se systémem podpory třetí strany. Při implementaci nových funkcí můžete vybrat jednu z následujících možností:

- **Vlastní hostování** výsledného portálu mimo vaši službu API Management. Po samoobslužném hostování portálu se stanete jeho údržbou a zodpovídáte za jeho upgrady. Pomocná podpora Azure je omezená jenom na základní nastavení samoobslužných portálů, jak je popsáno v [části wiki v úložišti][2].
- Otevřete žádost o přijetí změn pro tým API Management, aby se do základu kódu **spravovaného** portálu sloučila nová funkce.

Podrobnosti a pokyny k rozšíření najdete v [úložišti GitHubu][1] a v [kurzech k implementaci widgetu][3]. [Kurz pro přizpůsobení spravovaného portálu](api-management-howto-developer-portal-customize.md) vás provede pomocí panelu pro správu portálu, který je běžně pro **spravované** a **samoobslužné** verze.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Nejčastější dotazy

V této části odpovíme na běžné otázky k portálu pro vývojáře, které jsou obecné povahy. Otázky specifické pro samostatnou verzi najdete [v části wiki v úložišti GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Jak se dá migrovat z verze Preview portálu?

Při prvním spuštění verze Preview portálu pro vývojáře jste ve službě API Management zřídili verzi Preview jejího výchozího obsahu. Výchozí obsah byl významně upraven v všeobecně dostupné verzi. Například verze Preview výchozího obsahu nezahrnuje tlačítka OAuth na přihlašovacích stránkách, používá různá widgety pro zobrazení rozhraní API a spoléhá na omezené možnosti pro strukturování stránek portálu pro vývojáře. I když existují rozdíly v obsahu, stroj portálu (včetně základních widgetů) se automaticky aktualizuje při každém publikování portálu pro vývojáře.

Pokud jste portál silně přizpůsobili na základě verze Preview obsahu, můžete ho i nadále používat a na stránkách na portálu umístit nové widgety ručně. V opačném případě doporučujeme nahradit obsah vašeho portálu novým výchozím obsahem.

Chcete-li obnovit obsah na spravovaném portálu, vyberte možnost **obnovit obsah** v části nabídka **operace** . Tato operace odstraní veškerý obsah portálu a zřídí nový výchozí obsah. Ztratíte všechna přizpůsobení a změny portálu pro vývojáře. **Tuto akci nelze vrátit zpět**.

![Resetování obsahu portálu](media/api-management-howto-developer-portal/reset-content.png)

Pokud používáte samoobslužnou verzi, spusťte `scripts.v2/cleanup.bat` `scripts.v2/generate.bat` skript a z úložiště GitHubu, abyste odebrali existující obsah a zřídili nový obsah. Nezapomeňte předem upgradovat kód vašeho portálu na nejnovější verzi z úložiště GitHub.

Pokud jste na portál poprvé získali po oznámení o všeobecné dostupnosti v listopadu 2019, měl by již být součástí nového výchozího obsahu a nevyžaduje se žádná další akce.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funkce, které potřebuji, se na portálu nepodporují.

V [úložišti GitHubu][1] můžete otevřít žádost o funkci, nebo můžete [chybějící funkce implementovat sami][3]. Další podrobnosti najdete v části **rozšiřitelnost** výše.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Jak můžu automatizovat nasazení portálu?

Můžete programově přistupovat k obsahu portálu pro vývojáře a spravovat ho prostřednictvím REST API bez ohledu na to, jestli používáte spravovanou nebo místně hostovanou verzi.

Rozhraní API je popsané v [části wiki úložiště GitHubu][2]. Dá se použít k automatizaci migrace obsahu portálu mezi prostředími – například z testovacího prostředí do produkčního prostředí. Další informace o tomto procesu najdete [v tomto článku v dokumentaci](https://aka.ms/apimdocs/migrateportal) na GitHubu.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Návody se přesunout ze spravovaného na místně hostovanou verzi?

Přečtěte si podrobný článek v [části wiki v úložišti portálu pro vývojáře na GitHubu][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Můžu mít v jedné API Management službě víc portálů pro vývojáře?

Můžete mít jeden spravovaný portál a několik samoobslužných portálů. Obsah všech portálů je uložený ve stejné API Management službě, takže budou identické. Pokud chcete odlišit vzhled a funkčnost portálů, můžete je sami hostovat s vlastními widgety, které dynamicky přizpůsobují stránky za běhu, například na základě adresy URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Podporuje portál Azure Resource Manager šablony nebo je kompatibilní se sadou API Management DevOps Resource Kit?

Ne.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Je obsah portálu uložený s funkcí zálohování a obnovení v API Management?

Ne.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Musím pro závislosti spravovaného portálu povolit další připojení VNet?

Ve většině případů – ne.

Pokud je vaše služba API Management v interní virtuální síti, portál pro vývojáře je přístupný jenom v rámci sítě. Název hostitele koncového bodu správy se musí překládat na interní virtuální IP adresu služby z počítače, který používáte pro přístup k rozhraní pro správu portálu. Ujistěte se, že je koncový bod správy zaregistrován v DNS. V případě chybné konfigurace se zobrazí chyba: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Pokud je vaše služba API Management interní virtuální síť a k ní přistupujete prostřednictvím Application Gateway z Internetu, Nezapomeňte povolit připojení k portálu pro vývojáře a koncovým bodům správy API Management. Možná budete muset zakázat pravidla firewallu webových aplikací. Další podrobnosti najdete v [tomto článku v dokumentaci](api-management-howto-integrate-internal-vnet-appgateway.md) .

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Přiřadil (a) jsem vlastní doménu API Management a publikovaný portál nefunguje

Po aktualizaci domény je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Přidal (a) jsem poskytovatele identity a nevidím ho na portálu

Po nakonfigurování zprostředkovatele identity (například Azure AD, Azure AD B2C) je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily. Ujistěte se, že stránky portálu pro vývojáře obsahují widget tlačítek OAuth.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Nastavil (a) delegování a portál ho nepoužívá

Po nastavení delegování je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Změny konfigurace mých API Management nebyly rozšířeny na portál pro vývojáře.

Většina změn konfigurace (například virtuální síť, přihlášení, podmínek produktu) vyžaduje [Opětovné publikování portálu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Při použití interaktivní konzoly se mi zobrazuje chyba CORS

Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Přidejte [zásadu CORS](api-management-cross-domain-policies.md#CORS) do vašich rozhraní API a vyřešte problém s CORS.

Stav zásad CORS můžete zjistit v části **Přehled portálu** služby API Management v Azure Portal. Výstražné pole indikuje chybějící nebo nesprávně nakonfigurovanou zásadu.

![Snímek obrazovky, který ukazuje, kde můžete zjistit stav zásad CORS.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automaticky použít zásadu CORS kliknutím na tlačítko **Povolit CORS**

Můžete také povolit CORS ručně.

1. Pokud chcete zobrazit generovaný kód zásad, vyberte **ho ručně na odkaz na globální úrovni** .
2. V Azure Portal přejděte ke **všem rozhraním API** v části **rozhraní API** služby API Management.
3. Vyberte **</>** ikonu v části **příchozí zpracování** .
4. Vložte zásadu do **<inbound>** části souboru XML. Ujistěte se, že **<origin>** hodnota odpovídá vaší doméně portálu pro vývojáře.

> [!NOTE]
> 
> Pokud použijete zásadu CORS v oboru produktu místo rozhraní API (s) a vaše rozhraní API použije ověřování pomocí klíče předplatného v hlavičce, nebude vaše konzola fungovat.
>
> Prohlížeč automaticky vydá požadavek na možnost HTTP, který neobsahuje hlavičku s klíčem předplatného. Z důvodu chybějícího klíče předplatného API Management nemůže přidružit volání možností k produktu, takže nemůže uplatnit zásadu CORS.
>
> Jako alternativní řešení můžete klíč předplatného předat v parametru dotazu.

> [!NOTE]
> 
> Spustí se jenom jedna zásada CORS. Pokud jste zadali několik zásad CORS (například na úrovni rozhraní API a na úrovni All-API), vaše interaktivní konzola nemusí fungovat podle očekávání.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jaká oprávnění potřebuji k úpravám portálu pro vývojáře?

Pokud se vám při `Oops. Something went wrong. Please try again later.` otevření portálu v režimu správy zobrazuje chyba, možná nemáte požadovaná oprávnění (Azure RBAC).

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

| Prohlížeč                     | Podporováno       |
|-----------------------------|-----------------|
| Apple Safari                | Ano<sup>1</sup> |
| Google Chrome               | Ano<sup>1</sup> |
| Microsoft Edge              | Ano<sup>1</sup> |
| Microsoft Internet Explorer | Ne              |
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
