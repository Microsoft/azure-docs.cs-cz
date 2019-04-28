---
title: Kurz – konfigurace role řízení přístupu na základě role ve službě Azure Kubernetes Service (AKS) pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible v clusteru Azure Kubernetes Service(AKS) konfigurace RBAC
keywords: ansible, azure, devops, bash, cloud shell, playbooku, aks, kontejner, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdf3d719627673bfb2c6b25957abb849c46d4e9e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767261"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Kurz: Konfigurace role řízení přístupu na základě role ve službě Azure Kubernetes Service (AKS) pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS může být nakonfigurován pro použití [Azure Active Directory (AD)](/azure/active-directory/) k ověřování uživatelů. Po nakonfigurování používáte k přihlášení do clusteru AKS tokenu ověřování Azure AD. RBAC může být založen na identitu uživatele nebo členství ve skupině adresáře.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření clusteru služby Azure AD povolené AKS
> * Konfigurovat roli RBAC v clusteru

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Nainstalujte knihovnu Red Hat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurace služby Azure AD pro ověřování AKS

Při konfiguraci Azure AD pro ověřování AKS, jsou nakonfigurovány dvě aplikace Azure AD. Tuto operaci musíte dokončit microsoftem nebo správcem tenanta Azure. Další informace najdete v tématu [integrace Azure Active Directory s AKS](/azure/aks/aad-integration#create-server-application). 

Správce tenanta Azure získáte následující hodnoty:

- Tajný kód aplikace serveru
- ID aplikace serveru
- ID klientské aplikace 
- ID tenanta

Tyto hodnoty jsou potřeba ke spuštění playbooku vzorku.  

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V této části vytvoříte s AKS [aplikaci Azure AD](#configure-azure-ad-for-aks-authentication).

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Načte playbook `ssh_key` z `~/.ssh/id_rsa.pub`. Když ji upravíte, použijte jednořádkovém formátu – začíná na "ssh-rsa" (bez uvozovek).
- `client_id` a `client_secret` načtení hodnot z `~/.azure/credentials`, což je výchozí soubor přihlašovacích údajů. Můžete nastavit tyto hodnoty do vaší služby instančního objektu nebo načíst tyto hodnoty z proměnné prostředí:

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
  azure_rm_aks_version:
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

Pokud chcete vytvořit vazbu RBAC, musíte nejprve získat ID objektu Azure AD. 

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Do vyhledávacího pole v horní části stránky zadejte `Azure Active Directory`. 

1. Klikněte na `Enter` (Další).

1. V **spravovat** nabídce vyberte možnost **uživatelé**.

1. V poli Název vyhledejte svůj účet.

1. V **název** sloupce, vyberte odkaz k vašemu účtu.

1. V **Identity** tématu, zkopírujte **ID objektu**.

    ![Zkopírujte identifikátor objektu Azure AD](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Vytvoření vazby RBAC

V této části vytvoříte vazbu role nebo vazby rolí clusteru ve službě AKS. 

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

Nahradit `&lt;your-aad-account>` zástupný symbol v tenantu Azure AD [ID objektu](#get-the-azure-ad-object-id).

Uložte následující playbook –, který se nasazuje nová role do AKS – jako `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

Tato část obsahuje seznam playbook úplnou ukázku, která volá úkolů a vytvoření v tomto článku. 

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

V `vars` části, následující zástupné symboly s informacemi o službě Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Spuštění pomocí kompletní playbook `ansible-playbook` příkaz:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Zkontrolujte výsledky

V této části použijete kubectl seznam uzlů vytvoření v tomto článku.

Zadejte následující příkaz, v terminálu řádku:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Příkaz vás přesměrují na ověřovací stránku. Přihlaste se pomocí svého účtu Azure.

Po ověření kubectl seznam uzlů v podobně jako ochrana čipem následující výsledky:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ansible v Azure](/azure/ansible/)