---
title: Vytvoření prostředí s clusterem Service Fabric v Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí pomocí samostatný cluster Service Fabric a spuštění a zastavení clusteru pomocí plánů.
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
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318786"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Vytvoření prostředí s samostatný cluster Service Fabric v Azure DevTest Labs
Tento článek obsahuje informace o tom, jak vytvořit prostředí pomocí samostatný cluster Service Fabric v Azure DevTest Labs. 

## <a name="overview"></a>Přehled
DevTest Labs můžete vytvořit samostatný testovací prostředí, jak jsou definovány pomocí šablon Azure Resource Manageru. Tato prostředí obsahovat oba prostředky IaaS, jako jsou virtuální počítače a prostředky PaaS, jako je Service Fabric. DevTest Labs umožňuje spravovat virtuální počítače v prostředí tím, že poskytuje příkazy pro řízení virtuálních počítačů. Tyto příkazy nabízejí tyto možnosti spuštění nebo zastavení virtuálního počítače podle plánu. Obdobně DevTest Labs můžete také vám pomohou spravovat clustery Service Fabric v prostředí. Můžete spustit nebo zastavit cluster Service Fabric v prostředí, ručně nebo prostřednictvím plánu.

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric
Clustery Service Fabric jsou vytvořeny pomocí prostředí v DevTest Labs. Každé prostředí je definovaná pomocí šablony Azure Resource Manageru v úložišti Git. [Veřejného úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) obsahuje šablonu Resource Manageru k vytvoření clusteru Service Fabric v DevTest Labs [ServiceFabric Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) složky. 

1. Nejprve vytvořte testovací prostředí ve službě Azure DevTest Labs pomocí pokynů v následujícím článku: [Vytvoření testovacího prostředí](devtest-lab-create-lab.md). Všimněte si, že **veřejných prostředích** je možnost **na** ve výchozím nastavení. 
2. Potvrďte, že Service Fabric poskytovatel je zaregistrovaný pro vaše předplatné pomocí následujících kroků:
    1. Vyberte **předplatná** na levé navigační nabídce a vyberte vaše **předplatného**
    2. Na **předplatné** stránce **poskytovatelů prostředků** v **nastavení** části v nabídce vlevo. 
    3. Pokud **Microsoft.ServiecFabric** není registrovaný, vyberte **zaregistrovat**. 
