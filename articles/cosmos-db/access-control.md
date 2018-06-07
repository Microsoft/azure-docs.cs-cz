---
title: Nastavení řízení přístupu v Azure Cosmos DB | Microsoft Docs
description: Informace o nastavení řízení přístupu v Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 2dae2b6291aa7ce18cc6f612b25cd5bdcc1ba753
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611139"
---
# <a name="access-control-in-azure-cosmos-db"></a>Řízení přístupu v Azure Cosmos DB

Chcete-li přidat Azure Cosmos DB účet čtečky přístupu pro váš uživatelský účet, máte vlastník předplatného, proveďte následující kroky na portálu Azure.

1. Otevřete portál Azure a vyberte svůj účet Azure Cosmos DB.
2. Klikněte **přístup k ovládacímu prvku (IAM)** a pak klikněte **+ přidat**.
3. V **přidat oprávnění** podokně, v **Role** vyberte **Cosmos DB účet čtečky Role**.
4. V **přiřazení přístup k poli**, vyberte **uživatele Azure AD, skupinu nebo aplikaci**.
5. Vyberte uživatele, skupiny nebo aplikace ve vašem adresáři, do které chcete udělit přístup.  Můžete vyhledat v adresáři zobrazované jméno, e-mailovou adresu nebo identifikátory objektů.
    Vybrané uživatele, skupiny nebo aplikace se zobrazí v seznamu vybraných členů.
6. Klikněte na **Uložit**.

Entita může načíst prostředky Azure Cosmos DB.
