---
title: Kurz – Vytvoření vývojového kanálu v Azure pomocí Jenkinsu | Microsoft Docs
description: Kurz – V tomto kurzu se naučíte vytvořit v Azure virtuální počítač Jenkinsu, který při každém potvrzení kódu z GitHubu převezme data a sestaví nový kontejner Dockeru pro spuštění vaší aplikace.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 694448f6e3c788c0c9d336e75d5df579b90137df
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189863"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Kurz: Vytváření vývojové infrastruktury na virtuální počítač s Linuxem v Azure pomocí Jenkinse, Githubu a Dockeru

K automatizaci fázi sestavení a testování v rámci vývoje aplikace můžete použít kanál průběžné integrace a nasazení (CI/CD). V tomto kurzu vytvoříte kanál CI/CD na virtuálním počítači Azure a také se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Nainstalovat a konfigurovat Jenkinse
> * Vytvořit integraci webhooku mezi GitHubem a Jenkinsem
> * Vytvořit a aktivovat úlohy sestavení Jenkinse na základě potvrzení GitHubu
> * Vytvořit pro svou aplikaci image Dockeru
> * Ověřit, že po potvrzení GitHubu se sestaví nová image Dockeru a aktualizuje se spuštěná aplikace

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Vytvoření instance Jenkinse
V předchozím kurzu týkajícím se [postupu přizpůsobení virtuálního počítače s Linuxem při prvním spuštění](tutorial-automate-vm-deployment.md), jste se dozvěděli, jak automatizovat přizpůsobení virtuálního počítače s prostředím cloud-init. Tento kurz používá soubor cloud-init k instalaci Jenkinse a Dockeru na virtuální počítač. Jenkins je oblíbený open source automatizační server, který se bez problémů integruje s Azure a umožňuje průběžnou integraci (CI) a průběžné doručování (CD). Další kurzy týkající se používání Jenkinse najdete v článku [Jenkins v centru Azure](https://docs.microsoft.com/azure/jenkins/).

V aktuálním shellu vytvořte soubor *cloud-init-jenkins.txt* a vložte do něj následující konfiguraci. Soubor vytvořte například v Cloud Shellu, pokud nepracujete na místním počítači. Zadáním příkazu `sensible-editor cloud-init-jenkins.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install default-jre -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Než budete moct vytvořit virtuální počítač, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroupJenkins* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Teď pomocí příkazu [az vm create](/cli/azure/vm) vytvořte virtuální počítač. Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Zadejte úplnou cestu k souboru *cloud-init-jenkins.txt*, pokud jste ho uložili mimo aktuální pracovní adresář.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Vytvoření a konfigurace virtuálního počítače trvá několik minut.

Pokud chcete ve virtuálním počítači povolit webový provoz, pomocí příkazu [az vm open-port](/cli/azure/vm#az_vm_open_port) otevřete port *8080* pro provoz Jenkinse a port *1337* pro aplikaci Node.js používanou ke spouštění ukázkové aplikace:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Konfigurace Jenkinse
Pokud chcete získat přístup ke své instanci Jenkinse, zjistěte veřejnou IP adresu svého virtuálního počítače:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Z bezpečnostních důvodů musíte zadat počáteční heslo správce, které je uložené v textovém souboru na vašem virtuálním počítači, aby se instalace Jenkinse spustila. Veřejnou IP adresu získanou v předchozím kroku použijte pro připojení SSH k virtuálnímu počítači:

```bash
ssh azureuser@<publicIps>
```

Zobrazte soubor `initialAdminPassword` pro příslušnou instalaci Jenkinse a zkopírujte ho:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Pokud soubor ještě není k dispozici, počkejte několik dalších minut, než prostředí cloud-init dokončí instalaci Jenkinse a Dockeru.

Pak otevřete webový prohlížeč a přejděte na adresu `http://<publicIps>:8080`. Dokončete počáteční nastavení Jenkinse následujícím způsobem:

- Zvolte **Select plugins to install** (Vybrat moduly plug-in k instalaci).
- V textovém poli nahoře vyhledejte *GitHub*. Zaškrtněte políčko u položky *GitHub* a pak vyberte **Install** (Nainstalovat).
- Vytvořte prvního uživatele s rolí správce. Zadejte uživatelské jméno, například **admin**, a pak zadejte vlastní bezpečné heslo. Nakonec zadejte jméno a příjmení a e-mailovou adresu.
- Vyberte **Save and Finish** (Uložit a dokončit).
- Jakmile bude Jenkins připravený, vyberte **Start using Jenkins** (Začít používat Jenkinse).
  - Pokud se po začátku používání Jenkinse ve webovém prohlížeči zobrazí prázdná stránka, restartujte službu Jenkins. V relaci SSH zadejte `sudo service jenkins restart` a aktualizujte webový prohlížeč.
- Přihlaste se k Jenkinsu s použitím uživatelského jména a hesla, které jste vytvořili.


## <a name="create-github-webhook"></a>Vytvoření webhooku GitHubu
Pokud chcete nakonfigurovat integraci s GitHubem, otevřete [ukázkovou aplikaci Hello World Node.js](https://github.com/Azure-Samples/nodejs-docs-hello-world) z úložiště ukázek Azure. Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, vyberte tlačítko **Fork** (Vytvořit fork) v pravém horním rohu.

Ve forku, který jste vytvořili, vytvořte webhook:

- Vyberte **Settings** (Nastavení) a pak vyberte **Integrations & services** (Integrace a služby) na levé straně.
- Zvolte **Add service** (Přidat službu) a pak do pole filtru zadejte *Jenkins*.
- Vyberte *Jenkins (GitHub plugin)* (Jenkins – modul plug-in GitHubu).
- Do pole **Jenkins hook URL** (Adresa URL hooku Jenkinse) zadejte adresu `http://<publicIps>:8080/github-webhook/`. Nezapomeňte zadat i koncový znak /.
- Vyberte **Add service** (Přidat službu).

![Přidání webhooku GitHubu do vašeho rozvětveného úložiště](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Vytvoření úlohy Jenkinse
Pokud chcete, aby Jenkins reagoval na událost v GitHubu, například na potvrzení kódu, vytvořte úlohu Jenkinse. Použijte adresy URL vlastního forku GitHubu.

Na domovské stránce na webu Jenkinse vyberte **Create new jobs** (Vytvořit nové úlohy):

- Jako název úlohy zadejte *HelloWorld*. Zvolte **Freestyle project** (Volný projekt) a pak vyberte **OK**.
- V části **General** (Obecné) vyberte **GitHub project** (Projekt GitHub) a zadejte URL rozvětveného úložiště, například *https://github.com/cynthn/nodejs-docs-hello-world*.
- V části **Source code management** (Správa zdrojového kódu) vyberte **Git** a zadejte adresu URL rozvětveného úložiště *.git*, například *https://github.com/cynthn/nodejs-docs-hello-world.git*.
- V části **Build Triggers** (Triggery sestavení) vyberte **GitHub hook trigger for GITscm polling** (Trigger webhooku GitHubu pro dotazování GITscm).
- V části **Build** (Sestavení) zvolte **Add build step** (Přidat krok sestavení). Vyberte **Execute shell** (Spustit shell) a pak v příkazovém okně zadejte `echo "Test"`.
- V dolní části okna úloh vyberte **Save** (Uložit).


## <a name="test-github-integration"></a>Testování integrace GitHubu
Pokud chcete testovat integraci GitHubu s Jenkinsem, potvrďte změnu ve forku. 

Po návratu do webového uživatelského rozhraní GitHubu vyberte příslušné rozvětvené úložiště a pak vyberte soubor **index.js**. Vyberte ikonu tužky a upravte tento soubor tak, aby řádek 6 vypadal takto:

```nodejs
response.end("Hello World!");
```

Potvrďte změny výběrem tlačítka **Commit changes** (Potvrdit změny).

V Jenkinsovi se spustí nové sestavení v části **Build history** (Historie sestavení) v levém dolním rohu stránky úlohy. Zvolte odkaz na číslo sestavení a vyberte **Console output** (Výstup na konzole) na levé straně. Můžete zobrazit kroky, které Jenkins provádí při přebírání kódu z GitHubu a vytváření výstupu akce sestavení v podobě zprávy `Testing` na konzole. Po každém potvrzení v Githubu webhook kontaktuje Jenkinse a aktivuje tímto způsobem nové sestavení.


## <a name="define-docker-build-image"></a>Definování image sestavení Dockeru
Pokud chcete zobrazit aplikaci Node.js spouštěnou na základě potvrzení GitHubu, vytvořte image Dockeru pro spouštění aplikace. Image se sestavuje ze souboru Dockerfile, který definuje způsob konfigurace kontejneru spouštějícího aplikaci. 

V rámci připojení SSH k virtuálnímu počítači přejděte do adresáře pracovního prostoru Jenkinse s názvem podle úlohy, kterou jste vytvořili v předchozím kroku. V tomto příkladu byl použit název *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Pomocí příkazu `sudo sensible-editor Dockerfile` vytvořte v tomto adresáři pracovního prostoru soubor a vložte do něj následující obsah. Ujistěte se, že se celý soubor Dockerfile zkopíroval správně, zejména první řádek:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Tento soubor Dockerfile použije základní image Node.js v Alpine Linuxu, zpřístupní port 1337, na kterém běží aplikace Hello World, a pak zkopíruje soubory aplikace a inicializuje ji.


## <a name="create-jenkins-build-rules"></a>Vytváření pravidel sestavení Jenkinse
V předchozím kroku jste vytvořili základní pravidlo sestavení Jenkinse, jehož výstupem je zpráva na konzole. Teď vytvoříme krok sestavení, který použije příslušný soubor Dockerfile a spustí aplikaci.

Vraťte se do instance Jenkinse a vyberte úlohu, kterou jste vytvořili v předchozím kroku. Vyberte **Configure** (Konfigurovat) na levé straně a přejděte dolů k části **Build** (Sestavení):

- Odeberte stávající krok sestavení `echo "Test"`. Vyberte červený křížek v pravém horním rohu pole stávajícího kroku sestavení.
- Zvolte **Add build step** (Přidat krok sestavení) a pak vyberte **Execute shell** (Spustit shell).
- Do pole **Command** (Příkaz) zadejte následující příkazy Dockeru a pak vyberte **Save** (Uložit):

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Kroky sestavení Dockeru vytvoří image a označí ji číslem sestavení Jenkinse, aby bylo možné udržovat historii imagí. Všechny existující kontejnery se spuštěnou aplikací se zastaví a odeberou. Pak se pomocí image spustí nový kontejner, který spouští aplikaci Node.js podle nejnovějších potvrzení v GitHubu.


## <a name="test-your-pipeline"></a>Test kanálu
Pokud chcete zobrazit celý kanál v akci, znovu upravte soubor *index.js* ve svém rozvětveném úložišti GitHubu a vyberte **Commit change** (Potvrdit změnu). V Jenkinsovi se spustí nová úloha podle webhooku pro GitHub. Vytvoření image Dockeru a spuštění aplikace v novém kontejneru trvá několik sekund.

V případě potřeby znovu zjistěte veřejnou IP adresu svého virtuálního počítače:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Otevřete webový prohlížeč a zadejte adresu `http://<publicIps>:1337`. Zobrazí se vaše aplikace Node.js a bude odpovídat nejnovějším potvrzením ve vašem forku GitHubu:

![Spuštěná aplikace Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Teď v GitHubu proveďte další úpravu souboru *index.js* a potvrďte změnu. Počkejte několik sekund na dokončení úlohy v Jenkinsovi a pak aktualizujte obsah webového prohlížeče, aby se zobrazila aktualizovaná verze aplikace spuštěné v novém kontejneru:

![Spuštěná aplikace Node.js po dalším potvrzení GitHubu](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali GitHub tak, aby se po každém potvrzení uzlu spustila úloha sestavení Jenkinse, a pak jste nasadili kontejner Dockeru, aby se aplikace otestovala. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Nainstalovat a konfigurovat Jenkinse
> * Vytvořit integraci webhooku mezi GitHubem a Jenkinsem
> * Vytvořit a aktivovat úlohy sestavení Jenkinse na základě potvrzení GitHubu
> * Vytvořit pro svou aplikaci image Dockeru
> * Ověřit, že po potvrzení GitHubu se sestaví nová image Dockeru a aktualizuje se spuštěná aplikace

V dalším kurzu se dozvíte víc o tom, jak integrovat Jenkinse s Azure DevOps Services.

> [!div class="nextstepaction"]
> [Nasazování aplikací s Jenkinsem a Azure DevOps Services](tutorial-build-deploy-jenkins.md)
