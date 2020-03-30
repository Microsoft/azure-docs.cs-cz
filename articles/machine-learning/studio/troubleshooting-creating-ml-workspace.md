---
title: Poradce při potížích s pracovním prostorem
titleSuffix: ML Studio (classic) - Azure
description: Tato příručka poskytuje řešení pro některé často se vyskytující problémy při nastavování Azure Machine Learning Studio (klasické) pracovní prostory.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217839"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Průvodce odstraňováním problémů: Vytvoření a připojení k pracovnímu prostoru Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tato příručka poskytuje řešení pro některé často se vyskytující problémy při nastavování Azure Machine Learning Studio (klasické) pracovní prostory.

## <a name="workspace-owner"></a>Vlastník pracovního prostoru
Chcete-li otevřít pracovní prostor v machine learningovém studiu (klasickém), musíte být přihlášeni k účtu Microsoft, který jste použili k vytvoření pracovního prostoru, nebo musíte obdržet pozvánku od vlastníka, abyste se připojili k pracovnímu prostoru. Na portálu Azure můžete spravovat pracovní prostor, který zahrnuje možnost konfigurovat přístup.

Další informace o správě pracovního prostoru najdete v [tématu Správa pracovního prostoru Azure Machine Learning Studio (klasické).]

[Správa pracovního prostoru Azure Machine Learning Studio (klasické)]: manage-workspace.md

## <a name="allowed-regions"></a>Povolené oblasti
Strojové učení je v současné době k dispozici v omezeném počtu oblastí. Pokud vaše předplatné neobsahuje jednu z těchto oblastí, může se zobrazit chybová zpráva "Nemáte žádná předplatná v povolených oblastech."

Pokud chcete požádat o přidání oblasti do vašeho předplatného, vytvořte novou žádost o podporu Microsoftu z webu Azure Portal, jako typ problému zvolte **Fakturace** a postupujte podle pokynů k odeslání žádosti.

## <a name="storage-account"></a>Účet úložiště
Služba Machine Learning potřebuje účet úložiště pro ukládání dat. Můžete použít existující účet úložiště nebo můžete vytvořit nový účet úložiště při vytváření nového pracovního prostoru Machine Learning Studio (klasické) (pokud máte kvótu k vytvoření nového účtu úložiště).

Po vytvoření nového pracovního prostoru Machine Learning Studio (klasické) se můžete přihlásit do Machine Learning Studio (klasické) pomocí účtu Microsoft, který jste použili k vytvoření pracovního prostoru. Pokud narazíte na chybovou zprávu "Pracovní prostor nebyl nalezen" (podobně jako na následujícím snímku obrazovky), použijte následující kroky k odstranění souborů cookie prohlížeče.

![Pracovní prostor nebyl nalezen.](media/troubleshooting-creating-ml-workspace/screen3.png)

**Odstranění souborů cookie prohlížeče**

1. Pokud používáte Internet Explorer, klepněte na tlačítko **Nástroje** v pravém horním rohu a vyberte **možnosti Internetu**.  

   ![Možnosti Internetu](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na kartě **Obecné** klikněte na **Odstranit...**

   ![Karta Obecné](media/troubleshooting-creating-ml-workspace/screen5.png)

3. V dialogovém okně **Odstranit historii procházení** zkontrolujte, zda jsou vybrané soubory cookie a data **webu,** a klepněte na tlačítko **Odstranit**.

   ![Odstranění souborů cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po odstranění souborů cookie restartujte prohlížeč a přejděte na stránku [Microsoft Azure Machine Learning Studio (klasické).](https://studio.azureml.net) Po zobrazení výzvy k zadání uživatelského jména a hesla zadejte stejný účet Microsoft, který jste použili k vytvoření pracovního prostoru.

## <a name="comments"></a>Komentáře

Naším cílem je, aby byl zážitek z machine learningu co nejplynulejší. Komentáře a problémy můžete zveřejnit na [fóru Azure Machine Learning,](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) které nám pomůžou lépe vám sloužit.
