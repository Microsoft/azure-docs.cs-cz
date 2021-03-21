---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital doběhu – předpoklady pro ukázkový projekt
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622319"
---
## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto kurzu musíte nejdřív splnit následující požadavky. 

Pokud ještě nemáte předplatné Azure, vytvořte si **[bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** před tím, než začnete.

### <a name="get-required-resources"></a>Získat požadované prostředky

K dokončení tohoto kurzu **nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), verze 16,5 nebo novější** na svém vývojovém počítači. Pokud už máte nainstalovanou starší verzi, můžete na svém počítači otevřít aplikaci *instalační program pro Visual Studio* a podle pokynů instalaci aktualizovat.

>[!NOTE]
> Ujistěte se, že vaše instalace sady Visual Studio 2019 zahrnuje **[úlohu vývoj pro Azure](/dotnet/azure/configure-visual-studio)**. Tato úloha umožňuje aplikaci publikovat Azure functions a provádět další úlohy vývoje Azure.

Tento kurz je založen na ukázkovém projektu napsaném v jazyce C#. Ukázka se nachází tady: [Azure Digital vlákens – komplexní ukázky](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Získejte vzorový projekt** na vašem počítači tak, že přejdete na vzorový odkaz a vyberete tlačítko *Procházet kód* pod nadpisem. Tím přejdete do úložiště GitHub pro ukázky, které si můžete stáhnout jako *. ZIP* výběrem tlačítka *kód* a *Stáhnout soubor zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Podívejte se na úložiště digitálních vláken – Samples na GitHubu. Je vybráno tlačítko kód a vytvoří se malé dialogové okno, ve kterém je zvýrazněno tlačítko Stáhnout ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Tím se stáhne *. Složka ZIP* na váš počítač jako **digital-twins-samples-master.zip**. Rozbalte složku a extrahujte soubory.

### <a name="prepare-an-azure-digital-twins-instance"></a>Příprava instance digitálních vláken Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
