---
title: Nastavení řízení přístupu v Azure Cosmos DB | Microsoft Docs
description: Informace o nastavení řízení přístupu v Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 740f1ca560207ada95dd03fbecc7f9940ee7b2a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
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
