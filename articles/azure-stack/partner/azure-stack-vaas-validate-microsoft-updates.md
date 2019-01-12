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
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 8ea0f36f3d167109fc0dc0d91707a08cff1c6a17
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243980"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověření aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft pravidelně vydá aktualizace softwaru Azure Stack. Tyto aktualizace jsou k dispozici ke službě Azure Stack coengineering partnerů. Aktualizace jsou součástí zálohy veřejně k dispozici. Můžete zkontrolovat aktualizace pro vaše řešení a zasílat připomínky do Microsoftu.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Měsíční aktualizace

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Vytvoření pracovního postupu

Ověření aktualizace pomocí stejného postupu jako **ověření řešení**.

## <a name="run-tests"></a>Spouštění testů

1. Ověření aktualizace pomocí stejného postupu jako **ověření řešení**. 

2. Postupujte podle pokynů na adrese [testy spustit ověření řešení](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Místo toho vyberte následující testy:
    - Měsíční aktualizace ověřovací služby Azure Stack
    - Simulace modulu cloudu

Není nutné požádat o podpis pro ověření aktualizace balíčku.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)