---
title: OpenShift kontejnerová platforma 3,11 v úlohách po nasazení Azure
description: Další úlohy pro po nasazení clusteru OpenShift Container Platform 3,11
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: c3f9aaa15a697202aa76c563ed62bf37443d69ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669385"
---
# <a name="post-deployment-tasks"></a>Úkoly po nasazení

Po nasazení clusteru OpenShift můžete nakonfigurovat další položky. Tento článek popisuje:

- Jak nakonfigurovat jednotné přihlašování pomocí Azure Active Directory (Azure AD)
- Jak nakonfigurovat protokoly Azure Monitor pro monitorování OpenShift
- Jak nakonfigurovat metriky a protokolování
- Jak nainstalovat Open Service Broker pro Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurace jednotného přihlašování pomocí Azure Active Directory

Pokud chcete použít Azure Active Directory pro ověřování, nejdřív musíte vytvořit registraci aplikace Azure AD. Tento proces se skládá ze dvou kroků: Vytvoření registrace aplikace a konfigurace oprávnění.

### <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

Tyto kroky používají rozhraní příkazového řádku Azure CLI k vytvoření registrace aplikace a grafického uživatelského rozhraní (portálu) k nastavení oprávnění. K vytvoření registrace aplikace potřebujete následující pět informací:

- Zobrazovaný název: název registrace aplikace (například OCPAzureAD)
- Domovská stránka: adresa URL konzoly OpenShift (například `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Identifikátor URI: adresa URL konzoly OpenShift (například `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Adresa URL odpovědi: hlavní veřejná adresa URL a název registrace aplikace (například `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Heslo: zabezpečené heslo (použijte silné heslo)

Následující příklad vytvoří registraci aplikace pomocí předchozích informací:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Pokud je příkaz úspěšný, získáte výstup JSON podobný tomuto:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Poznamenejte si vlastnost appId vrácenou z příkazu pro pozdější krok.

Na webu Azure Portal:

1. Vyberte **Azure Active Directory**  >  **registraci aplikace**.
2. Vyhledejte registraci vaší aplikace (například OCPAzureAD).
3. Ve výsledcích klikněte na registrace aplikace.
4. V části **Nastavení** vyberte **požadovaná oprávnění**.
5. V části **požadovaná oprávnění** vyberte **Přidat**.

   ![Registrace aplikace](media/openshift-post-deployment/app-registration.png)

6. Klikněte na krok 1: Vyberte rozhraní API a pak klikněte na **Windows Azure Active Directory (Microsoft. Azure. Active Directory)**. V dolní části klikněte na **Vybrat** .

   ![Registrace aplikace – výběr rozhraní API](media/openshift-post-deployment/app-registration-select-api.png)

7. V kroku 2: vyberte oprávnění vyberte možnost **Přihlásit se a číst profil uživatele** v části **delegovaná oprávnění** a pak klikněte na **Vybrat**.

   ![Přístup k registraci aplikace](media/openshift-post-deployment/app-registration-access.png)

8. Vyberte **Hotovo**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurace OpenShift pro ověřování Azure AD

Pokud chcete nakonfigurovat OpenShift pro použití Azure AD jako poskytovatele ověřování, soubor/etc/origin/master/master-config.yaml se musí upravit na všech hlavních uzlech.

Pomocí následujícího příkazu rozhraní příkazového řádku Najděte ID tenanta:

```azurecli
az account show
```

V souboru YAML vyhledejte následující řádky:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Vložte následující řádky hned za předchozí řádky:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Ujistěte se, že text je v identityProviders správně zarovnán. Pomocí následujícího příkazu rozhraní příkazového řádku Najděte ID tenanta: ```az account show```

Restartujte hlavní služby OpenShift ve všech hlavních uzlech:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

V konzole OpenShift se teď zobrazují dvě možnosti ověřování: htpasswd_auth a [Registrace aplikace].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorování OpenShift pomocí protokolů Azure Monitor

Existují tři způsoby, jak přidat agenta Log Analytics do OpenShift.
- Instalace agenta Log Analytics pro Linux přímo na každý uzel OpenShift
- Povolit rozšíření Azure Monitor VM na každém uzlu OpenShift
- Instalace agenta Log Analytics jako sady OpenShift démona

Další podrobnosti si můžete přečíst v části úplné [pokyny](../../azure-monitor/containers/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) .

## <a name="configure-metrics-and-logging"></a>Konfigurace metrik a protokolování

Na základě větve můžou šablony Azure Resource Manager pro OpenShift kontejnerové platformy a OKD poskytovat vstupní parametry pro povolení metrik a protokolování v rámci instalace.

Nabídka OpenShift Container Platform Marketplace také nabízí možnost Povolit metriky a protokolování během instalace clusteru.

Pokud se metriky a protokolování nepovolily během instalace clusteru, můžete je po faktu snadno povolit.

### <a name="azure-cloud-provider-in-use"></a>Používaný poskytovatel cloudu Azure

SSH na uzel bastionu nebo na první uzel hlavního uzlu (na základě šablony a používané větve) pomocí přihlašovacích údajů zadaných během nasazování. Vydejte následující příkaz:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Poskytovatel cloudu Azure se nepoužívá.

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalace Open Service Broker pro Azure (OSBA)

Otevřete Service Broker pro Azure nebo OSBA vám umožní zřídit Azure Cloud Services přímo z OpenShift. OSBA se v otevřené implementaci rozhraní API Service Broker pro Azure. Rozhraní Open Service Broker API je specifikace, která definuje společný jazyk pro poskytovatele cloudu, který můžou cloudové nativní aplikace použít ke správě cloudových služeb bez použití zámku.

Pokud chcete nainstalovat OSBA na OpenShift, postupujte podle pokynů v tomto umístění: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Proveďte pouze kroky uvedené v části šablona projektu OpenShift, nikoli v celé části instalování.

## <a name="next-steps"></a>Další kroky

- [Začínáme s kontejnerovou platformou OpenShift](https://docs.openshift.com)
