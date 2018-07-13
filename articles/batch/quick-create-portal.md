---
title: Rychlý start Azure – spuštění úlohy služby Batch – portál
description: Můžete se rychle naučit, jak na webu Azure Portal spustit úlohu služby Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 7daaf042d22ba4ac0369b732b586a3760d8cd51c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859570"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Rychlý start: Spuštění první úlohy služby Batch na webu Azure Portal

Tento rychlý start ukazuje, jak na webu Azure Portal vytvořit účet Batch, *fond* výpočetních uzlů (virtuálních počítačů) a *úlohu*, která bude ve fondu spouštět základní *úkoly*. Po dokončení tohoto rychlého startu budete rozumět klíčovým konceptům služby Batch a budete moct službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

Pomocí těchto kroků si vytvořte ukázkový účet Batch pro účely testování. Účet Batch budete potřebovat k vytváření fondů a úloh. Jak vidíte, účet Batch můžete propojit s účtem Azure Storage. I když to k tomuto rychlému startu není nutné, účet úložiště je užitečný při nasazování aplikací a ukládání vstupních a výstupních dat ve většině reálných způsobů využití.


1. Vyberte **Vytvořit prostředek** > **Compute** > **Služba Batch**. 

  ![Batch na webu Marketplace][marketplace_portal]

2. Zadejte hodnoty položek **Název účtu** a **Skupina prostředků**. Název účtu musí být jedinečný v rámci vybrané **lokality** Azure, smí obsahovat jenom malé znaky nebo číslice a musí mít délku 3–24 znaků. 

3. V **Účtu úložiště** vyberte stávající účet úložiště nebo vytvořte nový.

4. U ostatních nastavení ponechte výchozí hodnoty a výběrem možnosti **Vytvořit** vytvořte účet.

  ![Vytvoření účtu Batch][account_portal]  

Po zobrazení zprávy **Nasazení bylo úspěšné** přejděte na portálu do účtu Batch.

## <a name="create-a-pool-of-compute-nodes"></a>Vytvoření fondu výpočetních uzlů

Teď máte účet Batch a můžete pro testovací účely vytvořit ukázkový fond výpočetních uzlů s Windows. V tomto rychlém příkladu fond obsahuje 2 uzly se spuštěnou imagí systému Windows Server 2012 R2 z Azure Marketplace.


1. V účtu Batch vyberte **Fondy** > **Přidat**.

2. Jako **ID fondu** zadejte *mypool*. 

3. V části **Operační systém** vyberte následující nastavení (můžete prozkoumat i jiné možnosti).
  
  |Nastavení  |Hodnota  |
  |---------|---------|
  |**Typ image**|Marketplace (Linux/Windows)|
  |**Publisher**     |MicrosoftWindowsServer|
  |**Nabídka**     |WindowsServer|
  |**Sku**     |2012-R2-Datacenter-smalldisk|

  ![Výběr operačního systému fondu][pool_os] 

4. Posuňte se dolů a zadejte nastavení **Velikost uzlu** a **Škálování**. Navržená velikost uzlu nabízí pro tento rychlý příklad dobrou rovnováhu mezi výkonem a náklady.
  
  |Nastavení  |Hodnota  |
  |---------|---------|
  |**Cenová úroveň uzlu**     |Standard_A1|
  |**Cílové vyhrazené uzly**     |2|

  ![Výběr velikosti fondu][pool_size] 

5. U ostatních nastavení ponechte výchozí hodnoty a výběrem možnosti **OK** vytvořte fond.

Služba Batch vytvoří fond okamžitě, ale přidělení a spuštění uzlů úložiště bude několik minut trvat. Během této doby bude mít položka **Stav přidělení** fondu hodnotu **Změna velikosti**. Během změny velikosti můžete pokračovat vytvořením úlohy a úkolů. 

![Fond ve stavu změny velikosti][pool_resizing]

Po několika minutách se stav fondu změní na hodnotu **Stabilní** a uzly se spustí. Vyberte **Uzly** a zkontrolujte stav uzlů. Když má některý uzel stav **Nečinný**, znamená to, že je připravený spouštět úkoly. 

