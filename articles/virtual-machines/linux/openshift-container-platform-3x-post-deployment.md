---
title: OpenShift Container Platform 3.11 v úlohách Azure po nasazení
description: Další úlohy po nasazení clusteru OpenShift Container Platform 3.11.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1635589b282dc33f6a1e9c2552dc8a73c67b9004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294747"
---
# <a name="post-deployment-tasks"></a>Úlohy po nasazení

Po nasazení clusteru OpenShift můžete nakonfigurovat další položky. Tento článek se zabývá:

- Jak nakonfigurovat jednotné přihlašování pomocí služby Azure Active Directory (Azure AD)
- Konfigurace protokolů Azure Monitor u sledování OpenShiftu
- Jak konfigurovat metriky a protokolování
- Jak nainstalovat Open Service Broker pro Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurace jednotného přihlášení pomocí služby Azure Active Directory

Pokud chcete používat Azure Active Directory pro ověřování, musíte nejdřív vytvořit registraci aplikace Azure AD. Tento proces zahrnuje dva kroky: vytvoření registrace aplikace a konfiguraci oprávnění.

### <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

Tyto kroky používají Azure CLI k vytvoření registrace aplikace a GUI (portál) nastavit oprávnění. K vytvoření registrace aplikace potřebujete následujících pět informací:

- Zobrazovaný název: Název registrace aplikace (například OCPAzureAD)
- Domovská stránka: Adresa URL konzoly OpenShift (například `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Identifikátor URI: Adresa URL konzoly `https://masterdns343khhde.westus.cloudapp.azure.com/console`OpenShift (například)
- Adresa URL odpovědi: Hlavní veřejná adresa URL `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`a název registrace aplikace (například)
- Heslo: Bezpečné heslo (použijte silné heslo)

Následující příklad vytvoří registraci aplikace pomocí předchozích informací:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Pokud je příkaz úspěšný, získáte výstup JSON podobný:

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

1. Vyberte Azure **Active Directory** > **App Registration**.
2. Vyhledejte registraci aplikace (například OCPAzureAD).
3. Ve výsledcích klikněte na registraci aplikace.
4. V části **Nastavení**vyberte **Požadovaná oprávnění**.
5. V části **Požadovaná oprávnění**vyberte **Přidat**.

   ![Registrace aplikace](media/openshift-post-deployment/app-registration.png)

6. Klikněte na krok 1: Vyberte rozhraní API a potom klikněte na **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory).** V dolní části klikněte na **Vybrat.**

   ![Rozhraní API pro výběr registrace aplikací](media/openshift-post-deployment/app-registration-select-api.png)

7. Krok 2: Vyberte oprávnění, **vyberte Přihlásit se a přečíst profil uživatele** v části **Delegovaná oprávnění**a potom klepněte na **vybrat**.

   ![Přístup k registraci aplikace](media/openshift-post-deployment/app-registration-access.png)

8. Vyberte **Done** (Hotovo).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurace ověřování OpenShift pro Azure AD

Chcete-li nakonfigurovat OpenShift používat Azure AD jako zprostředkovatele ověřování, musí být soubor /etc/origin/master/master-config.yaml upraven ve všech hlavních uzlech.

Najděte ID klienta pomocí následujícího příkazu příkazu příkazu příkazu:

```azurecli
az account show
```

V souboru yaml najděte následující řádky:

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

Bezprostředně za předchozí řádky vložte následující řádky:

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

Ujistěte se, že text zarovná správně pod identityProviders. Najděte ID klienta pomocí následujícího příkazu příkazu příkazu příkazu:```az account show```

Restartujte hlavní služby OpenShift ve všech hlavních uzlech:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

V konzole OpenShift nyní vidíte dvě možnosti ověřování: htpasswd_auth a [App Registration].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorování OpenShiftu pomocí protokolů Azure Monitoru

Existují tři způsoby, jak přidat agenta Log Analytics do OpenShift.
- Instalace agenta Log Analytics pro Linux přímo na každém uzlu OpenShift
- Povolení rozšíření virtuálních počítačů Azure Monitor na každém uzlu OpenShift
- Instalace agenta Log Analytics jako sady daemonů OpenShift

Přečtěte si úplné [pokyny](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) pro více informací.

## <a name="configure-metrics-and-logging"></a>Konfigurace metrik a protokolování

Na základě větve mohou šablony Azure Resource Manager u platformy OpenShift Container Platform a OKD poskytovat vstupní parametry pro povolení metrik a protokolování jako součást instalace.

Nabídka OpenShift Container Platform Marketplace také poskytuje možnost povolit metriky a protokolování během instalace clusteru.

Pokud metriky / protokolování nebyla povolena během instalace clusteru, mohou být snadno povoleny po faktu.

### <a name="azure-cloud-provider-in-use"></a>Poskytovatel Cloud Azure se používá

SSH do uzlu bastionu nebo prvního hlavního uzlu (na základě šablony a větve v provozu) pomocí pověření poskytnutých během nasazení. Vyvodit následující příkaz:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider se nepoužívá

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalace zprostředkovatele otevřených služeb pro Azure (OSBA)

Open Service Broker pro Azure nebo OSBA umožňuje zřizování cloudových služeb Azure přímo z OpenShiftu. OSBA v implementaci rozhraní API pro služby Open Service Broker pro Azure. Rozhraní API pro služby Open Service Broker je specifikace, která definuje společný jazyk pro poskytovatele cloudu, který mohou nativní cloudové aplikace používat ke správě cloudových služeb bez uzamčení.

Chcete-li nainstalovat osba na OpenShift, https://github.com/Azure/open-service-broker-azure#openshift-project-templatepostupujte podle pokynů zde: . 
> [!NOTE]
> Proveďte pouze kroky v části Šablona projektu OpenShift a ne celý oddíl Instalace.

## <a name="next-steps"></a>Další kroky

- [Začínáme s platformou OpenShift Container Platform](https://docs.openshift.com)
