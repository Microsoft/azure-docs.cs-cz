---
title: Konfigurace přihlašovacích údajů nasazení
description: Přečtěte si, jaké typy přihlašovacích údajů nasazení jsou v Azure App Service a jak je nakonfigurovat a používat.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c4e7a66a9535812da505045c26e7b1e6fbc6c661
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669973"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovacích údajů pro nasazení [místního úložiště Git](deploy-local-git.md) a [FTP/S](deploy-ftp.md). Tyto přihlašovací údaje nejsou stejné jako přihlašovací údaje vašeho předplatného Azure.

* **Přihlašovací údaje na úrovni uživatele**: jedna sada přihlašovacích údajů pro celý účet Azure. Dá se použít k nasazení App Service pro libovolnou aplikaci v libovolném předplatném, ke které má účet Azure oprávnění pro přístup. Je to výchozí sada, která je v grafickém uživatelském rozhraní na portálu (například **Přehled** a **vlastnosti** [stránky prostředku](../azure-resource-manager/manage-resources-portal.md#manage-resources)aplikace). Když se uživateli udělí přístup k aplikaci prostřednictvím Access Control na základě rolí (RBAC) nebo oprávnění spolusprávce, může tento uživatel použít vlastní přihlašovací údaje na úrovni uživatele, dokud přístup nevrátí. Nesdílejte tyto přihlašovací údaje s ostatními uživateli Azure.

* **Přihlašovací údaje na úrovni aplikace**: jedna sada přihlašovacích údajů pro každou aplikaci. Dá se použít jenom k nasazení do této aplikace. Přihlašovací údaje pro jednotlivé aplikace se generují automaticky při vytvoření aplikace. Nedají se nakonfigurovat ručně, ale můžete je kdykoli resetovat. Aby mohl uživatel udělit přístup k přihlašovacím údajům na úrovni aplikace přes (RBAC), musí být tento uživatel v aplikaci přispěvatel nebo vyšší. Čtenáři nemají oprávnění k publikování a nemají přístup k těmto přihlašovacím údajům.

## <a name="userscope"></a>Konfigurace přihlašovacích údajů na úrovni uživatele

Přihlašovací údaje na úrovni uživatele můžete nakonfigurovat na [stránce prostředků](../azure-resource-manager/manage-resources-portal.md#manage-resources)libovolné aplikace. Bez ohledu na to, která aplikace tyto přihlašovací údaje konfigurujete, platí pro všechny aplikace a pro všechna předplatná v účtu Azure. 

### <a name="in-the-cloud-shell"></a>V Cloud Shell

Chcete-li nakonfigurovat uživatele nasazení v [Cloud Shell](https://shell.azure.com), spusťte příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Nahraďte \<username > a \<hesla > pomocí uživatelského jména a hesla pro nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo jako `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

### <a name="in-the-portal"></a>Na portálu

V Azure Portal musíte mít alespoň jednu aplikaci, abyste mohli získat přístup k přihlašovací stránce nasazení. Konfigurace přihlašovacích údajů na úrovni uživatele:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **App Services** >  **\<any_app** ** > ovém** **řídicím panelu** **FTP > serveru FTP** .

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Nebo pokud jste už nakonfigurovali nasazení Git, vyberte **App Services** >  **&lt;any_app >**  > **centra nasazení** > **FTP/přihlašovací údaje**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Vyberte **přihlašovací údaje uživatele**, nakonfigurujte uživatelské jméno a heslo a pak vyberte **uložit přihlašovací údaje**.

Po nastavení přihlašovacích údajů pro nasazení můžete na stránce **přehledu** vaší aplikace najít uživatelské jméno nasazení *Git* .

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Pokud je nakonfigurované nasazení Git, na stránce se zobrazí **uživatelské jméno pro Git/nasazení**; v opačném případě se jedná o **uživatelské jméno pro FTP/nasazení**.

> [!NOTE]
> Azure nezobrazuje vaše heslo nasazení na úrovni uživatele. Pokud zapomenete heslo, můžete přihlašovací údaje resetovat podle kroků v této části.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Použití přihlašovacích údajů na úrovni uživatele s FTP/FTPS

Ověřování u koncového bodu FTP/FTPS pomocí přihlašovacích údajů na úrovni uživatele requirers uživatelské jméno v následujícím formátu: `<app-name>\<user-name>`

Vzhledem k tomu, že přihlašovací údaje na úrovni uživatele jsou propojené s uživatelem a ne konkrétním prostředkem, musí být uživatelské jméno v tomto formátu, aby se akce přihlášení nasměrovala do pravého koncového bodu aplikace.

## <a name="appscope"></a>Získání a resetování přihlašovacích údajů na úrovni aplikace
Získání přihlašovacích údajů na úrovni aplikace:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **App Services** >  **&lt;any_app >**  > **centra nasazení** > **FTP/přihlašovací údaje**.

2. Vyberte možnost **přihlašovací údaje aplikace**a kliknutím na odkaz **Kopírovat** zkopírujte uživatelské jméno nebo heslo.

Pokud chcete resetovat přihlašovací údaje na úrovni aplikace, vyberte **resetovat přihlašovací údaje** ve stejném dialogu.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak pomocí těchto přihlašovacích údajů nasadit aplikaci z [místního Gitu](deploy-local-git.md) nebo pomocí [FTP/S](deploy-ftp.md).
