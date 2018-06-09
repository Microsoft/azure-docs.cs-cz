---
title: V tomto kurzu vytvoříte pomocí šablony virtuálního počítače Azure zásobníku | Microsoft Docs
description: Popisuje, jak používat ASDK k vytvoření virtuálního počítače s použitím predfined šablony a vlastní šablony Githubu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: e772dc41ce2cb77a03b91515cae35ffc48f5dbc3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238360"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Kurz: vytvoření virtuálního počítače pomocí šablony komunity
Jako operátor zásobník Azure nebo uživatele, můžete vytvořit virtuální počítač pomocí [vlastní šablony rychlý start Githubu](https://github.com/Azure/AzureStack-QuickStart-Templates) místo nasazení ručně z zásobník Azure marketplace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o šablonách Azure zásobníku rychlý start 
> * Vytvoření virtuálního počítače pomocí vlastní šablony Githubu
> * Spusťte minikube a instalace aplikace

## <a name="learn-about-azure-stack-quickstart-templates"></a>Další informace o šablonách Azure zásobníku rychlý start
Šablony Azure rychlý start zásobníku jsou uložené v [veřejného úložiště šablony rychlý start AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) na Githubu. Toto úložiště obsahuje šablony nasazení Azure Resource Manager, které byly testovány s Microsoft Azure zásobníku Development Kit (ASDK). Aby bylo snazší pro vás k vyhodnocení zásobník Azure a použít ASDK prostředí můžete je používat. 

V čase mnoho uživatelů Githubu přispěla k úložišti, výsledkem obrovské kolekce více než 400 šablony nasazení. Toto úložiště je skvělý výchozí bod pro lepší pochopení, jak můžete nasadit různé druhy prostředí Azure zásobníku. 

>[!IMPORTANT]
> Některé tyto šablony jsou vytvořeny členové komunity a nikoliv společností Microsoft. Každá šablona je licencována pod licenční smlouvou jeho vlastníka, nikoli společnost Microsoft. Společnost Microsoft není zodpovědná za tyto šablony a nekontroluje zabezpečení, výkon nebo kompatibility. Šablony komunity nejsou podporovány v rámci žádného podporu programu či služby a jsou k dispozici "Tak jak je" bez záruky jakéhokoli druhu.

Pokud chcete, můžete přispívat ke Githubu šablony Azure Resource Manager, měli byste si vašeho příspěvku [šablon azure rychlý start úložiště](https://github.com/Azure/AzureStack-QuickStart-Templates).

Další informace o úložišti GitHub a jak přispívat k němu najdete v tématu [soubor readme pro úložiště](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Vytvoření virtuálního počítače pomocí vlastní šablony Githubu
V tomto kurzu příklad [101 virtuálních počítačů linux minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) zásobník Azure rychlý start šablona se používá pro nasazení virtuálního počítače Ubuntu 16.04 na AzureStack systémem Minikube ke správě clusteru kubenetes.

Minikube je nástroj, který usnadňuje Kubernetes spouštět místně. Minikube spustí každodenní jeden uzel clusteru Kubernetes uvnitř virtuálního počítače pro uživatele vyzkoušet Kubernetes nebo pro vývoj s ním. Podporuje jednoduchý, jeden uzel clusteru Kubernetes systémem virtuálního počítače s Linuxem. Je nejvíce rovnou dopředného a nejrychlejší způsob, jak získat plně funkční Kubernetes clusteru se systémem. To umožňuje vývojáři pro vývoj a testování jejich nasazení na základě Kubernetes aplikací na místních počítačích. Z pohledu architektury Minikube virtuální počítač běží hlavní a komponenty uzlu agenta místně:
- Hlavní uzel komponenty, například API Server, Plánovač a etcd serveru se spouštějí v jediném procesu Linux názvem LocalKube.
- Komponenty agenta uzlu se spouštějí uvnitř docker kontejnery přesně tak, jak budou běžet na normální uzlu agenta. Z hlediska nasazení aplikace není žádný rozdíl při nasazení aplikace na Minikube nebo v pravidelných Kubernetes clusteru.

Tato šablona nainstaluje následující součásti:

- Virtuálního počítače s Ubuntu 16.04 LTS
- [Docker CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Bitovou kopii virtuálního počítače s Ubuntu (Ubuntu Server 16.04 LTS v tomto příkladu) musí již být přidán do zásobníku Azure marketplace před zahájením těchto kroků.

1.  Klikněte na tlačítko **+ nový** > **vlastní** > **nasazení šablony**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Klikněte na tlačítko **úpravy šablony**.

   ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Klikněte na tlačítko **šablony rychlý Start**.

       ![](media/azure-stack-create-vm-template/3.PNG)

4. Vyberte **101 virtuálních počítačů linux minikube** z dostupných šablon pomocí **vyberte šablonu** rozevíracího seznamu a pak klikněte na tlačítko **OK**.  

   ![](media/azure-stack-create-vm-template/4.PNG)

5. Pokud chcete, aby se změny do šablony JSON, můžete použít, pokud není, nebo po dokončení klikněte na tlačítko **Uložit** zavřete dialogové okno Upravit šablonu.

   ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Klikněte na **parametry**, vyplňte nebo upravte dostupná pole podle potřeby a pak klikněte na tlačítko **OK**. Zvolte předplatné, které chcete používat, vytvořte nebo zvolit stávající název skupiny prostředků a pak klikněte na tlačítko **vytvořit** k inicializaci nasazení šablony.

       ![](media/azure-stack-create-vm-template/6.PNG)

7. Zvolte předplatné, které chcete používat, vytvořte nebo zvolit stávající název skupiny prostředků a pak klikněte na tlačítko **vytvořit** k inicializaci nasazení šablony.

   ![](media/azure-stack-create-vm-template/7.PNG)

8. Nasazení skupiny prostředků trvá několik minut pro vytvoření vlastního virtuálního počítače na základě šablon. Můžete monitorovat stav instalace prostřednictvím oznámení a z vlastností skupiny prostředků. 

   ![](media/azure-stack-create-vm-template/8.PNG)

   >[!NOTE]
   > Virtuální počítač bude spuštěna po dokončení nasazení. 

## <a name="start-minikube-and-install-an-application"></a>Spusťte minikube a instalace aplikace
Teď, když virtuální počítač s Linuxem se úspěšně vytvořila, vám umožní přihlásit ke spuštění minikube a instalaci aplikace. 

1. Po dokončení nasazení klikněte na tlačítko **Connect** zobrazíte veřejnou IP adresu, která se použije pro připojení k virtuálního počítače s Linuxem. 

   ![](media/azure-stack-create-vm-template/9.PNG)

2. Z příkazového řádku se zvýšenými oprávněními spusťte **mstsc.exe** k otevření připojení ke vzdálené ploše a připojení virtuálních počítačů Linux veřejné IP adresy v předchozím kroku. Po zobrazení výzvy k xRDP přihlásit pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.

   ![](media/azure-stack-create-vm-template/10.PNG)

3. Otevřete terminál emulátoru a zadejte tyto příkazy spustit minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/azure-stack-create-vm-template/11.PNG)

4. Otevřete webový prohlížeč a přejděte na adresu kubernetes řídicího panelu. Blahopřejeme, nyní máte plně funkční kubernetes instalací pomocí minikube.

   ![](media/azure-stack-create-vm-template/12.PNG)

5. Pokud chcete nasadit ukázková aplikace, přejděte na stránku oficiální dokumentaci kubernetes, přejděte v části "Vytvoření clusteru Minikube" jak jste již vytvořili jeden výše. Jednoduše přejít do části "Vytvoření aplikace Node.js" v https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Další informace o šablonách Azure zásobníku rychlý start 
> * Vytvoření virtuálního počítače pomocí vlastní šablony Githubu
> * Spusťte minikube a instalace aplikace

