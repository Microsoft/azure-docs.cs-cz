---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového řádku Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: d8d767b97e335feeb31851c89a9b21eddf7157ea
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962211"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Kurz: Vytvoření clusteru Azure Red Hat OpenShift

Tento kurz je první částí série. Budete se dozvíte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového řádku Azure, škálovat ji a pak jej odstranit a vyčištění prostředků.

Ve druhé části této série se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift
> * [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Tento kurz vyžaduje 2.0.65 verzi rozhraní příkazového řádku Azure.
>    
> Než budete moct použít Azure Red Hat OpenShift, budete muset zakoupit minimálně 4 uzly aplikace Azure Red Hat OpenShift vyhrazené, jak je popsáno v [nastavení vývojového prostředí Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Než začnete s tímto kurzem:

Ujistěte se, že jste [nastavení vývojového prostředí](howto-setup-environment.md), což zahrnuje:
- Instalace nejnovější rozhraní příkazového řádku (verze 2.0.65 nebo novější)
- Vytvoření klienta, pokud ho ještě nemáte
- Vytvoření objektu aplikace Azure, pokud ho ještě nemáte
- Vytvoření skupiny zabezpečení
- Vytvoření uživatele služby Active Directory pro přihlášení ke clusteru.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlásit se k Azure

Pokud používáte Azure CLI místně, spusťte příkaz prostředí Bash skořápce a spuštění `az login` pro přihlášení k Azure.

```bash
az login
```

 Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazení `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Vytvoření clusteru Azure Red Hat OpenShift

V příkazovém okně prostředí Bash nastavte následující proměnné:

> [!IMPORTANT]
> Zvolte název jste cluster, který je jedinečný a všechna malá nebo clusteru vytváření se nezdaří.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Zvolte umístění pro vytvoření clusteru. Seznam oblastí azure, která podporuje OpenShift v Azure najdete v tématu [podporované oblasti](supported-resources.md#azure-regions). Například: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Nastavte `APPID` hodnotě jste si uložili v kroku 5 části [registraci aplikace Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Nastavit 'GROUPID' na hodnotu jste si uložili v kroku 10 [vytvoření skupiny zabezpečení služby Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Nastavte `SECRET` hodnotě jste si uložili v kroku 8 [vytvořit tajný kód klienta](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Nastavte `TENANT` hodnotu ID tenanta, jste si uložili v kroku 7 [vytvořit nového tenanta](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Vytvořte skupinu prostředků clusteru. Ze stejného prostředí Bash, které jste podnikli k definování proměnné výše, spusťte následující příkaz:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Volitelné: Propojení virtuální sítě clusteru pro existující virtuální sítě

Pokud nepotřebujete připojení clusteru, které vytvoříte na existující virtuální síť přes partnerský vztah virtuální sítě (VNET), tento krok přeskočte.

Nejprve získejte identifikátor existující virtuální síť. Identifikátor bude ve tvaru: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Pokud neznáte název sítě nebo existující virtuální síť patří do skupiny prostředků, přejděte [okně virtuální sítě](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) a klikněte na tlačítko ve vaší virtuální síti. Na stránce virtuální sítě se zobrazí a bude název sítě a skupinu prostředků, do které patří do seznamu.

Definujte proměnnou VNET_ID v prostředí BASH pomocí následujícího příkazu rozhraní příkazového řádku:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Příklad: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Vytvoření clusteru

Nyní jste připraveni vytvořit cluster. Následující se vytvoření clusteru v zadané služby Azure AD tenanta, zadejte objekt Azure AD aplikace a tajný klíč, který se použije jako objekt zabezpečení a skupiny zabezpečení, která obsahuje členy, kteří mají přístup správce ke clusteru.

Pokud jste **není** partnerský vztah clusteru do virtuální sítě, použijte následující příkaz:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Pokud jste **jsou** partnerský vztah clusteru do virtuální sítě, použijte následující příkaz, který přidá `--vnet-peer` příznak:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Pokud dojde k chybě, název hostitele není k dispozici, může být vzhledem k tomu, že váš název clusteru není jedinečný. Zkuste odstranit původní registrace aplikace a opakování kroků s jiný název clusteru v [vytvoření registrace nové aplikace] (howto-aad-app-configuration.md#create-a-new-app-registration), vynechá krok vytváří se nová skupina uživatelů a zabezpečení.

Po několika minutách `az openshift create` dokončí.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Získat přihlašovací adresa URL pro váš cluster

Získáte adresu URL pro přihlášení ke clusteru pomocí následujícího příkazu:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Hledat `publicHostName` ve výstupu, například: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Přihlašovací adresa URL pro váš cluster bude `https://` následovaný `publicHostName` hodnotu.  Například: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Tento identifikátor URI v dalším kroku použije jako součást identifikátoru URI přesměrování registrace aplikací.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3: Aktualizovat vaše aplikace registrace identifikátor URI pro přesměrování

Teď, když máte přihlašovací adresa URL pro cluster, nastavte přesměrování registrace aplikace uživatelského rozhraní:

1. Otevřít [okně registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klikněte na objekt registrace vaší aplikace.
3. Klikněte na **přidejte identifikátor URI pro přesměrování**.
4. Ujistěte se, že **typ** je **webové** a nastavit **identifikátor URI pro PŘESMĚROVÁNÍ** pomocí následujícímu vzoru: `https://<public host name>/oauth2callback/Azure%20AD`. Příklad: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klikněte na **Uložit**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4: Přihlaste se ke konzole Openshiftu

Nyní jste připraveni přihlášení ke konzole OpenShift nového clusteru. [OpenShift Webová konzola](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) vám umožní vizualizovat, procházet a spravovat obsah vašich projektů OpenShift.

Budete potřebovat instance čerstvé prohlížeče, který nemá v mezipaměti uložené identity, které standardně používáte k přihlášení k webu Azure portal.

1. Otevřít *incognito* okno (Chrome) nebo *InPrivate* okno (Microsoft Edge).
2. Přejděte na adresu URL přihlašování, který jste získali výše, například: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Přihlaste se pomocí uživatelského jména, kterou jste vytvořili v kroku 3 postupu [vytvořte nového uživatele Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

A **oprávnění požadovaná** zobrazí se dialogové okno. Klikněte na tlačítko **souhlas jménem svojí organizace** a potom klikněte na tlačítko **přijmout**.

Nyní jste se přihlásili do konzoly clusteru.

![Snímek obrazovky konzoly cluster Openshiftu](./media/aro-console.png)

 Další informace o [pomocí konzoly nástroje OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) k vytvoření a součástí bitové kopie [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentaci.

## <a name="step-5-install-the-openshift-cli"></a>Krok 5: Instalace rozhraní příkazového řádku Openshiftu

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (nebo *OS nástroje*) poskytují příkazy pro správu aplikací a nižší úrovně nástroje pro práci s různými komponentami OpenShift clusteru.

V konzole nástroje OpenShift přihlašovacího jména kliknutím na tlačítko Nápověda v pravém horním rohu a vyberte **nástroje příkazového řádku**.  Postupujte podle **nejnovější vydaná verze** odkaz ke stažení a instalaci podporovaný OS rozhraní příkazového řádku pro Windows, MacOS nebo Linuxem.

> [!NOTE]
> Pokud nevidíte ikonu otazníku v pravém horním rohu, vyberte *modulu Service Catalog* nebo *konzolu Application* z horní levé rozevíracího seznamu.
>
> Alternativně můžete [stáhnout OS rozhraní příkazového řádku](https://www.okd.io/download.html) přímo.

**Nástroje příkazového řádku** stránka obsahuje příkaz formuláře `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Klikněte na tlačítko *kopírování do schránky* tlačítko a zkopírujte tento příkaz.  V okně terminálu [nastavit cestu k](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) zahrnout místní instalaci nástrojů pro OS. Přihlaste se ke clusteru pomocí příkazu rozhraní příkazového řádku ° c, který jste zkopírovali.

Pokud nelze získat hodnotu tokenu pomocí výše uvedených kroků, získat token hodnota z: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
