---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/19/2018
ms.author: mabrigg
ms.openlocfilehash: d23ba90f7cc0c5a03db2ef3eed4a662abe7ff27e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650525"
---
V pracovních postupech ověřování **plánování** testu používá společné parametry úrovni pracovního postupu, které jste zadali při vytváření pracovního postupu (viz [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](../azure-stack-vaas-parameters.md)). Pokud některý z hodnoty parametrů testu stane neplatným, je nutné resupply podle pokynů v tématu [upravit parametry pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Při plánování ověřovací test existující instance vytvoří novou instanci namísto původní instanci na portálu. Protokoly pro původní instanci se zachovají, ale nejsou přístupné z portálu.  
Po úspěšném dokončení testu **plán** akce bude zakázáno.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Vyberte **plán** z místní nabídky otevřete příkazový řádek pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **odeslat** naplánování test po dobu provádění.