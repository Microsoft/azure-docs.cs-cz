---
title: Rychlý start Azure – Vytvoření clusteru Batch AI – Portál | Microsoft Docs
description: Rychlý start – Vytvoření clusteru Batch AI pro trénování modelů strojového učení a umělé inteligence – Azure Portal
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057478"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Rychlý start: Vytvoření clusteru Batch AI pro úlohy trénování na webu Azure Portal

V tomto rychlém startu si ukážeme, jak používat Azure Portal k vytvoření clusteru Batch AI, který použijete k trénování modelů AI a strojového učení. Batch AI je spravovaná služba určená pro odborníky na data a výzkumníky v oblasti umělé inteligence. Umožňuje jim trénovat modely AI a strojového učení požadované velikosti v clusterech virtuálních počítačů Azure.

Na začátku má cluster jediný uzel GPU a připojený souborový server. Na konci tohoto rychlého startu budete mít cluster, který můžete škálovat a používat k trénování modelů hloubkového učení. K přidělení tréningových úloh clusteru můžete použít Batch AI, nástroje [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) nebo [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud máte existující pár klíčů SSH, můžete tento krok přeskočit.

Pokud chcete vytvořit pár klíčů SSH, spusťte v prostředí Bash následující příkaz a postupujte podle pokynů na obrazovce. Můžete použít například [Azure Cloud Shell](../cloud-shell/overview.md). Ve Windows můžete použít [subsystém Windows pro Linux](/windows/wsl/install-win10). Výstup příkazu zahrnuje název souboru veřejného klíče. Zkopírujte obsah souboru s veřejným klíčem (`cat ~/.ssh/id_rsa.pub`) do schránky nebo na jiné místo, kde k němu budete mít v dalším kroku přístup.

```bash
ssh-keygen -t rsa -b 2048
```

Podrobnější informace o vytvoření páru klíčů SSH najdete v článku o [vytvoření a použití páru veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Vytvoření pracovního prostoru Batch AI

Před vytvořením pracovního prostoru Batch AI si uspořádejte prostředky Batch AI. Pracovní prostor může obsahovat jeden nebo více clusterů nebo jiných prostředků Batch AI.

1. Vyberte **Všechny služby** a vyfiltrujte **Batch AI**.

2. Vyberte **Přidat pracovní prostor**.

3. Zadejte hodnoty **Název pracovního prostoru** a **Skupina prostředků**. Pokud chcete, můžete v polích **Předplatné** a **Umístění** vybrat pro pracovní prostor jinou možnost. Vyberte **Vytvořit pracovní prostor**.

  ![Vytvoření pracovního prostoru Batch AI](./media/quickstart-create-cluster-portal/create-workspace.png)

Po zobrazení zprávy **Nasazení bylo úspěšné** přejděte k vytvořenému prostředku a vyberte pracovní prostor.

## <a name="create-a-file-server"></a>Vytvoření souborového serveru

Souborový server Batch AI je systém souborů NFS s jedním uzlem, který můžete automaticky připojit k uzlům clusteru. Jde o jeden z mnoha způsobů jak zajistit úložiště pro vstupní data a výstupy tréninkových úloh.

1. V pracovním souboru vyberte **Souborový server** > **Přidat souborový server Batch AI**.

2. Zadejte hodnoty **Název souborového serveru** a **Velikost virtuálního počítače**. Pro tento rychlý start doporučujeme jako souborový server použít virtuální počítač o velikosti *Standard D1_v2*. Pokud potřebujete při tréninkových úlohách ukládat větší množství vstupních nebo výstupních dat, zvolte jinou velikost.

3. Zadejte **uživatelské jméno správce** a zkopírujte obsah souboru s veřejným klíčem SSH do pole **Klíč SSH**. Ve zbývajících polích potvrďte výchozí hodnoty a vyberte **Vytvořit souborový server**.

  ![Vytvoření souborového serveru Batch AI](./media/quickstart-create-cluster-portal/create-file-server.png)

Nasazení souborového serveru zabere jen pár minut.

Po vytvoření serveru klikněte na **Vlastnosti** a poznamenejte si hodnotu **Nastavení připojování**. Ke stažení cvičných dat a nahrání výstupních souborů do nebo z určeného adresáře (*/data*) použijte přístup SSH k veřejné IP adrese serveru.

![Vlastnosti souborového serveru](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Vytvoření clusteru

Následující postup slouží k vytvoření clusteru s jedním uzlem GPU. Jako uzel clusteru běží výchozí image Ubuntu Serveru, která je navržená na hostování kontejnerových aplikací. Můžete ji použít k většině tréninkových úloh. K bodu připojení uzlu clusteru je připojený souborový server. 

1. V pracovním prostoru Batch AI vyberte **Cluster** > **Přidat cluster Batch AI**.

2. Zadejte **Název clusteru** a následující nastavení. Navrhovaná velikost virtuálního počítače je jeden počítač NVIDIA Tesla K80 GPU.
  
   |Nastavení  |Hodnota  |
   |---------|---------|
   |**Velikost virtuálního počítače**     |Standard NC6|
   |**Cílový počet uzlů**     |1|

3. Zadejte **uživatelské jméno správce** a zkopírujte obsah souboru s veřejným klíčem SSH do pole **Klíč SSH**. Potvrďte zbývající výchozí hodnoty na této stránce a vyberte **Další: Nastavení uzlu**.

   ![Zadání základních informací o clusteru](./media/quickstart-create-cluster-portal/create-cluster.png)

4. V části **Připojit svazky** vyberte **Odkazy souborového serveru** > **Přidat**. Vyberte dříve vytvořený souborový server. Zadejte **Relativní cestu připojení**, kde je souborový server připojený ke každému uzlu clusteru. Vyberte **Uložit a pokračovat**.

   ![Přidání odkazu na souborový server](./media/quickstart-create-cluster-portal/file-server-reference.png)

Uložte nastavení uzlu a vyberte **Vytvořit cluster**.

Přidělení uzlu trvá službě Batch AI několik minut. Po tuto dobu bude mít **Stav přidělení** clusteru hodnotu **Změna velikosti**. Po několika minutách se stav clusteru změní na **Stabilní** a uzel se spustí.

![Spuštění clusteru](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Vyberte název clusteru a zkontrolujte stav uzlu. Když je stav uzlu **Nečinný**, je připravený na spuštění cvičných úloh.

### <a name="list-cluster-nodes"></a>Seznam uzlů clusteru

Pokud se potřebujete připojit k uzlům clusteru (v našem případě je uzel jeden) kvůli instalaci aplikací nebo provádění údržby, najdete informace o připojení na portálu. Po vytvoření clusteru klikněte na **Uzly** a poznamenejte si nastavení **připojení** SSH (IP adresu a číslo portu).

![Uzly clusteru](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Tyto informace použijte k vytvoření připojení SSH k uzlu. V následujícím příkazu nahraďte IP adresu a číslo portu správnou hodnotou svého uzlu:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Změna velikosti clusteru

Když cluster používáte k trénování modelu, může se stát, že budete potřebovat další výpočetní prostředky. Pokud třeba chcete kvůli distribuované tréninkové úloze zvětšit velikost na dva uzly, vyberte **Měřítko** a nastavte **Cílový počet uzlů** na 2. Konfiguraci uložte.

![Škálování clusteru](./media/quickstart-create-cluster-portal/scale-cluster.png)

Změna velikosti clusteru bude trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v dalších kurzech a ukázkách k Batch AI, použijte pracovní prostor Batch AI, souborový server a cluster, které jste vytvořili v tomto rychlém startu.

Bude se vám účtovat cluster Batch AI a souborový server na spuštěných virtuálních počítačích, i když nejsou žádné úlohy naplánované. Pokud chcete zachovat konfiguraci clusteru, a nemáte spuštěné žádné úlohy, změňte velikost clusteru na 0 uzlů. Později můžete velikost změnit na 1 nebo více uzlů, na kterých spustíte své úlohy. 

Až nebudete pracovní prostor Batch AI potřebovat, odstraňte ho i s clusterem a souborovým serverem. Uděláte to tak, že vyberete pracovní prostor Batch AI a vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili vytvářet cluster Batch AI a připojený souborový server na webu Azure Portal. Pokud se chcete dozvědět, jak používat cluster Batch AI k trénování modelu, pokračujte rychlým startem o trénování modelu hloubkového učení.

> [!div class="nextstepaction"]
> [Trénování modelu hloubkového učení](./quickstart-tensorflow-training-cli.md)
