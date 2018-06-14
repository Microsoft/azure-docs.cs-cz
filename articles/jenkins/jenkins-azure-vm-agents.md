---
title: Škálování volaných nasazení s agenty, virtuální počítač Azure.
description: Přidáte dodatečnou kapacitu pro kanály volaných virtuální počítače Azure pomocí agenta virtuálního počítače Azure volaných modulu plug-in.
services: multiple
documentationcenter: ''
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 4d45ed14be499ed927f1433e134a029066146eea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29392636"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Škálování vašeho nasazení volaných potřeby s agenty, virtuální počítač Azure

Tento kurz ukazuje, jak používat volaných [modul plug-in Azure virtuálních počítačů agentů](https://plugins.jenkins.io/azure-vm-agents) přidat kapacity na vyžádání s Linuxové virtuální počítače běžící v Azure.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Instalace modulu plug-in agenty virtuálních počítačů Azure
> * Konfigurace modulu plug-in vytvořit prostředky ve vašem předplatném Azure
> * Nastavte výpočetní prostředky, které jsou k dispozici na každého agenta
> * Nastavení operačního systému a nástroje nainstalované na každého agenta
> * Vytvořit novou úlohu volaných volný styl
> * Spustit úlohu v agenta virtuálního počítače Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure
* Volaných hlavní server. Pokud nemáte, zobrazit [rychlý Start](install-jenkins-solution-template.md) nastavit jeden v Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instalace modulu plug-in Azure VM Agents

> [!TIP]
> Pokud jste nasadili volaných na Azure pomocí [šablona řešení](install-jenkins-solution-template.md), modul plug-in agenta virtuálního počítače Azure je již nainstalován.

1. Na řídicím panelu volaných vyberte **spravovat volaných**, pak vyberte **Správa modulů plug-in**.
2. Vyberte **dostupné** a potom vyhledejte **agenti virtuálních počítačů Azure**. Zaškrtněte políčko vedle položky pro modul plug-in a vyberte **nainstalovat bez restartování** v dolní části řídicího panelu.

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurace modulu plug-in agenty virtuálních počítačů Azure

1. Na řídicím panelu volaných vyberte **spravovat volaných**, pak **nakonfigurujte systém**.
2. Přejděte do dolní části stránky a najít **cloudu** část s **přidat nové cloudové** rozevírací seznam a vyberte **agenti virtuálního počítače Microsoft Azure**.
3. Vyberte existující objekt zabezpečení služby z **přidat** rozevírací seznam v **přihlašovací údaje Azure** části. Pokud je uvedena žádná, proveďte následující postup [vytvořit objekt služby](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) pro Azure. váš účet a přidat jej do vaší konfigurace volaných:   

    a. Vyberte **přidat** vedle **přihlašovací údaje Azure** a zvolte **volaných**.   
    b. V **přidat přihlašovací údaje** dialogovém okně, vyberte **Microsoft Azure Service Principal** z **druh** rozevíracího seznamu.   
    c. Vytvořit objekt Active Directory Service z příkazového řádku Azure nebo [cloudové prostředí](/azure/cloud-shell/overview).
    
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
    d. Zadejte přihlašovací údaje z hlavní do služby **přidejte pověření** dialogové okno. Pokud si nejste jisti svoje ID předplatného Azure, můžete zadat dotaz z rozhraní příkazového řádku:
     
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

    Dokončený instanční objekt by měl používat `id` pole pro **ID předplatného**, `appId` hodnota **ID klienta**, `password` pro **tajný klíč klienta**, a `tenant` pro **ID klienta**. Vyberte **přidat** přidat objekt služby a potom nakonfigurovat modul plug-in používat nově vytvořené pověření.

    ![Konfigurace Azure instančního objektu](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. V **název skupiny prostředků** část, nechte **vytvořit nový** vybrané a zadejte `myJenkinsAgentGroup`.
5. Vyberte **ověřte konfiguraci** pro připojení k Azure k otestování nastavení profilu.
6. Vyberte **použít** aktualizace konfigurace modulu plug-in.

## <a name="configure-agent-resources"></a>Konfigurace agenta prostředků

Nakonfigurujte šablonu pro použití k definování agenta virtuálního počítače Azure. Tato šablona určuje výpočetní prostředky, které každý agent, je při vytvoření.

1. Vyberte **přidat** vedle **přidat šablonu virtuálního počítače Azure**.
2. Zadejte `defaulttemplate` pro **název**
3. Zadejte `ubuntu` pro **popisek**
4. Vyberte požadovanou [oblast Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) z pole se seznamem.
5. Vyberte [velikost virtuálního počítače](/azure/virtual-machines/linux/sizes) z rozevíracího seznamu v části **velikost virtuálního počítače**. Pro obecné účely `Standard_DS1_v2` velikost je vhodná pro tento kurz.   
6. Ponechte **dobu uchování** v `60`. Toto nastavení určuje počet minut, po které volaných můžete počkat, než ho navrácena nečinnosti agenty. Pokud nechcete, aby nečinnosti agenty bylo odstraněno automaticky, zadejte 0.

   ![Konfigurace virtuálního počítače obecné](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurace agenta operačního systému a nástroje

V **bitové kopie konfigurace** oddílu konfigurace modulu plug-in, vyberte **Ubuntu 16.04 LTS**. Zaškrtněte políčka vedle **nainstalovat Git (nejnovější)**, **nainstalovat Maven (V3.5.0)**, a **nainstalovat Docker** instalace těchto nástrojů na nově vytvořený agenty.

![Konfigurace operačního systému virtuálního počítače a nástroje](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Vyberte **přidat** vedle **přihlašovací údaje správce**, pak vyberte **volaných**. Zadejte uživatelské jméno a heslo použité k přihlášení do agentů a ujistěte se, že splňují [uživatelské jméno a heslo zásad](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) pro účty pro správu na virtuálních počítačích Azure.

Vyberte **ověřte šablony** ověřte konfiguraci a potom vyberte **Uložit** uložte změny a vrátit na řídicí panel volaných.

## <a name="create-a-job-in-jenkins"></a>Vytvoření úlohy v Jenkinsu

1. Na řídicím panelu Jenkinse klikněte na **New Item** (Nová položka). 
2. Zadejte `demoproject1` pro název a vyberte **volný styl projektu**, pak vyberte **OK**.
3. V **Obecné** , zvolte **omezit, kde je možné spustit projekt** a typ `ubuntu` v **výraz popisku**. Zobrazí výzvu k potvrzení, že popisek obsloužených konfigurace cloudu vytvořili v předchozím kroku. 
   ![Nastavení úloh](./media/jenkins-azure-vm-agents/job-config.png)
4. V **správu zdrojového kódu** vyberte **Git** a přidejte následující adresu URL do **adresu URL úložiště** pole: `https://github.com/spring-projects/spring-petclinic.git`
5. V **sestavení** vyberte **přidat krok sestavení**, pak **vyvolání nejvyšší úrovně cíle Maven**. Zadejte `package` v **cíle** pole.
6. Vyberte **Uložit** Uložit definici úlohy.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Vytvoření nové úlohy ve agenta virtuálního počítače Azure

1. Vraťte se na řídicí panel Jenkinse.
2. Vybrat úlohy, které jste vytvořili v předchozím kroku a pak klikněte na **sestavení teď**. Nové sestavení je zařadit do fronty, ale nespustí, dokud nebude vytvořen agenta virtuálního počítače ve vašem předplatném Azure.
3. Jakmile bude sestavování dokončeno, přejděte na **Console output** (Výstup konzoly). Uvidíte, že sestavení byla provedena vzdáleně na Azure agenta.

![Výstup konzoly](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [CI/CD do Azure App Service](java-deploy-webapp-tutorial.md)