3. Na **DevTest Lab** stránky pro vaše testovací prostředí, vyberte **+ přidat** na panelu nástrojů. 
    
    ![Přidání tlačítka na panelu nástrojů](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na **vyberte bázi** stránce **testovacího prostředí clusteru Service Fabric** v seznamu. 

    ![V seznamu vyberte Cluster Service Fabric testovacího prostředí](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na **nastavení** stránce, proveďte následující kroky: 
    1. Zadejte **název** pro váš cluster **prostředí**. Toto je název skupiny prostředků v Azure, ve kterém se bude vytvořit cluster Service Fabric. 
    2. Vyberte **operační systém (OS)** pro virtuální počítače clusteru. Výchozí hodnota je: **Windows**.
    3. Zadejte název **správce** pro cluster. 
    4. Zadejte **heslo** pro správce. 
    5. Pro **certifikát**, zadejte informace o certifikátu jako řetězec kódováním Base64. Vytvoření certifikátu, proveďte následující kroky:
        1. Stáhněte si **vytvořit ClusterCertificate.ps1** soubor [úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Můžete také klonujte úložiště na váš počítač. 
        2. Spusťte **PowerShell**. 
        3. Spustit **ps1** souborů pomocí příkazu `.\Create-ClusterCertificate.ps1`. Zobrazí textový soubor otevřít v programu Poznámkový blok s informacemi, které je potřeba vyplnit pole související s certifikátem na této stránce. . 
    6. Zadejte **heslo pro certifikát**.
    7. Zadejte **kryptografický otisk** pro váš certifikát.
    8. Vyberte **přidat** na **konfigurovat nastavení** stránky. 

        ![Konfigurace nastavení clusteru](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po vytvoření clusteru se zobrazí skupiny prostředků s názvem prostředí, které jste zadali v předchozím kroku. Když rozšiřujete, najdete v clusteru Service Fabric v ní. Pokud stav skupiny prostředků se zasekla na **vytváření**vyberte **aktualizovat** na panelu nástrojů. **Clusteru Service Fabric** prostředí vytvoří 5uzel 1-nodetype cluster v Linuxu nebo Windows.

    V následujícím příkladu **mysfabricclusterrg** je název skupiny prostředků, která je vytvořena speciálně pro cluster Service Fabric. Je důležité si uvědomit, že jsou obsaženy v rámci skupiny prostředků, ve kterém jsou vytvořeny testovací prostředí. To znamená, že šablona definující prostředí, které mají pouze přístup k prostředkům v rámci skupiny nově vytvořeného prostředku nebo [virtuální sítě, které jsou nakonfigurované k použití Lab](devtest-lab-configure-vnet.md). Tato ukázka výše uvedené všechny požadované prostředky vytvoří ve stejné skupině prostředků.

    ![Vytvoření clusteru](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Spuštění nebo zastavení clusteru
Můžete spustit nebo zastavit clusteru z obou DevTest Lab samotné stránky nebo ze stránky clusteru Service Fabric poskytuje DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na stránce DevTest Lab
Můžete spustit nebo zastavit clusteru na stránce DevTest Labs pro testovací prostředí. 

1. Vyberte **tři tečky (...)**  pro cluster Service Fabric, jak je znázorněno na následujícím obrázku: 

    ![Spuštění a zastavení příkazy pro cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Zobrazí dva příkazy v místní nabídce a **start** a **Zastavit** clusteru. Příkaz start spustí všechny uzly v clusteru. Příkaz stop zastaví všechny uzly v clusteru. Po zastavení clusteru samotného clusteru Service Fabric zůstane ve stavu Připraveno, ale dokud je ho znova vydat příkaz start clusteru v testovacím prostředí jsou k dispozici žádné uzly.

    Existuje několik důležitých informací při používání clusteru Service Fabric v testovacím prostředí. Může trvat nějakou dobu plně dosazení po spuštění uzly clusteru Service Fabric. Pokud chcete zachovat data z vypnutí spuštění, data musí být uložen na spravovaný disk připojen k virtuálnímu počítači. Při použití připojené spravovaný disk, proto se doporučuje jenom pro testovací prostředí se vliv na výkon. Pokud není podporovaný disk používat k ukládání dat, pak dojde ke ztrátě dat při vydání příkazu stop na clusteru.

### <a name="from-the-service-fabric-cluster-page"></a>Na stránce clusteru Service Fabric 
Neexistuje jiný způsob spuštění nebo zastavení clusteru. 

1. Vyberte svůj cluster Service Fabric ve stromovém zobrazení na stránce DevTest Lab. 

    ![Vyberte svůj cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na **Service Fabric Cluster** stránky pro cluster, uvidíte příkazy na panelu nástrojů pro spuštění nebo zastavení clusteru. 

    ![Spuštění nebo zastavení příkazů na stránce clusteru Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurace automatického spuštění a automatického vypnutí plán
Clustery Service Fabric můžete také spustit nebo zastavit podle plánu. Toto prostředí je podobný prostředí pro virtuální počítače v testovacím prostředí. Pro úsporu finančních prostředků, ve výchozím nastavení každý cluster automaticky vytvoří v testovacím prostředí, vypne v době určené testovacího prostředí [zásadu vypínání](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Můžete přepsat tak, že určíte, zda clusteru by měl být vypnut, nebo tak, že zadáte čas, kdy se vypne clusteru. 

![Některý existující plán pro automatické spuštění a automatické vypnutí](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Výslovný souhlas se plán automatického spuštění
Můžete vyjádřit výslovný souhlas plán spouštění, proveďte následující kroky:

1. Vyberte **automatického spuštění** v nabídce vlevo
2. Vyberte **na** pro **povolit tento cluster service fabric k naplánování automatického spuštění**. Na této stránce je povoleno pouze v případě, že vlastník testovacího prostředí povolil uživatelům autostart jejich virtuální počítače ani clustery Service Fabric.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Stránka automaticky hvězdičkami](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurace nastavení automatického vypnutí 
Chcete-li změnit nastavení pro vypnutí, proveďte následující kroky:

1. Vyberte **automatického vypínání** v nabídce vlevo. 
2. Na této stránce můžete zrušit automatické vypnutí tak, že vyberete **vypnout** pro **povoleno**. 
3. Pokud jste vybrali **na** pro **povoleno**, postupujte podle těchto kroků:
    1. Zadejte **čas** pro vypnutí.
    2. Zadejte **časové pásmo** přihlášení. 
    3. Určete, zda má DevTest Labs k odesílání **oznámení** před automatickým vypnutím. 
    4. Pokud jste vybrali **Ano** pro možnost platby na oznámení, zadejte **adresu URL Webhooku** a/nebo **e-mailová adresa** k odesílání oznámení. 
    5. Na panelu nástrojů vyberte **Uložit**.

        ![Automatické vypnutí stránky](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Chcete-li zobrazit uzly v clusteru Service Fabric
Na stránce clusteru Service Fabric, které jste viděli v krocích dříve je specifické pro stránku DevTest Labs. To není zobrazit uzly v clusteru. Pokud chcete zobrazit další informace o clusteru, postupujte takto:

1. Na **DevTest Lab** stránky pro vaše testovací prostředí, vyberte **skupiny prostředků** ve stromovém zobrazení v **Moje virtual machines** oddílu.

    ![Výběr skupiny prostředků](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na **skupiny prostředků** stránce se zobrazí všechny prostředky ve skupině prostředků v seznamu. Vyberte vaše **clusteru Service Fabric** ze seznamu. 

    ![V seznamu vyberte svůj cluster](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zobrazí **Service Fabric Cluster** stránky pro váš cluster. Toto je stránka, která poskytuje Service Fabric. Zobrazí všechny informace o clusterech, jako je například uzly, typy uzlů, atd.

    ![Domovská stránka clusteru Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích pro podrobnosti o prostředí: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředích ve službě Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
