---
title: Ověření aktualizace softwaru od Microsoftu v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu s ověřováním jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648778"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověření aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft pravidelně vydá aktualizace softwaru Azure Stack. Tyto aktualizace jsou k dispozici pro partnery společné technikům Azure Stack s předstihem prováděné veřejně dostupné, takže můžete ověřit aktualizací před jejich řešení a zasílat připomínky do Microsoftu.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Měsíční aktualizace

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Vytvoření pracovního postupu

Ověření aktualizace pomocí stejného postupu jako **ověřování balíčku**. Postupujte podle pokynů na adrese [vytvořit pracovní postup ověření balíčku](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow).

## <a name="run-tests"></a>Spustit testy

Ověření aktualizace pomocí stejného postupu jako **ověřování balíčku**. Postupujte podle pokynů na adrese [testy spustit ověření balíčku](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests).

Není nutné požádat o podpis pro ověření aktualizace balíčku.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
