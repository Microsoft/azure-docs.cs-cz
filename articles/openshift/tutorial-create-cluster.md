---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového řádku Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/08/2019
ms.openlocfilehash: baada8a5238725456ca4a2ec7e8257c229066115
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466177"
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

Než začnete s tímto kurzem:

Ujistěte se, že jste [nastavení vývojového prostředí](howto-setup-environment.md), což zahrnuje:
- Instalace nejnovější rozhraní příkazového řádku (verze 2.0.64 nebo novější)
- Vytvoření klienta
- Vytvoření objektu aplikace Azure
- Vytvoření uživatele služby Active Directory používat k přihlašování k aplikacím běžícím v clusteru.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlásit se k Azure

Pokud používáte Azure CLI místně, spusťte příkaz prostředí Bash skořápce a spuštění `az login` pro přihlášení k Azure.

```bash
az login
```

 Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazení `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Vytvoření clusteru Azure Red Hat OpenShift

V příkazovém okně prostředí Bash nastavte následující proměnné:

> [!IMPORTANT]
> Název clusteru musí být malými písmeny nebo vytvoření clusteru se nezdaří.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Použijte stejný název pro cluster, který jste zvolili v kroku 6 postupu [vytvořit registrace nové aplikace](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Zvolte umístění pro vytvoření clusteru. Seznam oblastí azure, která podporuje OpenShift v Azure najdete v tématu [podporované oblasti](supported-resources.md#azure-regions). Například: `LOCATION=eastus`.

Nastavte `FQDN` plně kvalifikovaný název vašeho clusteru. Tento název se skládá z názvu clusteru do umístění, a `.cloudapp.azure.com` příponou. Toto je stejná jako přihlašovací adresu URL jste vytvořili v kroku 6 postupu [vytvořit registrace nové aplikace](howto-aad-app-configuration.md#create-a-new-app-registration). Příklad:  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Nastavte `APPID` hodnotě jste si uložili v kroku 9 informace o [vytvořit registrace nové aplikace](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Nastavte `SECRET` hodnotě jste si uložili v kroku 6 [vytvořit tajný kód klienta](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Nastavte `TENANT` hodnotu ID tenanta, jste si uložili v kroku 7 [vytvořit nového tenanta](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Vytvořte skupinu prostředků clusteru. Spusťte následující příkaz z prostředí Bash, které jste podnikli k definování proměnné výše:

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

Nyní jste připraveni vytvořit cluster.

 Pokud virtuální síť clusteru nejsou připojení k existující virtuální síť, vynechejte koncové `--vnet-peer-id $VNET_ID` parametr v následujícím příkladu.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Po několika minutách `az openshift create` bude dokončena úspěšně a vrátí odpověď ve formátu JSON obsahující podrobnosti o vašem clusteru.

> [!NOTE]
> Pokud dojde k chybě, název hostitele není k dispozici, může být vzhledem k tomu, že váš název clusteru není jedinečný. Zkuste odstranit původní registrace aplikace a znovu provedení kroků v [vytvoření registrace nové aplikace] (howto-aad-app-configuration.md#create-a-new-app-registration) (vynechání posledním krokem vytvoření nového uživatele, od doby, kdy už nějakou vytvořili) s jiný název clusteru.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Krok 3: Přihlaste se ke konzole Openshiftu

Nyní jste připraveni přihlášení ke konzole OpenShift nového clusteru. [OpenShift Webová konzola](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) vám umožní vizualizovat, procházet a spravovat obsah vašich projektů OpenShift.

Přihlásíme jako [nového uživatele Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) vytvořily pro testování. K tomuto účelu bude nutné instance čerstvé prohlížeče, který nemá v mezipaměti uložené identity, které standardně používáte k přihlášení k webu Azure portal.

1. Otevřít *incognito* okno (Chrome) nebo *InPrivate* okno (Microsoft Edge).
2. Přejděte na adresu URL přihlašování, který jste vytvořili v kroku 6 postupu [vytvořit registrace nové aplikace](howto-aad-app-configuration.md#create-a-new-app-registration). Například https://constoso.eastus.cloudapp.azure.com.

> [!NOTE]
> OpenShift konzoly používá certifikát podepsaný svým držitelem.
> Po zobrazení výzvy v prohlížeči, obejít upozornění a "nedůvěryhodné" potvrzení.

Přihlaste se pomocí uživatele a heslo, které jste vytvořili v [vytvořte nového uživatele služby Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user) při **oprávnění požadovaná** se zobrazí dialogové okno, vyberte **souhlas jménem svojí organizace**  a potom **přijmout**.

Nyní jste se přihlásili do konzoly clusteru.

![Snímek obrazovky konzoly cluster Openshiftu](./media/aro-console.png)

 Další informace o [pomocí konzoly nástroje OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) k vytvoření a součástí bitové kopie [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentaci.

## <a name="step-4-install-the-openshift-cli"></a>Krok 4: Instalace rozhraní příkazového řádku Openshiftu

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