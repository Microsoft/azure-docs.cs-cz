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
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026424"
---
 Azure Machine Learning výpočetní clustery také podporují [spravované identity](../articles/active-directory/managed-identities-azure-resources/overview.md) pro ověřování přístupu k prostředkům Azure bez zahrnutí přihlašovacích údajů do kódu. Existují dva typy spravovaných identit:

* **Spravovaná identita přiřazená systémem** je povolena přímo na Azure Machine Learning výpočetním clusteru. Životní cyklus identity přiřazené systémem je přímo svázán s výpočetním clusterem. Pokud se výpočetní cluster odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.
* **Spravovaná identita přiřazená uživatelem** je samostatný prostředek Azure poskytovaný prostřednictvím spravované služby identity Azure. Uživatelsky přiřazenou identitu můžete přiřadit více prostředkům a trvá tak dlouho, dokud budete chtít.