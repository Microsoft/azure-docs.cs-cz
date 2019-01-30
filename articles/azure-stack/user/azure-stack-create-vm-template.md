---
title: V tomto kurzu vytvoříte pomocí šablony virtuálního počítače Azure Stack | Dokumentace Microsoftu
description: Popisuje způsob použití ASDK vytvoření virtuálního počítače pomocí šablony předdefinované a vlastní šablony na Githubu.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 9ccdea6ca0bf46bbc19e5a7e19ac2d3527138345
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241776"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Kurz: vytvoření virtuálního počítače s využitím komunitní šablony

Jako operátory Azure stacku nebo uživatele, můžete vytvořit virtuální počítač pomocí [vlastních šablon pro rychlý start Githubu](https://github.com/Azure/AzureStack-QuickStart-Templates) namísto nasazení ručně z marketplace služby Azure Stack.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití šablon rychlý start Azure Stack 
> * Vytvoření virtuálního počítače pomocí vlastní šablony Githubu
> * Spusťte minikube a instalace aplikace

## <a name="azure-stack-quickstart-templates"></a>Šablony rychlý start Azure Stack

Šablony rychlý start Azure Stack se ukládají v [veřejném úložišti šablon QuickStart pro Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) na Githubu. Toto úložiště obsahuje šablony nasazení Azure Resource Manageru, které byly testovány s Microsoft Azure Stack Development Kit (ASDK). Můžete využít k usnadňují vyhodnocení Azure Stack a používejte prostředí ASDK. 

V čase mnoho uživatelů Githubu přispět k úložišti, výsledkem je kolekce více než 400 šablony nasazení. Toto úložiště je skvělý výchozí bod pro lepší pochopení, jak můžete nasadit různé druhy prostředí do služby Azure Stack. 

>[!IMPORTANT]
> Některé z těchto šablon jsou vytvářeny členy komunity a ne Microsoft. Každá šablona je licenci licenční smlouvy uděluje její vlastník, ne Microsoft. Společnost Microsoft není zodpovědná za tyto šablony a nekontroluje jejich zabezpečení, kompatibilitu nebo výkon. Komunitní šablony nejsou podporované v rámci jakékoli služby nebo programu podpory Microsoftu a jsou k dispozici "Tak, jak jsou," bez záruky jakéhokoli druhu.

Pokud chcete přispívat šablony Azure Resource Manageru na GitHub, ujistěte se, váš příspěvek do [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) úložiště. Další informace o tomto úložišti a jak přispívat na to, najdete v článku [souboru readme](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 

## <a name="create-a-vm-using-a-custom-github-template"></a>Vytvoření virtuálního počítače pomocí vlastní šablony Githubu

V tomto kurzu příklad [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) šablonu pro rychlý start Azure Stack se používá k nasazení virtuálního počítače s Ubuntu 16.04 ve službě Azure Stack s Minikube ke správě clusteru Kubernetes.

Minikube je nástroj, který umožňuje snadno spouštět místně Kubernetes. Minikube běží clusteru Kubernetes s jedním uzlem uvnitř virtuálního počítače, umožňuje vyzkoušet Kubernetes nebo vyvíjet s ním každodenní. Podporuje jednoduchý, jedním uzlem clusterů Kubernetes spuštěných na virtuálním počítači s Linuxem. Minikube je nejrychlejší a nejjednodušší způsob, jak získat plně funkční clusterů Kubernetes spuštěných. Umožňuje vývojářům vyvíjet a testovat své aplikace pro systém Kubernetes nasazení na svých místních počítačích. Architektonicky, bude Minikube virtuální počítač spuštěný hlavního uzlu i komponenty agenta místně:

- Hlavní uzel komponent, jako jsou rozhraní API serveru, Plánovač, a [etcd Server](https://coreos.com/etcd/) jsou spuštěny v jediném procesu Linux volá LocalKube.
- Komponenty agenta uzlu běží v kontejnerech dockeru přesně tak, jak by spustit na normální agenta uzlu. Z pohledu nasazení aplikace není žádný rozdíl mezi když je aplikace nasazená Minikube, nebo v pravidelných clusteru Kubernetes.

Tato šablona nainstaluje následující součásti:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Image virtuálního počítače s Ubuntu (Ubuntu Server 16.04 LTS v tomto příkladu) musí již být přidán do Tržiště Azure Stack před provedením tohoto postupu.

1.  Vyberte **+ vytvořit prostředek**, pak **vlastní**, pak **nasazení šablony**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Vyberte **úpravy šablony**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Vyberte **šablonu pro rychlý Start**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Vyberte **101-vm-linux-minikube** z dostupných šablon pomocí **vyberte šablonu** rozevírací seznam a potom klikněte na tlačítko **OK**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Pokud chcete provést změny v šabloně JSON můžete to udělat. Pokud ne, nebo po dokončení vyberte **Uložit** zavřete **úpravy šablony** dialogového okna.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Vyberte **parametry**, vyplňte nebo upravte dostupných polí podle potřeby a pak klikněte na **OK**. Zvolte předplatné, které chcete používat, vytvořte nebo vyberte existující název skupiny prostředků a potom vyberte **vytvořit** k inicializaci nasazení šablony.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Zvolte předplatné, které chcete používat, vytvořte nebo vyberte existující název skupiny prostředků a potom vyberte **vytvořit** k inicializaci nasazení šablony.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. Nasazení skupiny prostředků trvá několik minut pro vytvoření vlastního virtuálního počítače založené na šabloně. Můžete monitorovat stav instalace pomocí upozornění a z vlastností skupiny prostředků. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Po dokončení nasazení, bude virtuální počítač spuštěný. 

## <a name="start-minikube-and-install-an-application"></a>Spusťte Minikube a instalace aplikace

Teď, když úspěšném vytvoření virtuálního počítače s Linuxem můžete přihlásit ke spuštění Minikube a instalovat aplikace. 

1. Po dokončení nasazení vyberte **připojit** zobrazíte veřejnou IP adresu, která se použije k připojení virtuálního počítače s Linuxem. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Z příkazového řádku se zvýšenými oprávněními spusťte **mstsc.exe** otevřete připojení ke vzdálené ploše a připojit se k virtuálnímu počítači s Linuxem veřejnou IP adresu v předchozím kroku. Po zobrazení výzvy k přihlášení k xRDP, použijte přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Otevřete terminál emulátor a zadejte následující příkazy ke spuštění Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Otevřete webový prohlížeč a přejděte na adresu řídicí panel Kubernetes. Ještě jednou vám Gratulujeme, teď máte plně funkční instalace Kubernetes pomocí Minikube!

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Pokud chcete nasadit ukázkovou aplikaci, najdete na stránce oficiální dokumentace systému Kubernetes a přeskočit v části "Vytvoření clusteru Minikube", protože jste si už nějakou vytvořili výše. Přejděte do části "Vytvoření aplikace Node.js" na https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Další informace o službě Azure Stack, šablony rychlý start 
> * Vytvoření virtuálního počítače pomocí vlastní šablony Githubu
> * Spusťte minikube a instalace aplikace

