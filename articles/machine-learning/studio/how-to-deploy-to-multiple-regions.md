---
title: Nasazení webové služby do několika oblastí - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Postup nasazení (kopie) nové webové služby do jiné oblasti. Snadno Nasaďte webovou službu do více oblastí, bez nutnosti více předplatná a pracovní prostory.
services: machine-learning
documentationcenter: ''
author: ericlicoding
manager: hjerez
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.custom: seodec18
ms.author: amlstudiodocs
ms.openlocfilehash: 21c930a4e9c28f3d3f612a89ce88accd2de2925e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092931"
---
# <a name="azure-machine-learning-studio-deploy-a-web-service-to-multiple-regions"></a>Azure Machine Learning Studio: Nasazení webové služby do více oblastí
Nové webové služby Azure umožňují snadno nasadit webovou službu do více oblastí, aniž by bylo více předplatná a pracovní prostory. 

Ceny se liší podle, že proto je nutné definovat fakturační plán pro každou oblast, ve kterém se nasadit také webovou službu oblastí.

## <a name="to-create-a-plan-in-another-region"></a>Vytvoření plánu v jiné oblasti
1. Přihlaste se do [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Klikněte na tlačítko **plány** nabídky.
3. O plánech přes stránku zobrazení, klikněte na tlačítko **nový**.
4. Z **předplatné** rozevíracím seznamu vyberte předplatné, ve kterém se bude nacházet nový plán.
5. Z **oblasti** rozevírací seznam, vyberte oblast pro nový plán. Možnosti plánování pro vybrané oblasti se zobrazí v **možnosti plánování** části stránky.
6. Z **skupiny prostředků** rozevíracím seznamu vyberte prostředek skupiny pro plán. Další informace o skupinách prostředků najdete v článku [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).
7. V **název plánu** zadejte název plánu.
8. V části **možnostech plánů**, klikněte na fakturace úroveň pro nový plán.
9. Klikněte na možnost **Vytvořit**.

## <a name="deploying-the-web-service-to-another-region"></a>Nasazení webové služby do jiné oblasti
1. Klikněte na tlačítko **webových služeb** nabídky.
2. Vyberte webovou službu, kterou nasazujete do nové oblasti.
3. Klikněte na tlačítko **kopírování**.
4. V **název webové služby**, zadejte nový název pro webovou službu.
5. V **webové služby popis**, zadejte popis pro webovou službu.
6. Z **předplatné** rozevíracím seznamu vyberte předplatné, ve kterém se bude nacházet nové webové služby.
7. Z **skupiny prostředků** rozevíracím seznamu vyberte prostředek skupiny pro webovou službu. Další informace o skupinách prostředků najdete v článku [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).
8. Z **oblasti** rozevíracím seznamu vyberte oblast, ve které chcete nasadit také webovou službu.
9. Z **účtu úložiště** rozevírací seznam, vyberte úložiště účtu pro uložení webové služby.
10. Z **cenový plán** rozevíracím seznamu vyberte plán v oblasti, které jste vybrali v kroku 8.
11. Klikněte na tlačítko **kopírování**.

