---
author: baanders
description: zahrnout soubor pro nastavení místního ověřování pro DefaultAzureCredential v ukázkách digitálních vláken Azure – bez úvodního
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212806"
---
V `DefaultAzureCredential` případě bude ukázka Hledat přihlašovací údaje v místním prostředí, jako je například přihlášení k Azure v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo v aplikaci Visual Studio nebo Visual Studio Code. Z tohoto důvodu byste se měli *přihlásit k Azure místně* prostřednictvím jednoho z těchto mechanismů a nastavit přihlašovací údaje pro ukázku.

Pokud používáte aplikaci Visual Studio nebo Visual Studio Code ke spuštění ukázky kódu, ujistěte se, že jste přihlášeni k tomuto editoru se stejnými přihlašovacími údaji Azure, které chcete použít pro přístup k instanci digitálních vláken Azure.

V opačném případě můžete [nainstalovat místní rozhraní](/cli/azure/install-azure-cli)příkazového řádku Azure, spustit příkazový řádek na svém počítači a spustit `az login` příkaz pro přihlášení k účtu Azure. Po přihlášení si můžete při spuštění ukázky kódu automaticky přihlásit.