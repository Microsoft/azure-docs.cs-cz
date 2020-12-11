---
title: Rychlý Start Azure – spuštění první úlohy služby Batch v Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit účet Batch, fond výpočetních uzlů a úlohu, která spouští základní úlohy ve fondu.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: d8584ecea8ddd5934771dbe5945a6172a28c801c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106470"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Rychlý start: Spuštění první úlohy služby Batch na webu Azure Portal

Začněte s Azure Batch pomocí Azure Portal k vytvoření účtu Batch, fondu výpočetních uzlů (virtuálních počítačů) a úlohy, která spouští úlohy ve fondu. Po dokončení tohoto rychlého startu budete porozumět klíčovým konceptům služby Batch a budete připraveni vyzkoušet dávku s více realistickými úlohami ve větším měřítku.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

Pomocí těchto kroků si vytvořte ukázkový účet Batch pro účely testování. Účet Batch budete potřebovat k vytváření fondů a úloh. Jak vidíte, účet Batch můžete propojit s účtem Azure Storage. I když to k tomuto rychlému startu není nutné, účet úložiště je užitečný při nasazování aplikací a ukládání vstupních a výstupních dat ve většině reálných způsobů využití.

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**  >    >  **Služba COMPUTE Batch**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Snímek obrazovky služby Batch v Azure Marketplace.":::

1. V poli **Skupina prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků.

1. Zadejte hodnotu **názvu účtu**. Tento název musí být v rámci vybraného **umístění** Azure jedinečný. Může obsahovat jenom malá písmena a číslice a musí mít 3-24 znaků.

1. V části **účet úložiště** vyberte existující účet úložiště nebo vytvořte nový.

1. Neměňte žádná další nastavení. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit** a vytvořte účet Batch.

Až se zobrazí zpráva o **úspěšném nasazení** , přejdete na účet Batch, který jste vytvořili.

## <a name="create-a-pool-of-compute-nodes"></a>Vytvořte fond výpočetních uzlů.

Teď máte účet Batch a můžete pro testovací účely vytvořit ukázkový fond výpočetních uzlů s Windows. Fond pro tento rychlý příklad se skládá ze dvou uzlů, na kterých je spuštěná image Windows serveru 2019 z Azure Marketplace.

1. V účtu Batch vyberte **fondy**  >  **Přidat**.

1. Jako **ID fondu** zadejte *mypool*.

1. V části **Operační systém** vyberte následující nastavení (můžete prozkoumat i jiné možnosti).
  
   |Nastavení  |Hodnota  |
   |---------|---------|
   |**Typ obrázku**|Marketplace|
   |**Publisher**     |microsoftwindowsserver|
   |**Nabídka**     |windowsserver|
   |**Skladové**     |2019 – Datacenter-Core – smalldisk|

1. Posuňte se dolů a zadejte nastavení **Velikost uzlu** a **Škálování**. Navržená velikost uzlu nabízí pro tento rychlý příklad dobrou rovnováhu mezi výkonem a náklady.
  
   |Nastavení  |Hodnota  |
   |---------|---------|
   |**Cenová úroveň uzlu**     |A1 úrovně Standard|
   |**Cílové vyhrazené uzly**     |2|

1. U ostatních nastavení ponechte výchozí hodnoty a výběrem možnosti **OK** vytvořte fond.

Služba Batch vytvoří fond okamžitě, ale přidělení a spuštění uzlů úložiště bude několik minut trvat. Během této doby bude mít položka **Stav přidělení** fondu hodnotu **Změna velikosti**. Během změny velikosti můžete pokračovat vytvořením úlohy a úkolů.

Po několika minutách se stav přidělení změní na **ustáleno** a uzly se spustí. Pokud chcete kontrolovat stav uzlů, vyberte fond a pak vyberte **uzly**. Když má některý uzel stav **Nečinný**, znamená to, že je připravený spouštět úkoly.

## <a name="create-a-job"></a>Vytvoření úlohy

