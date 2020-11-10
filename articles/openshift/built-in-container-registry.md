---
title: Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4
description: Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414543"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4

V tomto článku nakonfigurujete integrovaný registr imagí kontejneru pro cluster Azure Red Hat OpenShift (ARO) 4. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nastavení Azure AD
> * Nastavení OpenID Connect
> * Přístup k integrovanému registru imagí kontejneru

## <a name="prerequisites"></a>Předpoklady

* Pomocí postupu v části [Vytvoření clusteru Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster)vytvořte cluster. Nezapomeňte vytvořit cluster s `--pull-secret` argumentem na `az aro create` .  To je nutné v případě, že chcete nastavit službu Azure AD pro přihlášení, jak je požadováno v tomto článku.
* Pomocí postupu v části [připojení k clusteru Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster)se připojte ke clusteru.
   * Nezapomeňte postupovat podle kroků v části "instalace rozhraní CLI OpenShift", protože použijete `oc` příkaz později v tomto článku.
   * Poznamenejte si adresu URL konzoly clusteru, která vypadá nějak takto `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` :. Hodnoty pro `<random>` a `<region>` budou použity později v tomto článku.
   * Poznamenejte si `kubeadmin` přihlašovací údaje. Budou použity později v tomto článku.

## <a name="set-up-azure-active-directory"></a>Nastavit Azure Active Directory

Azure Active Directory (Azure AD) implementuje OpenID Connect (OIDC). OIDC vám umožňuje používat Azure AD pro přihlášení ke clusteru ARO. Pokud chcete nastavit Azure AD, postupujte podle následujících kroků.

1. Pomocí kroků v části rychlý Start nastavte tenanta Azure AD [: nastavte tenanta](/azure/active-directory/develop/quickstart-create-new-tenant). Váš účet Azure už může mít tenanta. Pokud ano, můžete ho vynechat, pokud máte v tenantovi dostatečná oprávnění, abyste mohli postupovat podle pokynů. Poznamenejte si **ID tenanta**. Tato hodnota bude použita později.
2. Pomocí postupu v části [Přidání nebo odstranění uživatelů pomocí Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory)vytvořte alespoň jednoho uživatele Azure AD. Tyto účty můžete použít k otestování aplikace. Poznamenejte si e-mailové adresy a hesla těchto účtů, abyste se přihlásili.
3. Pomocí postupu v rychlém startu vytvořte novou registraci aplikace v tenantovi Azure AD [: registrace aplikace s platformou Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) 
   1. Odvolání poznámky v požadavcích na hodnoty pro `<random>` a `<region>` . Pomocí těchto hodnot můžete vytvořit identifikátor URI podle následujícího vzorce:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Až se dostanete do kroku s polem **URI přesměrování** , zadejte identifikátor URI z předchozího kroku.
   1. Vyberte **Zaregistrovat**.
   1. Na stránce **Přehled** aplikace si poznamenejte hodnotu zobrazenou pro **aplikaci (ID klienta)** , jak je znázorněno zde.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Stránka s přehledem aplikace Azure AD":::

4. Vytvoří nový tajný klíč klienta. 
   1. V nově vytvořené registraci aplikace vyberte **certifikáty & tajné klíče** v levém navigačním podokně.
   1. Vyberte **Nový tajný klíč klienta**.
   1. Zadejte **Popis** a vyberte **Přidat**.
   1. Vygenerované hodnoty **tajného klíče klienta** uložte. Tato hodnota bude použita později v článku.

### <a name="add-openid-connect-identity-provider"></a>Přidat zprostředkovatele identity OpenID Connect

ARO poskytuje integrovaný registr kontejnerů.  Pokud chcete získat přístup, nakonfigurujte poskytovatele identity (IDP) pomocí Azure AD OIDC pomocí následujících kroků.

