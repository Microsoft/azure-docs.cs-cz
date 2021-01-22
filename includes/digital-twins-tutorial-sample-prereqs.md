---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital doběhu – předpoklady pro ukázkový projekt
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660517"
---
## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si **[bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** před tím, než začnete.

Než začnete, **nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), verze 16,5 nebo novější** na svém vývojovém počítači. Pokud už máte nainstalovanou starší verzi, můžete na svém počítači otevřít aplikaci *instalační program pro Visual Studio* a podle pokynů instalaci aktualizovat.

Tento kurz je založen na ukázkovém projektu napsaném v jazyce C#. Ukázka se nachází tady: [Azure Digital vlákens – komplexní ukázky](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Získejte vzorový projekt** na vašem počítači tak, že přejdete na vzorový odkaz a vyberete tlačítko *Procházet kód* pod nadpisem. Tím přejdete do úložiště GitHub pro ukázky, které si můžete stáhnout jako *. ZIP* výběrem tlačítka *kód* a *Stáhnout soubor zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Podívejte se na úložiště digitálních vláken – Samples na GitHubu. Je vybráno tlačítko kód a vytvoří se malé dialogové okno, ve kterém je zvýrazněno tlačítko Stáhnout ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Tím se stáhne *. Složka ZIP* na váš počítač jako **digital-twins-samples-master.zip**. Rozbalte složku a extrahujte soubory.

### <a name="prepare-an-azure-digital-twins-instance"></a>Příprava instance digitálních vláken Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
