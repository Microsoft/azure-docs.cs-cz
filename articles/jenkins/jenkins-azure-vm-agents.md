---
title: Škálování nasazení Jenkinse pomocí agentů virtuálních počítačů Azure
description: Pomocí modulu plug-in Jenkins Azure VM Agents můžete do svých kanálů Jenkinse využívajících virtuální počítače Azure přidat dodatečnou kapacitu.
ms.service: jenkins
keywords: jenkins, azure, devops, virtual machine, agents
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 5cfece551f99a0925099b6ef936703e72f078985
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074661"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Škálování nasazení Jenkinse podle potřeby pomocí agentů virtuálních počítačů Azure

V tomto kurzu se dozvíte, jak pomocí [modulu plug-in Jenkins Azure VM Agents](https://plugins.jenkins.io/azure-vm-agents) přidat kapacitu na vyžádání s použitím virtuálních počítačů s Linuxem spuštěných v Azure.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Instalace modulu plug-in Azure VM Agents
> * Konfigurace modulu plug-in pro vytváření prostředků ve vašem předplatném Azure
> * Nastavení výpočetních prostředků dostupných pro jednotlivé agenty
> * Nastavení operačního systému a nástrojů nainstalovaných v jednotlivých agentech
> * Vytvoření nové volné úlohy Jenkinse
> * Spuštění úlohy v agentovi virtuálního počítače Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure
* Hlavní server Jenkins. Pokud žádný nemáte, projděte si [rychlý start](install-jenkins-solution-template.md) a nastavte ho v Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instalace modulu plug-in Azure VM Agents

> [!TIP]
> Pokud jste k nasazení Jenkinse v Azure použili [šablonu řešení](install-jenkins-solution-template.md), pak je modul plug-in Azure VM Agents již nainstalovaný.

1. Na řídicím panelu Jenkinse vyberte **Manage Jenkins** (Správa Jenkinse) a pak **Manage Plugins** (Správa modulů plug-in).
1. Vyberte kartu **Available** (K dispozici) a pak vyhledejte **Azure VM Agents**. Zaškrtněte políčko vedle položky modulu plug-in a v dolní části řídicího panelu vyberte **Install without restart** (Nainstalovat bez restartování).

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurace modulu plug-in Azure VM Agents

1. Na řídicím panelu Jenkinse vyberte **Manage Jenkins** (Správa Jenkinse) a pak **Configure System** (Konfigurace systému).
1. Přejděte do dolní části stránky, vyhledejte část **Cloud** s rozevíracím seznamem **Add new cloud** (Přidat nový cloud) a zvolte **Microsoft Azure VM Agents**.
1. V rozevíracím seznamu **Add** (Přidat) v části **Azure Credentials** (Přihlašovací údaje Azure) vyberte existující instanční objekt. Pokud se žádný nezobrazí, pomocí následujících kroků [vytvořte instanční objekt](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) pro váš účet Azure a přidejte ho do vaší konfigurace Jenkinse:   

    a. Vyberte **Add** (Přidat) vedle položky **Azure Credentials** (Přihlašovací údaje Azure) a zvolte **Jenkins**.   
    b. V dialogovém okně **Add Credentials** (Přidání přihlašovacích údajů) v rozevíracím seznamu **Kind** (Druh) vyberte **Microsoft Azure Service Principal** (Instanční objekt Microsoft Azure).   
    c. Pomocí Azure CLI nebo služby [Cloud Shell](/azure/cloud-shell/overview) vytvořte instanční objekt Active Directory.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Zadejte přihlašovací údaje z instančního objektu do dialogového okna **Add credentials** (Přidání přihlašovacích údajů). Pokud neznáte ID vašeho předplatného Azure, můžete ho získat dotazem z rozhraní příkazového řádku:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Dokončený instanční objekt by měl jako **Subscription ID** (ID předplatného) používat pole `id`, jako **Client ID** (ID klienta) hodnotu `appId`, jako **Client Secret** (Tajný klíč klienta) hodnotu `password` a jako **Tenant ID** (ID tenanta) hodnotu `tenant`. Výběrem možnosti **Add** (Přidat) přidejte instanční objekt a pak nakonfigurujte modul plug-in tak, aby používal nově vytvořené přihlašovací údaje.

    ![Konfigurace instančního objektu Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. V části **Resource Group Name** (Název skupiny prostředků) nechte vybranou možnost **Create new** (Vytvořit novou) a zadejte `myJenkinsAgentGroup`.
1. Vyberte **Verify configuration** (Ověřit konfiguraci) a připojte se k Azure kvůli otestování nastavení profilu.
1. Výběrem možnosti **Apply** (Použít) aktualizujte konfiguraci modulu plug-in.

## <a name="configure-agent-resources"></a>Konfigurace prostředků agenta

Nakonfigurujte šablonu, která se použije k definování agenta virtuálního počítače Azure. Tato šablona definuje výpočetní prostředky, které budou mít po vytvoření všichni agenti.

1. Vyberte **Add** (Přidat) vedle položky **Add Azure Virtual Machine Template** (Přidat šablonu virtuálního počítače Azure).
1. Jako **Name** (Název) zadejte `defaulttemplate`.
1. Jako **Label** (Popisek) zadejte `ubuntu`.
1. V poli se seznamem vyberte požadovanou [oblast Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. V rozevíracím seznamu v části **Virtual Machine Size** (Velikost virtuálního počítače) vyberte [velikost virtuálního počítače](/azure/virtual-machines/linux/sizes). Pro tento kurz je dostačující velikost `Standard_DS1_v2` pro obecné účely.   
1. Položku **Retention time** (Doba uchovávání) nechte na hodnotě `60`. Toto nastavení definuje počet minut, po které může Jenkins čekat před uvolněním nečinných agentů. Pokud nechcete, aby se nečinní agenti automaticky odebírali, zadejte 0.

   ![Obecná konfigurace virtuálního počítače Azure](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurace nástrojů a operačního systému agenta

V části **Image Configuration** (Konfigurace image) v konfiguraci modulu plug-in vyberte **Ubuntu 16.04 LTS**. Zaškrtněte políčka vedle možností **Install Git (Latest)** (Nainstalovat nejnovější verzi Gitu), **Install Maven (V3.5.0)** (Nainstalovat Maven verze 3.5.0) a **Install Docker** (Nainstalovat Docker), aby se tyto nástroje nainstalovaly na nově vytvořené agenty.

![Konfigurace operačního systému a nástrojů virtuálního počítače](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Vyberte **Add** (Přidat) vedle položky **Admin Credentials** (Přihlašovací údaje správce) a pak vyberte **Jenkins**. Zadejte uživatelské jméno a heslo sloužící k přihlašování k agentům a ujistěte se, že splňují [zásady uživatelských jmen a hesel](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) pro účty pro správu na virtuálních počítačích Azure.

Výběrem možnosti **Verify Template** (Ověřit šablonu) ověřte konfiguraci a pak výběrem možnosti **Save** (Uložit) uložte provedené změny a vraťte se na řídicí panel Jenkinse.

## <a name="create-a-job-in-jenkins"></a>Vytvoření úlohy v Jenkinsu

1. Na řídicím panelu Jenkinse klikněte na **New Item** (Nová položka). 
1. Jako název zadejte `demoproject1`, vyberte **Freestyle project** (Volný projekt) a pak vyberte **OK**.
1. Na kartě **General** (Obecné) zvolte možnost **Restrict where project can be run** (Omezit, kde je možné projekt spustit) a do pole **Label Expression** (Výraz popisku) zadejte `ubuntu`. Zobrazí se zpráva potvrzující, že tento popisek obsluhuje konfigurace cloudu vytvořená v předchozím kroku. 
   ![Nastavení úlohy](./media/jenkins-azure-vm-agents/job-config.png)
1. Na kartě **Source Code Management** (Správa zdrojového kódu), vyberte **Git** a do pole **Repository URL** (Adresa URL úložiště) zadejte následující adresu URL: `https://github.com/spring-projects/spring-petclinic.git`.
1. Na kartě **Build** (Sestavení) vyberte **Add build step** (Přidat krok sestavení) a pak **Invoke top-level Maven targets** (Vyvolat cíle Maven nejvyšší úrovně). Do pole **Goals** (Cíle) zadejte `package`.
1. Vyberte **Save** (Uložit) a uložte definici úlohy.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Sestavení nové úlohy v agentovi virtuálního počítače Azure

1. Vraťte se na řídicí panel Jenkinse.
1. Vyberte úlohu, kterou jste vytvořili v předchozím kroku, a klikněte na **Build now** (Sestavit). Do fronty se zařadí nové sestavení, které se však nespustí, dokud se v předplatném Azure nevytvoří virtuální počítač agenta.
1. Jakmile bude sestavování dokončeno, přejděte na **Console output** (Výstup konzoly). Uvidíte, že se sestavení provedlo vzdáleně v agentovi Azure.

![Výstup konzoly](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průběžná integrace a doručování do služby Azure App Service](java-deploy-webapp-tutorial.md)