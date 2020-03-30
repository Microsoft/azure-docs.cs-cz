---
title: Nasazení nástroje pro správu pro Virtuální plochu Windows pomocí instančního objektu – Azure
description: Jak nasadit nástroj pro správu pro virtuální plochu Windows pomocí PowerShellu.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127791"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Nasazení nástroje pro správu pomocí PowerShellu

Tento článek vám ukáže, jak nasadit nástroj pro správu pomocí prostředí PowerShell.

## <a name="important-considerations"></a>Důležité informace

Každé předplatné klienta Azure Active Directory (Azure AD) potřebuje vlastní samostatné nasazení nástroje pro správu. Tento nástroj nepodporuje scénáře Azure AD Business-to-Business (B2B). 

Tento nástroj pro správu je ukázka. Společnost Microsoft bude poskytovat důležité aktualizace zabezpečení a kvality. [Zdrojový kód je k dispozici v GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Ať už jste zákazník nebo partner, doporučujeme vám přizpůsobit nástroj tak, aby uspokojil vaše obchodní potřeby.

Následující prohlížeče jsou kompatibilní s nástrojem pro správu:

- Google Chrome 68 nebo novější
- Microsoft Edge 40.15063 nebo novější
- Mozilla Firefox 52.0 nebo novější
- Safari 10 nebo novější (pouze macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co potřebujete k nasazení nástroje pro správu

Před nasazením nástroje pro správu budete potřebovat uživatele služby Azure Active Directory (Azure AD), který vytvoří registraci aplikace a nasadí uživatelské rozhraní pro správu. Tento uživatel musí:

- Máte oprávnění k vytváření prostředků ve vašem předplatném Azure
- Máte oprávnění k vytvoření aplikace Azure AD. Podle těchto kroků zkontrolujte, zda má uživatel požadovaná oprávnění podle pokynů v [části Požadovaná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Chcete-li úspěšně nasadit a nakonfigurovat nástroj pro správu, musíte nejprve stáhnout následující skripty prostředí PowerShell z [úložiště GitHub v rds-templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) a uložit je do stejné složky v místním počítači.

  - createWvdMgmtUxAppRegistration.ps1
  - aktualizaceWvdMgmtUxApiUrl.ps1

Po nasazení a konfiguraci nástroje pro správu doporučujeme požádat uživatele o spuštění uživatelského rozhraní pro správu, abyste se ujistili, že vše funguje. Uživatel, který spustí uživatelské rozhraní pro správu, musí mít přiřazení role, které mu umožní zobrazit nebo upravit klienta virtuální plochy Windows.

## <a name="set-up-powershell"></a>Nastavení PowerShellu

Můžete začít přihlášením k modulům Az i Azure AD PowerShell. Tady je postup, jak se přihlásit:

1. Otevřete PowerShell jako správce a přejděte do adresáře, do kterého jste uložili skripty PowerShellu.
2. Přihlaste se k Azure pomocí účtu, který má oprávnění vlastníka nebo přispěvatele k předplatnému Azure, které chcete použít k vytvoření nástroje pro správu spuštěním následující rutiny:

    ```powershell
    Login-AzAccount
    ```

3. Spusťte následující rutinu pro přihlášení k Azure AD se stejným účtem, který jste použili pro modul Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Poté přejděte do složky, do které jste uložili dva skripty prostředí PowerShell z úložiště GitHub v rds-templates.

Ponechte okno Prostředí PowerShell, které jste použili k přihlášení, otevřené, abyste spouštěli další rutiny Prostředí PowerShell při přihlášení.

## <a name="create-an-azure-active-directory-app-registration"></a>Vytvoření registrace aplikace Azure Active Directory

Chcete-li vytvořit registraci aplikace s požadovanými oprávněními rozhraní API, spusťte následující příkazy:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teď, když jste dokončili registraci aplikace Azure AD, můžete nasadit nástroj pro správu.

## <a name="deploy-the-management-tool"></a>Nasazení nástroje pro správu

Spusťte následující příkazy prostředí PowerShell k nasazení nástroje pro správu a přidružte ho k instančnímu objektu, který jste právě vytvořili:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Po vytvoření webové aplikace je nutné přidat identifikátor URI přesměrování do aplikace Azure AD, abyste úspěšně přihlašovali uživatele.

## <a name="set-the-redirect-uri"></a>Nastavení identifikátoru URI přesměrování

Spuštěním následujících příkazů Prostředí PowerShell načtěte adresu URL webové aplikace a nastavte ji jako identifikátor URI přesměrování ověřování (označovaný také jako adresa URL odpovědi):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Teď, když jste přidali identifikátor URI přesměrování, budete dále muset aktualizovat adresu URL rozhraní API, aby nástroj pro správu mohl pracovat se službou back-end rozhraní API.

## <a name="update-the-api-url-for-the-web-application"></a>Aktualizace adresy URL rozhraní API pro webovou aplikaci

Spusťte následující skript pro aktualizaci konfigurace adresy URL rozhraní API v front-endu webové aplikace:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teď, když jste plně nakonfigurovali webovou aplikaci nástroje pro správu, je čas ověřit aplikaci Azure AD a poskytnout souhlas.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Ověření aplikace Azure AD a poskytnutí souhlasu

Ověření konfigurace aplikace Azure AD a poskytnutí souhlasu:

1. Otevřete svůj internetový prohlížeč a přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí svého účtu pro správu.
2. Na vyhledávacím panelu v horní části portálu Azure vyhledejte **registrace aplikací** a vyberte položku v části **Služby**.
3. Vyberte **Všechny aplikace** a vyhledejte jedinečný název aplikace, který jste zadali pro skript Prostředí PowerShell, v [části Vytvoření registrace aplikace Azure Active Directory](#create-an-azure-active-directory-app-registration).
4. V panelu na levé straně prohlížeče vyberte **Ověřování** a ujistěte se, že identifikátor URI přesměrování je stejný jako adresa URL webové aplikace pro nástroj pro správu, jak je znázorněno na následujícím obrázku.
   
   [![Stránka ověřování se zadaným](media/management-ui-redirect-uri-inline.png) identifikátorem URI přesměrování](media/management-ui-redirect-uri-expanded.png#lightbox)

5. V levém panelu vyberte **oprávnění rozhraní API,** abyste potvrdili, že byla přidána oprávnění. Pokud jste globální správce, vyberte tlačítko **Udělit souhlas `tenantname` správce** a postupujte podle pokynů v dialogovém okně, abyste vaší organizaci poskytli souhlas správce.
    
    [![Stránka](media/management-ui-permissions-inline.png) oprávnění rozhraní API](media/management-ui-permissions-expanded.png#lightbox)

Nyní můžete začít používat nástroj pro správu.

## <a name="use-the-management-tool"></a>Použití nástroje pro správu

Nyní, když jste nástroj pro správu nastavili kdykoli, můžete jej spustit kdykoli a kdekoli. Zde je návod, jak spustit nástroj:

1. Otevřete adresu URL webové aplikace ve webovém prohlížeči. Pokud si nepamatujete adresu URL, můžete se přihlásit do Azure, najít službu aplikace, kterou jste nasadili pro nástroj pro správu, a pak vyberte adresu URL.
2. Přihlaste se pomocí přihlašovacích údajů k virtuální ploše Windows.
   
   > [!NOTE]
   > Pokud jste při konfiguraci nástroje pro správu neudělili souhlas správce, každý uživatel, který se přihlásí, bude muset poskytnout svůj vlastní souhlas uživatele, aby mohl nástroj používat.

3. Po zobrazení výzvy k výběru skupiny klientů vyberte v rozevíracím seznamu **výchozí skupinu klientů.**
4. Vyberete-li **výchozí skupinu klientů**, měla by se na levé straně okna zobrazit nabídka. V této nabídce najděte název skupiny klientů a vyberte ji.
   
   > [!NOTE]
   > Pokud máte vlastní skupinu klientů, zadejte název ručně namísto výběru z rozevíracího seznamu.

## <a name="report-issues"></a>Nahlášení potíží

Pokud narazíte na nějaké problémy s nástrojem pro správu nebo jinými nástroji virtuální plochy Windows, postupujte podle pokynů v [šablonách Azure Resource Manager pro vzdálenou plochu a](https://github.com/Azure/RDS-Templates/blob/master/README.md) oznamte je na GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit a připojit se k nástroji pro správu, můžete se naučit používat Azure Service Health ke sledování problémů se službami a doporučení stavu. Další informace najdete v našem [kurzu Nastavení upozornění služby](./set-up-service-alerts.md).
