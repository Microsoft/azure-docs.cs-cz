---
title: Export nebo odstranění dat – Custom Vision Service
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
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718964"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Export nebo odstranění uživatelských dat v Custom Vision

Custom Vision shromažďuje data uživatelů za účelem provozu služby, ale zákazníci mají plnou kontrolu nad zobrazením, exportem a odstraněním jejich dat pomocí [rozhraní API pro školení](https://go.microsoft.com/fwlink/?linkid=865446)Custom Vision.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Informace o tom, jak exportovat a odstranit data uživatelů v Custom Vision, najdete v následující tabulce.

| Data | Operace exportu | Odstranit operaci |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstranění pomocí Azure Portal (předplatná Azure) Nebo pomocí tlačítka "odstranit váš účet" na stránce nastavení CustomVision.ai (odběry účtu Microsoft) | 
| Podrobnosti iterace | [Getiterace](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti o výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Seznam iterací | [Getiterace](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti o projektech a projektech | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) Getproject a [getprojects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Značky obrázku | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrázky | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor URI pro stažení obrázku) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor URI pro stažení obrázku) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportované modely | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraněno při odstraňování účtu |
