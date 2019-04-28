---
title: Kurz – konfigurace mezipaměti pro Redis pomocí Ansible v Azure Cache | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytváření, škálování a po restartování počítače mezipaměti Azure Redis
keywords: ansible v azure, devops, bash, playbooku, mezipaměť, redis
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 453a54bd8d81b541cdc42cb09163be9e0b6b6ac2
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767177"
---
# <a name="tutorial-configure-caches-in-azure-cache-for-redis-using-ansible"></a>Kurz: Konfigurace mezipaměti v Azure Cache pro Redis pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Mezipaměť Azure pro Redis](/azure/azure-cache-for-redis/) je open source kompatibilní služba, která vám umožní vytvářet přizpůsobivých aplikací s rychlým přístupem k datům. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření mezipaměti
> * Škálování mezipaměti
> * Restartování mezipaměti
> * Přidání pravidla brány firewall k mezipaměti
> * Odstranění mezipaměti

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-cache"></a>Vytvoření mezipaměti

Vytvoření Azure Cache pro Redis v rámci nové skupiny prostředků.

```yml
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1 
```

Může trvat několik minut, než se zřízení mezipaměti. Následující kód říká Ansible počkejte na dokončení operace:

```yml
  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Během procesu zřizování zdlouhavé se zobrazí několik zpráv "Chyba". Tyto zprávy můžete ignorovat. Je důležité zpráva poslední zprávou. V následujícím příkladu existují mnoho chybových zpráv až do konečného zprávu ("ok").

```Output
FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]
```

## <a name="scale-the-cache"></a>Škálování mezipaměti

Mezipaměti Redis Azure má různé nabídky v závislosti na potřebách vaší aplikace. Tyto možnosti mezipaměti poskytují flexibilitu při výběru velikosti a funkcí mezipaměti. Pokud vaše aplikace požadavky se změní po vytvoření mezipaměti, můžete škálovat do mezipaměti podle potřeby. Další informace o škálování najdete v tématu [postup škálování Azure mezipaměti Redis](/azure/azure-cache-for-redis/cache-how-to-scale).

Následující ukázkový kód škáluje mezipaměti **standardní**:

```yml
- name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1
```

Může trvat několik minut na škálování mezipaměti. Následující kód říká Ansible počkejte na dokončení operace:

```yml
  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Podobně jako na zřízení mezipaměti Azure pro Redis úlohy, výstup podobný následující zpráva je normální:

```Ouput
**FAILED - RETRYING: Get facts (100 retries left)** is normal.
```

## <a name="reboot-the-cache"></a>Restartování mezipaměti

Následující kód restartování mezipaměti vytvořený v předchozích částech.

```yml
  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all
```

### <a name="add-firewall-rule"></a>Přidání pravidla brány firewall

Následující kód přidává do mezipaměti, pravidlo brány firewall:

```yml
  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4
```

## <a name="delete-the-cache"></a>Odstranění mezipaměti

Následující kód odstraní mezipaměti:

```yml
  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Získat ukázky playbook

Existují dva způsoby, jak získat playbook úplnou ukázku:
- [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/rediscache.yml) a uložit ho. tím `rediscache.yml`.
- Vytvořte nový soubor s názvem `rediscache.yml` a zkopírujte do něj následující obsah:

```yml
- name: Manage Azure Cache for Redis
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    redis_name: "redis{{ resource_group_name }}"
    location: eastus2

  roles:
    - azure.azure_preview_modules

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1

  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1

  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all

  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4

  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

V této části se spusťte playbook pro testování různých funkcí uvedených v tomto článku.

V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook rediscache.yml
```

Výstup vypadá podobně jako následující výsledky:

```Output
TASK [create resource group] 
Tuesday 12 March 2019  16:21:07 +0800 (0:00:00.054)       0:00:01.503 
ok: [localhost]

TASK [Create Azure Cache for Redis] 
Tuesday 12 March 2019  16:21:09 +0800 (0:00:01.950)       0:00:03.454 
 [WARNING]: Azure API profile latest does not define an entry for RedisManagementClient

changed: [localhost]

TASK [Dump host name] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:40.125)       0:00:43.580 
ok: [localhost] =>
  output['host_name']: redis0312.redis.cache.windows.net

TASK [Get facts] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:00.056)       0:00:43.636 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
FAILED - RETRYING: Get facts (90 retries left).
ok: [localhost]

TASK [Scale up Azure Cache for Redis] 
Tuesday 12 March 2019  16:33:20 +0800 (0:11:31.296)       0:12:14.933 
changed: [localhost]

TASK [Get facts] 
Tuesday 12 March 2019  16:33:29 +0800 (0:00:09.164)       0:12:24.097 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]

TASK [Reboot Azure Cache for Redis] 
Tuesday 12 March 2019  16:43:57 +0800 (0:10:27.740)       0:22:51.838 
ok: [localhost]

TASK [Add Firewall rule] 
Tuesday 12 March 2019  16:44:02 +0800 (0:00:05.432)       0:22:57.271 
changed: [localhost]

TASK [Delete Azure Cache for Redis] 
Tuesday 12 March 2019  16:44:08 +0800 (0:00:05.137)       0:23:02.409 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=10   changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

Tuesday 12 March 2019  16:44:14 +0800 (0:00:06.217)       0:23:08.626 

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)