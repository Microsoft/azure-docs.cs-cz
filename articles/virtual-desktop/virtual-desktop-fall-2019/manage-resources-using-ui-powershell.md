---
title: Nasazení nástroje pro správu pro virtuální plochu Windows (Classic) pomocí instančního objektu – Azure
description: Postup nasazení nástroje pro správu pro virtuální plochu Windows (Classic) pomocí prostředí PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069013"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Nasazení nástroje pro správu virtuálních ploch Windows (Classic) pomocí PowerShellu

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Tento článek vám ukáže, jak nasadit nástroj pro správu pomocí PowerShellu.

## <a name="important-considerations"></a>Důležité informace

Každé předplatné tenanta Azure Active Directory (Azure AD) vyžaduje vlastní samostatné nasazení nástroje pro správu. Tento nástroj nepodporuje scénáře B2B (Business-to-Business) pro Azure AD.

Tento nástroj pro správu je ukázka. Microsoft bude poskytovat důležité aktualizace zabezpečení a kvality. [Zdrojový kód je k dispozici na GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Bez ohledu na to, jestli jste zákazník nebo partner, doporučujeme, abyste nástroj přizpůsobili, aby vyhovoval vašim obchodním potřebám.

Následující prohlížeče jsou kompatibilní s nástrojem pro správu:

- Google Chrome 68 nebo novější
- Microsoft Edge 40,15063 nebo novější
- Mozilla Firefox 52,0 nebo novější
- Safari 10 nebo novější (jenom macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co potřebujete k nasazení nástroje pro správu

Před nasazením nástroje pro správu budete potřebovat uživatele Azure Active Directory (Azure AD) k vytvoření registrace aplikace a nasazení uživatelského rozhraní pro správu. Tento uživatel musí:

- Mít oprávnění k vytváření prostředků ve vašem předplatném Azure
- Mít oprávnění k vytvoření aplikace služby Azure AD. Pomocí těchto kroků zkontrolujete, jestli má uživatel požadovaná oprávnění, podle pokynů v tématu [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Po nasazení a konfiguraci nástroje pro správu doporučujeme, abyste požádali uživatele, aby spustil uživatelské rozhraní pro správu, aby se zajistilo, že všechno funguje. Uživatel, který spouští uživatelské rozhraní pro správu, musí mít přiřazení role, které jim umožní zobrazit nebo upravit klienta virtuální plochy Windows.

## <a name="set-up-powershell"></a>Nastavení PowerShellu

Začněte tím, že se přihlásíte k modulům AZ a Azure AD PowerShell. Tady je postup, jak se přihlásit:

1. Otevřete PowerShell jako správce a přejděte do adresáře, kam jste uložili skripty PowerShellu.
2. Přihlaste se k Azure pomocí účtu, který má oprávnění vlastníka nebo přispěvatele v předplatném Azure, které plánujete použít k vytvoření nástroje pro správu, spuštěním následující rutiny:

    ```powershell
    Login-AzAccount
    ```

3. Spuštěním následující rutiny se přihlaste ke službě Azure AD pomocí stejného účtu, který jste použili pro modul AZ PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Potom přejděte do složky, kam jste uložili dva skripty PowerShellu z RDS-Templates úložiště GitHub.

Nechejte okno prostředí PowerShell, které jste použili k přihlášení, a spusťte další rutiny prostředí PowerShell, které se přihlásí.

## <a name="create-an-azure-active-directory-app-registration"></a>Vytvoření registrace aplikace Azure Active Directory

Aby bylo možné úspěšně nasadit a nakonfigurovat nástroj pro správu, je třeba nejprve stáhnout následující skripty PowerShellu z [úložiště GitHub šablon RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) .

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Spuštěním následujících příkazů vytvořte registraci aplikace s požadovanými oprávněními API:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teď, když jste dokončili registraci aplikace služby Azure AD, můžete nasadit nástroj pro správu.

## <a name="deploy-the-management-tool"></a>Nasazení nástroje pro správu

Spuštěním následujících příkazů PowerShellu nasaďte Nástroj pro správu a přidružte ho k objektu služby, který jste právě vytvořili:

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

Po vytvoření webové aplikace je nutné přidat identifikátor URI přesměrování do aplikace Azure AD, aby bylo možné uživatele úspěšně přihlašovat.

## <a name="set-the-redirect-uri"></a>Nastavit identifikátor URI pro přesměrování

Spuštěním následujících příkazů PowerShellu načtěte adresu URL webové aplikace a nastavte ji jako identifikátor URI pro přesměrování ověřování (označuje se taky jako adresa URL odpovědi):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Teď, když jste přidali identifikátor URI pro přesměrování, budete dál muset aktualizovat adresu URL rozhraní API, aby mohl nástroj pro správu komunikovat se službou back-endu API.

## <a name="update-the-api-url-for-the-web-application"></a>Aktualizace adresy URL rozhraní API pro webovou aplikaci

Spuštěním následujícího skriptu aktualizujte konfiguraci adresy URL rozhraní API v front-endu webové aplikace:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teď, když máte plně nakonfigurovanou webovou aplikaci nástroje pro správu, je čas ověřit aplikaci Azure AD a vyjádřit souhlas.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Ověření aplikace Azure AD a poskytnutí souhlasu

Ověření konfigurace aplikace Azure AD a poskytnutí souhlasu:

1. Otevřete internetový prohlížeč a přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce.
2. Na panelu hledání v horní části Azure Portal vyhledejte **Registrace aplikací** a vyberte položku v části **služby**.
3. Vyberte **všechny aplikace** a vyhledejte jedinečný název aplikace, který jste zadali pro skript prostředí PowerShell v části [vytvoření registrace aplikace Azure Active Directory](#create-an-azure-active-directory-app-registration).
4. V panelu na levé straně prohlížeče vyberte **ověřování** a ujistěte se, že identifikátor URI přesměrování je stejný jako adresa URL webové aplikace pro nástroj pro správu, jak je znázorněno na následujícím obrázku.

   [![Ověřovací stránka se zadaným identifikátorem URI ](../media/management-ui-redirect-uri-inline.png) pro přesměrování](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. Na levém panelu vyberte **oprávnění rozhraní API** a potvrďte, že se přidaná oprávnění přidala. Pokud jste globální správce, klikněte na tlačítko **udělit souhlas `tenantname` správce** a postupujte podle pokynů k zadání souhlasu správce pro vaši organizaci.

    [![Stránka ](../media/management-ui-permissions-inline.png) oprávnění API](../media/management-ui-permissions-expanded.png#lightbox)

Nyní můžete začít používat nástroj pro správu.

## <a name="use-the-management-tool"></a>Použití nástroje pro správu

Teď, když jste nastavili nástroj pro správu kdykoli, můžete ho spustit kdykoli a kdekoli. Tady je postup, jak tento nástroj spustit:

1. Otevřete adresu URL webové aplikace ve webovém prohlížeči. Pokud si adresu URL nepamatujete, můžete se přihlásit k Azure, najít službu App Service, kterou jste nasadili pro nástroj pro správu, a pak vybrat adresu URL.
2. Přihlaste se pomocí svých přihlašovacích údajů k virtuálnímu počítači s Windows.

   > [!NOTE]
   > Pokud jste neudělili souhlas správce při konfiguraci nástroje pro správu, každý uživatel, který se přihlásí, bude muset poskytnout vlastní souhlas uživatele, aby mohl nástroj použít.

3. Po zobrazení výzvy k výběru skupiny tenantů vyberte v rozevíracím seznamu **výchozí skupinu tenantů** .
4. Když vyberete **výchozí skupinu tenantů**, měla by se zobrazit nabídka na levé straně okna. V této nabídce najděte název skupiny tenantů a vyberte ji.

   > [!NOTE]
   > Pokud máte vlastní skupinu tenantů, zadejte název ručně místo volby v rozevíracím seznamu.

## <a name="report-issues"></a>Nahlášení potíží

Pokud jste nahlásili mezi problémy s nástrojem pro správu nebo jinými nástroji pro virtuální počítače s Windows, postupujte podle pokynů v části [šablony Azure Resource Manager pro vzdálenou plochu](https://github.com/Azure/RDS-Templates/blob/master/README.md) , které jim podávají oznámení na GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit nástroj pro správu a připojit se k němu, se dozvíte, jak pomocí Azure Service Health monitorovat problémy se službami a Poradce pro stav. Další informace najdete v našem [kurzu nastavení výstrah služby](set-up-service-alerts-2019.md).
