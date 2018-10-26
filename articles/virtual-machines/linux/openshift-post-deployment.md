---
title: OpenShift v Azure po nasazení úloh | Dokumentace Microsoftu
description: Další úkoly pro za OpenShift cluster byla nasazena.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 7b129eea513b7856ca99b02842b3b9c33c6ec19b
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50084981"
---
# <a name="post-deployment-tasks"></a>Úlohy po nasazení

Po nasazení clusteru služby OpenShift, můžete nakonfigurovat další položky. Tento článek se týká:

- Jak nakonfigurovat jednotné přihlašování pomocí Azure Active Directory (Azure AD)
- Postup konfigurace Log Analytics k monitorování Openshiftu
- Konfigurace metrik a protokolování
- Postup instalace zprostředkovatele Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurace jednotného přihlašování pomocí Azure Active Directory

Pro účely ověření služby Azure Active Directory, je třeba nejprve vytvořit registrace aplikace Azure AD. Tento proces zahrnuje dva kroky: registrace aplikace pro vytváření a konfigurace oprávnění.

### <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

Tyto kroky používají Azure CLI k vytvoření registrace aplikace a grafickým uživatelským rozhraním (portál) k nastavení oprávnění. K vytvoření registrace aplikace, budete potřebovat následující pět druhy údajů:

- Zobrazovaný název: název registrace aplikace (například OCPAzureAD)
- Domovská stránka: OpenShift konzoly adresy URL (například https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Identifikátor URI: Adresa URL konzoly OpenShift (např. https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Adresa URL odpovědi: Hlavní veřejnou adresu URL a název registrace aplikace (například) https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
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

1.  Vyberte **Azure Active Directory** > **registrace aplikace**.
2.  Hledání registrace vaší aplikace (například OCPAzureAD).
3.  Ve výsledcích klikněte na registraci aplikace.
4.  V části **nastavení**vyberte **požadovaná oprávnění**.
5.  V části **požadovaná oprávnění**vyberte **přidat**.

  ![Registrace aplikace](media/openshift-post-deployment/app-registration.png)

6.  Klikněte na krok 1: Vyberte rozhraní API a pak klikněte na tlačítko **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klikněte na tlačítko **vyberte** v dolní části.

  ![Vyberte rozhraní API registrace aplikace](media/openshift-post-deployment/app-registration-select-api.png)

7.  V kroku 2: Vyberte oprávnění, vyberte **přihlášení a čtení profilu uživatele** pod **delegovaná oprávnění**a potom klikněte na tlačítko **vyberte**.

  ![Registrace aplikace Access](media/openshift-post-deployment/app-registration-access.png)

8.  Vyberte **Done** (Hotovo).

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

**OpenShift Container Platform (OCP) s více hlavních serverů**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform se na jediném masteru**

```bash
sudo systemctl restart atomic-openshift-master
```

**OKD s více hlavních serverů**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OKD s na jediném masteru**

```bash
sudo systemctl restart origin-master
```

V konzole nástroje OpenShift, uvidíte teď dvě možnosti pro ověřování: htpasswd_auth a [registrace aplikace].

## <a name="monitor-openshift-with-log-analytics"></a>OpenShift monitorování pomocí Log Analytics

Existují tři způsoby, jak přidat agenta Log Analytics do OpenShift.
- Instalace agenta Log Analytics pro Linux přímo na každém uzlu Openshiftu
- Povolení rozšíření Log Analytics pro virtuální počítač na každém uzlu Openshiftu
- Instalace agenta Log Analytics jako OpenShift démon sadu

Úplné pokyny se nachází tady: https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift.

## <a name="configure-metrics-and-logging"></a>Konfigurace metrik a protokolování

Na základě větve, šablon Azure Resource Manageru pro OpenShift Container Platform a OKD zadejte vstupní parametry pro zapnutí metrik a protokolování jako součást instalace.

OpenShift Container Platform Marketplace nabídka také nabízí možnost povolení metrik a protokolování během instalace clusteru.

Pokud metriky / během instalace clusteru není povoleno protokolování, můžete je snadno povolit po jejich výskytu.

### <a name="ansible-inventory-pre-work"></a>Před pracovní inventáře Ansible

Zkontrolujte soubor inventáře ansible (/ etc/ansible/hostitele) má příslušných proměnných pro metriky / protokolování. Soubor inventáře můžete najít na různých hostitelích, na základě šablony použité.

Pro šablony OpenShift Container a nabídky Marketplace se nachází soubor inventáře na Bastion host. Pro šablonu OKD inventáře soubor se nachází na hostiteli master-0 nebo bastion host na základě větve používá.

1. Upravte soubor /etc/ansible/hosts a přidejte následující řádky za část zprostředkovatele Identity (# Povolit HTPasswdPasswordIdentityProvider). Pokud tyto řádky jsou již přítomny, nemusíte je znovu přidat.

   OpenShift / OKD verze 3.9 a starší

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

   OpenShift / OKD verze 3.10 a vyšší

   ```yaml
   # Setup metrics
   openshift_metrics_install_metrics=false
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}

   # Setup logging
   openshift_logging_install_logging=false
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_kibana_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_curator_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_master_public_url=https://kibana.$ROUTING
   ```

3. Nahraďte řetězec použitý pro možnost openshift_master_default_subdomain ve stejném souboru /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Poskytovatel cloudu Azure používá

SSH bastionu uzel nebo první hlavní uzly (na základě šablony a větve používá) pomocí přihlašovacích údajů zadali při nasazení. Vydejte následující příkaz:

**OpenShift Container Platform 3.7 nebo starší**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OpenShift Container Platform 3.9 a novější**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

**OKD 3.7 nebo starší**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OKD 3.9 a novější**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Nepoužíváte Azure poskytovatel cloudu

SSH bastionu uzel nebo první hlavní uzly (na základě šablony a větve používá) pomocí přihlašovacích údajů zadali při nasazení. Vydejte následující příkaz:


**OpenShift Container Platform 3.7 nebo starší**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OpenShift Container Platform 3.9 a novější**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.7 nebo starší**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.9 a novější**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True
ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalace technologie Open Service Broker for Azure (OSBA)

Otevřete službu Service Broker pro Azure nebo OSBA, umožňuje zřizovat služby Azure Cloud Services přímo z OpenShift. Osba, POUŽIJTE v implementaci otevřené rozhraní API služby Service Broker for Azure. Otevřené rozhraní API služby Service Broker je specifikace, která definuje společný jazyk pro cloud, kterému zprostředkovatelů, které cloud nativních aplikací můžete použít ke správě cloudových služeb bez zámku v.

Nainstalovat OSBA OpenShift, postupujte podle pokynů tady: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 

## <a name="next-steps"></a>Další postup

- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform)
- [Začínáme s OKD](https://docs.okd.io/latest)
