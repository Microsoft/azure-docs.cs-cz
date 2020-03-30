---
title: Konfigurace přihlašovacích údajů pro nasazení
description: Zjistěte, jaké typy přihlašovacích údajů k nasazení jsou ve službě Azure App Service a jak je nakonfigurovat a používat.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266075"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurace přihlašovacích údajů pro nasazení služby Azure App Service
[Služba Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovacích údajů pro [místní nasazení Gitu](deploy-local-git.md) a [nasazení FTP/S](deploy-ftp.md). Tato pověření nejsou stejná jako pověření předplatného Azure.

* **Pověření na úrovni uživatele**: jedna sada přihlašovacích údajů pro celý účet Azure. Dá se použít k nasazení do služby App Service pro jakoukoli aplikaci, v libovolném předplatném, že účet Azure má oprávnění k přístupu. Jedná se o výchozí sadu, která se zobrazuje v grafickém uživatelském rozhraní portálu (například **přehled** a **vlastnosti** [stránky prostředků](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)aplikace). Když je uživateli udělen přístup k aplikacím prostřednictvím řízení přístupu na základě rolí (RBAC) nebo oprávnění coadmin, může tento uživatel používat vlastní pověření na úrovni uživatele, dokud nebude přístup odvolán. Nesdílejte tato pověření s ostatními uživateli Azure.

* **Přihlašovací údaje na úrovni aplikace**: jedna sada přihlašovacích údajů pro každou aplikaci. Lze jej použít k nasazení pouze do této aplikace. Přihlašovací údaje pro každou aplikaci se generují automaticky při vytváření aplikací. Nelze je nakonfigurovat ručně, ale lze je kdykoli resetovat. Aby uživateli byl udělen přístup k přihlašovacím údajům na úrovni aplikace prostřednictvím (RBAC), musí být v aplikaci přispěvatelem nebo vyšším (včetně integrované role přispěvatele webu). Čtenáři nemohou publikovat a nemají přístup k těmto přihlašovacím údajům.

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurace pověření na úrovni uživatele

Pověření na úrovni uživatele můžete nakonfigurovat na [stránce prostředků](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)libovolné aplikace . Bez ohledu na to, ve které aplikaci tato pověření nakonfigurujete, platí pro všechny aplikace a pro všechna předplatná ve vašem účtu Azure. 

### <a name="in-the-cloud-shell"></a>V cloudovém prostředí

Chcete-li nakonfigurovat uživatele nasazení v [prostředí Cloud Shell](https://shell.azure.com), spusťte příkaz pro nastavení uživatelské sady nasazení [az webapp.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Nahraďte \<uživatelské \<jméno> a heslo> uživatelským jménem a heslem nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @. 
- Heslo musí mít trvat nejméně osm znaků, přičemž dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo `null`jako . Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

### <a name="in-the-portal"></a>Na portálu

Na webu Azure Portal musíte mít alespoň jednu aplikaci, než budete mít přístup k přihlašovací stránce nasazení. Postup konfigurace přihlašovacích údajů na úrovni uživatele:

1. Na [portálu Azure](https://portal.azure.com)v levé nabídce vyberte panel **Deployment center** > **FTP** > **Dashboard** **centra pro nasazení služby App Services** > **\<any_app>. **  > 

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Pokud jste nasazení Gitu už nakonfigurovali, vyberte**&lt;možnost ** **Služby** > aplikací any_app> >  **centrum** > nasazení**FTP/Credentials**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Vyberte **Pověření uživatele**, nakonfigurujte uživatelské jméno a heslo a pak vyberte **Uložit pověření**.

Jakmile nastavíte přihlašovací údaje pro nasazení, najdete uživatelské jméno pro nasazení *Gitu* na stránce **Přehled** aplikace,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Pokud je nakonfigurováno nasazení Gitu, na stránce se zobrazí **uživatelské jméno Git/deployment**; v opačném případě **uživatelské jméno FTP/deployment**.

> [!NOTE]
> Azure nezobrazuje heslo pro nasazení na úrovni uživatele. Pokud heslo zapomenete, můžete obnovit přihlašovací údaje podle kroků v této části.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Použití pověření na úrovni uživatele s FTP/FTPS

Ověření koncového bodu FTP/FTPS pomocí požadavků pověření na úrovni uživatele pomocí požadavků na uživatelská úroveň uživatelské jméno v následujícím formátu:`<app-name>\<user-name>`

Vzhledem k tomu, že pověření na úrovni uživatele jsou propojeny s uživatelem a není konkrétní prostředek, uživatelské jméno musí být v tomto formátu přímé akce přihlášení na koncový bod aplikace.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Získání a obnovení přihlašovacích údajů na úrovni aplikace
Získání přihlašovacích údajů na úrovni aplikace:

1. Na [portálu Azure](https://portal.azure.com)v yberte v levé nabídce **služby App Services** > **&lt;any_app>**  >  **centrum** > nasazení**FTP/pověření**.

2. Vyberte **Pověření aplikace**a vyberte odkaz **Kopírovat,** chcete-li zkopírovat uživatelské jméno nebo heslo.

Pokud chcete obnovit přihlašovací údaje na úrovni aplikace, vyberte **Obnovit přihlašovací údaje** ve stejném dialogovém okně.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak pomocí těchto přihlašovacích údajů nasadit aplikaci z [místního Gitu](deploy-local-git.md) nebo pomocí [FTP/S](deploy-ftp.md).
