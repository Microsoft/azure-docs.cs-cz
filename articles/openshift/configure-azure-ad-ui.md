---
title: Azure Red Hat OpenShift se systémem OpenShift 4 – Konfigurace ověřování Azure Active Directory pomocí Azure Portal a webové konzole OpenShift
description: Naučte se konfigurovat ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift se systémem OpenShift 4 pomocí Azure Portal a webové konzole OpenShift.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 2cb54c202af04996080cda970b3d327145f0e72b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469877"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Konfigurace ověřování Azure Active Directory pro cluster Azure Red Hat OpenShift 4 (portál)

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Než začnete

Vytvořte **adresu URL zpětného volání OAuth** clusteru a poznamenejte si ho. Nezapomeňte nahradit položku **ARO – RG** pomocí názvu skupiny prostředků a **ARO – cluster** s názvem vašeho clusteru.

> [!NOTE]
> `AAD`Oddíl v adrese URL zpětného volání OAuth by se měl shodovat s názvem zprostředkovatele identity OAuth, který nastavíte později.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Vytvoření aplikace Azure Active Directory pro ověřování

Přihlaste se k Azure Portal a přejděte do okna [Registrace aplikací](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)a pak klikněte na **Nová registrace** a vytvořte novou aplikaci.

Zadejte název aplikace, například **ARO – azuread-auth**, a zadejte **identifikátor URI pro přesměrování** pomocí hodnoty adresy URL zpětného volání OAuth, kterou jste načetli dříve.

![Registrace nové aplikace](media/aro4-ad-registerapp.png)

Přejděte na **certifikáty & tajných** kódů a klikněte na **nový tajný klíč klienta** a vyplňte podrobnosti. Poznamenejte si hodnotu klíče, protože ji budete používat v pozdější fázi. Nebudete ho moct znovu načíst.

![Vytvoření tajného klíče](media/aro4-ad-clientsecret.png)

Přejděte na **Přehled** a poznamenejte si **ID aplikace (klienta)** a **ID adresáře (tenant)**. Budete je potřebovat v pozdější fázi.

![Načtení ID aplikace (klienta) a adresáře (tenant)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Konfigurace volitelných deklarací identity

Vývojáři aplikací můžou ve svých aplikacích Azure AD použít [volitelné deklarace identity](../active-directory/develop/active-directory-optional-claims.md) a určit, které deklarace identity se mají v tokenech odeslaných do jejich aplikace.

Volitelné deklarace identity můžete použít k těmto akcím:

* Vyberte další deklarace identity, které chcete zahrnout do tokenů pro vaši aplikaci.
* Změna chování určitých deklarací identity, které Azure AD vrací v tokenech
* Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.

OpenShift nakonfigurujeme tak, aby používala `email` deklaraci identity, a vraťte se k `upn` nastavení preferovaného uživatelského jména tak, že přidáte `upn` jako součást tokenu ID vráceného Azure Active Directory.

Přejděte na **Konfigurace tokenu (Preview)** a klikněte na **přidat volitelnou deklaraci identity**. Vyberte **ID** a pak zkontrolujte deklarace **e-mailu** a **UPN** .

![Vytvoření tajného klíče](media/aro4-ad-tokens.png)

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

Adresu URL konzoly clusteru můžete najít spuštěním následujícího příkazu, který bude vypadat nějak takto: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

V prohlížeči spusťte adresu URL konzoly a přihlaste se pomocí `kubeadmin` přihlašovacích údajů.

Přejděte na **Správa**, klikněte na **Nastavení clusteru**a pak vyberte kartu **globální konfigurace** . Posuňte se na výběr **OAuth**.

Posuňte se dolů a vyberte **Přidat** pod **zprostředkovateli identity** a vyberte **OpenID připojit**.
![V rozevíracím seznamu zprostředkovatelé identity vyberte OpenID připojit.](media/aro4-oauth-idpdrop.png)

Zadejte název jako **AAD**, **ID klienta** jako **ID aplikace** a **tajný klíč klienta**. **Adresa URL vystavitele** je formátována takto: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Nahraďte zástupný symbol ID tenanta, který jste získali dříve.

![Vyplnit podrobnosti OAuth](media/aro4-oauth-idp-1.png)

Posuňte se dolů k části **deklarace identity** a aktualizujte **preferované uživatelské jméno** tak, aby používala hodnotu z deklarace **hlavního názvu uživatele (UPN)** .

![Vyplnit podrobnosti deklarací identity](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Ověření přihlašovacích údajů prostřednictvím Azure Active Directory

Pokud se teď odhlásíte do webové konzoly OpenShift a zkusíte se znovu přihlásit, zobrazí se nová možnost přihlášení pomocí **AAD**. Možná budete muset několik minut počkat.

![Přihlašovací obrazovka s možností Azure Active Directory](media/aro4-login-2.png)