Teď máte vytvořený fond a můžete vytvořit úlohu, která se v něm bude spouštět. Úloha služby Batch je logická skupina jednoho nebo víc úkolů. Úloha zahrnuje nastavení společná všem úkolům, jako je priorita a fond, ve kterém se mají úkoly spouštět. Na začátku úloha neobsahuje žádné úkoly.

1. V zobrazení účtu Batch vyberte **úlohy**  >  **Přidat**.

1. Jako **ID úlohy** zadejte *myjob*. U položky **Fond** vyberte *mypool*. U ostatních nastavení ponechte výchozí hodnoty a vyberte **OK**.

## <a name="create-tasks"></a>Vytváření úloh

Teď vyberte úlohu a otevřete stránku **úlohy** . Tady vytvoříte ukázkové úlohy, které se spustí v rámci úlohy. Obvykle vytváříte více úloh, které Batch zařadí do fronty a distribuuje je ke spouštění na výpočetních uzlech. V tomto příkladu vytvoříte dva stejné úkoly. Každý úkol spustí příkazový řádek, na kterém se zobrazí proměnné prostředí služby Batch ve výpočetním uzlu, a potom 90 sekund čeká.

Při použití služby Batch se aplikace nebo skript zadávají právě na příkazovém řádku. Služba Batch poskytuje několik způsobů, jak nasazovat aplikace a skripty do výpočetních uzlů.

Vytvoření prvního úkolu:

1. Vyberte **Přidat**.

1. Jako **ID úkolu** zadejte *mytask*.

1. V okně **Příkazový řádek** zadejte `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. U zbývajících nastavení ponechte výchozí hodnoty a vyberte **Odeslat**.

Po vytvoření služba Batch zařadí úkol do fronty pro spuštění ve fondu. Jakmile bude dostupný uzel, který ho bude moct spustit, úkol se spustí.

Pokud chcete vytvořit druhou úlohu, opakujte výše uvedené kroky. Zadejte jiné **ID úkolu**, ale na příkazový řádek zadejte totéž. Pokud je první úkol pořád spuštěný, služba Batch spustí druhý úkol ve druhém uzlu ve fondu.

## <a name="view-task-output"></a>Zobrazení výstupu úkolu

Ukázkové úlohy, které jste vytvořili, budou dokončeny během několika minut. Chcete-li zobrazit výstup dokončené úlohy, vyberte úlohu a pak vyberte **soubory v uzlu**. Vyberte soubor `stdout.txt` pro zobrazení standardního výstupu úkolu. Obsah by měl vypadat zhruba takto:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Snímek obrazovky s výstupem z dokončené úlohy":::

Obsah zahrnuje proměnné prostředí služby Azure Batch nastavené v uzlu. Při vytváření vlastních úloh a úkolů služby Batch můžete na tyto proměnné prostředí odkazovat na příkazových řádcích úkolů a v aplikacích a skriptech spouštěných těmito příkazovými řádky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prohlížení kurzů a ukázek služby Batch, použijte účet Batch a propojený účet úložiště, které jste vytvořili v tomto rychlém startu. Za samotný účet Batch se neúčtují žádné poplatky.

Poplatky se účtují za fond, ve kterém jsou spuštěné uzly, i když nejsou naplánované žádné úlohy. Až fond nebudete potřebovat, odstraňte ho. V zobrazení účtu vyberte **Fondy** a název fondu. Vyberte **Odstranit**.  Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech.

Pokud už je nepotřebujete, odstraňte skupinu prostředků, účet Batch a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků účtu Batch a vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Batch, fond služby Batch a úlohu služby Batch. Úloha spustila ukázkové úkoly a prohlédli jste si výstup vytvořený v jednom z uzlů. Teď chápete klíčové koncepty služby Batch a můžete službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě. Další informace o službě Azure Batch najdete v dalších kurzech o službě Azure Batch.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Batch](./tutorial-parallel-dotnet.md)
