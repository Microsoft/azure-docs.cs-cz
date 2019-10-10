---
title: Kurz – konfigurace rolí řízení přístupu na základě role (RBAC) ve službě Azure Kubernetes Service (AKS) s využitím Ansible
description: Naučte se používat Ansible ke konfiguraci RBAC v clusteru Azure Kubernetes Service (AKS).
keywords: Ansible, Azure, DevOps, bash, cloudshellu, PlayBook, AKS, Container, AKS, Kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 36a6f5ade7a60a989d2e80f2405aaa2d1d50b756
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242333"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace rolí řízení přístupu na základě role (RBAC) ve službě Azure Kubernetes Service (AKS) pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS je možné nakonfigurovat tak, aby pro ověřování uživatelů používala [Azure Active Directory (AD)](/azure/active-directory/) . Po nakonfigurování se pomocí ověřovacího tokenu Azure AD přihlásíte ke clusteru AKS. RBAC může být založen na identitě nebo členství uživatele ve skupině adresáře.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS s povolenou službou Azure AD
> * Konfigurace role RBAC v clusteru

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalace knihovny RedHat OpenShift** -  @ no__t-2

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurace Azure AD pro ověřování AKS

Při konfiguraci služby Azure AD pro ověřování AKS jsou nakonfigurované dvě aplikace Azure AD. Tuto operaci musí dokončit správce tenanta Azure. Další informace najdete v tématu věnovaném [integraci Azure Active Directory s AKS](/azure/aks/aad-integration#create-the-server-application). 

Od správce tenanta Azure Získejte následující hodnoty:

- Tajný kód serverové aplikace
- ID aplikace serveru
- ID klientské aplikace 
- ID tenanta

Tyto hodnoty jsou potřebné ke spuštění ukázkové PlayBook.  

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V této části vytvoříte AKS s [aplikací Azure AD](#configure-azure-ad-for-aks-authentication).

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- PlayBook načítá `ssh_key` z `~/.ssh/id_rsa.pub`. Pokud ho upravíte, použijte jednořádkový formát začínající na "SSH-RSA" (bez uvozovek).
- Hodnoty `client_id` a `client_secret` jsou načteny z `~/.azure/credentials`, což je výchozí soubor přihlašovacích údajů. Tyto hodnoty můžete nastavit na instanční objekt nebo načíst tyto hodnoty z proměnných prostředí:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Uložte následující ukázkový playbook jako `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Získat ID objektu Azure AD

Pokud chcete vytvořit vazbu RBAC, musíte nejdřív získat ID objektu Azure AD. 

1. Přihlaste se na web [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Do vyhledávacího pole v horní části stránky zadejte `Azure Active Directory`. 

1. Klikněte na `Enter` (Další).

1. V nabídce **Správa** vyberte **Uživatelé**.

1. Do pole Název vyhledejte svůj účet.

1. Ve sloupci **název** vyberte odkaz na svůj účet.

1. V části **Identita** zkopírujte **ID objektu**.

    ![Zkopírujte ID objektu Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Vytvořit vazbu RBAC

V této části vytvoříte vazbu role nebo vazbu role clusteru v AKS. 

Uložte následující ukázkový playbook jako `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Zástupné znaky `&lt;your-aad-account>` nahraďte vaším [ID objektu](#get-the-azure-ad-object-id)TENANTA Azure AD.

Uložte následující PlayBook – nasazení nové role do AKS-as `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Spuštění ukázkové PlayBook

V této části je uveden úplný vzorový PlayBook, který volá úkoly vytvořené v tomto článku. 

Uložte následující ukázkový playbook jako `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

V části `vars` nahraďte následující zástupné symboly informacemi o službě Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Spusťte úplnou PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Ověřit výsledky

V této části použijete kubectl k vypsání uzlů, které se vytvářejí v tomto článku.

Do příkazového řádku terminálu zadejte následující příkaz:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Příkaz vás přesměruje na ověřovací stránku. Přihlaste se pomocí svého účtu Azure.

Po ověření kubectl seznam uzlů podobným způsobem jako u následujících výsledků:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Následující kód uložte jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)
