---
title: Kurz přizpůsobení virtuálního počítače s Linuxem pomocí nástroje cloud-init v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak používat cloud-init a Key Vault k přizpůsobení virtuálních počítačů s Linuxem při jejich prvním spuštění v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fa1e95263559906ebfd0df82b2756043e38852a6
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305153"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Kurz: Jak používat cloud-init k přizpůsobení virtuálního počítače s Linuxem v Azure při prvním spuštění počítače

V předchozím kurzu jste se dozvěděli, jak se přes SSH připojit k virtuálnímu počítači a ručně nainstalovat server NGINX. Pokud chcete vytvářet virtuální počítače rychle a konzistentně, zřejmě uvítáte nějakou formu automatizace. Běžný postup k přizpůsobení virtuálního počítače při prvním spuštění je použití nástroje [cloud-init](https://cloudinit.readthedocs.io). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření konfiguračního souboru cloud-init
> * Vytvoření virtuálního počítače, který používá soubor cloud-init
> * Zobrazení spuštěné aplikace Node.js po vytvoření virtuálního počítače
> * Bezpečné uložení certifikátů ve službě Key Vault
> * Automatizace zabezpečeného nasazení serveru NGINX s nástrojem cloud-init

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Přehled Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

S našimi partnery spolupracujeme na začlenění nástroje cloud-init, aby fungoval v imagích, které pro Azure poskytují. Následující tabulka popisuje aktuální dostupnost cloudu-init pro image platformy Azure:

| Alias | Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |nejnovější |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |nejnovější |
| CoreOS |CoreOS |CoreOS |Stable |nejnovější |
| | OpenLogic | CentOS | 7-CI | nejnovější |
| | RedHat | RHEL | 7-RAW-CI | nejnovější


## <a name="create-cloud-init-config-file"></a>Vytvoření konfiguračního souboru cloud-init
Pokud chcete cloud-init vidět v praxi, vytvořte virtuální počítač, který nainstaluje server NGINX a spustí jednoduchou aplikaci v Node.js s názvem Hello World. Následující konfigurace cloud-init nainstaluje požadované balíčky, vytvoří aplikaci Node.js a poté ji inicializuje a spustí.

V aktuálním prostředí vytvořte soubor *cloud-init.txt* a vložte do něj následující konfiguraci. Soubor vytvořte například v Cloud Shellu, pokud nepracujete na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Další informace o možnostech konfigurace nástroje cloud-init najdete v [příkladech konfigurace cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Než budete moct vytvořit virtuální počítač, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). V následujícím příkladu se v umístění *eastus* vytvoří skupina prostředků *myResourceGroupAutomate*:

```azurecli-interactive 
az group create --name myResourceGroupAutomate --location eastus
```

Teď pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) vytvořte virtuální počítač. Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. V následujícím příkladu se vytvoří virtuální počítač *myAutomatedVM*:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Vytvoření virtuálního počítače, instalace balíčků a spuštění aplikace trvá několik minut. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Než k aplikaci budete mít přístup, může to ještě několik minut trvat. Po vytvoření virtuálního počítače si poznamenejte hodnotu `publicIpAddress` zobrazenou v Azure CLI. Tato adresa slouží k přístupu k aplikaci v Node.js přes webový prohlížeč.

Pokud chcete umožnit přístup k virtuálnímu počítači webovému provozu, otevřete port 80 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Otestování webové aplikace
Teď můžete otevřít webový prohlížeč a do adresního řádku zadat *http://<publicIpAddress>*. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Zobrazí se aplikace Node.js, jak je vidět v následujícím příkladu:

