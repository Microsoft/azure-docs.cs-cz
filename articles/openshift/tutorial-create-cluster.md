---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 4c186787af08a565dc100dfbd79d166688d89d8f
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013439"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Kurz: Vytvoření clusteru Azure Red Hat OpenShift

Tento kurz je první částí série. Naučíte se, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI, škálovat ho a pak odstranit a vyčistit prostředky.

V první části série se naučíte:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift
> * [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Tento kurz vyžaduje 2.0.65 verze Azure CLI.
>    
> Než budete moct použít Azure Red Hat OpenShift, musíte si koupit aspoň čtyři uzly rezervované aplikace Azure Red Hat OpenShift, jak je popsáno v tématu [Nastavení vývojového prostředí Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Než začnete s tímto kurzem:

Ujistěte se, že jste [nastavili vývojové prostředí](howto-setup-environment.md), které zahrnuje:
- Instalace nejnovějšího rozhraní příkazového řádku (verze 2.0.65 nebo vyšší)
- Vytváření tenanta, pokud ho ještě nemáte
- Vytvoření objektu aplikace Azure, pokud ho ještě nemáte
- Vytvoření skupiny zabezpečení
- Vytvoření uživatele služby Active Directory pro přihlášení ke clusteru.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlášení k Azure

Pokud používáte Azure CLI místně, otevřete příkazové prostředí bash a spusťte `az login` příkaz pro přihlášení k Azure.

```bash
az login
```

 Pokud máte přístup k několika předplatným, `az account set -s {subscription ID}` spusťte `{subscription ID}` nahrazení pomocí předplatného, které chcete použít.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Vytvoření clusteru Azure Red Hat OpenShift

V okně příkazového řádku bash nastavte následující proměnné:

> [!IMPORTANT]
> Vyberte název clusteru, který je jedinečný a všechna malá písmena nebo vytvoření clusteru selže.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Vyberte umístění pro vytvoření clusteru. Seznam oblastí Azure, které podporují OpenShift v Azure, najdete v tématu [podporované oblasti](supported-resources.md#azure-regions). Například: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Nastavte `APPID` na hodnotu, kterou jste uložili v kroku 5 [Vytvoření registrace aplikace Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Nastavte GROUPID na hodnotu, kterou jste uložili v kroku 10 [Vytvoření skupiny zabezpečení Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Nastavte `SECRET` na hodnotu, kterou jste uložili v kroku 8 v části [Vytvoření tajného klíče klienta](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Nastavte `TENANT` na hodnotu ID tenanta, kterou jste uložili v kroku 7 [Vytvoření nového tenanta](howto-create-tenant.md#create-a-new-azure-ad-tenant) .  

```bash
TENANT=<tenant ID>
```

Vytvořte skupinu prostředků pro cluster. Spusťte následující příkaz ze stejného prostředí bash, které jste použili k definování proměnných uvedených výše:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Volitelné: Připojení virtuální sítě clusteru k existující virtuální síti

Pokud nepotřebujete připojit virtuální síť (VNET) clusteru, který jste vytvořili v existující virtuální síti prostřednictvím partnerského vztahu, tento krok přeskočte.

Pokud se partnerský vztah k síti mimo výchozí předplatné pak v tomto předplatném, budete muset také zaregistrovat poskytovatele Microsoft. ContainerService. Uděláte to tak, že v tomto předplatném spustíte níže uvedený příkaz. Jinak, pokud virtuální síť, kterou využíváte, se nachází ve stejném předplatném, můžete přeskočit krok registrace. 

`az provider register -n Microsoft.ContainerService --wait`

Nejprve získejte identifikátor existující virtuální sítě. Identifikátor bude ve formátu: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Pokud neznáte název sítě nebo skupinu prostředků, do které patří existující virtuální síť, přejděte do okna [virtuální sítě](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) a klikněte na svou virtuální síť. Zobrazí se stránka virtuální síť a zobrazí se seznam názvů sítí a skupiny prostředků, do které patří.

Pomocí následujícího příkazu rozhraní příkazového řádku v prostředí BASH definujte proměnnou VNET_ID:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Příklad: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Vytvoření clusteru

Nyní jste připraveni vytvořit cluster. V následujícím seznamu se vytvoří cluster v zadaném tenantovi Azure AD, určíte objekt aplikace Azure AD a tajný klíč, který chcete použít jako objekt zabezpečení, a skupinu zabezpečení obsahující členy, kteří mají oprávnění správce ke clusteru.

Pokud svůj cluster nechcete navázat na virtuální síť, použijte následující příkaz:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Pokud vytváříte partnerský vztah ke clusteru virtuální sítě, použijte následující příkaz, který přidá `--vnet-peer` příznak:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Pokud se zobrazí chyba, že název hostitele není k dispozici, může to být způsobeno tím, že název clusteru není jedinečný. Zkuste odstranit původní registraci aplikace a zopakovat postup s jiným názvem clusteru v části [Vytvoření nové registrace aplikace](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), přičemž vynechejte krok vytvoření nového uživatele a skupiny zabezpečení.

Po několika minutách `az openshift create` se dokončí.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Získat adresu URL pro přihlášení ke clusteru

Spuštěním následujícího příkazu Získejte adresu URL pro přihlášení ke clusteru:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

`publicHostName` Vyhledejte ve výstupu, například:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

`https://` Za adresu URL pro přihlášení ke clusteru bude následovat `publicHostName` hodnota.  Například: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  V dalším kroku použijete tento identifikátor URI jako součást identifikátoru URI přesměrování registrace aplikace.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3: Aktualizujte identifikátor URI pro přesměrování registrace aplikace.

Teď, když máte adresu URL pro přihlášení ke clusteru, nastavte uživatelské rozhraní pro přesměrování registrace aplikace:

1. Otevřete okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klikněte na objekt pro registraci aplikace.
3. Klikněte na **Přidat identifikátor URI přesměrování**.
4. Zajistěte, aby byl **typ** **Web** , a nastavte **identifikátor URI přesměrování** pomocí `https://<public host name>/oauth2callback/Azure%20AD`následujícího vzoru:. Příklad: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klikněte na **Uložit**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4: Přihlaste se ke konzole OpenShift

Nyní jste připraveni se přihlásit ke konzole OpenShift pro nový cluster. [Webová konzola OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) vám umožňuje vizualizovat, Procházet a spravovat obsah vašich projektů OpenShift.

Budete potřebovat novou instanci prohlížeče, která neukládá do mezipaměti identitu, kterou běžně používáte pro přihlášení k Azure Portal.

1. Otevřete okno *anonymním* (Chrome) nebo okno *InPrivate* (Microsoft Edge).
2. Přejděte na adresu URL pro přihlášení, kterou jste získali výše, například:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Přihlaste se pomocí uživatelského jména, které jste vytvořili v kroku 3 v části [Vytvoření nového uživatele Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Zobrazí se dialogové okno **požadovaná oprávnění** . Klikněte **jménem vaší organizace na souhlas** a pak klikněte na **přijmout**.

Nyní jste přihlášeni ke konzole clusteru.

![Snímek obrazovky s konzolou clusteru OpenShift](./media/aro-console.png)

 Přečtěte si další informace o [používání konzoly OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) k vytváření a sestavování imagí v dokumentaci k [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) .

## <a name="step-5-install-the-openshift-cli"></a>Krok 5: Instalace rozhraní příkazového řádku OpenShift

[OPENSHIFT CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (nebo *Nástroje na oC*) poskytují příkazy pro správu aplikací a nástrojů nižší úrovně pro interakci s různými komponentami clusteru OpenShift.

V konzole OpenShift klikněte na otazník v pravém horním rohu podle přihlašovacího jména a vyberte **nástroje příkazového řádku**.  Použijte odkaz **nejnovější verze** ke stažení a instalaci podporovaného rozhraní příkazového řádku pro systém Linux, MacOS nebo Windows.

> [!NOTE]
> Pokud v pravém horním rohu nevidíte ikonu otazníku, vyberte v levém horním rohu položku *katalog služeb* nebo *aplikační konzola* .
>
> Alternativně můžete stáhnout také [rozhraní příkazového řádku oC](https://www.okd.io/download.html) přímo.

Stránka **nástroje příkazového řádku** poskytuje příkaz formuláře `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Kliknutím na tlačítko *Kopírovat do schránky* zkopírujte tento příkaz.  V okně terminálu [nastavte cestu](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) tak, aby zahrnovala místní instalaci nástrojů oC. Pak se přihlaste ke clusteru pomocí příkazu oC CLI, který jste zkopírovali.

Pokud jste nedokázali získat hodnotu tokenu pomocí výše uvedených kroků, Získejte hodnotu tokenu `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`z:.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
