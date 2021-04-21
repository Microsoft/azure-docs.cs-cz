---
title: Vytvoření nebo Správa účtů pro integraci B2B
description: Vytváření, propojení a Správa integračních účtů pro podnikovou integraci s Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 9a7a0795a15de52c34b8591c4224c3ca5883445c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772050"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Vytváření a správa účtů integrace pro podnikové integrace B2B v Azure Logic Apps

Abyste mohli začít vytvářet [řešení podnikové integrace a B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) s využitím [Azure Logic Apps](../logic-apps/logic-apps-overview.md), musíte vytvořit účet integrace, což je samostatný prostředek Azure, který poskytuje zabezpečený, škálovatelný a spravovatelný kontejner pro artefakty integrace, které definujete a používáte v rámci pracovních postupů vašich aplikací logiky.

Můžete například vytvářet, ukládat a spravovat artefakty B2B, jako jsou obchodní partneři, smlouvy, mapy, schémata, certifikáty a konfigurace služby Batch. Před tím, než aplikace logiky může s těmito artefakty pracovat a používat Logic Apps B2B konektory, je také nutné [propojit účet pro integraci](#link-account) s vaší aplikací logiky. Váš účet pro integraci i aplikace logiky musí existovat ve *stejném* umístění nebo oblasti.

> [!IMPORTANT]
> Na základě typu účtu pro integraci, který vyberete, se vytvoří účet pro integraci s náklady. Další informace najdete v tématu [Logic Apps cen a modelech fakturace](logic-apps-pricing.md#integration-accounts) a v [Logic Apps cenách](https://azure.microsoft.com/pricing/details/logic-apps/).

V tomto tématu se dozvíte, jak provádět tyto úlohy:

* Vytvořte účet pro integraci.

  > [!TIP]
  > Pokud chcete vytvořit účet pro integraci v [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), přečtěte si téma [Vytvoření integračních účtů v ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

* Propojit účet pro integraci s aplikací logiky.

* Změňte cenovou úroveň účtu pro integraci.

* Odpojte účet pro integraci z aplikace logiky.

* Přesuňte účet pro integraci do jiné skupiny prostředků nebo předplatného Azure.

* Odstraňte účet pro integraci.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Vytvoření účtu integrace

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)nebo [Azure CLI](/cli/azure/resource#az_resource_create).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte jako filtr "účet pro integraci" a vyberte účet pro **integraci**.

   ![Vytvořit nový účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. V části **účet integrace** vyberte **vytvořit**.

   ![Pokud chcete vytvořit účet pro integraci, vyberte Přidat.](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Zadejte informace o účtu pro integraci:

   ![Zadání podrobností o účtu pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Yes | <*Integration-Account-Name*> | Název vašeho účtu pro integraci, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). V tomto příkladu se používá "Fabrikam-Integration". |
   | **Předplatné** | Yes | <*Azure – předplatné – název*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | Yes | <*Azure-Resource-Group-Name*> | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) , která se má použít pro uspořádání souvisejících prostředků V tomto příkladu vytvořte novou skupinu prostředků s názvem "FabrikamIntegration-RG". |
   | **Cenová úroveň** | Yes | <*úroveň cen*> | Cenová úroveň pro účet pro integraci, kterou můžete později změnit. V tomto příkladu vyberte **Free (zdarma**). Další informace najdete v těchto tématech: <p>- [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Omezení Logic Apps a konfigurace](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Umístění** | Yes | <*Oblast Azure*> | Oblast, kam se mají ukládat metadata účtu pro integraci Buď vyberte stejné umístění jako aplikace logiky, nebo vytvořte své aplikace logiky ve stejném umístění jako váš účet pro integraci. V tomto příkladu použijte "Západní USA". <p>**Poznámka**: Pokud chcete vytvořit účet pro integraci v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vyberte tento ISE jako umístění. Další informace najdete v tématu [Vytvoření integračních účtů v ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | No | Vypnuto, zapnuto | Pro tento příklad nechejte nastavení **vypnuto** . |
   |||||

1. Až budete hotovi, vyberte **vytvořit**.

   Po dokončení nasazení Azure otevře účet pro integraci.

   ![Azure otevře účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Předtím, než může vaše aplikace logiky použít váš účet pro integraci, postupujte podle dalších kroků a propojte účet pro integraci a aplikaci logiky.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Účet pro integraci můžete vytvořit pomocí příkazů Azure CLI v této části.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Vytvoření účtu pro integraci

Pomocí těchto příkazů vytvořte účet pro integraci.

1. Pokud chcete přidat rozšíření [AZ Logic integr-Account](/cli/azure/ext/logic/logic/integration-account) Extension, použijte příkaz [AZ Extension Add](/cli/azure/extension#az_extension_add) :

   ```azurecli
   az extension add –-name logic
   ```

1. Pokud chcete vytvořit skupinu prostředků nebo použít existující skupinu prostředků, spusťte příkaz [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Chcete-li zobrazit seznam účtů pro integraci pro skupinu prostředků, použijte příkaz [AZ Logic Integration Account list](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list) :

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Pokud chcete vytvořit účet pro integraci, spusťte příkaz [AZ Logic Integration Account Create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) :

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Název účtu pro integraci může obsahovat jenom písmena, číslice, spojovníky (-), podtržítka (_), kulaté závorky ((,)) a tečky (.).

   > [!TIP]
   > Pokud chcete vytvořit účet pro integraci v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vyberte tento ISE jako umístění. Další informace najdete v tématu [Vytvoření integračních účtů v ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Pokud chcete zobrazit konkrétní účet pro integraci, použijte příkaz [AZ Logic Integration Account show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show) :

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   SKLADOVOU položku nebo cenovou úroveň můžete změnit pomocí příkazu [AZ Logic Integration Account Update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) :

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Další informace o cenách najdete v těchto zdrojích:

   * [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Omezení Logic Apps a konfigurace](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)

Pokud chcete importovat účet pro integraci pomocí souboru JSON, použijte příkaz [AZ Logic Integration Account import](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import) :

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Účet pro integraci můžete odstranit pomocí příkazu [AZ Logic Integration Account Delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) :

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Předtím, než vaše aplikace logiky bude moci používat váš účet pro integraci, propojte účet pro integraci a aplikaci logiky společně. Další část popisuje propojování.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Připojení k aplikaci logiky

Aby vaše aplikace logiky měla přístup k účtu pro integraci, který obsahuje vaše artefakty B2B, musíte nejdřív propojit účet pro integraci s vaší aplikací logiky. Aplikace logiky i účet pro integraci musí existovat ve stejné oblasti. K dokončení této úlohy můžete použít Azure Portal. Pokud používáte Visual Studio a vaše aplikace logiky je v [projektu skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), můžete [propojit aplikaci logiky s účtem pro integraci pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V [Azure Portal](https://portal.azure.com)otevřete existující aplikaci logiky nebo vytvořte novou aplikaci logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Nastavení pracovního postupu**. V části **účet integrace** otevřete seznam **Vybrat účet pro integraci** . Vyberte účet pro integraci, který chcete propojit s vaší aplikací logiky.

   ![Vyberte účet pro integraci.](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Propojení dokončíte výběrem **Uložit**.

   ![Snímek obrazovky, který ukazuje, kde vybrat Save (Uložit) pro výběr účtu pro integraci.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po úspěšném propojení účtu pro integraci zobrazí Azure zprávu s potvrzením.

   ![Azure potvrdí úspěšné propojení](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Aplikace logiky teď může použít artefakty v účtu integrace a konektory B2B, jako je ověřování XML a kódování a dekódování plochého souboru.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Změna cenové úrovně

Pokud chcete zvýšit [omezení](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) pro účet pro integraci, můžete [upgradovat na vyšší cenovou úroveň](#upgrade-pricing-tier), pokud je dostupná. Můžete například upgradovat z úrovně Free na úroveň Basic nebo na úroveň Standard. Můžete také [downgradovat na nižší úroveň](#downgrade-pricing-tier), pokud je k dispozici. Další informace o cenách najdete v těchto tématech:

* [Ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Upgradovat cenovou úroveň

Tuto změnu můžete provést buď pomocí Azure Portal, nebo pomocí Azure CLI.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace** vyberte účet pro integraci, který chcete přesunout. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte možnost **upgradovat cenovou úroveň**, která obsahuje seznam dostupných vyšších úrovní. Když vyberete určitou úroveň, projeví se okamžitě změna.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pokud jste to ještě neudělali, [nainstalujte požadavky rozhraní příkazového řádku Azure CLI](/cli/azure/get-started-with-azure-cli).

1. V Azure Portal otevřete [Azure Cloud Shell](../cloud-shell/overview.md) prostředí.

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte [příkaz **AZ Resource** Command](/cli/azure/resource#az_resource_update)a nastavte `skuName` na vyšší úroveň, kterou chcete.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Pokud máte například úroveň Basic, můžete nastavit `skuName` na `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Cenová úroveň downgrade

Pokud chcete tuto změnu provést, použijte rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli).

1. Pokud jste to ještě neudělali, [nainstalujte požadavky rozhraní příkazového řádku Azure CLI](/cli/azure/get-started-with-azure-cli).

1. V Azure Portal otevřete [Azure Cloud Shell](../cloud-shell/overview.md) prostředí.

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte [příkaz **AZ Resource**](/cli/azure/resource#az_resource_update) a nastavte `skuName` na nižší úroveň, kterou chcete.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Například pokud máte úroveň Standard, můžete nastavit `skuName` na `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Odpojení od aplikace logiky

Pokud chcete aplikaci logiky propojit s jiným účtem pro integraci nebo už nepoužíváte účet pro integraci s vaší aplikací logiky, odstraňte odkaz pomocí Azure Resource Explorer.

1. Otevřete okno prohlížeče a přejít na [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Přihlaste se pomocí stejných přihlašovacích údajů k účtu Azure.

   ![Průzkumník prostředků Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Do vyhledávacího pole zadejte název vaší aplikace logiky, abyste mohli najít a vybrat aplikaci logiky.

   ![Hledání a výběr aplikace logiky](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. V záhlaví Průzkumníka vyberte možnost **čtení/zápis**.

   ![Zapnout režim pro čtení a zápis](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na kartě **data** vyberte **Upravit**.

   ![Na kartě data vyberte Upravit.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. V editoru vyhledejte `integrationAccount` objekt a odstraňte tuto vlastnost, která má tento formát:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Například:

   ![Najít objekt "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na kartě **data** klikněte **na tlačítko Uložit** a uložte provedené změny.

   ![Pokud chcete změny uložit, vyberte PUT.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. V Azure Portal vyhledejte a vyberte svou aplikaci logiky. V části **Nastavení pracovního postupu** vaší aplikace ověřte, zda je vlastnost **účet integrace** nyní zobrazená jako prázdná.

   ![Ověřte, že účet pro integraci není propojený.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Přesun účtu integrace

Účet pro integraci můžete přesunout do jiné skupiny prostředků Azure nebo do předplatného Azure. Když přesunete prostředky, Azure vytvoří nová ID prostředků, takže nezapomeňte místo toho použít nová ID a aktualizovat skripty nebo nástroje přidružené k přesunutým prostředkům. Pokud chcete změnit předplatné, musíte zadat taky existující nebo novou skupinu prostředků.

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části nebo v [Azure CLI](/cli/azure/resource#az_resource_move).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace** vyberte účet pro integraci, který chcete přesunout. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Vedle pole název **skupiny prostředků** nebo **předplatného** vyberte **změnit**.

   ![Změna skupiny prostředků nebo předplatného](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Vyberte všechny související prostředky, které chcete přesunout.

1. V závislosti na vašem výběru použijte následující postup ke změně skupiny prostředků nebo předplatného:

   * Skupina prostředků: v seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu prostředků**.

   * Předplatné: v seznamu **předplatné** vyberte cílové předplatné. V seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu prostředků**.

1. Abyste potvrdili, že všechny skripty nebo nástroje přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete pomocí nových ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

1. Po dokončení nezapomeňte aktualizovat všechny skripty s novými ID prostředků pro přesunuté prostředky.  

## <a name="delete-integration-account"></a>Odstranění účtu integrace

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části, [Azure CLI](/cli/azure/resource#az_resource_delete)nebo [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace** vyberte účet pro integraci, který chcete odstranit. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte **Odstranit**.

   ![V podokně Přehled vyberte Odstranit.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Pokud chcete potvrdit, že chcete účet pro integraci odstranit, vyberte **Ano**.

   ![Kliknutím na tlačítko Ano potvrďte odstranění.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Další kroky

* [Vytváření obchodních partnerů v účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Vytváření smluv mezi partnery v účtu integrace](../logic-apps/logic-apps-enterprise-integration-agreements.md)
