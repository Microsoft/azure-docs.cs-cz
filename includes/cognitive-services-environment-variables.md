---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274657"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurace proměnné prostředí pro ověřování

Aplikace potřebují ověřovat přístup k Cognitive Services, kterou používají. K ověření doporučujeme vytvořit proměnnou prostředí pro ukládání klíčů pro vaše prostředky Azure. 

Po zadání klíče ho zapište do nové proměnné prostředí v místním počítači, na kterém je aplikace spuštěná. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `your-key` jedním z klíčů pro váš prostředek.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Po přidání proměnné prostředí možná bude nutné restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí, a to včetně okna konzoly. Například pokud používáte Visual Studio jako editor, před spuštěním příkladu restartujte Visual Studio.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte svůj soubor .bash_profile a přidejte do něj proměnnou prostředí:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

***