---
title: Vytvoření prostředí Service Fabric clusteru v Azure DevTest Labs
description: Naučte se vytvářet prostředí pomocí samostatného Service Fabricho clusteru a spouštět a zastavovat cluster pomocí plánů.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85484005"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Vytvoření prostředí pomocí samostatného Service Fabricho clusteru v Azure DevTest Labs
Tento článek obsahuje informace o tom, jak vytvořit prostředí s integrovaným Service Fabricm clusterem v Azure DevTest Labs. 

## <a name="overview"></a>Přehled
DevTest Labs můžou vytvářet samostatně obsažená testovací prostředí definovaná šablonami Azure Resource Management. Tato prostředí obsahují IaaS prostředky, jako jsou virtuální počítače, a prostředky PaaS, jako je Service Fabric. DevTest Labs vám umožní spravovat virtuální počítače v prostředí tím, že poskytují příkazy pro řízení virtuálních počítačů. Tyto příkazy poskytují možnost spustit nebo zastavit virtuální počítač podle plánu. Podobně vám DevTest Labs taky může pomáhat při správě Service Fabric clusterů v prostředí. Cluster Service Fabric můžete v prostředí spustit nebo zastavit buď ručně, nebo pomocí plánu.

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric
Clustery Service Fabric se vytvářejí pomocí prostředí v DevTest Labs. Každé prostředí je definováno šablonou Azure Resource Manager v úložišti Git. [Veřejné úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) pro DevTest Labs obsahuje šablonu správce prostředků pro vytvoření clusteru Service Fabric ve složce [ServiceFabric-clustering](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) . 

1. Nejprve vytvořte testovací prostředí v Azure DevTest Labs pomocí pokynů v následujícím článku: [Vytvoření testovacího prostředí](devtest-lab-create-lab.md). Všimněte si, že možnost **veřejné prostředí** je ve výchozím nastavení **zapnutá** . 
2. Pomocí následujících kroků ověřte, že je poskytovatel Service Fabric zaregistrován pro vaše předplatné:
    1. V levém navigačním panelu vyberte **předplatná** a vyberte své **předplatné** .
    2. Na stránce **předplatné** vyberte **poskytovatele prostředků** v části **Nastavení** v nabídce vlevo. 
    3. Pokud není **Microsoft. ServiecFabric** zaregistrovaný, vyberte **zaregistrovat**. 