![Zobrazení spuštěného webu NGINX](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Vložení certifikátů ze služby Key Vault
V této volitelné části se dozvíte, jak můžete bezpečně uložit certifikáty ve službě Azure Key Vault a během nasazení virtuálního počítače je do něj vložit. Na rozdíl od použití vlastní image zahrnující integrované certifikáty zajišťuje tento proces, že se do virtuálního počítače při prvním spuštění vloží nejaktuálnější certifikáty. Během celého procesu certifikát neopustí platformu Azure ani se nezveřejní v žádném skriptu, historii příkazového řádku ani šabloně.

Azure Key Vault chrání kryptografické klíče a tajné kódy, jako jsou certifikáty a hesla. Key Vault pomáhá zjednodušit proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Tento scénář využívá některé klíčové prvky služby Key Vault k vytvoření a používání certifikátu. Není to ale vyčerpávající příručka, jak Key Vault používat.

Následující postupy vám ukážou:

- Vytvoření služby Azure Key Vault
- Generování nebo nahrání certifikátu do služby Key Vault
- Vytvoření tajného klíče z certifikátu k vložení do virtuálního počítače
- Vytvoření virtuálního počítače a vložení certifikátu

### <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault
Nejdřív pomocí příkazu [az keyvault create](/cli/azure/keyvault#az_keyvault_create) vytvořte službu Key Vault a povolte její použití při nasazování virtuálního počítače. Každá služba Key Vault vyžaduje jedinečný název, který by měl být malými písmeny. Řetězec *mykeyvault* v následujícím příkladu nahraďte vlastním jedinečným názvem služby Key Vault:

```azurecli-interactive 
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Vygenerování certifikátu a jeho uložení do služby Key Vault
V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem pomocí příkazu [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Pro účely tohoto kurzu následující příklad ukazuje, jak můžete pomocí příkazu [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) vygenerovat certifikát podepsaný svým držitelem, který využívá výchozí zásady certifikátu:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Příprava certifikátu k použití na virtuálním počítači
Pokud chcete certifikát použít během procesu vytváření virtuálního počítače, získejte ID vašeho certifikátu pomocí příkazu [az keyvault secret list-versions](/cli/azure/keyvault/secret#az_keyvault_secret_list_versions). Certifikát je do virtuálního počítače nutné při spuštění vložit v určitém formátu, proto jej převeďte pomocí příkazu [az vm format-secret](/cli/azure/vm#az_vm_format_secret). Z důvodu snadnějšího použití v dalších krocích přiřadí následující příklad výstup těchto příkazů do proměnných:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Vytvoření konfigurace cloud-init pro zabezpečení serveru NGINX
Při vytváření virtuálního počítače se certifikáty a klíče uloží do chráněného adresáře */var/lib/waagent/*. K automatizaci přidání certifikátu do virtuálního počítače a konfigurace serveru NGINX můžete použít aktualizovanou konfiguraci cloud-init z předchozího příkladu.

Vytvořte soubor *cloud-init-secured.txt* a vložte do něj následující konfiguraci: Opět platí, že pokud používáte prostředí Cloud Shell, je třeba vytvořit konfigurační soubor cloud-init v něm, ne na místním počítači. Příkazem `sensible-editor cloud-init-secured.txt` soubor vytvořte a zobrazte seznam dostupných editorů. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Vytvoření zabezpečeného virtuálního počítače
Teď pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) vytvořte virtuální počítač. Data certifikátu ze služby Key Vault se vloží pomocí parametru `--secrets`. Jako v předchozím příkladu můžete pomocí parametru `--custom-data` předat konfiguraci cloud-init:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Vytvoření virtuálního počítače, instalace balíčků a spuštění aplikace trvá několik minut. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Než k aplikaci budete mít přístup, může to ještě několik minut trvat. Po vytvoření virtuálního počítače si poznamenejte hodnotu `publicIpAddress` zobrazenou v Azure CLI. Tato adresa slouží k přístupu k aplikaci v Node.js přes webový prohlížeč.

Pokud chcete pro přístup k virtuálnímu počítači povolit zabezpečený webový provoz, otevřete port 443 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Testování zabezpečené webové aplikace
Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat *https://<publicIpAddress>*. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Pokud jste použili certifikát podepsaný svým držitelem, přijměte upozornění zabezpečení:

![Přijetí upozornění zabezpečení ve webovém prohlížeči](./media/tutorial-automate-vm-deployment/browser-warning.png)

Následně se zobrazí váš zabezpečený web NGINX a aplikace v Node.js, jak je vidět v následujícím příkladu:

![Zobrazení spuštěného zabezpečeného webu NGINX](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste nakonfigurovali virtuální počítače při prvním spuštění pomocí nástroje cloud-init. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření konfiguračního souboru cloud-init
> * Vytvoření virtuálního počítače, který používá soubor cloud-init
> * Zobrazení spuštěné aplikace Node.js po vytvoření virtuálního počítače
> * Bezpečné uložení certifikátů ve službě Key Vault
> * Automatizace zabezpečeného nasazení serveru NGINX s nástrojem cloud-init

V dalším kurzu se dozvíte, jak vytvořit vlastní image virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytváření vlastních imagí virtuálních počítačů](./tutorial-custom-images.md)
