---
title: Vytvoření instance služby Azure API Management pomocí Visual Studio Code | Microsoft Docs
description: Visual Studio Code vytvořit instanci Azure API Management.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 3105b6f34d7ece81e8145fdd9e89568e66360ddb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649508"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Rychlý Start: vytvoření nové instance služby Azure API Management Service pomocí Visual Studio Code

Azure API Management (APIM) pomáhá organizacím při publikování rozhraní API pro externí, partnerské a interní vývojáře, aby tak uvolnila potenciál jejich dat a služeb. Služba API Management nabízí základní možnosti pro zajištění úspěšného programu s rozhraním API prostřednictvím zapojení vývojářů, informací o podniku, analýz, zabezpečení a ochrany. APIM umožňuje vytvářet a spravovat moderní brány API pro existující backendové služby hostované kdekoli. Další informace najdete v tématu [Přehled](api-management-key-concepts.md).

Tento rychlý Start popisuje kroky pro vytvoření nové instance API Management pomocí *rozšíření Azure API Management* pro Visual Studio Code. Rozšíření můžete použít také k provádění běžných operací správy na instanci API Management.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Navíc se ujistěte, že máte nainstalované následující:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Rozšíření Azure API Management pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Spusťte Visual Studio Code a otevřete rozšíření Azure. (Pokud se na řádku aktivity nezobrazuje ikona Azure, ujistěte se, že je povolená přípona *azure API Management* .)

Vyberte možnost **Přihlásit se k Azure...** a spusťte okno prohlížeče a přihlaste se ke svému účet Microsoft.

![Přihlaste se k Azure z rozšíření API Management pro VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Vytvoření služby API Management

Jakmile se přihlásíte k vašemu účet Microsoft, v podokně *Azure: API Management* Explorer se zobrazí seznam vašich předplatných Azure.

Klikněte pravým tlačítkem na předplatné, které chcete použít, a vyberte **vytvořit API Management v Azure**.

![Průvodce vytvořením API Management v VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

V podokně, které se otevře, zadejte název nové instance API Management. Musí být globálně jedinečný v rámci Azure a musí obsahovat 1-50 alfanumerických znaků a/nebo spojovníky a začínat písmenem a končit alfanumerickým znakem.

Vytvoří se nová instance API Management (a Nadřazená skupina prostředků) se zadaným názvem. Ve výchozím nastavení je instance vytvořena v *západní USA* oblasti s SKU *spotřeby* .

> [!TIP]
> Pokud povolíte **Rozšířené vytváření** v *nastaveních rozšíření Azure API Management*, můžete také zadat [API Management SKU](https://azure.microsoft.com/pricing/details/api-management/), [oblast Azure](https://status.azure.com/en-us/status)a [skupinu prostředků](../azure-resource-manager/management/overview.md) pro nasazení instance API Management.
>
> I když *se skladová položka ke zřízení* trvá méně než minutu, jiné skladové jednotky obvykle zabírají 30-40 minut.

V tuto chvíli jste připraveni k importu a publikování vašeho prvního rozhraní API. Můžete to udělat a také provádět běžné operace API Management v rámci rozšíření pro Visual Studio Code. Další informace najdete [v tomto kurzu](visual-studio-code-tutorial.md) .

![Nově vytvořená instance API Management v podokně rozšíření API Management VS Code](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte instanci API Management tak, že kliknete pravým tlačítkem a vyberete **otevřít na portálu** a [odstraníte API Management službu](get-started-create-service-instance.md#clean-up-resources) a její skupinu prostředků.

Alternativně můžete vybrat možnost **odstranit API Management** a odstranit pouze instanci API Management (Tato operace neodstraní skupinu prostředků).

![Odstranit instanci API Management z VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import a správa rozhraní API pomocí rozšíření API Management](visual-studio-code-tutorial.md)