3. Na stránce **testovací prostředí DevTest** pro testovací prostředí vyberte **+ Přidat** na panelu nástrojů. 
    
    ![Přidat tlačítko na panelu nástrojů](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na stránce **Zvolte základní** stránku vyberte v seznamu **Service Fabric cluster testovacího prostředí** . 

    ![V seznamu vyberte Cluster Service Fabric Lab.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na stránce **Konfigurovat nastavení** proveďte následující kroky: 
    1. Zadejte **název** pro **prostředí** clusteru. Toto je název skupiny prostředků v Azure, ve které se vytvoří cluster Service Fabric. 
    2. Vyberte **operační systém (OS)** pro virtuální počítače clusteru. Výchozí hodnota je: **Windows**.
    3. Zadejte název **správce** clusteru. 
    4. Zadejte **heslo** pro správce. 
    5. Pro **certifikát** zadejte informace o certifikátu jako řetězec kódovaný v kódování Base64. Chcete-li vytvořit certifikát, proveďte následující kroky:
        1. Stáhněte soubor **Create-ClusterCertificate.ps1** z [úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Případně naklonujte úložiště na svém počítači. 
        2. Spusťte **PowerShell**. 
        3. Spusťte soubor **ps1** pomocí příkazu `.\Create-ClusterCertificate.ps1` . V programu Poznámkový blok se zobrazí textový soubor s informacemi, které potřebujete k vyplnění polí souvisejících s certifikátem na této stránce. . 
    6. Zadejte **heslo pro certifikát**.
    7. Zadejte **kryptografický otisk** certifikátu.
    8. Na stránce **Konfigurovat nastavení** vyberte **Přidat** . 

        ![Konfigurace nastavení clusteru](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po vytvoření clusteru se zobrazí skupina prostředků s názvem prostředí, které jste zadali v předchozím kroku. Když rozbalíte, zobrazí se v něm Cluster Service Fabric. Pokud je stav skupiny prostředků zablokovaný při **vytváření**, vyberte **aktualizovat** na panelu nástrojů. Prostředí **Service Fabric clusteru** vytvoří cluster s jedním uzlem 1-NodeType v systému Linux nebo Windows.

    V následujícím příkladu je **mysfabricclusterrg** název skupiny prostředků, která je vytvořená speciálně pro cluster Service Fabric. Je důležité si uvědomit, že testovací prostředí jsou samostatná v rámci skupiny prostředků, ve které se vytvářejí. To znamená, že šablona definující prostředí, která může přistupovat jenom k prostředkům v nově vytvořené skupině prostředků nebo [virtuálních sítích nakonfigurovaných pro použití v testovacím](devtest-lab-configure-vnet.md)prostředí. Tato ukázka výše vytvoří všechny požadované prostředky ve stejné skupině prostředků.

    ![Cluster vytvořen](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Spuštění nebo zastavení clusteru
Cluster můžete spustit nebo zastavit buď přímo ze stránky DevTest Lab, nebo ze stránky Service Fabric clusteru, kterou poskytuje DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na stránce testovacího prostředí DevTest
Cluster můžete spustit nebo zastavit na stránce DevTest Lab pro vaše testovací prostředí. 

1. Pro Service Fabric cluster Vyberte **tři tečky (...)** , jak je znázorněno na následujícím obrázku: 

    ![Spuštění a zastavení příkazů pro cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. V místní nabídce se zobrazí dva příkazy pro **spuštění** a **zastavení** clusteru. Příkaz spustit spustí všechny uzly v clusteru. Příkaz Stop zastaví všechny uzly v clusteru. Po zastavení clusteru zůstane Cluster Service Fabric sám ve stavu připraveno, ale nejsou k dispozici žádné uzly, dokud se znovu nevydá spouštěcí příkaz v clusteru v testovacím prostředí.

    Při použití Service Fabricho clusteru v testovacím prostředí se Pamatujte na několik důležitých informací. Může trvat nějakou dobu, než se Cluster Service Fabric plně restartuje po restartování uzlů. Pokud chcete zachovat data z vypnutí po spuštění, musí být data uložená na spravovaném disku připojeném k virtuálnímu počítači. Použití připojeného spravovaného disku má vliv na výkon, takže se doporučuje jenom pro testovací prostředí. Pokud není disk používaný pro úložiště dat zálohovaný, ztratí se při vydání příkazu zastavit v clusteru data.

### <a name="from-the-service-fabric-cluster-page"></a>Na stránce Service Fabric clusteru 
Existuje další způsob, jak spustit nebo zastavit cluster. 

1. Ve stromovém zobrazení na stránce DevTest Lab vyberte svůj Cluster Service Fabric. 

    ![Vyberte svůj cluster.](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na stránce **Service Fabric clusteru** pro cluster vidíte na panelu nástrojů příkazy pro spuštění nebo zastavení clusteru. 

    ![Spuštění nebo zastavení příkazů na stránce Service Fabric clusteru](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurovat automatické spouštění a plán automatického vypnutí
Clustery Service Fabric lze také spustit nebo zastavit podle plánu. Toto prostředí je podobné prostředí pro virtuální počítače v testovacím prostředí. V případě úspory peněz se ve výchozím nastavení každý cluster vytvořený v laboratoři automaticky vypne v čase definovaném [zásadami vypnutí](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)testovacího prostředí. Můžete přepsat zadáním, jestli má být cluster vypnutý, nebo zadáním času, kdy se cluster vypne. 

![Existující plány pro automatické spuštění a automatické vypnutí](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Přihlášení k plánu automatického spuštění
Pokud se chcete přihlásit k plánovanému spuštění, proveďte následující kroky:

1. V nabídce vlevo vyberte **automatické spuštění** .
2. Pokud chcete, aby **se tento cluster Service Fabric naplánoval na automatické spuštění**, vyberte **zapnuto** . Tato stránka je povolena pouze v případě, že vlastník testovacího prostředí povolil automatické spouštění virtuálních počítačů nebo Service Fabric clusterů.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Stránka automatické hvězdičky](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurovat nastavení automatického vypnutí 
Chcete-li změnit nastavení pro vypnutí, proveďte následující kroky:

1. V nabídce vlevo vyberte **Automatické vypnutí** . 
2. Na této stránce se můžete odhlásit z automatického vypnutí tak, že vyberete **vypnout** pro **povoleno**. 
3. Pokud jste vybrali **zapnuto** pro **zapnuto**, postupujte takto:
    1. Zadejte **čas** pro vypnutí.
    2. Zadejte časové **pásmo** pro čas. 
    3. Určete, jestli chcete, aby DevTest Labs odesílala **oznámení** před automatickým vypnutím. 
    4. Pokud jste pro možnost oznámení zvolili **Ano** , zadejte **adresu URL Webhooku** a/nebo **e-mailovou adresu** pro odesílání oznámení. 
    5. Na panelu nástrojů vyberte **Uložit**.

        ![Automaticky vypnout stránku](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Zobrazení uzlů v clusteru Service Fabric
Stránka Service Fabric clusteru, kterou jste si poznamenali v předchozích krocích, je specifická pro stránku DevTest Labs. Nezobrazuje uzly v clusteru. Chcete-li zobrazit další informace o clusteru, postupujte podle následujících kroků:

1. Na stránce **DevTest Lab** pro testovací prostředí vyberte **skupinu prostředků** ve stromovém zobrazení v části **moje virtuální počítače** .

    ![Výběr skupiny prostředků](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na stránce **Skupina prostředků** se zobrazí všechny prostředky ve skupině prostředků v seznamu. Ze seznamu vyberte svůj **cluster Service Fabric** . 

    ![V seznamu vyberte svůj cluster.](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zobrazí se stránka **Service Fabric cluster** pro váš cluster. Toto je stránka, kterou poskytuje Service Fabric. Zobrazí se všechny informace o clusterech, jako jsou uzly, typy uzlů atd.

    ![Domovská stránka clusteru Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích najdete v následujících článcích: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a použití veřejných prostředí v Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
