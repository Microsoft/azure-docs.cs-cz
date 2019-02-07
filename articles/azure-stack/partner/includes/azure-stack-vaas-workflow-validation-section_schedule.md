---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807229"
---
V pracovních postupech ověřování **plánování** testu používá společné parametry úrovni pracovního postupu, které jste zadali při vytváření pracovního postupu (viz [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](../azure-stack-vaas-parameters.md)). Pokud některý z hodnoty parametrů testu stane neplatným, je nutné resupply podle pokynů v tématu [upravit parametry pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Při plánování ověřovací test existující instance vytvoří novou instanci namísto původní instanci na portálu. Protokoly pro původní instanci se zachovají, ale nejsou přístupné z portálu.  
Po úspěšném dokončení testu **plán** akce bude zakázáno.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Vyberte **plán** z místní nabídky otevřete příkazový řádek pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **odeslat** naplánování test po dobu provádění.