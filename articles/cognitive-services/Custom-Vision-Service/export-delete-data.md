---
title: Zobrazení nebo odstranění dat – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Můžete udržet plnou kontrolu nad daty. Tento článek vysvětluje, jak můžete zobrazit, exportovat nebo odstranit data v Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527390"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Zobrazení nebo odstranění uživatelských dat v Custom Vision

Custom Vision shromažďuje data uživatelů za účelem provozu služby, ale zákazníci mají plnou kontrolu nad zobrazením a odstraněním jejich dat pomocí [rozhraní API pro školení](https://go.microsoft.com/fwlink/?linkid=865446)Custom Vision.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Informace o tom, jak zobrazit a odstranit data uživatelů v Custom Vision, najdete v následující tabulce.

| Data | Zobrazit operaci | Odstranit operaci |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstranění pomocí Azure Portal (předplatná Azure) Nebo pomocí tlačítka "odstranit váš účet" na stránce nastavení CustomVision.ai (odběry účtu Microsoft) | 
| Podrobnosti iterace | [Getiterace](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti o výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Seznam iterací | [Getiterace](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti o projektech a projektech | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) Getproject a [getprojects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Značky obrázku | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Image | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor URI pro stažení obrázku) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor URI pro stažení obrázku) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportované iterace | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraněno při odstraňování účtu |
