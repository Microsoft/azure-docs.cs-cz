---
title: OpenShift v Azure po nasazení úloh | Dokumentace Microsoftu
description: Další úkoly pro za OpenShift cluster byla nasazena.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: najoshi
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
ms.openlocfilehash: d400512c2e96e0e24bbf965b2e201adf92ccbb0f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434887"
---
# <a name="post-deployment-tasks"></a>Úlohy po nasazení

Po nasazení clusteru služby OpenShift, můžete nakonfigurovat další položky. V tomto článku najdete následující:

- Jak nakonfigurovat jednotné přihlašování pomocí Azure Active Directory (Azure AD)
- Postup konfigurace Log Analytics k monitorování Openshiftu
- Konfigurace metrik a protokolování

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurace jednotného přihlašování pomocí Azure Active Directory

Pro účely ověření služby Azure Active Directory, je třeba nejprve vytvořit registrace aplikace Azure AD. Tento proces zahrnuje dva kroky: registrace aplikace pro vytváření a konfigurace oprávnění.

### <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

Tyto kroky používají Azure CLI k vytvoření registrace aplikace a grafickým uživatelským rozhraním (portál) k nastavení oprávnění. K vytvoření registrace aplikace, budete potřebovat následující pět druhy údajů:

- Zobrazovaný název: název registrace aplikace (například OCPAzureAD)
- Domovská stránka: OpenShift konzoly adresy URL (například https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Identifikátor URI: Adresa URL konzoly OpenShift (např. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Adresa URL odpovědi: Hlavní veřejnou adresu URL a název registrace aplikace (například) https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Heslo: Zabezpečené heslo (použijte silné heslo)

Následující příklad vytvoří registrace aplikace pomocí výše uvedených informací:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Pokud příkaz je úspěšné, získáte výstup JSON podobný:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
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
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
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

Najdete ID tenanta pomocí následujícího příkazu rozhraní příkazového řádku: ```az account show```

Restartujte hlavní služby OpenShift na všechny hlavní uzly:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) s více hlavních serverů**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform se na jediném masteru**

```bash
sudo systemctl restart atomic-openshift-master
```

V konzole nástroje OpenShift, uvidíte teď dvě možnosti pro ověřování: htpasswd_auth a [registrace aplikace].

## <a name="monitor-openshift-with-log-analytics"></a>OpenShift monitorování pomocí Log Analytics

Pokud chcete monitorovat OpenShift službou Log Analytics, můžete použít jednu ze dvou možností: Instalace agenta OMS na hostitele virtuálního počítače nebo kontejnerů OMS. Tento článek obsahuje pokyny pro nasazení kontejnerů OMS.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Vytvoření projektu aplikace OpenShift ke službě Log Analytics a nastavení přístupu uživatelů

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Vytvořte soubor yaml démon set

Vytvořte soubor s názvem ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Vytvořte soubor yaml tajného kódu

Chcete-li vytvořit soubor yaml tajných kódů, budete potřebovat dva druhy údajů: ID pracovního prostoru Log Analytics a Log Analytics pracovní prostor sdíleného klíče. 

Následuje ukázkový soubor ocp-secret.yml: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Nahradit wsid_data s Base64 kódované ID pracovního prostoru Log Analytics. Potom nahraďte key_data s kódováním Base64 Log Analytics pracovní prostor sdílené klíče.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Vytvoření tajného kódu a démona

Nasaďte soubor tajného kódu:

```bash
oc create -f ocp-secret.yml
```

Nasazení sady démon agenta OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Konfigurace metrik a protokolování

Šablony Azure Resource Manageru pro OpenShift Container Platform obsahuje vstupní parametry pro zapnutí metrik a protokolování. OpenShift Container Platform Marketplace nabídku a šablony OpenShift Origin Resource Manageru nepodporují.

Pokud jste použili šablony Resource Manageru OCP a metriky a protokolování nebyly povoleny v době instalace nebo pokud jste použili nabídky OCP Marketplace, lze snadno povolte tyto po jejich výskytu. Pokud používáte šablony OpenShift Origin Resource Manageru, některé předběžné práce je nutná.

### <a name="openshift-origin-template-pre-work"></a>Před pracovní šablony OpenShift Origin

1. Připojte přes SSH k hlavnímu uzlu první pomocí portu 2200.

   Příklad:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Upravte soubor /etc/ansible/hosts a přidejte následující řádky za část zprostředkovatele Identity (# Povolit HTPasswdPasswordIdentityProvider):

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

3. Nahraďte řetězec použitý pro možnost openshift_master_default_subdomain ve stejném souboru /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Poskytovatel cloudu Azure používá

První hlavní uzel (původní) nebo bastionu uzlů (OCP), SSH s použitím přihlašovacích údajů zadali při nasazení. Vydejte následující příkaz:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Nepoužíváte Azure poskytovatel cloudu

První hlavní uzel (původní) nebo bastionu uzlů (OCP), SSH s použitím přihlašovacích údajů zadali při nasazení. Vydejte následující příkaz:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Další postup

- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Začínáme s OpenShiftem Origin](https://docs.openshift.org/latest/getting_started/index.html)
