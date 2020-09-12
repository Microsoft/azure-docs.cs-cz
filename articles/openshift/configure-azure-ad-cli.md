---
title: Azure Red Hat OpenShift se systémem OpenShift 4 – Konfigurace ověřování Azure Active Directory pomocí příkazového řádku
description: Naučte se konfigurovat ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift se systémem OpenShift 4 pomocí příkazového řádku.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: fd6ea0749cce154ae20479bc54ef9b7374a69d0c
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469418"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurace ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift 4 (CLI)

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Načtěte adresy URL pro konkrétní clustery, které se budou používat ke konfiguraci aplikace Azure Active Directory.

Vytvořte adresu URL zpětného volání OAuth clusteru a uložte ji do proměnné **oauthCallbackURL**. Nezapomeňte nahradit položku **ARO – RG** pomocí názvu skupiny prostředků a **ARO – cluster** s názvem vašeho clusteru.

> [!NOTE]
> `AAD`Oddíl v adrese URL zpětného volání OAuth by se měl shodovat s názvem zprostředkovatele identity OAuth, který nastavíte později.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Vytvoření aplikace Azure Active Directory pro ověřování

Vytvořte aplikaci Azure Active Directory a načtěte vytvořený identifikátor aplikace. Nahraďte **\<ClientSecret>** zabezpečeným heslem.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Měli byste se vrátit jako takový. Poznamenejte si ji, protože se jedná o **AppID** , který budete potřebovat v pozdějších krocích.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Načtěte ID tenanta předplatného, které vlastní aplikace.

```azure
az account show --query tenantId -o tsv
```

Měli byste se vrátit jako takový. Poznamenejte si ho, protože to je **TenantId** , které budete potřebovat v pozdějších krocích.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
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

Nahraďte **\<AppID>** ID, které jste získali dříve.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aktualizace oprávnění rozsahu Azure Active Directory aplikace

Aby bylo možné číst informace o uživateli z Azure Active Directory, musíme definovat správné obory.

Nahraďte **\<AppID>** ID, které jste získali dříve.

Přidejte oprávnění pro obor **Azure Active Directory Graph. User. Read** , aby bylo možné povolit přihlášení a čtení profilu uživatele.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> Pokud nejste ověřeni jako globální správce pro tento Azure Active Directory, můžete zprávu pro udělení souhlasu ignorovat, protože po přihlášení k vlastnímu účtu se zobrazí výzva.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Přiřazení uživatelů a skupin ke clusteru (volitelné)

Aplikace zaregistrované v tenantovi Azure Active Directory (Azure AD) jsou ve výchozím nastavení dostupné všem uživatelům tenanta, kteří se úspěšně ověřují. Azure AD umožňuje správcům a vývojářům klientů omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi.

Při [přiřazování uživatelů a skupin do aplikace](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)postupujte podle pokynů v dokumentaci k Azure Active Directory.

## <a name="configure-openshift-openid-authentication"></a>Konfigurace ověřování OpenShift OpenID

Načtěte `kubeadmin` přihlašovací údaje. Spusťte následující příkaz a vyhledejte heslo pro `kubeadmin` uživatele.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Následující příklad výstupu ukazuje, že heslo bude v `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Přihlaste se k serveru rozhraní API OpenShift clusteru pomocí následujícího příkazu. `$apiServer`Proměnná byla nastavena [dříve](). Nahraďte **\<kubeadmin password>** heslem, které jste načetli.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Vytvoření tajného klíče OpenShift pro uložení tajného kódu aplikace Azure Active Directory nahraďte **\<ClientSecret>** tajným klíčem, který jste získali dříve.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```

Vytvořte soubor **oidc. yaml** pro konfiguraci ověřování OpenShift OpenID proti Azure Active Directory. **\<AppID>** Hodnoty a nahraďte **\<TenantId>** hodnotami, které jste získali dříve.

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
      clientID: <AppId>
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
      issuer: https://login.microsoftonline.com/<TenantId>
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