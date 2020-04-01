---
title: Kurz – konfigurace rolí řízení přístupu na základě rolí (RBAC) ve službě Azure Kubernetes Service (AKS) pomocí ansible
description: Přečtěte si, jak pomocí ansible nakonfigurovat RBAC v clusteru Služby Azure Kubernetes (AKS).
keywords: ansible, azurové, devops, bash, cloudshell, playbook, aks, kontejner, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836962"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace rolí řízení přístupu na základě rolí (RBAC) ve službě Azure Kubernetes Service (AKS) pomocí ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS lze nakonfigurovat tak, aby používal [azure active directory (AD)](/azure/active-directory/) pro ověřování uživatelů. Po konfiguraci použijete ověřovací token Azure AD k přihlášení do clusteru AKS. RBAC může být založen na identitě uživatele nebo členství ve skupině adresářů.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru AKS s podporou Azure AD
> * Konfigurace role RBAC v clusteru

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalace knihovny RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurace azure ad pro ověřování AKS

Při konfiguraci Azure AD pro ověřování AKS jsou nakonfigurované dvě aplikace Azure AD. Tuto operaci musí dokončit správce klienta Azure. Další informace najdete [v tématu Integrace služby Azure Active Directory s AKS](/azure/aks/aad-integration#create-the-server-application). 

Od správce klienta Azure získáte následující hodnoty:

- Tajný klíč serverové aplikace
- ID serverové aplikace
- ID klientské aplikace 
- ID tenanta

Tyto hodnoty jsou potřebné ke spuštění ukázkového playbooku.  

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V této části vytvoříte AKS s [aplikací Azure AD](#configure-azure-ad-for-aks-authentication).

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- Playbook `ssh_key` načte `~/.ssh/id_rsa.pub`z . Pokud jej upravíte, použijte jednořádkový formát - počínaje "ssh-rsa" (bez uvozovek).
- Hodnoty `client_id` `client_secret` a jsou `~/.azure/credentials`načteny z , což je výchozí soubor pověření. Tyto hodnoty můžete nastavit na instanční objekt nebo načíst tyto hodnoty z proměnných prostředí:

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

## <a name="get-the-azure-ad-object-id"></a>Získání ID objektu Azure AD

Chcete-li vytvořit vazbu RBAC, musíte nejprve získat ID objektu Azure AD. 

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Do vyhledávacího pole v horní části `Azure Active Directory`stránky zadejte . 

1. Klikněte na `Enter` (Další).

1. V nabídce **Manage** vyberte **Položku Uživatelé**.

1. V poli název vyhledejte svůj účet.

1. Ve sloupci **Název** vyberte odkaz na svůj účet.

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

Nahraďte `<your-aad-account>` zástupný symbol [id objektu klienta](#get-the-azure-ad-object-id)Azure AD .

Uložte následující playbook - který nasazuje vaši `aks-kube-deploy.yml`novou roli do AKS - jako :

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Spuštění ukázkového playbooku

V této části je uveden úplný ukázkový playbook, který volá úkoly, které se vytvářejí v tomto článku. 

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

V `vars` části nahraďte následující zástupné symboly informacemi o Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Spusťte kompletní playbook pomocí příkazu: `ansible-playbook`

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Ověření výsledků

V této části použijete kubectl seznam uzlů vytváření v tomto článku.

Na terminálovém řádku zadejte následující příkaz:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Příkaz vás přesměruje na stránku ověřování. Přihlaste se pomocí svého účtu Azure.

Po ověření udá uzly podobným způsobem jako následující výsledky:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující `cleanup.yml`kód jako :

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)
