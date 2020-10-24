---
author: baanders
description: zahrnout soubor pro nastavení místního ověřování pro DefaultAzureCredential v ukázkách digitálních vláken Azure – bez úvodního
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494663"
---
V `DefaultAzureCredential` případě bude ukázka Hledat přihlašovací údaje v místním prostředí, jako je přihlášení Azure v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) nebo v aplikaci Visual Studio/Visual Studio Code. To znamená, že byste se měli **přihlásit do Azure místně** prostřednictvím jednoho z těchto mechanismů pro nastavení přihlašovacích údajů pro ukázku.

Pokud používáte aplikaci Visual Studio nebo Visual Studio Code ke spuštění ukázky kódu, ujistěte se, že jste přihlášeni k tomuto editoru se stejnými přihlašovacími údaji Azure, které chcete použít pro přístup k instanci digitálních vláken Azure.

V opačném případě můžete [nainstalovat místní **Azure CLI**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), spustit příkazový řádek na počítači a spustit `az login` příkaz pro přihlášení k účtu Azure. Po tomto případě byste si měli při spuštění ukázky kódu automaticky přihlásit.