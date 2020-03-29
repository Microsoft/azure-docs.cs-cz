---
title: Export nebo odstranění dat – služba Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad svými daty. Tento článek vysvětluje, jak můžete zobrazit, exportovat nebo odstranit data ve službě Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718964"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Export nebo odstranění uživatelských dat ve vlastní vizi

Vlastní vize shromažďuje uživatelská data pro provoz služby, ale zákazníci mají plnou kontrolu nad prohlížení, exportace a odstranění jejich dat pomocí vlastní vize [školení rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Informace o exportu a odstranění uživatelských dat ve vlastní vizi naleznete v následující tabulce.

| Data | Exportní operace | Odstranit operaci |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraňte pomocí portálu Azure (předplatná Azure). Nebo pomocí tlačítka "Smazat svůj účet" na stránce nastavení CustomVision.ai (Předplatné účtu Microsoft) | 
| Podrobnosti o iteraci | [Getiteration](https://go.microsoft.com/fwlink/?linkid=865446) | [Odstranititit](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti o výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [Odstranititit](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Seznam iterací | [Getiterations](https://go.microsoft.com/fwlink/?linkid=865446) | [Odstranititit](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty a podrobnosti o projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) a [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [Odstranit projekt](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Značky obrázků | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [Odstranit tag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrázky | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje uri pro stahování obrázků) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje uri pro stahování obrázků) | [Odstranit obrázky](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportované modely | [GetExport](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraněno při odstranění účtu |
