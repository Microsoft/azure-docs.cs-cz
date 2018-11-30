---
title: Ověření nové řešení Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak ověřit nové řešení Azure Stack s ověřováním jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7949e764baa7a4e20eb988c78817b6b4f0045593
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333764"
---
# <a name="validate-a-new-azure-stack-solution"></a>Ověření nové řešení Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Další informace o použití **ověření řešení** pracovní postup, chcete-li certifikovat nová řešení Azure Stack.

Řešení s Azure Stack je hardwaru kusovník (BoM), který se společně dohodli mezi společnostmi Microsoft a partnera po splnění požadavky na certifikaci loga Windows serveru. Řešení musí recertified, pokud došlo ke změně hardwaru BoM. Další otázky o tom, kdy provést opětovnou certifikaci i řešení, kontaktujte tým na adrese [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Chcete-li certifikovat vašeho řešení, spusťte pracovní postup řešení ověření dvakrát. Spustit jednou pro *minimálně* podporované konfigurace. Druhý dobu spuštění *lze uchovávat* podporované konfigurace. Microsoft certifikuje řešení, pokud obě konfigurace projít všemi testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Vytvořit pracovní postup řešení ověření

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Vyberte **Start** na **ověření řešení** dlaždici.

    ![Dlaždice řešení ověření pracovního postupu](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Vyberte **konfigurace řešení**.
    - **Minimální**: řešení má nakonfigurovanou Minimální podporovaný počet uzlů.
    - **Maximální**: řešení má nakonfigurovanou maximální podporovaný počet uzlů.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informace o ověření řešení](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu není možné měnit parametry prostředí.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku Souhrn testů.

## <a name="execute-solution-validation-tests"></a>Spustit testy pro ověření řešení

V **souhrn testů pro ověření řešení** stránky, zobrazí se seznam testů, které jsou potřebné k dokončení ověření.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Plán řešení ověřovací test](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)