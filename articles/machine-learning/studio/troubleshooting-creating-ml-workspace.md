---
title: 'Řešení potíží: Vytvořit, připojit k pracovnímu prostoru Machine Learning Studio – Azure | Dokumentace Microsoftu'
description: Tato příručka poskytuje řešení pro některé často problémy při nastavení pracovních prostorů Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5e52abeeef7d40ac5594f4edfa2b80ef553081e4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312045"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-studio-workspace"></a>Průvodce odstraňováním potíží: vytvoření a připojení k pracovnímu prostoru Machine Learning Studio
Tato příručka poskytuje řešení pro některé často problémy při nastavení pracovních prostorů Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Vlastník pracovního prostoru
Otevřete pracovní prostor v Machine Learning Studio, musíte být přihlášeni k Account Microsoft jste použili k vytvoření pracovního prostoru, nebo potřebujete přijmout pozvánku od vlastníka k pracovnímu prostoru. Na webu Azure Portal můžete spravovat pracovní prostor, který zahrnuje možnost konfigurovat přístup.

Další informace o správě pracovního prostoru, naleznete v tématu [Správa pracovního prostoru Azure Machine Learning].

[Správa pracovního prostoru Azure Machine Learning]: manage-workspace.md

## <a name="allowed-regions"></a>Povolených oblastí
Machine Learning je aktuálně k dispozici v omezeném počtu oblastí. Pokud součástí vašeho předplatného není jedna z těchto oblastí, může se zobrazit chybová zpráva, "V oblasti povolené nemáte žádná předplatná."

Požádat o přidaná v oblasti vašeho předplatného, vytvořit novou žádost o podporu společnosti Microsoft na webu Azure Portal, vyberte **fakturace** jako typ problému a postupujte podle výzev k odeslání vaší žádosti.

## <a name="storage-account"></a>Účet úložiště
Služba Machine Learning potřebuje účet úložiště k ukládání dat. Můžete použít existující účet úložiště, nebo můžete vytvořit nový účet úložiště, když vytvoříte nový pracovní prostor Machine Learning (Pokud nemáte kvótu pro vytvoření nového účtu úložiště).

Po vytvoření nového pracovního prostoru Machine Learning se můžete přihlásit ke službě Machine Learning Studio pomocí účtu Microsoft, který jste použili k vytvoření pracovního prostoru. Pokud narazíte na chybovou zprávu, "Pracovní prostor nebyl nalezen" (podobně jako na následujícím snímku obrazovky), použijte následující kroky a odstranit soubory cookie v prohlížeči.

![Pracovní prostor nebyl nalezen][screen3]

**Chcete-li odstranit soubory cookie v prohlížeči**

1. Pokud používáte Internet Explorer, klikněte na tlačítko **nástroje** tlačítko v pravém horním rohu a vyberte **Možnosti Internetu**.  

![Možnosti Internetu][screen4]

2. V části **Obecné** klikněte na tlačítko **odstranit...**

![Karta Obecné][screen5]

3. V **odstranit historii procházení** dialogové okno pole, ujistěte se, že **soubory cookie a data webu** je vybraná a klikněte na tlačítko **odstranit**.

![Odstranění souborů cookie][screen6]

Po odstranění souborů cookie, restartujte prohlížeč a přejděte [Microsoft Azure Machine Learning](https://studio.azureml.net) stránky. Po zobrazení výzvy k zadání uživatelského jména a hesla, zadejte stejný účet Microsoft, který jste použili k vytvoření pracovního prostoru.

## <a name="comments"></a>Komentáře

Naším cílem je, aby co nejvíc jako bezproblémové prostředí Machine Learning. Zveřejněte ji prosím všechny komentáře a problémy ve [fórum pro Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) nám můžete poskytovat lepší.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
