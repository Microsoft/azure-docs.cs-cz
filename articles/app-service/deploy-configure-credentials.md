---
title: Konfigurace přihlašovacích údajů pro nasazení
description: Přečtěte si, jaké typy přihlašovacích údajů nasazení jsou v Azure App Service a jak je nakonfigurovat a používat.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 50b3cae00110a64e4d95171822bf1d2a282d2cc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715413"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovacích údajů pro nasazení [místního úložiště Git](deploy-local-git.md) a [FTP/S](deploy-ftp.md). Tyto přihlašovací údaje nejsou stejné jako přihlašovací údaje vašeho předplatného Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurace přihlašovacích údajů na úrovni uživatele

Přihlašovací údaje na úrovni uživatele můžete nakonfigurovat na [stránce prostředků](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)libovolné aplikace. Bez ohledu na to, která aplikace tyto přihlašovací údaje konfigurujete, platí pro všechny aplikace a pro všechna předplatná v účtu Azure. 

### <a name="in-the-cloud-shell"></a>V Cloud Shell

Chcete-li nakonfigurovat uživatele nasazení v [Cloud Shell](https://shell.azure.com), spusťte příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Nahraďte \<username> a \<password> pomocí uživatelského jména a hesla pro nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo jako `null` . Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

### <a name="in-the-portal"></a>Na portálu

V Azure Portal musíte mít alespoň jednu aplikaci, abyste mohli získat přístup k přihlašovací stránce nasazení. Konfigurace přihlašovacích údajů na úrovni uživatele:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **App Services**  >  **\<any_app>**  >  **Deployment center**  >  **FTP**  >  **řídicí panel**FTP centra nasazení.

    ![Ukazuje, jak můžete vybrat řídicí panel FTP z centra nasazení v Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

    Nebo, pokud jste již nakonfigurovali nasazení Git, vyberte **App Services**  >  ** &lt; any_app>**  >  **Deployment center**  >  **FTP/přihlašovacích údajů**centra nasazení.

    ![Ukazuje, jak můžete vybrat řídicí panel FTP z centra nasazení v Azure App Services pro vaše nakonfigurované nasazení Git.](./media/app-service-deployment-credentials/access-with-git.png)

2. Vyberte **přihlašovací údaje uživatele**, nakonfigurujte uživatelské jméno a heslo a pak vyberte **uložit přihlašovací údaje**.

Po nastavení přihlašovacích údajů pro nasazení můžete na stránce **přehledu** vaší aplikace najít uživatelské jméno nasazení *Git* .

![Ukazuje, jak najít uživatelské jméno nasazení Git na stránce s přehledem vaší aplikace.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Pokud je nakonfigurované nasazení Git, na stránce se zobrazí **uživatelské jméno pro Git/nasazení**; v opačném případě se jedná o **uživatelské jméno pro FTP/nasazení**.

> [!NOTE]
> Azure nezobrazuje vaše heslo nasazení na úrovni uživatele. Pokud zapomenete heslo, můžete přihlašovací údaje resetovat podle kroků v této části.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Použití přihlašovacích údajů na úrovni uživatele s FTP/FTPS

Ověřování u koncového bodu FTP/FTPS pomocí přihlašovacích údajů na úrovni uživatele requirers uživatelské jméno v následujícím formátu: `<app-name>\<user-name>`

Vzhledem k tomu, že přihlašovací údaje na úrovni uživatele jsou propojené s uživatelem a ne konkrétním prostředkem, musí být uživatelské jméno v tomto formátu, aby se akce přihlášení nasměrovala do pravého koncového bodu aplikace.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Získání a resetování přihlašovacích údajů na úrovni aplikace
Získání přihlašovacích údajů na úrovni aplikace:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **App Services**  >  ** &lt; any_app>**  >  **služby pro nasazení**  >  **FTP/přihlašovací údaje**.

2. Vyberte možnost **přihlašovací údaje aplikace**a kliknutím na odkaz **Kopírovat** zkopírujte uživatelské jméno nebo heslo.

Pokud chcete resetovat přihlašovací údaje na úrovni aplikace, vyberte **resetovat přihlašovací údaje** ve stejném dialogu.

## <a name="disable-basic-authentication"></a>Zakázat základní ověřování

Některé organizace potřebují splňovat požadavky na zabezpečení a místo toho mají přístup přes FTP nebo WebDeploy. Členové organizace tak můžou k tomuto App Services přistupovat jenom prostřednictvím rozhraní API, která jsou řízená Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Chcete-li zakázat přístup FTP k webu, spusťte následující příkaz CLI. Zástupné symboly nahraďte skupinou prostředků a názvem webu. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pokud chcete potvrdit, že je přístup k FTP blokovaný, můžete se pokusit ověřit pomocí klienta FTP, jako je FileZilly. Přihlašovací údaje pro publikování načtete tak, že přejdete do okna Přehled vaší lokality a kliknete na stáhnout profil publikování. K ověření použijte název hostitele FTP, uživatelské jméno a heslo souboru a zobrazí se chybová zpráva 401 s oznámením, že nemáte oprávnění.

### <a name="webdeploy-and-scm"></a>WebDeploy a SCM

Chcete-li zakázat základní přístup k ověřování na portu WebDeploy a na webu SCM, spusťte následující příkaz CLI. Zástupné symboly nahraďte skupinou prostředků a názvem webu. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pokud chcete potvrdit, že se přihlašovací údaje profilu publikování zablokují na webu WebDeploy, zkuste [publikovat webovou aplikaci pomocí sady Visual Studio 2019](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

### <a name="disable-access-to-the-api"></a>Zakázání přístupu k rozhraní API

Rozhraní API v předchozí části se zavedlo na řízení přístupu na základě role Azure (Azure RBAC), což znamená, že můžete [vytvořit vlastní roli](https://docs.microsoft.com/azure/role-based-access-control/custom-roles#steps-to-create-a-custom-role) a přiřadit k ní priveldged uživatele s nižšími nároky, aby nemohly na všech lokalitách povolit základní ověřování. Pokud chcete nakonfigurovat vlastní roli, [postupujte podle těchto pokynů](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

[Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) můžete použít také k auditování všech úspěšných žádostí o ověření a pomocí [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) vynutili tuto konfiguraci pro všechny weby v rámci vašeho předplatného.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak pomocí těchto přihlašovacích údajů nasadit aplikaci z [místního Gitu](deploy-local-git.md) nebo pomocí [FTP/S](deploy-ftp.md).
