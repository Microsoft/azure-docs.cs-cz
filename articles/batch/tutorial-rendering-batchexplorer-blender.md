---
title: Vykreslení scény Blenderu pomocí služby Azure Batch a nástroje Batch Explorer
description: Kurz – Vykreslení několika snímků ze scény Blenderu pomocí služby Azure Batch a klientské aplikace Batch Explorer
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 8a512676ab0e56f51c0fb9c59f2e530cfcf73333
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791422"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Kurz: Vykreslení scény s blendrem. Tato použití Průzkumníka služby Batch

V tomto kurzu se dozvíte, jak vykreslit několik snímků ukázkové scény Blenderu. V tomto kurzu se používá Blender, protože je pro klientské i vykreslovací virtuální počítače zdarma, ale postup je velmi podobný i při použití jiných aplikací, jako je Maya nebo 3ds Max.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nahrání scény Blenderu do úložiště Azure
> * Vytvoření fondu Batch s několika uzly pro účely vykreslování
> * Vykreslení několika snímků
> * Zobrazení a stažení souborů s vykreslenými snímky

## <a name="prerequisites"></a>Požadavky

Budete potřebovat předplatné s průběžnými platbami nebo jiné možnosti nákupu Azure, abyste použili vykreslovací aplikace ve službě Batch na základě pay-per-use plateb. Licencování s platbami za na základě využití není podporováno, pokud používáte bezplatnou nabídku Azure, která poskytuje peněžní kredit.

