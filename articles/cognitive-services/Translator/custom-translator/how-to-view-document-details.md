---
title: Podrobnosti o dokumentu – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Stránka seznam dokumentů zobrazuje prvních 10 dokumentů ve vašem pracovním prostoru. U každého dokumentu se zobrazí název, párování, typ, jazyk, časové razítko nahrávání a e-mailová adresa uživatele, který dokument odeslal.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595580"
---
# <a name="view-document-details"></a>Zobrazení podrobností o dokumentu

Stránka seznam dokumentů zobrazuje prvních 10 dokumentů ve vašem pracovním prostoru. U každého dokumentu se zobrazí název, párování, typ, jazyk, časové razítko nahrávání a e-mailová adresa uživatele, který dokument odeslal.

Kliknutím na jednotlivý dokument zobrazíte stránku s podrobnostmi dokumentu. Stránka s podrobnostmi dokumentu zobrazuje seznam extrahovaných vět z dokumentu.

- Ve výchozím nastavení je v rozevíracím poli vybraná možnost "zdrojový" jazyk, ale můžete ho přepínat a zobrazit v cílovém jazyce věty.
- ve výchozím nastavení se na stránku zobrazují 20 vět. Pomocí ovládacího prvku stránkování můžete procházet mezi stránkami.

![Podrobnosti o dokumentu](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Odstranění dokumentu

Aby bylo možné dokument odstranit, musí být uživatel vlastníkem pracovního prostoru. Pokud se navíc dokument používá v modelu, který je v jakékoli části školicího procesu nebo v jakékoli části procesu nasazení, dokument se nedá odstranit.

1. Přejít na stránku dokumentu
2.  Najeďte myší na libovolný záznam dokumentu a klikněte na ikonu koše.

    ![Odstranit dokument](media/how-to/how-to-delete-document-1.png)

3.  Potvrďte odstranění.

    ![Odstranit potvrzení](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Další postup

- Naučte se naučit [model](how-to-train-model.md).