1. Přihlaste se k webové konzoli OpenShift z prohlížeče jako `kubeadmin` .  To je stejný postup, který se používá při provádění kroků v [kurzu: Připojte se ke clusteru Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. V levém navigačním podokně vyberte **Správa**  >  **Nastavení clusteru**.
1. Uprostřed stránky vyberte **globální konfigurace**.
1. Vyhledejte **OAuth** ve sloupci **prostředek konfigurace** v tabulce a vyberte ho.
1. V části **Zprostředkovatelé identity** vyberte **Přidat** a zvolte **OpenID připojit**.
1. Nastavte **název** jako **OpenID**.  Tato hodnota již může být vyplněna.
1. Nastavte **ID klienta** a **tajný klíč klienta** jako hodnoty z předchozího kroku.
1. Podle tohoto kroku Najděte hodnotu pro **adresu URL vystavitele**.
   1. Nahraďte parametrem **\<tenant-id>** uloženým v části **Nastavení Azure Active Directory** v adrese URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Otevřete adresu URL ve stejném prohlížeči, který jste používali k interakci s Azure Portal.
   1. Zkopírujte hodnotu **vystavitele** vlastnosti v části VRÁCENÝ text JSON a vložte ji do textového pole s POPISKem **URL vystavitele**.  Hodnota **vystavitele** bude vypadat nějak takto `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Přejděte do dolní části stránky a vyberte **Přidat**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect v OpenShift.":::
1. Odhlaste se z webové konzoly OpenShift tak, že vyberete tlačítko **Kube: admin** v pravém horním rohu okna prohlížeče a zvolíte **Odhlásit** se.

### <a name="access-the-built-in-container-image-registry"></a>Přístup k integrovanému registru imagí kontejneru

Následující pokyny umožňují přístup k integrovanému registru.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definujte uživatele Azure AD jako správce.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí přihlašovacích údajů uživatele Azure AD.

   1. K přihlášení do konzoly použijte funkci InPrivate, anonymním nebo jiné podobné funkce okna prohlížeče.
   1. Po povolení OIDC bude okno vypadat jinak.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Přihlašovací okno s povoleným OpenID připojením":::
   1. Vybrat **OpenID**

   > [!NOTE]
   > Poznamenejte si uživatelské jméno a heslo, které používáte k přihlášení. Toto uživatelské jméno a heslo budou fungovat jako správce pro další akce v tomto a dalších článcích.
1. Přihlaste se pomocí OpenShift CLI pomocí následujících kroků.  Pro účely diskuze se tento proces nazývá `oc login` .
   1. V pravém horním rohu webové konzoly rozbalte kontextovou nabídku přihlášeného uživatele a pak vyberte **příkaz Kopírovat přihlašovací údaje**.
   1. V případě potřeby se přihlaste k novému oknu s kartami stejným uživatelem.
   1. Vyberte možnost **Zobrazit token**.
   1. Zkopírujte níže uvedenou hodnotu **Přihlásit se pomocí tohoto tokenu** do schránky a spusťte ji v prostředí, jak je znázorněno zde.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Spusťte `oc whoami` v konzole nástroje a poznamenejte si výstup **\<aad-user>** .  Tuto hodnotu použijeme později v článku.
1. Odhlaste se z webové konzoly OpenShift. Vyberte tlačítko v pravém horním rohu okna prohlížeče označené jako **\<aad-user>** a zvolte **Odhlásit**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Udělte uživateli Azure AD potřebné role pro interakci s registrem.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí `kubeadmin` přihlašovacích údajů.
1. Přihlaste se k rozhraní příkazového řádku OpenShift pomocí tokenu `kubeadmin` , a to pomocí kroků `oc login` uvedených výše, ale po přihlášení ke konzole webu pomocí `kubeadmin` .
1. Spuštěním následujících příkazů povolte přístup k integrovanému registru pro **uživatele AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   Výstup by měl vypadat podobně jako následující.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   Výstup by měl vypadat podobně jako následující.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   Výstup by měl vypadat podobně jako následující.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   Výstup by měl vypadat podobně jako následující.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

Použijte integrovaný registr imagí kontejneru nasazením aplikace na OpenShift.  V případě aplikací v jazyce Java postupujte podle pokynů průvodce, [Nasaďte aplikaci Java s otevřeným/WebSpherem v clusteru Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
