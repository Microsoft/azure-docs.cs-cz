---
title: Vytvoření clusterového prostředí Service Fabric v laboratořích Azure DevTest Labs
description: Zjistěte, jak vytvořit prostředí s samostatným clusterem Service Fabric a spustit a zastavit cluster pomocí plánů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170343"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Vytvoření prostředí pomocí samostatného clusteru Service Fabric v laboratořích Azure DevTest Labs
Tento článek obsahuje informace o tom, jak vytvořit prostředí s samostatným clusterem Service Fabric v Azure DevTest Labs. 

## <a name="overview"></a>Přehled
DevTest Labs můžete vytvořit samostatná testovací prostředí, jak je definováno v šablonách Azure Resource Management. Tato prostředí obsahují prostředky IaaS, jako jsou virtuální počítače, a prostředky PaaS, jako je Service Fabric. DevTest Labs umožňuje spravovat virtuální počítače v prostředí tím, že poskytuje příkazy pro řízení virtuálních počítačů. Tyto příkazy vám možnost spustit nebo zastavit virtuální počítač podle plánu. Podobně DevTest Labs můžete také pomoci spravovat clustery Service Fabric v prostředí. Cluster Service Fabric můžete spustit nebo zastavit v prostředí ručně nebo prostřednictvím plánu.

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric
Clustery Service Fabric jsou vytvořeny pomocí prostředí v DevTest Labs. Každé prostředí je definováno šablonou Azure Resource Manager v úložišti Git. [Veřejné úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) pro DevTest Labs obsahuje šablonu Správce prostředků pro vytvoření clusteru Service Fabric ve složce [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Nejprve vytvořte testovací prostředí v Azure DevTest Labs pomocí pokynů v následujícím článku: [Vytvoření testovacího prostředí](devtest-lab-create-lab.md). Všimněte si, že **možnost Veřejné prostředí** je ve výchozím nastavení **zapnuta.** 
2. Potvrďte, že je poskytovatel Service Fabric registrovaný pro vaše předplatné, postupujte takto:
    1. V levé navigační nabídce vyberte **Předplatná** a vyberte **předplatné.**
    2. Na stránce **Odběr** vyberte **zprostředkovatele prostředků** v části **Nastavení** v levé nabídce. 
    3. Pokud **microsoft.ServiecFabric** není registrován, vyberte **Registrovat**. 
3. Na stránce **DevTest Lab** pro testovací prostředí vyberte **+ Přidat** na panelu nástrojů. 
    
    ![Tlačítko Přidat na panelu nástrojů](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na stránce **Zvolit základní** vyberte v seznamu **cluster unavování infrastruktury služby.** 

    ![V seznamu vyberte cluster lab service fabric.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na stránce **Konfigurovat nastavení** proveďte následující kroky: 
    1. Zadejte **název** **prostředí**clusteru . Toto je název skupiny prostředků v Azure, ve kterém se vytvoří cluster Service Fabric. 
    2. Vyberte **operační systém (OS)** pro virtuální počítače clusteru. Výchozí hodnota je: **Windows**.
    3. Zadejte název **správce** clusteru. 
    4. Zadejte **heslo** pro správce. 
    5. Pro **certifikát**zadejte informace o certifikátu jako řetězec zakódovaný base64. Chcete-li vytvořit certifikát, postupujte takto:
        1. Stáhněte soubor **Create-ClusterCertificate.ps1** z [úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Případně klonovat úložiště na vašem počítači. 
        2. Spusťte **prostředí PowerShell**. 
        3. Spusťte soubor **ps1** pomocí příkazu `.\Create-ClusterCertificate.ps1`. V poznámkovém bloku se zobrazí textový soubor s informacemi, které potřebujete k vyplnění polí souvisejících s certifikátem na této stránce. . 
    6. Zadejte **heslo k certifikátu**.
    7. Zadejte **kryptografický otisk** certifikátu.
    8. Na stránce **Konfigurovat nastavení** vyberte **Přidat.** 

        ![Konfigurace nastavení clusteru](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po vytvoření clusteru se zobrazí skupina prostředků s názvem prostředí, které jste zadali v předchozím kroku. Při rozbalení se zobrazí cluster Service Fabric v něm. Pokud se stav skupiny prostředků zasekl na **okně Vytváření**, vyberte na panelu nástrojů **možnost Aktualizovat.** Prostředí **clusteru Service Fabric** vytvoří cluster s 5 nodetypem v linuxu nebo systému Windows.

    V následujícím příkladu **je mysfabricclusterrg** název skupiny prostředků, která je vytvořena speciálně pro cluster Service Fabric. Je důležité si uvědomit, že testovací prostředí jsou samostatné v rámci skupiny prostředků, ve kterém jsou vytvořeny. To znamená, že šablona, která definuje prostředí, které může přistupovat pouze k prostředkům v rámci nově vytvořené skupiny prostředků nebo [virtuálních sítí nakonfigurovaných pro použití v testovacím prostředí](devtest-lab-configure-vnet.md). Tato ukázka výše vytvoří všechny požadované prostředky ve stejné skupině prostředků.

    ![Cluster vytvořen](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Spuštění nebo zastavení clusteru
Cluster můžete spustit nebo zastavit ze samotné stránky DevTest Lab nebo ze stránky clusteru Service Fabric poskytované společností DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Ze stránky DevTest Lab
Cluster můžete spustit nebo zastavit na stránce DevTest Lab pro testovací prostředí. 

1. Vyberte **tři tečky (...)** pro cluster Service Fabric, jak je znázorněno na následujícím obrázku: 

    ![Příkazy spuštění a zastavení pro cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Zobrazí se dva příkazy v místní nabídce **pro spuštění** a **zastavení** clusteru. Příkaz start spustí všechny uzly v clusteru. Příkaz stop zastaví všechny uzly v clusteru. Po zastavení clusteru zůstane samotný cluster Service Fabric ve stavu připravenosti, ale dokud nebude příkaz start znovu vydán v clusteru v testovacím prostředí, nebudou k dispozici žádné uzly.

    Existuje několik důležité informace, na které je třeba poznamenat, při použití clusteru Service Fabric v testovacím prostředí. Může trvat nějakou dobu, než cluster service fabric plně rehydratuje po restartování uzlů. Chcete-li zachovat data od vypnutí do spuštění, musí být data uložena na spravovaném disku připojeném k virtuálnímu počítači. Při použití připojeného spravovaného disku jsou důsledky pro výkon, proto se doporučuje pouze pro testovací prostředí. Pokud disk použitý pro ukládání dat není zálohován, dojde při vydání příkazu stop v clusteru ke ztrátě dat.

### <a name="from-the-service-fabric-cluster-page"></a>Na stránce Cluster prostředků service fabric 
Existuje jiný způsob, jak spustit nebo zastavit cluster. 

1. Vyberte cluster Service Fabric ve stromovém zobrazení na stránce DevTest Lab. 

    ![Výběr clusteru](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na stránce **Cluster service fabric** pro cluster uvidíte příkazy na panelu nástrojů pro spuštění nebo zastavení clusteru. 

    ![Spuštění nebo zastavení příkazů na stránce Cluster u prostředků infrastruktury služeb](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurace plánu automatického spuštění a automatického vypnutí
Clustery Service Fabric lze také spustit nebo zastavit podle plánu. Toto prostředí je podobné prostředí pro virtuální počítače v testovacím prostředí. Chcete-li ušetřit peníze, ve výchozím nastavení se každý cluster vytvořený v testovacím prostředí automaticky vypne v době definované [zásadami vypnutí](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)testovacího prostředí . Můžete přepsat zadáním, zda má být cluster vypnut, nebo určením času vypnutí clusteru. 

![Existující plány pro automatické spuštění a automatické vypnutí](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Přihlásit se k plánu automatického spuštění
Pokud se chcete přihlásit k plánu spuštění, postupujte takto:

1. V levé nabídce vyberte **možnost Automatické spuštění.**
2. Vyberte **zapnuto,** **chcete-li povolit, aby byl tento cluster prostředků infrastruktury služby naplánován pro automatické spuštění**. Tato stránka je povolena pouze v případě, že vlastník testovacího prostředí povolil uživatelům automatické spuštění virtuálních počítačů nebo clusterů Service Fabric.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Stránka automatické hvězdy](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurace nastavení automatického vypnutí 
Chcete-li změnit nastavení pro vypnutí, postupujte takto:

1. V levé nabídce vyberte **Automatické vypnutí.** 
2. Na této stránce se můžete odhlásit z automatického vypnutí výběrem **možnosti Vypnuto** pro **Povoleno**. 
3. Pokud jste vybrali **možnost Zapnuto** pro **Povoleno**, postupujte takto:
    1. Zadejte **čas** vypnutí.
    2. Zadejte **časové pásmo** pro daný čas. 
    3. Určete, zda chcete, aby devTest Labs odesílat **oznámení** před automatickým vypnutím. 
    4. Pokud jste pro možnost oznámení vybrali **ano,** zadejte **adresu URL webhooku** nebo **e-mailovou adresu** pro odesílání oznámení. 
    5. Na panelu nástrojů vyberte **Uložit**.

        ![Stránka automatického vypnutí](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Zobrazení uzlů v clusteru Service Fabric
Stránka clusteru Service Fabric, kterou jste viděli v předchozích krocích, je specifická pro stránku DevTest Labs. Nezobrazuje uzly v clusteru. Chcete-li zobrazit další informace o clusteru, postupujte takto:

1. Na stránce **DevTest Lab** pro vaše testovací prostředí vyberte **skupinu prostředků** ve stromovém zobrazení v části **Moje virtuální počítače.**

    ![Výběr skupiny prostředků](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na stránce **Skupina prostředků** se v seznamu zobrazí všechny prostředky ve skupině prostředků. V seznamu vyberte **cluster Service Fabric.** 

    ![Výběr clusteru v seznamu](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zobrazí se stránka **Clusteru Prostředků pro služby** pro váš cluster. Toto je stránka, která poskytuje service fabric. Zobrazí se všechny informace o clusterech, jako jsou uzly, typy uzlů atd.

    ![Domovská stránka clusteru Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích naleznete v následujících článcích: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředí v laboratořích Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
