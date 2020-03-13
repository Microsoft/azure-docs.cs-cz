---
title: Řešení potíží s pracovním prostorem
titleSuffix: ML Studio (classic) - Azure
description: Tato příručka poskytuje řešení pro některé časté problémy, které se vyskytly při nastavování Azure Machine Learning Studioch (klasických) pracovních prostorů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217839"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Průvodce odstraňováním potíží: vytvoření a připojení k pracovnímu prostoru Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tato příručka poskytuje řešení pro některé časté problémy, které se vyskytly při nastavování Azure Machine Learning Studioch (klasických) pracovních prostorů.

## <a name="workspace-owner"></a>Vlastník pracovního prostoru
Pokud chcete otevřít pracovní prostor v Machine Learning Studio (Classic), musíte být přihlášeni k účtu Microsoft, který jste použili k vytvoření pracovního prostoru, nebo potřebujete získat pozvánku od vlastníka, aby se připojil k pracovnímu prostoru. Z Azure Portal můžete spravovat pracovní prostor, který zahrnuje možnost konfigurace přístupu.

Další informace o správě pracovního prostoru najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)].

[Správa pracovního prostoru Azure Machine Learning Studio (Classic)]: manage-workspace.md

## <a name="allowed-regions"></a>Povolené oblasti
Machine Learning je aktuálně k dispozici v omezeném počtu oblastí. Pokud vaše předplatné neobsahuje jednu z těchto oblastí, může se zobrazit chybová zpráva "nemáte žádná předplatná v povolených oblastech".

Pokud chcete požádat o přidání oblasti do předplatného, vytvořte z Azure Portal novou žádost o podporu od Microsoftu, jako typ problému vyberte **fakturace** a podle pokynů odešlete žádost.

## <a name="storage-account"></a>Účet úložiště
Služba Machine Learning potřebuje účet úložiště pro ukládání dat. Můžete použít existující účet úložiště, nebo můžete vytvořit nový účet úložiště, když vytvoříte nový pracovní prostor Machine Learning Studio (klasický) (Pokud máte kvótu pro vytvoření nového účtu úložiště).

Po vytvoření nového pracovního prostoru Machine Learning Studio (Classic) se můžete přihlásit k Machine Learning Studio (Classic) pomocí účet Microsoft, které jste použili k vytvoření pracovního prostoru. Pokud se zobrazí chybová zpráva "pracovní prostor nebyl nalezen" (podobně jako na následujícím snímku obrazovky), použijte následující postup k odstranění souborů cookie prohlížeče.

![Pracovní prostor nebyl nalezen](media/troubleshooting-creating-ml-workspace/screen3.png)

**Odstranění souborů cookie prohlížeče**

1. Pokud používáte Internet Explorer, klikněte na tlačítko **nástroje** v pravém horním rohu a vyberte **Možnosti Internetu**.  

   ![Možnosti Internetu](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na kartě **Obecné** klikněte na **Odstranit...**

   ![Karta Obecné](media/troubleshooting-creating-ml-workspace/screen5.png)

3. V dialogovém okně **Odstranit historii procházení** zkontrolujte, že jsou vybrané **soubory cookie a data webu** , a klikněte na **Odstranit**.

   ![Odstranit soubory cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po odstranění souborů cookie Restartujte prohlížeč a pak přejdete na stránku [Microsoft Azure Machine Learning Studio (Classic)](https://studio.azureml.net) . Až se zobrazí výzva k zadání uživatelského jména a hesla, zadejte stejné účet Microsoft, jako jste použili k vytvoření pracovního prostoru.

## <a name="comments"></a>Komentáře

Naším cílem je zajistit co nejrychlejší Machine Learning prostředí. Pošlete prosím všechny komentáře a problémy na [Azure Machine Learning Fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) , abychom vám pomohli lépe se doručovat.
