---
title: Nasazení sady Studio webové služby do více oblastí
titleSuffix: Azure Machine Learning Studio
description: Postup nasazení (kopie) nové webové služby do jiné oblasti. Snadno Nasaďte webovou službu do více oblastí, bez nutnosti více předplatná a pracovní prostory.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 536a4ae0b740eae7f6072cbd23d96e199e1598e7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487061"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>Nasazení webové služby Azure Machine Learning Studio do více oblastí

Nové webové služby Azure umožňují snadno nasadit webové služby Azure Machine Learning Studio do více oblastí, aniž by bylo více předplatná a pracovní prostory. 

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

