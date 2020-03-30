---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78304919"
---
## <a name="configure-the-data-source"></a>Konfigurace zdroje dat

Pomocí následujících kroků nakonfigurujete Azure Data Explorer jako zdroj dat pro nástroj řídicího panelu. Tyto kroky podrobněji probereme v této části:

1. Vytvořte instanční objekt služby Azure Active Directory (Azure AD). Instanční objekt služby používá nástroj řídicího panelu pro přístup ke službě Azure Data Explorer.

1. Přidejte instanční objekt služby Azure AD do role *diváků* v databázi Průzkumníka dat Azure.

1. Zadejte vlastnosti připojení nástroje řídicího panelu na základě informací z instančního objektu služby Azure AD a otestujte připojení.

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Instancí objekt služby můžete vytvořit na [webu Azure Portal](#azure-portal) nebo pomocí prostředí příkazového řádku Azure [CLI.](#azure-cli) Bez ohledu na to, kterou metodu použijete, po vytvoření získáte hodnoty pro čtyři vlastnosti připojení, které budete používat v pozdějších krocích.

#### <a name="azure-portal"></a>portál Azure

1. Chcete-li vytvořit instanční objekt, postupujte podle pokynů v [dokumentaci k portálu Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. V části [Přiřadit aplikaci k](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) roli přiřaďte typu role **Čtečky** clusteru Průzkumníka dat Azure.

    1. V části [Získat hodnoty pro přihlášení](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) zkopírujte tři hodnoty vlastností, které jsou popsány v krocích: **ID adresáře** (ID klienta), **ID aplikace**a **heslo**.

1. Na webu Azure Portal vyberte **Předplatná** a zkopírujte ID předplatného, ve kterém jste vytvořili instanční objekt.

    ![ID předplatného - portál](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Vytvořte instanční objekt. Nastavte odpovídající obor a typ `reader`role .

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Další informace najdete [v tématu Vytvoření instančního objektu Azure pomocí azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Příkaz vrátí sadu výsledků jako následující. Zkopírujte tři hodnoty vlastností: **appID**, **heslo**a **tenanta**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Získejte seznam svých předplatných.

    ```azurecli
    az account list --output table
    ```

    Zkopírujte příslušné ID předplatného.

    ![ID předplatného – CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Přidání instančního objektu do role diváků

Teď, když máte instanční objekt, přidáte ho do role *diváků* v databázi Průzkumníka dat Azure. Tuto úlohu můžete provést v části **Oprávnění** na webu Azure Portal nebo v části **Dotaz** pomocí příkazu pro správu.

#### <a name="azure-portal---permissions"></a>Portál Azure – oprávnění

1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer.

1. V části **Přehled** vyberte databázi s ukázkovými daty StormEvents.

    ![Výběr databáze](media/data-explorer-configure-data-source/select-database.png)

1. Vyberte **oprávnění** a pak **přidejte**.

    ![Oprávnění databáze](media/data-explorer-configure-data-source/database-permissions.png)

1. V části **Přidat oprávnění k databázi**vyberte roli **Prohlížeč** a pak **vyberte objekty zabezpečení**.

    ![Přidání oprávnění k databázi](media/data-explorer-configure-data-source/add-permission.png)

1. Vyhledejte instanční objekt, který jste vytvořili. Vyberte objekt zabezpečení a **vyberte**.

    ![Správa oprávnění na webu Azure Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Vyberte **Uložit**.

    ![Správa oprávnění na webu Azure Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Příkaz Správa – dotaz

1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer a vyberte **Dotaz**.

    ![Dotaz](media/data-explorer-configure-data-source/query.png)

1. Spusťte následující příkaz v okně dotazu. Použijte ID aplikace a ID klienta z portálu Azure nebo CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Příkaz vrátí sadu výsledků jako následující. V tomto příkladu je první řádek pro existujícího uživatele v databázi a druhý řádek je pro instanční objekt, který byl právě přidán.

    ![Sada výsledků](media/data-explorer-configure-data-source/result-set.png)
