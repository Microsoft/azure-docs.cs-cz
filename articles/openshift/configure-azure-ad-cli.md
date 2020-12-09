---
title: Azure Red Hat OpenShift se systémem OpenShift 4 – Konfigurace ověřování Azure Active Directory pomocí příkazového řádku
description: Naučte se konfigurovat ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift se systémem OpenShift 4 pomocí příkazového řádku.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d69fa10408618fb188b42e1dd8f7b9d02820cc3
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862407"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurace ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift 4 (CLI)

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Načtěte adresy URL pro konkrétní clustery, které se budou používat ke konfiguraci aplikace Azure Active Directory.

Nastavte proměnné pro skupinu prostředků a název clusteru.

Nahraďte **\<resource_group>** názvem vaší skupiny prostředků a **\<aro_cluster>** názvem vašeho clusteru.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Vytvořte adresu URL zpětného volání OAuth clusteru a uložte ji do proměnné **oauthCallbackURL**. 

> [!NOTE]
> `AAD`Oddíl v adrese URL zpětného volání OAuth by se měl shodovat s názvem zprostředkovatele identity OAuth, který nastavíte později.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

Formát oauthCallbackURL se mírně liší od vlastních domén:

* Spusťte následující příkaz, pokud používáte vlastní doménu, např. `contoso.com` . 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Pokud nepoužíváte vlastní doménu, `$domain` bude to osm znaků alnum řetězec a je rozšířeno o `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> `AAD`Oddíl v adrese URL zpětného volání OAuth by se měl shodovat s názvem zprostředkovatele identity OAuth, který nastavíte později.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Vytvoření aplikace Azure Active Directory pro ověřování

Nahraďte **\<client_secret>** zabezpečeným heslem pro aplikaci.

```azurecli-interactive
client_secret=<client_secret>
```

Vytvořte aplikaci Azure Active Directory a načtěte vytvořený identifikátor aplikace.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Načtěte ID tenanta předplatného, které vlastní aplikace.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Vytvořte soubor manifestu, který definuje nepovinné deklarace identity, které se mají zahrnout do tokenu ID.

Vývojáři aplikací můžou ve svých aplikacích Azure AD použít [volitelné deklarace identity](../active-directory/develop/active-directory-optional-claims.md) a určit, které deklarace identity se mají v tokenech odeslaných do jejich aplikace.

Volitelné deklarace identity můžete použít k těmto akcím:

- Vyberte další deklarace identity, které chcete zahrnout do tokenů pro vaši aplikaci.
- Změna chování určitých deklarací identity, které Azure AD vrací v tokenech
- Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.

OpenShift nakonfigurujeme tak, aby používala `email` deklaraci identity, a vraťte se k `upn` nastavení preferovaného uživatelského jména tak, že přidáte `upn` jako součást tokenu ID vráceného Azure Active Directory.

Vytvořte **manifest.js** pro soubor pro konfiguraci Azure Active Directory aplikace.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Aktualizace optionalClaims aplikace Azure Active Directory s manifestem

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aktualizace oprávnění rozsahu Azure Active Directory aplikace

Aby bylo možné číst informace o uživateli z Azure Active Directory, musíme definovat správné obory.

Přidejte oprávnění pro obor **Azure Active Directory Graph. User. Read** , aby bylo možné povolit přihlášení a čtení profilu uživatele.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Zprávu můžete bez obav ignorovat, pokud nejste ověřeni jako globální správce tohoto Azure Active Directory. Standardní uživatelé domény budou požádáni o udělení souhlasu při prvním přihlášení ke clusteru pomocí svých přihlašovacích údajů AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Přiřazení uživatelů a skupin ke clusteru (volitelné)

Aplikace zaregistrované v tenantovi Azure Active Directory (Azure AD) jsou ve výchozím nastavení dostupné všem uživatelům tenanta, kteří se úspěšně ověřují. Azure AD umožňuje správcům a vývojářům klientů omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi.

Při [přiřazování uživatelů a skupin do aplikace](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)postupujte podle pokynů v dokumentaci k Azure Active Directory.

## <a name="configure-openshift-openid-authentication"></a>Konfigurace ověřování OpenShift OpenID

Načtěte `kubeadmin` přihlašovací údaje. Spusťte následující příkaz a vyhledejte heslo pro `kubeadmin` uživatele.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Přihlaste se k serveru rozhraní API OpenShift clusteru pomocí následujícího příkazu. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Vytvořte tajný klíč OpenShift pro uložení tajného kódu aplikace Azure Active Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Vytvořte soubor **oidc. yaml** pro konfiguraci ověřování OpenShift OpenID proti Azure Active Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Použijte konfiguraci na cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Zobrazí se odpověď podobná následující.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Ověření přihlašovacích údajů prostřednictvím Azure Active Directory

Pokud se teď odhlásíte do webové konzoly OpenShift a zkusíte se znovu přihlásit, zobrazí se nová možnost přihlášení pomocí **AAD**. Možná budete muset několik minut počkat.

![Přihlašovací obrazovka s možností Azure Active Directory](media/aro4-login-2.png)
