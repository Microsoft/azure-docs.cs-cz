---
title: Nastavení řízení přístupu ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak nastavit řízení přístupu ve službě Azure Cosmos DB.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055226"
---
# <a name="access-control-in-azure-cosmos-db"></a>Řízení přístupu ve službě Azure Cosmos DB

Přidat přístup čtenáře účtu služby Azure Cosmos DB s vaším uživatelským účtem, jste vlastníkem předplatného, proveďte následující kroky na webu Azure Portal.

1. Otevřete na webu Azure portal a vyberte svůj účet služby Azure Cosmos DB.
2. Klikněte na tlačítko **řízení přístupu (IAM)** kartu a potom klikněte na tlačítko **+ přidat**.
3. V **přidat oprávnění** podokno v **Role** vyberte **Role čtenáře účtu Cosmos DB**.
4. V **přiřadit přístup k poli**vyberte **uživatele Azure AD, skupinu nebo aplikaci**.
5. Vyberte uživatele, skupinu nebo aplikaci ve vašem adresáři, ke kterému chcete udělit přístup.  Zobrazované jméno, e-mailovou adresu nebo identifikátory objektu v adresáři můžete vyhledávat.
    Vybraný uživatel, skupina nebo aplikace se zobrazí v seznamu vybraných členů.
6. Klikněte na **Uložit**.

Entita může načíst prostředky Azure Cosmos DB.
