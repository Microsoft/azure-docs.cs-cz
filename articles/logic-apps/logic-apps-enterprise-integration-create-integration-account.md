---
title: Vytvoření nebo správa účtů integrace B2B
description: Vytváření, propojujte a spravujte účty integrace pro podnikovou integraci pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270326"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Vytváření a správa účtů integrace pro podnikové integrace B2B v Aplikacích Azure Logic Apps

Abyste mohli začít vytvářet [řešení podnikové integrace a B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) s využitím [Azure Logic Apps](../logic-apps/logic-apps-overview.md), musíte vytvořit účet integrace, což je samostatný prostředek Azure, který poskytuje zabezpečený, škálovatelný a spravovatelný kontejner pro artefakty integrace, které definujete a používáte v rámci pracovních postupů vašich aplikací logiky.

Můžete například vytvářet, ukládat a spravovat artefakty B2B, jako jsou obchodní partneři, smlouvy, mapy, schémata, certifikáty a dávkové konfigurace. Také před vaší aplikace logiky můžete pracovat s těmito artefakty a používat logic Apps B2B konektory, musíte [propojit účet integrace](#link-account) s vaší aplikace logiky. Váš účet integrace i aplikace logiky musí existovat ve *stejném* umístění nebo oblasti.

> [!TIP]
> Chcete-li vytvořit účet integrace v [prostředí služby integrace](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), přečtěte si informace [o vytvoření účtů integrace v systému ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Toto téma ukazuje, jak provádět tyto úkoly:

* Vytvořte si účet integrace.
* Propojte svůj účet integrace s aplikací logiky.
* Změňte cenovou úroveň pro váš účet integrace.
* Odpojte účet integrace z aplikace logiky.
* Přesuňte svůj účet integrace do jiné skupiny prostředků nebo předplatného Azure.
* Odstraňte svůj účet integrace.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Vytvoření účtu integrace

Pro tuto úlohu můžete použít buď portál Azure podle kroků v této části [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)nebo [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte jako filtr účet integrace a vyberte **účet integrace**.

   ![Vytvořit nový účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. V části **Účet integrace**vyberte **Vytvořit**.

   ![Chcete-li vytvořit účet integrace, zvolte možnost Přidat.](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Zadejte tyto informace o svém účtu pro integraci:

   ![Poskytnutí podrobností o integračním účtu](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*integrace-název účtu*> | Název účtu integrace, který může obsahovat pouze písmena,`-`čísla,`_`pomlčky ( ),`(` `)`podtržítka`.`( ), závorky ( , ) a tečky ( ). Tento příklad používá "Fabrikam-Integration". |
   | **Předplatné** | Ano | <*Název předplatného Azure*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | Ano | <*Název skupiny Azure-resource-group*> | Název [skupiny prostředků Azure,](../azure-resource-manager/management/overview.md) který se má použít pro uspořádání souvisejících prostředků. V tomto příkladu vytvořte novou skupinu prostředků s názvem "FabrikamIntegration-RG". |
   | **Cenová úroveň** | Ano | <*cenová úroveň*> | Cenová úroveň pro účet integrace, kterou můžete později změnit. V tomto příkladu vyberte **možnost Zdarma**. Další informace najdete v těchto tématech: <p>- [Cenový model aplikace Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Omezení a konfigurace aplikací logiky](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Ceny aplikací Logika](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Umístění** | Ano | <*Oblast Azure*> | Oblast, kde chcete uložit metadata účtu integrace. Buď vyberte stejné umístění jako aplikace logiky, nebo vytvořte aplikace logiky ve stejném umístění jako váš účet integrace. V tomto příkladu použijte "Západní USA". <p>**Poznámka:** Chcete-li vytvořit účet integrace v [prostředí služby integrace (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)vyberte tuto službu ISE jako umístění. Další informace naleznete v [tématu Vytvoření účtů integrace v systému ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Ne | Vypnuto, zapnuto | V tomto příkladu ponechte nastavení **Vypnuto.** |
   |||||

1. Až budete hotovi, vyberte **Vytvořit**.

   Po dokončení nasazení Azure otevře váš účet integrace.

   ![Azure otevírá účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Než vaše aplikace logiky bude moci používat váš účet pro integraci, postupujte podle následujících kroků a propojte účet integrace a aplikaci logiky.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Připojení k aplikaci logiky

Chcete-li poskytnout vašim aplikacím logiky přístup k účtu integrace, který obsahuje vaše artefakty B2B, musíte nejprve propojit účet integrace s aplikací logiky. Aplikace logiky i účet integrace musí existovat ve stejné oblasti. K dokončení tohoto úkolu můžete použít portál Azure. Pokud používáte Visual Studio a vaše aplikace logiky je v [projektu Skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), můžete propojit [aplikaci logiky s účtem integrace pomocí Sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Na webu Azure Portal najděte a otevřete aplikaci logiky.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete existující aplikaci logiky nebo vytvořte novou aplikaci logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **nastavení pracovního postupu**. V části **Účet integrace**otevřete seznam Vybrat **účet integrace.** Vyberte účet integrace, který chcete propojit s aplikací logiky.

   ![Vyberte si účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Chcete-li propojení dokončit, vyberte **uložit**.

   ![Vyberte si účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po úspěšném propojení vašeho integračního účtu se Azure zobrazí potvrzovací zpráva.

   ![Azure potvrzuje úspěšné propojení](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Teď vaše aplikace logiky můžete použít artefakty v účtu integrace plus konektory B2B, jako je například ověření XML a ploché kódování nebo dekódování souborů.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Změna cenové úrovně

Chcete-li zvýšit [limity](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) pro účet integrace, můžete [upgradovat na vyšší cenovou úroveň](#upgrade-pricing-tier), pokud je k dispozici. Můžete například upgradovat z úrovně Free na úroveň Basic nebo Standard. Můžete také [přejít na nižší úroveň](#downgrade-pricing-tier), pokud je k dispozici. Další informace o cenách naleznete v těchto tématech:

* [Ceny aplikací Logika](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Cenová úroveň upgradu

Chcete-li provést tuto změnu, můžete použít buď portál Azure podle kroků v této části nebo [v příkazovém příkazovém příkazu k onomu Azure](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního vyhledávacího pole Azure zadejte jako filtr "účty integrace" a vyberte **účty integrace**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazuje všechny účty integrace ve vašich předplatných Azure.

1. V části **Účty integrace**vyberte účet integrace, který chcete přesunout. V nabídce integračního účtu vyberte **Přehled**.

   ![V nabídce integračního účtu vyberte "Přehled"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte **Upgrade cenové úrovně**, která obsahuje seznam všech dostupných vyšších úrovní. Když vyberete úroveň, změna se okamžitě projeví.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Pokud jste tak ještě neučinili, [nainstalujte požadavky Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Na webu Azure Portal otevřete prostředí Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte příkaz [ **az resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)a nastavte `skuName` na vyšší úroveň, kterou chcete.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Pokud máte například úroveň Basic, můžete `skuName` `Standard`nastavit na :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Úroveň ocenění downgrade

Chcete-li provést tuto změnu, použijte [azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Pokud jste tak ještě neučinili, [nainstalujte požadavky Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Na webu Azure Portal otevřete prostředí Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte příkaz `skuName` [ **az prostředku** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) a nastavte na nižší úroveň, kterou chcete.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Například pokud máte úroveň Standard, můžete `skuName` `Basic`nastavit na :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Odpojení od aplikace logiky

Pokud chcete propojit aplikaci logiky s jiným účtem integrace nebo už nepoužívat účet integrace s vaší aplikací logiky, odstraňte odkaz pomocí Průzkumníka prostředků Azure.

1. Otevřete okno prohlížeče a přejděte do [Průzkumníka prostředků Azure (https://resources.azure.com)](https://resources.azure.com). Přihlaste se pomocí stejných přihlašovacích údajů účtu Azure.

   ![Průzkumník prostředků Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Do vyhledávacího pole zadejte název aplikace logiky, abyste mohli najít a vybrat aplikaci logiky.

   ![Hledání a výběr aplikace logiky](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. V záhlaví průzkumníka vyberte **Číst nebo psát**.

   ![Zapnutí režimu "Čtení/zápis"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na kartě **Data** vyberte **Upravit**.

   ![Na kartě Data vyberte "Upravit"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. V editoru najděte `integrationAccount` objekt a odstraňte tuto vlastnost, která má tento formát:

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

   ![Najít objekt integrationAccount](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na kartě **Data** vyberte **Umístit,** chcete-li změny uložit.

   ![Chcete-li uložit změny, vyberte možnost "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Na webu Azure Portal najděte a vyberte aplikaci logiky. V části **Nastavení pracovního postupu**aplikace zkontrolujte, jestli je vlastnost účtu **integrace** prázdná.

   ![Zkontrolujte, zda není propojený účet integrace.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Přesun účtu integrace

Svůj účet integrace můžete přesunout do jiné skupiny prostředků Azure nebo předplatného Azure. Když přesunete prostředky, Azure vytvoří nová ID prostředků, takže se ujistěte, že místo toho použijete nová ID a aktualizujete všechny skripty nebo nástroje přidružené k přesunutým prostředkům. Pokud chcete změnit předplatné, musíte také zadat existující nebo novou skupinu prostředků.

Pro tuto úlohu můžete použít buď portál Azure podle kroků v této části nebo [v příkazovém příkazovém příkazu k onomu Azure](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního vyhledávacího pole Azure zadejte jako filtr "účty integrace" a vyberte **účty integrace**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazuje všechny účty integrace ve vašich předplatných Azure.

1. V části **Účty integrace**vyberte účet integrace, který chcete přesunout. V nabídce integračního účtu vyberte **Přehled**.

   ![V nabídce integračního účtu vyberte "Přehled"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Vedle **skupiny prostředků** nebo **názvu předplatného**vyberte **změnit**.

   ![Změna skupiny prostředků nebo předplatného](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Vyberte všechny související zdroje, které chcete také přesunout.

1. Na základě výběru můžete změnit skupinu prostředků nebo předplatné takto:

   * Skupina prostředků: Ze seznamu **Skupina zdrojů** vyberte cílovou skupinu prostředků. Chcete-li vytvořit jinou skupinu prostředků, vyberte možnost **Vytvořit novou skupinu prostředků**.

   * Předplatné: Ze seznamu **Odběr** vyberte cílové předplatné. V seznamu **Skupina zdrojů** vyberte cílovou skupinu prostředků. Chcete-li vytvořit jinou skupinu prostředků, vyberte možnost **Vytvořit novou skupinu prostředků**.

1. Chcete-li potvrdit, že skripty nebo nástroje přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete novými ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

1. Po dokončení se ujistěte, že aktualizujete všechny skripty pomocí nových ID prostředků pro přesunuté prostředky.  

## <a name="delete-integration-account"></a>Odstranění účtu integrace

Pro tuto úlohu můžete použít portál Azure podle kroků v této části [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)nebo [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního vyhledávacího pole Azure zadejte jako filtr "účty integrace" a vyberte **účty integrace**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazuje všechny účty integrace ve vašich předplatných Azure.

1. V části **Účty integrace**vyberte účet integrace, který chcete odstranit. V nabídce integračního účtu vyberte **Přehled**.

   ![V nabídce integračního účtu vyberte "Přehled"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte **Odstranit**.

   ![V podokně "Přehled" vyberte "Odstranit"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Chcete-li potvrdit, že chcete odstranit účet integrace, vyberte **možnost Ano**.

   ![Chcete-li potvrdit odstranění, vyberte možnost "Ano"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Další kroky

* [Vytvořte si obchodní partnery ve svém integračním účtu](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Vytváření smluv mezi partnery ve vašem integračním účtu](../logic-apps/logic-apps-enterprise-integration-agreements.md)
