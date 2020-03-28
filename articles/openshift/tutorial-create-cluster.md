---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového příkazu Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455294"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Kurz: Vytvoření clusteru Azure Red Hat OpenShift

Tento kurz je první částí série. Dozvíte se, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového příkazu Azure, škálovat ho a pak ho odstranit, abyste vyčistili prostředky.

V první části seriálu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift
> * [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Tento kurz vyžaduje verzi 2.0.65 azure cli.

Než začnete s tímto kurzem:

Ujistěte se, že jste [nastavili vývojové prostředí](howto-setup-environment.md), které zahrnuje:
- Instalace nejnovějšího cli (verze 2.0.65 nebo vyšší)
- Vytvoření klienta, pokud ho ještě nemáte
- Vytvoření objektu aplikace Azure, pokud ho ještě nemáte
- Vytvoření skupiny zabezpečení
- Vytvoření uživatele služby Active Directory pro přihlášení ke clusteru.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlášení do Azure

Pokud používáte Azure CLI místně, otevřete příkazové `az login` prostředí Bash a spusťte přihlášení k Azure.

```azurecli
az login
```

 Pokud máte přístup k více `az account set -s {subscription ID}` předplatných, spusťte nahrazení `{subscription ID}` předplatného, které chcete použít.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Vytvoření clusteru Azure Red Hat OpenShift

V příkazovém okně Bash nastavte následující proměnné:

> [!IMPORTANT]
> Zvolte název clusteru, který je jedinečný a vytvoření všech malých písmen nebo clusteru se nezdaří.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Zvolte umístění pro vytvoření clusteru. Seznam oblastí Azure, které podporují OpenShift v Azure, najdete v [tématu Podporované oblasti](supported-resources.md#azure-regions). Například: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Nastavte `APPID` na hodnotu uloženou v kroku 5 [vytvoření registrace aplikace Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Nastavte "GROUPID" na hodnotu uloženou v kroku 10 [vytvoření skupiny zabezpečení Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Nastavte `SECRET` hodnotu uloženou v kroku 8 [vytvoření tajného klíče klienta](howto-aad-app-configuration.md#create-a-client-secret).

```bash
SECRET=<secret value>
```

Nastavit `TENANT` na hodnotu ID klienta, kterou jste uložili v kroku 7 [vytvoření nového klienta](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Vytvořte skupinu prostředků pro cluster. Spusťte následující příkaz ze stejného prostředí Bash, který jste použili k definování výše uvedených proměnných:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Volitelné: Připojení virtuální sítě clusteru k existující virtuální síti

Pokud nepotřebujete připojit virtuální síť (VNET) clusteru, který vytvoříte, k existující virtuální síti prostřednictvím partnerského vztahu, tento krok přeskočte.

Pokud partnerský vztah k síti mimo výchozí předplatné pak v tomto předplatném, budete také muset zaregistrovat poskytovatele Microsoft.ContainerService. Chcete-li to provést, spusťte níže uvedený příkaz v tomto předplatném. Jinak pokud virtuální síť, kterou partnerského vztahu je umístěn ve stejném předplatném, můžete přeskočit krok registrace.

`az provider register -n Microsoft.ContainerService --wait`

Nejprve získat identifikátor existující virtuální sítě. Identifikátor bude mít formu: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Pokud neznáte název sítě nebo skupinu prostředků, do které patří existující virtuální síť, přejděte do [okna Virtuální sítě](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) a klikněte na virtuální síť. Zobrazí se stránka Virtuální síť, která zobrazí název sítě a skupiny prostředků, do které patří.

Definujte proměnnou VNET_ID pomocí následujícího příkazu příkazu příkazu příkazu příkazu PŘÍKAZ VŠAK v prostředí BASH:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Příklad: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Volitelné: Připojení clusteru k azure monitoringu

Nejprve získejte identifikátor **existujícího** pracovního prostoru analýzy protokolů. Identifikátor bude mít formu:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Pokud neznáte název pracovního prostoru analýzy protokolů nebo skupinu prostředků, do které patří existující pracovní prostor analýzy protokolů, přejděte do [pracovního prostoru Log-Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) a klikněte na pracovní prostory analýzy protokolů. Zobrazí se stránka pracovního prostoru analýzy protokolu a zobrazí se název pracovního prostoru a skupiny prostředků, do které patří.

_Vytvoření pracovního prostoru analýzy protokolů naleznete v tématu [Vytvoření pracovního prostoru analýzy protokolů](../azure-monitor/learn/quick-create-workspace-cli.md)_

Definujte proměnnou WORKSPACE_ID pomocí následujícího příkazu příkazu příkazu příkazu příkazu PŘÍKAZ VŠAK v prostředí BASH:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Vytvoření clusteru

Nyní jste připraveni vytvořit cluster. Následující vytvoří cluster v zadaném tenantovi Azure AD, zadejte objekt a tajný klíč aplikace Azure AD, který se má použít jako objekt zabezpečení, a skupinu zabezpečení, která obsahuje členy, kteří mají přístup správce ke clusteru.

> [!IMPORTANT]
> Ujistěte se, že jste správně přidali příslušná oprávnění pro aplikaci Azure AD, jak [je podrobně uvedeno zde](howto-aad-app-configuration.md#add-api-permissions) před vytvořením clusteru

Pokud **cluster neinkují** do virtuální sítě nebo **nechcete** Azure Monitoring, použijte následující příkaz:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Pokud **cluster** předáváte do virtuální sítě, použijte následující `--vnet-peer` příkaz, který přidá příznak:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Pokud **chcete** Azure Monitoring s clusterem použít `--workspace-id` následující příkaz, který přidá příznak:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Pokud se zobrazí chyba, že název hostitele není k dispozici, může to být proto, že název clusteru není jedinečný. Zkuste smazat původní registraci aplikace a předělat kroky s jiným názvem [clusteru](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)v části Vytvoření nové registrace aplikace , vynechání kroku vytvoření nového uživatele a skupiny zabezpečení.




Po několika minutách se `az openshift create` dokončí.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Získání přihlašovací adresy URL pro váš cluster

Získejte adresu URL pro přihlášení ke clusteru spuštěním následujícího příkazu:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Podívejte se `publicHostName` na výstupu, například:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Za přihlašovací adresou URL `https://` vašeho clusteru `publicHostName` bude následovat hodnota.  Například: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Tento identifikátor URI použijete v dalším kroku jako součást identifikátoru URI přesměrování registrace aplikace.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3: Aktualizace identifikátoru URI přesměrování registrace aplikace

Teď, když máte přihlašovací adresu URL pro cluster, nastavte urozhraní přesměrování registrace aplikace:

1. Otevřete [okno Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klikněte na objekt registrace aplikace.
3. Klikněte na **Přidat identifikátor URI přesměrování**.
4. Ujistěte se, že **typ** je **web** a nastavte identifikátor **URI přesměrování** pomocí následujícího vzoru: `https://<public host name>/oauth2callback/Azure%20AD`. Příklad: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klikněte na **Uložit.**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4: Přihlášení ke konzoli OpenShift

Nyní jste připraveni přihlásit se ke konzoli OpenShift pro váš nový cluster. [Webová konzola OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) umožňuje vizualizovat, procházet a spravovat obsah vašich projektů OpenShift.

Budete potřebovat novou instanci prohlížeče, která není uložena do mezipaměti identity, kterou běžně používáte k přihlášení k portálu Azure.

1. Otevřete *anonymní* okno (Chrome) nebo *InPrivate* (Microsoft Edge).
2. Přejděte na přihlašovací adresu URL, kterou jste získali výše, například:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Přihlaste se pomocí uživatelského jména, které jste vytvořili v kroku 3 [vytvoření nového uživatele služby Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Zobrazí se dialogové okno **Požadovaná oprávnění.** Klikněte **jménem organizace na Souhlas** a potom klikněte na **Přijmout**.

Nyní jste přihlášeni ke konzole clusteru.

![Snímek obrazovky clusterové konzole OpenShift](./media/aro-console.png)

 Další informace o [používání konzoly OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) k vytváření a vytváření obrázků v dokumentaci [k Red Hat OpenShift.](https://docs.openshift.com/aro/welcome/index.html)

## <a name="step-5-install-the-openshift-cli"></a>Krok 5: Instalace cli OpenShift

[Příkazy Cli (nebo](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) *OC Tools)* openshift poskytují příkazy pro správu aplikací a nástroje nižší úrovně pro interakci s různými součástmi clusteru OpenShift.

V konzole OpenShift klikněte na otazník v pravém horním rohu podle přihlašovacího jména a vyberte **Nástroje příkazového řádku**.  Chcete-li stáhnout a nainstalovat podporované rozhraní OK CLI pro Linux, MacOS nebo Windows, postupujte podle odkazu **Nejnovější verze.**

> [!NOTE]
> Pokud se ikona otazníku v pravém horním rohu nezobrazuje, vyberte *katalog služeb* nebo *konzolu aplikací* z rozevíracího seznamu v levém horním rohu.
>
> Alternativně si můžete [stáhnout ok CLI](https://www.okd.io/download.html) přímo.

Stránka **Nástroje příkazového řádku** poskytuje `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`příkaz formuláře .  Chcete-li tento příkaz zkopírovat, klepněte na tlačítko Kopírovat *do schránky.*  V okně terminálu [nastavte cestu](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) tak, aby zahrnovala místní instalaci nástrojů oc. Pak se přihlaste ke clusteru pomocí příkazu příkazu příkazu příkazu příkazu c) oc, který jste zkopírovali.

Pokud se vám nepodařilo získat hodnotu tokenu pomocí výše `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`uvedených kroků, získejte hodnotu tokenu z: .

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
