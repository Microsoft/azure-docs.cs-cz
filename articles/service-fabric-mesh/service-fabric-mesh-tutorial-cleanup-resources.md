---
title: Kurz – Vyčištění prostředků Azure Service Fabric Mesh | Microsoft Docs
description: Naučte se, jak odebrat prostředky Azure Service Fabric Mesh, aby vám nebyly účtovány prostředky, které už nepoužíváte.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: fb7a444c54a57e7f2c38d941eb99f2fea7eebcef
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993353"
---
# <a name="tutorial-remove-azure-resources"></a>Kurz: Odebrání prostředků Azure

V tomto kurzu, který je pátou částí série, se dozvíte, jak odstranit aplikaci a její prostředky, abyste za ně nemuseli platit.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vyčistit prostředky používané aplikací tak, aby vám za ně nenabíhaly poplatky

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvořit aplikaci Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladit aplikaci Service Fabric Mesh běžící v místním clusteru pro vývoj](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Nasadit aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Upgradovat aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * Vyčištění prostředků Service Fabric Mesh

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud jste nenasadili aplikaci seznamu úkolů, postupujte podle pokynů v článku o [publikování webové aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Toto je konec tohoto kurzu. Jakmile budete s vytvořenými prostředky hotovi, odstraňte je, aby vám nebyly účtovány prostředky, které už nepoužíváte. To je zvlášť důležité, protože Mesh je bezserverová služba účtovaná po sekundách. Další informace o cenách služby Mesh najdete na adrese https://aka.ms/sfmeshpricing.

Jednou z výhod nabízených Azure je to, že když vytvoříte prostředky přidružené ke konkrétní skupině prostředků, při odstranění této skupiny prostředků se odstraní všechny přidružené prostředky. Nemusíte je tak odstraňovat jeden po druhém.

Protože jste při vytváření aplikace seznamu úkolů vytvořili novou skupinu prostředků, můžete ji bezpečně odstranit, čímž odstraníte všechny přidružené prostředky.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternativně můžete skupinu prostředků **sfmeshTutorial1RG** odstranit [z portálu](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Další kroky

Nyní, když jste dokončili publikování aplikace Service Fabric Mesh do Azure, vyzkoušejte toto:

* Pokud se chcete podívat na další příklad komunikace mezi službami, prohlédněte si [ukázku hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
* Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
* Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
* Přečtěte si o [Cloud Shellu](https://docs.microsoft.com/azure/cloud-shell/overview).