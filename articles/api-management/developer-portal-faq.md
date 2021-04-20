---
title: Portál pro vývojáře – Nejčastější dotazy
titleSuffix: Azure API Management
description: Nejčastější dotazy týkající se portálu pro vývojáře v API Management. Portál pro vývojáře je přizpůsobitelný web, kde můžou uživatelé rozhraní API prozkoumat vaše rozhraní API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741607"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management portálu pro vývojáře – Nejčastější dotazy

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Co když potřebuji funkci, která není na portálu podporovaná?

V [úložišti GitHubu](https://github.com/Azure/api-management-developer-portal) můžete otevřít žádost o funkci, nebo můžete [chybějící funkce implementovat sami](developer-portal-implement-widgets.md). Přečtěte si další informace o [rozšiřitelnosti](api-management-howto-developer-portal.md#managed-vs-self-hosted)portálu pro vývojáře.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Můžu mít v jedné API Management službě víc portálů pro vývojáře?

Můžete mít jeden spravovaný portál a několik samoobslužných portálů. Obsah všech portálů je uložený ve stejné API Management službě, takže budou identické. Pokud chcete odlišit vzhled a funkčnost portálů, můžete je sami hostovat s vlastními widgety, které dynamicky přizpůsobují stránky za běhu, například na základě adresy URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Podporuje portál Azure Resource Manager šablony nebo je kompatibilní se sadou API Management DevOps Resource Kit?

No.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Je obsah portálu uložený s funkcí zálohování a obnovení v API Management?

No.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Musím pro závislosti spravovaného portálu povolit další připojení VNet?

Ve většině případů – ne.

Pokud je vaše služba API Management v interní virtuální síti, portál pro vývojáře je přístupný jenom v rámci sítě. Název hostitele koncového bodu správy se musí překládat na interní virtuální IP adresu služby z počítače, který používáte pro přístup k rozhraní pro správu portálu. Ujistěte se, že je koncový bod správy zaregistrován v DNS. V případě chybné konfigurace se zobrazí chyba: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Pokud je vaše služba API Management interní virtuální síť a k ní přistupujete prostřednictvím Application Gateway z Internetu, Nezapomeňte povolit připojení k portálu pro vývojáře a koncovým bodům správy API Management. Možná budete muset zakázat pravidla firewallu webových aplikací. Další podrobnosti najdete v [tomto článku v dokumentaci](api-management-howto-integrate-internal-vnet-appgateway.md) .

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Mám přiřazenou vlastní doménu API Management a publikovaný portál nefunguje

Po aktualizaci domény je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Přidal (a) jsem poskytovatele identity a nevidím ho na portálu

Po nakonfigurování zprostředkovatele identity (například Azure AD, Azure AD B2C) je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily. Ujistěte se, že stránky portálu pro vývojáře obsahují widget tlačítek OAuth.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Nastavil (a) delegování a portál ho nepoužívá

Po nastavení delegování je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) , aby se změny projevily.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Změny konfigurace mých API Management nebyly rozšířeny na portál pro vývojáře.