Potřebujete účet Azure Batch s přidruženým účtem úložiště.  Pokud chcete vytvořit účet Batch, přečtěte si některý z článků Rychlý start pro službu Batch, například [článek věnovaný rozhraní příkazového řádku](https://docs.microsoft.com/azure/batch/quick-create-cli).

Velikost a počet virtuálních počítačů uvedené v tomto kurzu vyžadují kvótu pro jádra s nízkou prioritou alespoň 50 jader. Je možné použít výchozí kvótu, ale bude potřeba použít menší velikost virtuálních počítačů, což znamená, že vykreslování obrázků bude trvat déle. Proces pro vyžádání zvýšené kvóty pro jádra je podrobně popsaný v [tomto článku](https://docs.microsoft.com/azure/batch/batch-quota-limit).

Také je potřeba mít nainstalovaný nástroj [Batch Explorer](https://azure.github.io/BatchExplorer/), který je k dispozici pro Windows, OSX a Linux. Instalace [Blenderu](https://www.blender.org/download/) je volitelná, ale pokud je nainstalovaný, můžete zobrazit soubor ukázkového modelu.

## <a name="download-the-demo-scene"></a>Stažení ukázkové scény

Z [webové stránky s ukázkovými soubory pro Blender](https://www.blender.org/download/demo-files/) si stáhněte ukázkový soubor ZIP Class room pro Blender a rozbalte ho na místní disk.

## <a name="upload-a-scene-to-azure-storage"></a>Nahrání scény do úložiště Azure

Vytvořte kontejner účtu úložiště pro soubory ukázkové scény:

* Spusťte Batch Explorer.
* V hlavní nabídce na levé straně vyberte položku nabídky Data.
* Zkontrolujte, že je v rozevírací nabídce vybraná položka File groups (Skupiny souborů).
* Vyberte tlačítko + a vytvořte novou prázdnou skupinu souborů (Empty file group) s názvem blender-classroom.
  * Skupina souborů je jednoduše kontejner objektů blob služby Azure Storage s předponou „fgrp-“. Jedná se o konvenci, která slouží k vyfiltrování ostatních kontejnerů v účtu úložiště.

![Skupina souborů pro soubory scény](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Nahrajte soubory scény:

* V nástroji Batch Explorer vyberte nový kontejner a přetáhněte do něj obsah složky classroom.

![Nahrané soubory scény](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Vytvoření kontejneru úložiště Azure pro výstupní obrázky

Vytvořte kontejner účtu úložiště pro výstupní soubory ukázkové scény:

* V hlavní nabídce na levé straně vyberte položku nabídky Data.
* Vyberte tlačítko + a vytvořte novou prázdnou skupinu souborů (Empty file group) s názvem render-output.

![Skupina souborů pro výstupní soubory](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Vytvoření fondu virtuálních počítačů pro účely vykreslování

Vytvořte fond Batch s využitím image vykreslovacího virtuálního počítače z Azure Marketplace, která obsahuje aplikaci Blender:

* V hlavní nabídce na levé straně vyberte položku nabídky Gallery (Galerie).
* Výběrem položky Blender zobrazte seznam položek aplikací.
* Vyberte položky pro vykreslování snímků na Windows Serveru.
* Volitelně můžete vybrat ikonu odkazu na pravé straně položky a zobrazit tak soubory šablony, které se použijí k vytvoření fandu a úlohy.

![Položky galerie pro Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Vyberte tlačítko Create pool for later use (Vytvořit fond pro pozdější použití).
  * Jako název fondu ponechte blender-windows.
  * Nastavte Dedicated vm count (Počet vyhrazených virtuálních počítačů) na hodnotu 0.
  * Nastavte Low priority vm count (Počet virtuálních počítačů s nízkou prioritou) na hodnotu 3.
  * Nastavte Node size (Velikost uzlu) na hodnotu Standard_F16. Můžete vybrat jinou velikost virtuálních počítačů, ale doba potřebná k vykreslení snímku bude záviset hlavně na počtu jader.
* Výběrem zeleného tlačítka vytvořte fond.
  * Fond se vytvoří téměř okamžitě, ale přidělení a spuštění virtuálních počítačů bude několik minut trvat.

![Šablona fondu pro Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Mějte na paměti, že když fond obsahuje virtuální počítače, náklady na tyto virtuální počítače se účtují na vaše předplatné Azure. Pokud chcete účtování těchto poplatků zastavit, je potřeba odstranit fond nebo virtuální počítače. Na konci tohoto kurzu fond odstraňte, aby vám zbytečně nenabíhaly poplatky.

Stav fondu a virtuální počítače můžete sledovat v zobrazení 'Fondy'; Následující příklad ukazuje všechny tři virtuální počítače se přidělilo, dvě spuštění a jsou nečinné, stále se spouští: ![Fond heatmapu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Vytvoření úlohy vykreslování

Vytvořte úlohu vykreslování pro vykreslení několika snímků s využitím fondu, který jste vytvořili:
* V hlavní nabídce na levé straně vyberte položku nabídky Gallery (Galerie).
* Výběrem položky Blender zobrazte seznam položek aplikací.
* Vyberte položky pro vykreslování snímků na Windows Serveru.
* Vyberte tlačítko Run job with existing pool (Spustit úlohu v existujícím fondu).
* Vyberte fond blender-windows.
* Nastavte Job name (Název úlohy) na blender-render-tutorial1.
* V části Input data (Vstupní data) vyberte fgrp-blender-classroom.
* V části Blend file (Soubor Blend) vyberte ikonu souboru a pak vyberte soubor classroom.blend.
* U položky Frame start (Počáteční snímek) ponechte hodnotu 1 a u položky Frame end (Konečný snímek) nastavte hodnotu 5.
* Nastavte Outputs (Výstupy) na fgrp-render-output.
* Výběrem zeleného tlačítka vytvořte úlohu. Vytvoří se úloha s pěti úkoly, jedním pro každý snímek.

![Šablona úlohy pro Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Po vytvoření úlohy a všechny úkoly úlohy se zobrazí spolu s úkoly úlohy: ![Seznam úkolů](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Při prvním spuštění úkolu na virtuálním počítači fondu se spustí přípravný úkol úlohy Batch, který zkopíruje soubory scény ze skupiny souborů úložiště do virtuálního počítače, aby k nim měl Blender přístup.
Stav vykreslování můžete určit zobrazením souboru protokolu stdout.txt, který vytváří Blender.  Vyberte úkol a ve výchozím nastavení se zobrazí Task Outputs (Výstupy úkolu). Pak můžete vybrat a zobrazit soubor stdout.txt.
![Soubor stdout](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Pokud fond blenderu pro windows fondu virtuálních počítačů se projeví ve spuštěném stavu: ![Heatmapu fondu se spouštěním uzly](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

Vykreslení obrázků bude v závislosti na vybrané velikosti virtuálních počítačů trvat několik minut.  Při použití virtuálních počítačů F16 zadaných dříve trvalo vykreslení snímků přibližně 16 minut.

## <a name="view-the-rendering-output"></a>Zobrazení výstupu vykreslování

Po dokončení vykreslení rámce, zobrazí se jako dokončené úkoly: ![Dokončení úlohy](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

Vykreslený obrázek se zapisují do virtuálního počítače, nejprve a lze je zobrazit tak, že vyberete složku "wd": ![Vykreslení obrázku na uzlech fondu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

Šablona úlohy také určuje, že se výstupní soubory snímku a protokolu zapíší zpět do skupiny souborů účtu služby Azure Storage, kterou jste zadali při vytváření úlohy.  'Data' uživatelského rozhraní je možné zobrazit výstupní soubory a protokoly. To také umožňuje stahovat soubory: ![Vykreslení obrázku ve skupině souborů úložiště](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Když se dokončí všechny úlohy, úlohy budou označeny jako probíhá její dokončování: ![Úloha a všechny úkoly dokončené](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!WARNING]
> Fond je potřeba odstranit (nebo změnit jeho velikost na nula uzlů), aby se pro předplatné Azure zastavilo účtování poplatků za virtuální počítače.

* Vyberte Pools (Fondy).
* Vyberte fond blender-windows.
* Klikněte na něj pravým tlačítkem a vyberte Delete (Odstranit) nebo vyberte ikonu koše nad fondem.

## <a name="next-steps"></a>Další postup
* V části Gallery (Galerie) prozkoumejte vykreslovací aplikace, které jsou v nástroji Batch Explorer k dispozici.
* Pro každou aplikaci je k dispozici několik šablon a jejich počet se časem bude rozšiřovat.  Pro Blender například existují šablony, které rozdělí obrázek na čtverce, aby bylo možné části obrázku vykreslit paralelně.
* Komplexní popis možností vykreslování najdete v [této](https://docs.microsoft.com/azure/batch/batch-rendering-service) sadě článků.