## <a name="create-a-job"></a>Vytvoření úlohy

Teď máte vytvořený fond a můžete vytvořit úlohu, která se v něm bude spouštět. Úloha služby Batch je logická skupina jednoho nebo víc úkolů. Úloha zahrnuje nastavení společná všem úkolům, jako je priorita a fond, ve kterém se mají úkoly spouštět. Na začátku úloha neobsahuje žádné úkoly. 

1. V zobrazení účtu Batch vyberte **Úlohy** > **Přidat**. 

2. Jako **ID úlohy** zadejte *myjob*. U položky **Fond** vyberte *mypool*. U ostatních nastavení ponechte výchozí hodnoty a vyberte **OK**.

  ![Vytvoření úlohy][job_create]

Po vytvoření úlohy se otevře stránka **Úkoly**.

## <a name="create-tasks"></a>Vytvoření úkolů

Teď vytvoříte ukázkové úkoly, které se budou v úloze spouštět. Obvykle se vytváří několik úkolů, služba Batch je zařadí do fronty a distribuuje je pro spuštění ve výpočetních uzlech. V tomto příkladu vytvoříte dva stejné úkoly. Každý úkol spustí příkazový řádek, na kterém se zobrazí proměnné prostředí služby Batch ve výpočetním uzlu, a potom 90 sekund čeká. 

Při použití služby Batch se aplikace nebo skript zadávají právě na příkazovém řádku. Služba Batch poskytuje několik způsobů, jak nasazovat aplikace a skripty do výpočetních uzlů. 

Vytvoření prvního úkolu:

1. Vyberte **Přidat**.

2. Jako **ID úkolu** zadejte *mytask*. 

3. V okně **Příkazový řádek** zadejte `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. U ostatních nastavení ponechte výchozí hodnoty a vyberte **OK**.

  ![Vytvoření úkolu][task_create]

Služba Batch úkol po vytvoření zařadí do fronty pro spuštění ve fondu. Jakmile bude dostupný uzel, který ho bude moct spustit, úkol se spustí.

Pokud chcete vytvořit druhý úkol, vraťte se ke kroku 1. Zadejte jiné **ID úkolu**, ale na příkazový řádek zadejte totéž. Pokud je první úkol pořád spuštěný, služba Batch spustí druhý úkol ve druhém uzlu ve fondu.

## <a name="view-task-output"></a>Zobrazení výstupu úkolu

Předchozí příklady úkolů se dokončí během pár minut. Pokud chcete zobrazit výstup dokončeného úkolu, vyberte **Soubory v uzlu** a vyberte soubor `stdout.txt`. Tento soubor obsahuje standardní výstup úkolu. Obsah by měl vypadat zhruba takto:

![Zobrazení výstupu úkolu][task_output]

Obsah zahrnuje proměnné prostředí služby Azure Batch nastavené v uzlu. Při vytváření vlastních úloh a úkolů služby Batch můžete na tyto proměnné prostředí odkazovat na příkazových řádcích úkolů a v aplikacích a skriptech spouštěných těmito příkazovými řádky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prohlížení kurzů a ukázek služby Batch, použijte účet Batch a propojený účet úložiště, které jste vytvořili v tomto rychlém startu. Za samotný účet Batch se neúčtují žádné poplatky.

Poplatky se účtují za fond, ve kterém jsou spuštěné uzly, i když nejsou naplánované žádné úlohy. Až fond nebudete potřebovat, odstraňte ho. V zobrazení účtu vyberte **Fondy** a název fondu. Vyberte **Odstranit**.  Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. 

Pokud už je nepotřebujete, odstraňte skupinu prostředků, účet Batch a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků účtu Batch a vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Batch, fond služby Batch a úlohu služby Batch. Úloha spustila ukázkové úkoly a prohlédli jste si výstup vytvořený v jednom z uzlů. Teď chápete klíčové koncepty služby Batch a můžete službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě. Další informace o službě Azure Batch najdete v dalších kurzech o službě Azure Batch. 

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Batch](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png