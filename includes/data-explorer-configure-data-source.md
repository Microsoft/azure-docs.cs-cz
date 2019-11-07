---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581997"
---
## <a name="configure-the-data-source"></a>Konfigurace zdroje dat

Provedením následujících kroků nakonfigurujete Azure Průzkumník dat jako zdroj dat pro nástroj řídicího panelu. Tyto kroky pokryjeme podrobněji v této části:

1. Vytvoření instančního objektu služby Azure Active Directory (Azure AD) Instanční objekt se používá v nástroji řídicího panelu pro přístup ke službě Azure Průzkumník dat.

1. Přidejte instanční objekt služby Azure AD do role *Návštěvníci* v databázi Azure Průzkumník dat.

1. Určete vlastnosti připojení nástroje řídicího panelu na základě informací z instančního objektu služby Azure AD a pak otestujte připojení.

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Instanční objekt můžete vytvořit v [Azure Portal](#azure-portal) nebo pomocí prostředí příkazového řádku [Azure CLI](#azure-cli) . Bez ohledu na to, kterou metodu použijete, po vytvoření získáte hodnoty pro čtyři vlastnosti připojení, které budete používat v pozdějších krocích.

#### <a name="azure-portal"></a>portál Azure

1. Chcete-li vytvořit instanční objekt, postupujte podle pokynů v [dokumentaci k Azure Portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. V části [přiřazení aplikace k roli](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) přiřaďte k vašemu clusteru Azure Průzkumník dat typ role **Čtenář** .

    1. V části [získat hodnoty pro přihlášení](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) zkopírujte tři hodnoty vlastností, které jsou pokryté v postupech: **ID adresáře** (ID tenanta), **ID aplikace**a **heslo**.

1. V Azure Portal vyberte **předplatná** a potom zkopírujte ID předplatného, ve kterém jste objekt služby vytvořili.

    ![ID předplatného – portál](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Vytvoření instančního objektu. Nastavte příslušný obor a typ role `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Další informace najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Příkaz vrátí sadu výsledků dotazu jako následující. Zkopírujte tři hodnoty vlastností: **AppID**, **Password**a **tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Získejte seznam předplatných.

    ```azurecli
    az account list --output table
    ```

    Zkopírujte příslušné ID předplatného.

    ![ID předplatného-CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Přidání instančního objektu do role prohlížeče

Teď, když máte instanční objekt, přidáte ho do role *Návštěvníci* v databázi Azure Průzkumník dat. Tuto úlohu lze provést v rámci **oprávnění** v Azure Portal nebo v části **dotaz** pomocí příkazu pro správu.

#### <a name="azure-portal---permissions"></a>Azure Portal – oprávnění

1. V Azure Portal přejdete do svého clusteru Azure Průzkumník dat.

1. V části **Přehled** vyberte databázi s ukázkovými daty StormEvents.

    ![vybrat databázi](media/data-explorer-configure-data-source/select-database.png)

1. Vyberte **oprávnění** a pak **Přidat**.

    ![Databázová oprávnění](media/data-explorer-configure-data-source/database-permissions.png)

1. V části **Přidat oprávnění databáze**vyberte možnost role **prohlížeče** a potom **Vyberte objekty zabezpečení**.

    ![Přidat databázová oprávnění](media/data-explorer-configure-data-source/add-permission.png)

1. Vyhledejte instanční objekt, který jste vytvořili. Vyberte objekt zabezpečení a pak **Vyberte**.

    ![Správa oprávnění v Azure Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Vyberte **Uložit**.

    ![Správa oprávnění v Azure Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Příkaz pro správu – dotaz

1. V Azure Portal otevřete cluster Azure Průzkumník dat a vyberte **dotaz**.

    ![Dotaz](media/data-explorer-configure-data-source/query.png)

1. Spusťte následující příkaz v okně dotazu. Použijte ID aplikace a ID tenanta z Azure Portal nebo CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Příkaz vrátí sadu výsledků dotazu jako následující. V tomto příkladu je první řádek pro stávajícího uživatele v databázi a druhý řádek pro instanční objekt, který byl právě přidán.

    ![Sada výsledků](media/data-explorer-configure-data-source/result-set.png)
