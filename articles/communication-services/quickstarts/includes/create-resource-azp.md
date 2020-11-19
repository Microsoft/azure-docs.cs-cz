---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886092"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Vytvořit prostředek komunikace Azure

Pokud chcete vytvořit prostředek služby Azure Communication Services, přihlaste se nejprve k [Azure Portal](https://portal.azure.com). V levém horním rohu stránky vyberte **+ vytvořit prostředek**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Snímek obrazovky se zvýrazněním tlačítka vytvořit prostředek v Azure Portal.":::

Zadejte **komunikaci** buď na vstupu **na webu Marketplace** , nebo na panelu hledání v horní části portálu.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Snímek obrazovky znázorňující hledání komunikačních služeb na panelu hledání.":::

Ve výsledcích vyberte **komunikační služby** a pak vyberte **Přidat**.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Snímek obrazovky, na kterém se nachází panel Azure, zvýraznění tlačítka Přidat":::

Nyní můžete nakonfigurovat prostředek komunikačních služeb. Na první stránce procesu vytváření budete požádáni o zadání:

* Předplatné
* Skupina prostředků (můžete vytvořit novou nebo vybrat existující skupinu prostředků).
* Název prostředku komunikačních služeb
* Zeměpisná oblast, ke které bude prostředek přidružen

V dalším kroku můžete přiřadit značky k prostředku. Značky lze použít k uspořádání prostředků Azure. Další informace o značkách najdete v [dokumentaci k označování prostředků](../../../azure-resource-manager/management/tag-resources.md) .

Nakonec můžete zkontrolovat svou konfiguraci a **vytvořit** prostředek. Všimněte si, že dokončení nasazení bude trvat několik minut.

## <a name="manage-your-communication-services-resource"></a>Správa prostředku komunikačních služeb

Pokud chcete spravovat prostředek komunikačních služeb, otevřete [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **komunikační služby Azure**.

Na stránce **komunikační služby** vyberte název prostředku.

Stránka **Přehled** pro váš prostředek obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V levé nabídce stránky prostředků můžete najít další možnosti konfigurace.