---
title: Konfigurace nastavení exportu v rozhraní API Azure pro FHIR
description: Tento článek popisuje, jak nakonfigurovat nastavení exportu v rozhraní Azure API pro FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046971"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurace nastavení exportu a nastavení účtu úložiště

Azure API pro FHIR podporuje příkaz $export, který umožňuje exportovat data z Azure API pro účet FHIR do účtu úložiště.

Při konfiguraci exportu v rozhraní Azure API pro FHIR se účastní tři kroky:

1. Povolte spravovanou identitu na Azure API pro službu FHIR.
2. Vytvoření účtu Azure Storage (Pokud se ještě neudělalo) a přiřazení oprávnění rozhraní Azure API pro FHIR k účtu úložiště.
3. Výběr účtu úložiště v Azure API pro FHIR jako export účtu úložiště.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Povolení spravované identity v rozhraní API Azure pro FHIR

Prvním krokem při konfiguraci rozhraní API Azure pro FHIR pro export je povolení spravované identity systému v rámci služby. Další informace o spravovaných identitách v Azure najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Provedete to tak, že přejdete do Azure API pro službu FHIR a vyberete **Identita**. Když změníte stav na **zapnuto** , povolí se spravovaná identita v rozhraní Azure API pro službu FHIR.

![Povolit spravovanou identitu](media/export-data/fhir-mi-enabled.png)

Nyní můžete přejít k dalšímu kroku vytvořením účtu úložiště a přiřazením oprávnění naší službě.

## <a name="adding-permission-to-storage-account"></a>Přidává se oprávnění k účtu úložiště.

Dalším krokem v exportu je přiřazení oprávnění pro službu Azure API pro službu FHIR k zápisu do účtu úložiště.

Po vytvoření účtu úložiště v účtu úložiště klikněte na **Access Control (IAM)** a vyberte **Přidat přiřazení role**.

![Exportovat přiřazení role](media/export-data/fhir-export-role-assignment.png)

Tady je, že do názvu služby přidáte **přispěvatele dat objektů BLOB úložiště** rolí a pak vyberete **Uložit**.

![Přidat roli](media/export-data/fhir-export-role-add.png)

Teď jste připraveni vybrat účet úložiště v Azure API pro FHIR jako výchozí účet úložiště pro $export.

## <a name="selecting-the-storage-account-for-export"></a>Výběr účtu úložiště pro $export

Posledním krokem je přiřazení účtu Azure Storage, který Azure API for FHIR použije k exportu dat do. Provedete to tak, že přejdete na **integrace** v Azure API pro službu FHIR a vyberete účet úložiště.

![Úložiště exportu FHIR](media/export-data/fhir-export-storage.png)

Po dokončení tohoto závěrečného kroku teď můžete data exportovat pomocí příkazu $export.

> [!Note]
> Jako cíl pro $export operace se smějí registrovat jenom účty úložiště ve stejném předplatném, jako je pro Azure API pro FHIR.

Další informace o konfiguraci nastavení databáze, řízení přístupu, povolení protokolování diagnostiky a použití vlastních hlaviček k přidávání dat do protokolů auditu najdete v těchto tématech:

>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)
