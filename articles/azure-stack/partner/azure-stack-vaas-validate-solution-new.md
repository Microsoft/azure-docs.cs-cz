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
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: c8a723137761c12ab335af79dfffb9e124348eac
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246598"
---
# <a name="validate-a-new-azure-stack-solution"></a>Ověření nové řešení Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Další informace o použití **ověření řešení** pracovní postup, chcete-li certifikovat nová řešení Azure Stack.

Řešení s Azure Stack je hardwaru kusovník (BoM), který se společně dohodli mezi společnostmi Microsoft a partnera po splnění požadavky na certifikaci loga Windows serveru. Řešení musí recertified, pokud došlo ke změně hardwaru BoM. Další otázky o tom, kdy provést opětovnou certifikaci i řešení, kontaktujte tým na adrese [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Chcete-li certifikovat vašeho řešení, spusťte pracovní postup řešení ověření dvakrát. Spustit jednou pro *minimálně* podporované konfigurace. Druhý dobu spuštění *lze uchovávat* podporované konfigurace. Microsoft certifikuje řešení, pokud obě konfigurace projít všemi testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Vytvořit pracovní postup řešení ověření

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Vyberte **Start** na **ověření řešení** dlaždici.

    ![Dlaždice řešení ověření pracovního postupu](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Vyberte **konfigurace řešení**.
    - **Minimální**: řešení má nakonfigurovanou Minimální podporovaný počet uzlů.
    - **Maximální**: řešení má nakonfigurovanou maximální podporovaný počet uzlů.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informace o ověření řešení](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu není možné měnit parametry prostředí.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku Souhrn testů.

## <a name="run-solution-validation-tests"></a>Spustit testy pro ověření řešení

V **řešení ověřovací testy Souhrn** stránky, zobrazí se seznam testů, které jsou potřebné k dokončení ověření.

V pracovních postupech ověřování **plánování** testu používá společné parametry úrovni pracovního postupu, které jste zadali při vytváření pracovního postupu (viz [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md)). Pokud některý z hodnoty parametrů testu stane neplatným, je nutné resupply podle pokynů v tématu [upravit parametry pracovního postupu](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Při plánování ověřovací test existující instance vytvoří novou instanci namísto původní instanci na portálu. Protokoly pro původní instanci se zachovají, ale nejsou přístupné z portálu.  
Po úspěšném dokončení testu **plán** akce bude zakázáno.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Vyberte následující testy:
    - Simulace modulu cloudu
    - COMPUTE provozní sady SDK
    - Test identifikace disku
    - Provozní sady SDK rozšíření služby KeyVault
    - Provozní sady SDK služby KeyVault
    - Provozní sadě SDK sítě
    - Provozní sady SDK účtu úložiště

3. Vyberte **plán** z místní nabídky otevřete příkazový řádek pro plánování testovací instance.

4. Zkontrolujte parametry testu a pak vyberte **odeslat** naplánování test po dobu provádění.

![Plán řešení ověřovací test](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)