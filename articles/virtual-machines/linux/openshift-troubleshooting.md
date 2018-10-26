---
title: Řešení potíží s nasazení OpenShift v Azure | Dokumentace Microsoftu
description: Řešení potíží s nasazení OpenShift v Azure.
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
ms.openlocfilehash: 6a4af0efb14d8ad45add906262ffd2121e8b78d0
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085789"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Řešení potíží s nasazení OpenShift v Azure

Pokud není správně nasadit OpenShift clusteru, na webu Azure portal bude poskytovat chybový výstup. Výstup může být obtížné číst, což ztěžuje identifikovat problém. Rychle prohledejte tento výstup pro kód ukončení 3, 4 nebo 5. Následující poskytuje informace o těchto tří ukončovací kód:

- Ukončovací kód 3: Red Hat předplatné uživatelské jméno / heslo nebo ID organizace / aktivační klíč je nesprávný.
- Ukončovací kód 4: vaše ID fondu Red Hat je nesprávný nebo nejsou k dispozici žádná oprávnění
- Ukončovací kód 5: nejde zřídit svazek fondu dynamického zajišťování Dockeru

Pro všechny ostatní kódy ukončení připojení k hostiteli, které prostřednictvím ssh Chcete-li zobrazit soubory protokolu.

**OpenShift Container Platform**

Připojte přes SSH k hostiteli playbook ansible. Pro šablony nebo nabídku Marketplace použijte bastion host. Z bastionu získat přístup přes SSH do všech dalších uzlů v clusteru (hlavní, infrastruktura, CNS, compute). Budete muset být kořenový, chcete-li zobrazit soubory protokolu. Kořenové zakázána pro přístup přes SSH ve výchozím nastavení, takže nepoužívejte kořenové SSH do dalších uzlů.

**OKD**

Připojte přes SSH k hostiteli playbook ansible. OKD šablony (verzi 3.9 a starší) použijte hostitele master-0. OKD šablony (verze 3.10 a novější) použijte hostitele bastionu. Z hostitele playbook ansible získat přístup přes SSH do všech dalších uzlů v clusteru (hlavní, infrastruktura, CNS, compute). Budete muset být kořenový (sudo su –) Chcete-li zobrazit soubory protokolu. Kořenové zakázána pro přístup přes SSH ve výchozím nastavení, takže nepoužívejte kořenové SSH do dalších uzlů.

## <a name="log-files"></a>Soubory protokolu

Soubory protokolu (stderr a stdout) pro skripty Příprava hostitele jsou umístěny v /var/lib/waagent/custom-script/download/0 na všech hostitelích. Pokud došlo k chybě při přípravě hostitele, zobrazte tyto soubory protokolu k určení chybu.

Pokud skripty přípravy proběhla úspěšně, souborů protokolů v adresáři /var/lib/waagent/custom-script/download/1 hostitele playbook ansible nutné prověřit. Pokud během vlastní instalace OpenShift došlo k chybě, soubor stdout zobrazí chyba. Tyto informace slouží k další pomoc požádejte podporu.

Příklad výstupu

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Většina běžných chyb při instalaci jsou:

1. Privátní klíč má přístupové heslo
2. Tajný kód trezoru klíčů se soukromým klíčem nebyl správně vytvořen.
3. Přihlašovací údaje instančního objektu služby byly zadány správně
4. Instanční objekt služby nemá přístup přispěvatele do skupiny prostředků

### <a name="private-key-has-a-passphrase"></a>Privátní klíč má přístupové heslo

Zobrazí se vám chyba, že oprávnění bylo odepřeno pro SSH. Připojte přes SSH k hostiteli playbook ansible vyhledat heslo privátního klíče.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Tajný kód trezoru klíčů se soukromým klíčem nebyl správně vytvořen.

Privátní klíč se vloží do hostitele playbook ansible - ~/.ssh/id_rsa. Potvrďte, že tento soubor je správná. Testování tak, že otevřete relaci SSH k jednomu z uzlů clusteru z hostitele playbook ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Přihlašovací údaje instančního objektu služby byly zadány správně

Při zadávání vstup do šablony nebo nabídky Marketplace, byla zadána nesprávná informace. Ujistěte se, že používáte správné ID aplikace (ID klienta) a heslo (clientSecret) k objektu služby. Ověřte, že pomocí následujícího příkazu rozhraní příkazového řádku azure.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Instanční objekt služby nemá přístup přispěvatele do skupiny prostředků

Pokud je povolen zprostředkovatel cloudu Azure, musí mít instanční objekt služby používá přístup přispěvatele do skupiny prostředků. Ověřte, že pomocí následujícího příkazu rozhraní příkazového řádku azure.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Další nástroje

Některé chyby můžete také následující příkazy zobrazíte další informace:

1. Stav systemctl <service>
2. journalctl -xe
