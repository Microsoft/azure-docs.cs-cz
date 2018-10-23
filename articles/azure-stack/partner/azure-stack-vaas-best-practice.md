---
title: Osvědčené postupy ověření služby Azure Stack. | Dokumenty Microsoft
description: Tento článek obsahuje osvědčené postupy pro ověření jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: 988b32d378b9affccb79f3351761f0eca5c91346
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651407"
---
# <a name="best-practices-for-validation-as-a-service"></a>Osvědčené postupy pro ověření jako služba

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tento článek se zabývá osvědčenými postupy pro správu prostředků v ověřování jako služba (VaaS). Přehled prostředků VaaS, naleznete v tématu [ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Správa řešení

### <a name="naming-convention-for-vaas-solutions"></a>Zásady vytváření názvů pro VaaS řešení

Použití konzistentní zásady vytváření názvů pro všechna řešení zaregistrovaná v VaaS. Například název řešení lze zkonstruovat z vlastností hardwaru pod následujícím způsobem:

|Název produktu | Element hardwaru 1 | Element hardwaru 2 | Název řešení
|---|---|---|---|
Moje řešení XYZ |  Všechny Flash | Tento přepínač X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Kdy vytvořit nové řešení VaaS

Využívat stejná řešení VaaS při spouštění pracovních postupů na stejném hardwaru SKU. Nové řešení VaaS by být vytvořeny pouze v případě, že dojde ke změně hardwaru SKU.

## <a name="workflow-management"></a>Správa pracovního postupu

### <a name="naming-convention-for-vaas-workflows"></a>Zásady vytváření názvů pro pracovní postupy VaaS

Použijte konzistentní zásady vytváření názvů pro všechny VaaS běhů pracovního postupu. Například vytvoření pracovního postupu název z níže uvedených vlastností sestavení následujícím způsobem:

|Číslo (hlavní) sestavení | Datum | Velikost řešení | Název pracovního postupu
|---|---|---| ---|
. 1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Další postup

- Další informace o [ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md)