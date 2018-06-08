---
title: Postup nasazení webové služby do několika oblastí | Microsoft Docs
description: Postup nasazení (kopírování) novou webovou službu k jiné oblasti.
services: machine-learning
documentationcenter: ''
author: aashishb
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
ms.author: aashishb
ms.openlocfilehash: 78b37f0e7ac554c1823a0607e43718e5a0ac0067
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835130"
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Jak nasadit webovou službu do více oblastí
Nové webové služby Azure umožňují snadno nasadit webovou službu do několika oblastí bez nutnosti více předplatných nebo pracovních prostorů. 

Ceny je oblast konkrétní, že proto je nutné definovat plán fakturace pro každou oblast, ve které budete nasazovat webové služby.

## <a name="to-create-a-plan-in-another-region"></a>Vytvoření plánu v jiné oblasti
1. Přihlaste se k [Microsoft Azure Machine Learning webové služby](https://services.azureml.net/).
2. Klikněte **plány** možnost nabídky.
3. V plánech přes stránka zobrazení, klikněte na tlačítko **nový**.
4. Z **předplatné** rozevíracího seznamu, vyberte předplatné, ve kterém se bude nacházet nový plán.
5. Z **oblast** rozevíracího seznamu, vyberte oblast pro nový plán. Možnosti plánování pro vybrané oblasti se zobrazí v **možnosti plánování** části stránky.
6. Z **skupiny prostředků** rozevíracího seznamu, vyberte prostředek skupiny pro plán. Další informace o skupinách prostředků najdete v části [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).
7. V **název plánu** zadejte název plánu.
8. V části **plán možnosti**, klikněte na úroveň fakturace pro nový plán.
9. Klikněte na možnost **Vytvořit**.

## <a name="deploying-the-web-service-to-another-region"></a>Nasazení webové služby pro jiné oblasti
1. Klikněte **webové služby** možnost nabídky.
2. Vyberte webovou službu, kterou nasazujete do nové oblasti.
3. Klikněte na tlačítko **kopie**.
4. V **název webové služby**, zadejte nový název pro webovou službu.
5. V **webové služby popis**, zadejte popis pro webovou službu.
6. Z **předplatné** rozevíracího seznamu, vyberte předplatné, ve kterém se bude nacházet novou webovou službu.
7. Z **skupiny prostředků** rozevíracího seznamu, vyberte prostředek skupiny pro webovou službu. Další informace o skupinách prostředků najdete v části [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).
8. Z **oblast** rozevíracího seznamu, vyberte oblast, ve které chcete nasadit webovou službu.
9. Z **účet úložiště** účet rozevíracího seznamu, vyberte úložiště pro uložení webovou službu.
10. Z **cena plán** rozevíracího seznamu, vyberte plán v oblasti, které jste vybrali v kroku 8.
11. Klikněte na tlačítko **kopie**.

