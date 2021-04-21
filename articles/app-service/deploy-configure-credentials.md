---
title: Konfigurace přihlašovacích údajů pro nasazení
description: Přečtěte si, jaké typy přihlašovacích údajů nasazení jsou v Azure App Service a jak je nakonfigurovat a používat.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830570"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service
Aby bylo možné zabezpečit nasazení aplikace z místního počítače, [Azure App Service](./overview.md) podporuje dva typy přihlašovacích údajů pro nasazení [místního úložiště Git](deploy-local-git.md) a [FTP/S](deploy-ftp.md). Tyto přihlašovací údaje nejsou stejné jako přihlašovací údaje vašeho předplatného Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Stránka **centra pro vývoj (Classic)** v Azure Portal, což je staré prostředí pro nasazení, bude zastaralá v březnu 2021. Tato změna nebude mít vliv na žádná existující nastavení nasazení v aplikaci a můžete pokračovat ve správě nasazení aplikace na stránce **centra nasazení** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Konfigurace přihlašovacích údajů uživatele do oboru

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) . Nahraďte \<username> a \<password> pomocí uživatelského jména a hesla pro nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo jako `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Pomocí Azure PowerShell nelze nakonfigurovat pověření oboru uživatele. Použijte jinou metodu, nebo zvažte [použití pověření pro rozsah aplikace](#appscope). 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Přihlašovací údaje oboru uživatele můžete nakonfigurovat na [stránce prostředků](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)libovolné aplikace. Bez ohledu na to, která aplikace tyto přihlašovací údaje konfigurujete, platí pro všechny aplikace pro všechna předplatná v účtu Azure. 

V [Azure Portal](https://portal.azure.com)musíte mít alespoň jednu aplikaci, abyste mohli získat přístup k přihlašovací stránce nasazení. Konfigurace přihlašovacích údajů uživatelského oboru:

1. V levé nabídce aplikace vyberte > pověření **centra nasazení**  >  **FTPS** nebo **místní přihlašovací údaje Git/FTPS**.

    ![Ukazuje, jak můžete vybrat řídicí panel FTP z centra nasazení v Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Posuňte se dolů k **oboru uživatele**, nakonfigurujte **uživatelské jméno** a **heslo** a pak vyberte **Uložit**.

Po nastavení přihlašovacích údajů pro nasazení můžete na stránce **přehledu** vaší aplikace najít uživatelské jméno nasazení *Git* .

![Ukazuje, jak najít uživatelské jméno nasazení Git na stránce s přehledem vaší aplikace.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Pokud je nakonfigurované nasazení Git, na stránce se zobrazí **uživatelské jméno pro Git/nasazení**; v opačném případě se jedná o **uživatelské jméno pro FTP/nasazení**.

> [!NOTE]
> Azure nezobrazuje vaše heslo nasazení uživatelského oboru. Pokud zapomenete heslo, můžete přihlašovací údaje resetovat podle kroků v této části.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Použití přihlašovacích údajů pro obor uživatele s FTP/FTPS

Ověřování u koncového bodu FTP/FTPS pomocí pověření uživatele v oboru vyžaduje uživatelské jméno v následujícím formátu: `<app-name>\<user-name>`

Vzhledem k tomu, že přihlašovací údaje oboru uživatele jsou propojené s uživatelem a ne konkrétním prostředkem, musí být uživatelské jméno v tomto formátu, aby se akce přihlášení nasměrovala do pravého koncového bodu aplikace.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Získat pověření pro rozsah aplikace

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pomocí příkazu [AZ WebApp Deployment list-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Získejte přihlašovací údaje rozsahu aplikace. Například:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Pro [místní nasazení Git](deploy-local-git.md)můžete k získání vzdáleného identifikátoru URI Git pro vaši aplikaci použít taky příkaz [AZ WebApp Deployment list-Publish-Credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) , kde jsou už vložené přihlašovací údaje rozsahu aplikace. Například:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Pomocí příkazu [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) Získejte přihlašovací údaje rozsahu aplikace. Například:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. V levé nabídce aplikace vyberte možnost **centrum nasazení**  >  **FTPS přihlašovací údaje** nebo **místní přihlašovací údaje Git/FTPS**.

    ![Ukazuje, jak můžete vybrat řídicí panel FTP z centra nasazení v Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. V části **obor aplikace** vyberte odkaz **Kopírovat** pro zkopírování uživatelského jména nebo hesla.

-----

## <a name="reset-application-scope-credentials"></a>Resetovat přihlašovací údaje rozsahu aplikace

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Obnovte přihlašovací údaje rozsahu aplikace pomocí příkazu [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Obnovte přihlašovací údaje rozsahu aplikace pomocí příkazu [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. V levé nabídce aplikace vyberte možnost **centrum nasazení**  >  **FTPS přihlašovací údaje** nebo **místní přihlašovací údaje Git/FTPS**.

    ![Ukazuje, jak můžete vybrat řídicí panel FTP z centra nasazení v Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. V části **obor aplikace** vyberte **obnovit**.

-----

## <a name="disable-basic-authentication"></a>Zakázat základní ověřování

Některé organizace potřebují splňovat požadavky na zabezpečení a místo toho mají přístup přes FTP nebo WebDeploy. Členové organizace tak můžou k tomuto App Services přistupovat jenom prostřednictvím rozhraní API, která jsou řízená Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Chcete-li zakázat přístup FTP k webu, spusťte následující příkaz CLI. Zástupné symboly nahraďte skupinou prostředků a názvem webu. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pokud chcete potvrdit, že je přístup k FTP blokovaný, můžete se pokusit ověřit pomocí klienta FTP, jako je FileZilly. Přihlašovací údaje pro publikování načtete tak, že přejdete do okna Přehled vaší lokality a kliknete na stáhnout profil publikování. K ověření použijte název hostitele FTP, uživatelské jméno a heslo souboru a zobrazí se chybová zpráva 401 s oznámením, že nemáte oprávnění.

### <a name="webdeploy-and-scm"></a>WebDeploy a SCM

Chcete-li zakázat základní přístup k ověřování na portu WebDeploy a na webu SCM, spusťte následující příkaz CLI. Zástupné symboly nahraďte skupinou prostředků a názvem webu. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pokud chcete potvrdit, že se přihlašovací údaje profilu publikování zablokují na webu WebDeploy, zkuste [publikovat webovou aplikaci pomocí sady Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Zakázání přístupu k rozhraní API

Rozhraní API v předchozí části se zavedlo na řízení přístupu na základě role Azure (Azure RBAC), což znamená, že můžete [vytvořit vlastní roli](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) a přiřadit k ní priveldged uživatele s nižšími nároky, aby nemohly na všech lokalitách povolit základní ověřování. Pokud chcete nakonfigurovat vlastní roli, [postupujte podle těchto pokynů](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

[Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) můžete použít také k auditování všech úspěšných žádostí o ověření a pomocí [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) vynutili tuto konfiguraci pro všechny weby v rámci vašeho předplatného.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak pomocí těchto přihlašovacích údajů nasadit aplikaci z [místního Gitu](deploy-local-git.md) nebo pomocí [FTP/S](deploy-ftp.md).
