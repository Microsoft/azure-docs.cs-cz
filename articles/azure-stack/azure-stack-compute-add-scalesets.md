---
title: Zpřístupnit Škálovací sady virtuálních počítačů ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete přidat Škálovací sady virtuálních počítačů na Azure Marketplace zásobníku operátor cloudu
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: ddde2e6bad8a373df405ac05e78a5dbccd0257fc
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "34800636"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Zpřístupnit Škálovací sady virtuálních počítačů ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure Stack. Můžete využít k nasazení a správě sady identických virtuálních počítačů. Všechny virtuální počítače nakonfigurované stejně, nevyžadují škálovací sady virtuálních počítačů předem zřizování. Je snazší zajistit rozsáhlé služby zaměřené na vysoký výpočetní výkon, velké objemy dat a kontejnerizované úlohy.

Tento článek vás provede procesem vytvoření škálovací sady dostupné v Tržišti Azure Stack. Po dokončení tohoto postupu můžete přidat uživatele škálovacích sad virtuálních počítačů pro svá předplatná.

Jsou škálovací sady virtuálních počítačů ve službě Azure Stack jako škálovací sady virtuálních počítačů v Azure. Další informace najdete v tématu následujícího videa:
* [Mark Russinovich hovoří o škálovacích sadách Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman provádí škálovacími sadami virtuálních počítačů](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Ve službě Azure Stack nepodporují škálovací sady virtuálních počítačů automatického škálování. Přidání instancí na škálovací sadu pomocí šablony Resource Manageru, rozhraní příkazového řádku nebo Powershellu.

## <a name="prerequisites"></a>Požadavky

- **Syndikace Marketplace**  
    Registrace Azure Stack s globální Azure a umožňuje Marketplace syndikace. Postupujte podle pokynů v [registrace Azure Stack s využitím Azure](azure-stack-registration.md).
- **Image operačního systému**  
    Pokud jste nepřidali image operačního systému na Azure Marketplace zásobníku, přečtěte si téma [přidat položku marketplace služby Azure Stack od Azure](asdk/asdk-marketplace-item.md).

## <a name="add-the-virtual-machine-scale-set"></a>Přidat Škálovací sadu virtuálních počítačů

1. Otevřete Azure Marketplace zásobníku a připojte se k Azure. Vyberte **Marketplace správu**> **+ přidat z Azure**.

    ![Správa webu Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Přidat a stáhnout položky marketplace Škálovací sady virtuálních počítačů.

    ![Škálovací sada virtuálních počítačů](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizace Image ve Škálovací sadě virtuálních počítačů

Když vytvoříte škálovací sadu virtuálních počítačů, mohou uživatelé aktualizovat Image ve škálovací sadě bez škálovací sady, by bylo nutné znovu vytvořit. Proces pro aktualizaci bitové kopie závisí na následujících scénářů:

1. Škálovací sady virtuálních počítačů šablony nasazení **určuje nejnovější** pro *verze*:  

   Když *verze* je nastaven jako **nejnovější** v *imageReference* část šablony pro změnu velikosti nastavit, vertikálně navýšit kapacitu operace týkající se použití škálovací sadě na nejnovější dostupnou verzi image pro škálovací sady instancí. Po dokončení vertikálního navýšení můžete odstranit starší instance sady škálování virtuálních počítačů.  (Hodnoty *vydavatele*, *nabízejí*, a *sku* zůstanou beze změny). 

   Následuje příklad určení *nejnovější*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Vertikální navýšení mohli používat novou bitovou kopii, je nutné stáhnout této nové bitové kopie:  

   - Pokud image na webu Marketplace je novější verze než image ve škálovací sadě: Stáhněte si novou image, který nahrazuje starší obrázek. Po nahrazuje obrázek uživatele můžete přejít k vertikálně navýšit kapacitu. 

   - Když se verze image na webu Marketplace je stejný jako image ve škálovací sadě: odstranit image, která se používá ve škálovací sadě a pak si stáhnout novou bitovou kopii. V době mezi odebrání původní bitové kopie a stažení image nového nejde vertikálně navýšit kapacitu. 
      
     Tento proces je vyžadován resyndicate imagí, které využívají chování řídkého souboru formátu, představený poprvé ve verzi 1803. 
 

2. Škálovací sady virtuálních počítačů šablony nasazení **neurčuje nejnovější** pro *verze* a místo toho určuje číslo verze:  

    Pokud si stáhnete obrázek z novější verze (která se změní na dostupnou verzi), nelze škálovat škálovací sady. Jedná se o účel jako verze image zadané v šabloně škálovací sady, musí být k dispozici.  

Další informace najdete v tématu [disky operačního systému a image](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Odstranit Škálovací sadu virtuálních počítačů

K odebrání virtuálního počítače změnit velikost položky galerie sady, spusťte následující příkaz Powershellu:

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> Položky galerie nemůže být okamžitě odstraněna. Noční musíte aktualizovat portál několikrát, než položka zobrazuje jako odebrané z webu Marketplace.

## <a name="next-steps"></a>Další postup
[Nejčastější dotazy ke službě Azure Stack](azure-stack-faq.md)