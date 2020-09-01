---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147406"
---
 Azure Machine Learning výpočetní clustery také podporují [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro ověřování přístupu k prostředkům Azure bez zahrnutí přihlašovacích údajů do kódu. Existují dva typy spravovaných identit:

* **Spravovaná identita přiřazená systémem** je povolena přímo na Azure Machine Learning výpočetním clusteru. Životní cyklus identity přiřazené systémem je přímo svázán s výpočetním clusterem. Pokud se výpočetní cluster odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.
* **Spravovaná identita přiřazená uživatelem** je samostatný prostředek Azure poskytovaný prostřednictvím spravované služby identity Azure. Uživatelsky přiřazenou identitu můžete přiřadit více prostředkům a trvá tak dlouho, dokud budete chtít.