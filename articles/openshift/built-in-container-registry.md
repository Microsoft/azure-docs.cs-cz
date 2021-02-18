---
title: Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4
description: Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636373"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4

Azure Red Hat OpenShift poskytuje integrovaný registr kontejnerů imagí nazvaný [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) , který přidává možnost automatického zřizování nových úložišť imagí na vyžádání. Tato operace poskytuje uživatelům předdefinované umístění pro jejich sestavení aplikací, aby mohl nabízet výsledné obrázky.

V tomto článku nakonfigurujete integrovaný registr imagí kontejneru pro cluster Azure Red Hat OpenShift (ARO) 4. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nastavení Azure AD
> * Nastavení OpenID Connect
> * Přístup k integrovanému registru imagí kontejneru

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster ARO. Pokud potřebujete cluster ARO, Projděte si kurz ARO a [vytvořte cluster Azure Red Hat OpenShift 4](./tutorial-create-cluster.md). Nezapomeňte vytvořit cluster s `--pull-secret` argumentem na `az aro create` .  To je nezbytné pro konfiguraci ověřování Azure Active Directory a integrovaného registru kontejnerů.

Jakmile budete mít cluster, připojte se ke clusteru pomocí postupu v části [připojení k clusteru Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Nezapomeňte postupovat podle kroků v části "instalace rozhraní CLI OpenShift", protože použijete `oc` příkaz později v tomto článku.
   * Poznamenejte si adresu URL konzoly clusteru, která vypadá nějak takto `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` :. Hodnoty pro `<random>` a `<region>` budou použity později v tomto článku.
   * Poznamenejte si `kubeadmin` přihlašovací údaje. Budou také použity později v tomto článku.

### <a name="configure-azure-active-directory-authentication"></a>Konfigurace ověřování Azure Active Directory 

Azure Active Directory (Azure AD) implementuje OpenID Connect (OIDC). OIDC vám umožňuje používat Azure AD pro přihlášení ke clusteru ARO. Postupujte podle kroků v části [Konfigurace ověřování Azure Active Directory](configure-azure-ad-cli.md) pro nastavení clusteru.

## <a name="access-the-built-in-container-image-registry"></a>Přístup k integrovanému registru imagí kontejneru

Teď, když jste nastavili metody ověřování na cluster ARO, pojďme povolit přístup k integrovanému registru.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definujte uživatele Azure AD jako správce.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí přihlašovacích údajů uživatele Azure AD. Pro definování správce budeme využívat ověřování OpenShift OpenID s Azure Active Directory.

   1. K přihlášení do konzoly použijte funkci InPrivate, anonymním nebo jiné podobné funkce okna prohlížeče. Po povolení OIDC bude okno vypadat jinak.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Přihlašovací okno s povoleným OpenID připojením":::
   1. Vybrat **OpenID**

   > [!NOTE]
   > Poznamenejte si uživatelské jméno a heslo, které používáte k přihlášení. Toto uživatelské jméno a heslo budou fungovat jako správce pro další akce v tomto a dalších článcích.
2. Přihlaste se pomocí OpenShift CLI pomocí následujících kroků.  Pro účely diskuze se tento proces nazývá `oc login` .
   1. V pravém horním rohu webové konzoly rozbalte kontextovou nabídku přihlášeného uživatele a pak vyberte **příkaz Kopírovat přihlašovací údaje**.
   2. V případě potřeby se přihlaste k novému oknu s kartami stejným uživatelem.
   3. Vyberte možnost **Zobrazit token**.
   4. Zkopírujte níže uvedenou hodnotu **Přihlásit se pomocí tohoto tokenu** do schránky a spusťte ji v prostředí, jak je znázorněno zde.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Spusťte `oc whoami` v konzole nástroje a poznamenejte si výstup **\<aad-user>** .  Tuto hodnotu použijeme později v článku.
4. Odhlaste se z webové konzoly OpenShift. Vyberte tlačítko v pravém horním rohu okna prohlížeče označené jako **\<aad-user>** a zvolte **Odhlásit**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Udělte uživateli Azure AD potřebné role pro interakci s registrem.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí `kubeadmin` přihlašovacích údajů.
1. Přihlaste se k rozhraní příkazového řádku OpenShift pomocí tokenu `kubeadmin` , a to pomocí kroků `oc login` uvedených výše, ale po přihlášení ke konzole webu pomocí `kubeadmin` .
1. Spuštěním následujících příkazů povolte přístup k integrovanému registru pro **uživatele AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Získat adresu URL registru kontejneru

`oc get route`K získání adresy URL registru kontejneru použijte příkaz, jak je uvedeno dále.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Všimněte si výstupu konzoly **Container Registry adresa URL**. Použije se jako plně kvalifikovaný název registru pro tuto příručku a následné.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili vestavěný registr imagí kontejneru, můžete začít nasazením aplikace na OpenShift. V případě aplikací Java si [v clusteru Azure Red Hat OpenShift 4 podívejte na nasazení aplikace Java s otevřeným: Svoboda/WebSphere svoboda](howto-deploy-java-liberty-app.md).