Většina změn konfigurace (například virtuální síť, přihlášení, podmínek produktu) vyžaduje [Opětovné publikování portálu](api-management-howto-developer-portal-customize.md#publish).

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Při použití interaktivní konzoly se mi zobrazuje chyba CORS

Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Přidejte [zásadu CORS](api-management-cross-domain-policies.md#CORS) do vašich rozhraní API a vyřešte problém s CORS.

Stav zásad CORS můžete zjistit v části **Přehled portálu** služby API Management v Azure Portal. Výstražné pole indikuje chybějící nebo nesprávně nakonfigurovanou zásadu.

> [!NOTE]
> 
> Spustí se jenom jedna zásada CORS. Pokud jste zadali několik zásad CORS (například na úrovni rozhraní API a na úrovni All-API), vaše interaktivní konzola nemusí fungovat podle očekávání.

![Snímek obrazovky, který ukazuje, kde můžete zjistit stav zásad CORS.](media/developer-portal-faq/cors-azure-portal.png)

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
> Prohlížeč automaticky vydá `OPTIONS` požadavek HTTP, který neobsahuje hlavičku s klíčem předplatného. Z důvodu chybějícího klíče předplatného API Management nemůže přidružit `OPTIONS` volání k produktu, takže nejde použít zásadu CORS.
>
> Jako alternativní řešení můžete klíč předplatného předat v parametru dotazu.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Jaká je funkce proxy CORS a kdy ji mám použít?

Vyberte možnost **použít proxy server CORS** v konfiguraci widgetu podrobnosti operace rozhraní API, abyste mohli směrovat volání rozhraní API interaktivní konzoly přes back-end portálu ve vaší službě API Management. V této konfiguraci už nemusíte pro svoje rozhraní API používat zásadu CORS a připojení ke koncovému bodu brány z místního počítače se nevyžaduje. Pokud jsou rozhraní API vystavená prostřednictvím samoobslužné brány nebo je služba ve virtuální síti, je potřeba připojení ze služby back-end API Management k bráně. Pokud používáte Samoobslužný portál, určete koncový bod back-end portálu pomocí `backendUrl` Možnosti v konfiguračních souborech. V opačném případě portál pro místní hostování nebude vědět o umístění služby back-end.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jaká oprávnění potřebuji k úpravám portálu pro vývojáře?

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

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Zobrazuje se `Unable to start the portal. See if settings are specified correctly (...)` Chyba

Tato chyba se zobrazí, když `GET` volání `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` neproběhne úspěšně. Volání je vystaveno z prohlížeče pomocí rozhraní pro správu portálu.

Pokud je vaše služba API Management ve virtuální síti, přečtěte si [otázku připojení virtuální](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)sítě.

Selhání volání může být způsobeno také certifikátem TLS/SSL, který je přiřazen vlastní doméně a není důvěryhodný pro prohlížeč. Jako zmírnění můžete odebrat vlastní doménu koncového bodu správy, API Management se vrátí k výchozímu koncovému bodu s důvěryhodným certifikátem.

## <a name="whats-the-browser-support-for-the-portal"></a>Jaká je podpora prohlížeče pro portál?

| Prohlížeč                     | Podporováno       |
|-----------------------------|-----------------|
| Apple Safari                | Ano<sup>1</sup> |
| Google Chrome               | Ano<sup>1</sup> |
| Microsoft Edge              | Ano<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ano<sup>1</sup> |

 <small><sup>1</sup> podporuje se v obou nejnovějších produkčních verzích.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Místní vývoj mého samoobslužného portálu už nefunguje

Pokud vaše místní verze portálu pro vývojáře nemůže ukládat nebo načítat informace z účtu úložiště nebo instance API Management, mohlo dojít k vypršení platnosti tokenů SAS. Tuto chybu můžete opravit vygenerováním nových tokenů. Pokyny najdete v kurzu pro [samoobslužné hostování portálu pro vývojáře](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings).

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Jak mohu odebrat obsah portálu pro vývojáře zřízený do služby API Management?

Zadejte požadované parametry ve `scripts.v3/cleanup.bat` skriptu v [úložišti GitHub](https://github.com/Azure/api-management-developer-portal)portálu pro vývojáře a spusťte skript.

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Návody povolit ověřování jednotného přihlašování (SSO) pro samoobslužný portál pro vývojáře?

Portál pro vývojáře kromě dalších metod ověřování podporuje jednotné přihlašování (SSO). K ověřování pomocí této metody je nutné provést volání `/signin-sso` s tokenem v parametru dotazu:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Generování tokenů uživatelů
Můžete generovat *tokeny specifické pro uživatele* (včetně tokenů správce) pomocí operace [získání sdíleného přístupového tokenu](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) [REST API API Management](/rest/api/apimanagement/apimanagementrest/api-management-rest).

> [!NOTE]
> Token musí být kódovaný v adrese URL.

## <a name="next-steps"></a>Další kroky

Další informace o novém portálu pro vývojáře:

- [Přístup k portálu spravovaného vývojáře a jeho přizpůsobení](api-management-howto-developer-portal-customize.md)
- [Nastavení samoobslužné verze portálu](developer-portal-self-host.md)
- [Implementace vlastního widgetu](developer-portal-implement-widgets.md)

Procházet Další prostředky:

- [Úložiště GitHub se zdrojovým kódem](https://github.com/Azure/api-management-developer-portal)

