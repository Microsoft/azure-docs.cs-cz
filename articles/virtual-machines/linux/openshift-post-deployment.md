---
title: OpenShift Azure po nasazení úloh | Microsoft Docs
description: Další úlohy při po clusteru služby OpenShift byly nasazeny.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
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
ms.openlocfilehash: 1fe44f6d18199fe1a37db566f8b30eeaa4fbfab2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="post-deployment-tasks"></a>Úlohy po nasazení

Po nasazení clusteru služby OpenShift, můžete nakonfigurovat další položky. Tento článek obsahuje následující:

- Jak nakonfigurovat jednotné přihlašování pomocí služby Azure Active Directory (Azure AD)
- Postup konfigurace analýzy protokolů pro monitorování OpenShift
- Postup konfigurace metrik a protokolování

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurovat jednotné přihlašování pomocí služby Azure Active Directory

Pokud chcete použít pro ověřování Azure Active Directory, musíte vytvořit registrační aplikace služby Azure AD. Tento proces zahrnuje dva kroky: vytváření registrace aplikací a konfigurace oprávnění.

### <a name="create-an-app-registration"></a>Vytvořit registraci aplikace

Tyto kroky používají rozhraní příkazového řádku Azure k vytvoření registrace aplikací a grafickým uživatelským rozhraním (portál) Chcete-li nastavit oprávnění. Chcete-li vytvořit registrace aplikace, je třeba pět následující informace:

- Zobrazovaný název: název registraci aplikace (například OCPAzureAD)
- Domovská stránka: OpenShift konzole adresy URL (například) https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Identifikátor URI: Adresa URL konzoly OpenShift (např. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Adresa URL odpovědi: Hlavní veřejnou adresu URL a název registraci aplikace (například) https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Heslo: Zabezpečené heslo (použijte silné heslo)

Následující příklad vytvoří registraci aplikací s použitím předchozích informací:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Pokud je příkaz úspěšný, získáte výstup JSON podobně jako:

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
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Poznamenejte si vlastnost appId vrácená z příkazu pro později.

Na webu Azure Portal:

1.  Vyberte **Azure Active Directory** > **registrace aplikací**.
2.  Vyhledávání pro registraci vaší aplikace (například OCPAzureAD).
3.  Ve výsledcích klikněte na možnost registrace aplikací.
4.  V části **nastavení**, vyberte **požadovaná oprávnění**.
5.  V části **požadovaných oprávnění**, vyberte **přidat**.

  ![Registrace aplikací](media/openshift-post-deployment/app-registration.png)

6.  Klikněte na krok 1: Vyberte rozhraní API a pak klikněte na tlačítko **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klikněte na tlačítko **vyberte** v dolní části.

  ![Registrace aplikací vybrané rozhraní API](media/openshift-post-deployment/app-registration-select-api.png)

7.  Na krok 2: Vyberte oprávnění, vyberte **přihlášení a čtení profilu uživatele** pod **delegovaná oprávnění**a potom klikněte na **vyberte**.

  ![Registrace aplikace Access](media/openshift-post-deployment/app-registration-access.png)

8.  Vyberte **provádí**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurace OpenShift pro ověřování Azure AD

Ke konfiguraci OpenShift používat Azure AD jako zprostředkovatele ověřování, je nutné upravit soubor /etc/origin/master/master-config.yaml na všech uzlech, hlavní.

Najít ID klienta pomocí rozhraní příkazového řádku následující příkaz:

```azurecli
az account show
```

V souboru yaml najdete následující řádky:

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

Vložte následující řádky ihned po předchozí řádky:

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

Najít ID klienta pomocí rozhraní příkazového řádku následující příkaz: ```az account show```

Restartujte služby hlavní OpenShift na všech uzlech, hlavní:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift kontejneru platformy (OCP) s více hlavních serverů**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Platforma kontejneru OpenShift jeden hlavní server**

```bash
sudo systemctl restart atomic-openshift-master
```

V konzole OpenShift uvidíte teď dvě možnosti pro ověřování: htpasswd_auth a [registrace aplikací].

## <a name="monitor-openshift-with-log-analytics"></a>Monitorování OpenShift s analýzy protokolů

Pokud chcete monitorovat OpenShift s analýzy protokolů, můžete použít jednu ze dvou možností: Instalace agenta OMS na hostiteli virtuálního počítače nebo kontejner OMS. Tento článek obsahuje pokyny pro nasazení kontejneru OMS.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Vytvoření projektu OpenShift pro analýzy protokolů a nastavení přístupu uživatele

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Vytvořte soubor démon sadu yaml

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

## <a name="create-a-secret-yaml-file"></a>Vytvořte soubor tajný yaml

K vytvoření souboru tajný yaml, budete potřebovat dva kusy informací: ID pracovního prostoru analýzy protokolů a protokolu sdílený klíč pracovního prostoru analýzy. 

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

Nahraďte wsid_data s Base64, pomocí kódování ID pracovní prostor Log Analytics. Potom můžete nahraďte key_data s kódováním base64, pomocí protokolu Analytics prostoru sdílený klíč.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Vytvořit sadu tajný klíč a démon

Nasazení tajný souboru:

```bash
oc create -f ocp-secret.yml
```

Nasazení sady démon agenta OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Konfigurace protokolování a metriky

Šablony Azure Resource Manageru pro platformu kontejneru OpenShift poskytuje vstupní parametry pro povolení metrik a protokolování. Nabídku Marketplace platformy OpenShift kontejneru a šablony Resource Manageru OpenShift počátek nepodporují.

Pokud jste použili šablony Resource Manageru OCP a metriky a protokolování nebyly povoleny v průběhu instalace nebo pokud jste použili nabídku OCP Marketplace, můžete být snadno povolte tyto ve skutečnosti. Pokud používáte šablony Resource Manageru OpenShift původu, některé předběžné pracovní není potřeba.

### <a name="openshift-origin-template-pre-work"></a>Předběžné pracovní šablony OpenShift původu

1. SSH na první hlavní uzel pomocí portu 2200.

   Příklad:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Upravte soubor /etc/ansible/hosts a přidejte následující řádky po části zprostředkovatele Identity (# Povolit HTPasswdPasswordIdentityProvider):

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

### <a name="azure-cloud-provider-in-use"></a>Zprostředkovatel Azure Cloud používá

První hlavní uzel (původ) nebo bastionu uzlu (OCP), SSH pomocí přihlašovacích údajů během nasazení zadána. Vydejte následující příkaz:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Zprostředkovatel Azure Cloud není používán

První hlavní uzel (původ) nebo bastionu uzlu (OCP), SSH pomocí přihlašovacích údajů během nasazení zadána. Vydejte následující příkaz:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Další postup

- [Začínáme s platformou OpenShift kontejneru](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Začínáme s OpenShiftem Origin](https://docs.openshift.org/latest/getting_started/index.html)
