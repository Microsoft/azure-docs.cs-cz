---
title: OpenShift v Azure po nasazení úloh | Dokumentace Microsoftu
description: Další úkoly pro za OpenShift cluster byla nasazena.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: fba29cd55f2d765faa107de3a8961032ef44deec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771320"
---
# <a name="post-deployment-tasks"></a>Úlohy po nasazení

Po nasazení clusteru služby OpenShift, můžete nakonfigurovat další položky. Tento článek se týká:

- Jak nakonfigurovat jednotné přihlašování pomocí Azure Active Directory (Azure AD)
- Konfigurace protokolů Azure Monitor k monitorování Openshiftu
- Konfigurace metrik a protokolování
- Postup instalace zprostředkovatele Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurace jednotného přihlašování pomocí Azure Active Directory

Pro účely ověření služby Azure Active Directory, je třeba nejprve vytvořit registrace aplikace Azure AD. Tento proces zahrnuje dva kroky: registrace aplikace pro vytváření a konfigurace oprávnění.

### <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

Tyto kroky používají Azure CLI k vytvoření registrace aplikace a grafickým uživatelským rozhraním (portál) k nastavení oprávnění. K vytvoření registrace aplikace, budete potřebovat následující pět druhy údajů:

- Zobrazovaný název: Název registrace aplikace (například OCPAzureAD)
- Domovská stránka: Adresa URL konzoly OpenShift (např. https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Identifikátor URI: Adresa URL konzoly OpenShift (např. https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Adresa URL odpovědi: Hlavním veřejnou adresu URL a název registrace aplikace (například) https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Heslo: Zabezpečené heslo (použijte silné heslo)

Následující příklad vytvoří registrace aplikace pomocí výše uvedených informací:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Pokud příkaz je úspěšné, získáte výstup JSON podobný:

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

Poznamenejte si vlastnost appId vrácenou příkazem pro později.

Na webu Azure Portal:

1. Vyberte **Azure Active Directory** > **registrace aplikace**.
2. Hledání registrace vaší aplikace (například OCPAzureAD).
3. Ve výsledcích klikněte na registraci aplikace.
4. V části **nastavení**vyberte **požadovaná oprávnění**.
5. V části **požadovaná oprávnění**vyberte **přidat**.

   ![Registrace aplikace](media/openshift-post-deployment/app-registration.png)

6. Klikněte na krok 1: Vyberte rozhraní API a potom klikněte na tlačítko **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klikněte na tlačítko **vyberte** v dolní části.

   ![Vyberte rozhraní API registrace aplikace](media/openshift-post-deployment/app-registration-select-api.png)

7. V kroku 2: Vyberte oprávnění, vyberte **přihlášení a čtení profilu uživatele** pod **delegovaná oprávnění**a potom klikněte na tlačítko **vyberte**.

   ![Registrace aplikace Access](media/openshift-post-deployment/app-registration-access.png)

8. Vyberte **Done** (Hotovo).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurace OpenShift pro ověřování Azure AD

Ke konfiguraci OpenShift používání Azure AD jako zprostředkovatele ověřování, je nutné upravit soubor /etc/origin/master/master-config.yaml na všechny hlavní uzly.

Najdete ID tenanta pomocí následujícího příkazu rozhraní příkazového řádku:

```azurecli
az account show
```

V souboru yaml vyhledejte následující řádky:

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

Ihned po předchozím řádků vložte následující řádky:

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

Zkontrolujte, zda že bude text zarovnán v rámci identityProviders správně. Najdete ID tenanta pomocí následujícího příkazu rozhraní příkazového řádku: ```az account show```

Restartujte hlavní služby OpenShift na všechny hlavní uzly:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

V konzole nástroje OpenShift, uvidíte teď dvě možnosti pro ověřování: htpasswd_auth a [registrace aplikace].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorování OpenShift s protokoly Azure monitoru

Existují tři způsoby, jak přidat agenta Log Analytics do OpenShift.
- Instalace agenta Log Analytics pro Linux přímo na každém uzlu Openshiftu
- Povolení rozšíření virtuálního počítače Azure Monitor na každém uzlu Openshiftu
- Instalace agenta Log Analytics jako démon OpenShift-set

Přečtěte si celý [pokyny](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) další podrobnosti.

## <a name="configure-metrics-and-logging"></a>Konfigurace metrik a protokolování

Na základě větve, šablon Azure Resource Manageru pro OpenShift Container Platform a OKD zadejte vstupní parametry pro zapnutí metrik a protokolování jako součást instalace.

OpenShift Container Platform Marketplace nabídka také nabízí možnost povolení metrik a protokolování během instalace clusteru.

Pokud metriky / během instalace clusteru není povoleno protokolování, můžete je snadno povolit po jejich výskytu.

### <a name="azure-cloud-provider-in-use"></a>Poskytovatel cloudu Azure používá

SSH bastionu uzel nebo první hlavní uzly (na základě šablony a větve používá) pomocí přihlašovacích údajů zadali při nasazení. Vydejte následující příkaz:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Nepoužíváte Azure poskytovatel cloudu

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalace technologie Open Service Broker for Azure (OSBA)

Otevřete službu Service Broker pro Azure nebo OSBA, umožňuje zřizovat služby Azure Cloud Services přímo z OpenShift. Osba, POUŽIJTE v implementaci otevřené rozhraní API služby Service Broker for Azure. Otevřené rozhraní API služby Service Broker je specifikace, která definuje společný jazyk pro cloud, kterému zprostředkovatelů, které cloud nativních aplikací můžete použít ke správě cloudových služeb bez zámku v.

Nainstalovat OSBA OpenShift, postupujte podle pokynů tady: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Pouze dokončete kroky v části šablony projektu OpenShift a ne celý oddíl instalace.

## <a name="next-steps"></a>Další postup

- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform)
