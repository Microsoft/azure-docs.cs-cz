---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455186"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj.

1. Pokud chcete funkci otestovat, nastavte zarážku v kódu funkce a stiskněte klávesu F5, abyste spustili projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP. Tato adresa URL obsahuje funkční klávesy, které jsou předány `code` parametr dotazu.

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Spuštění se při dosažení zarážky pozastaví.

1. Pokud pokračujete v provádění, dále je uvedená ukázka odezvy na požadavek GET v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Pokud chcete zastavit ladění, stiskněte Shift + F5.