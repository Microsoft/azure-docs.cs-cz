---
title: Konfigurace nastavení exportu v rozhraní API Azure pro FHIR
description: Tento článek popisuje, jak nakonfigurovat nastavení exportu v rozhraní Azure API pro FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: bb728b81d4dad5d880c27d1ebe8f85f1508f8231
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018094"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurace nastavení exportu a nastavení účtu úložiště

Azure API pro FHIR podporuje příkaz $export, který umožňuje exportovat data z Azure API pro účet FHIR do účtu úložiště.

Při konfiguraci exportu v rozhraní Azure API pro FHIR se účastní tři kroky:

1. Povolení spravované identity v rozhraní Azure API pro službu FHIR
2. Vytvoření účtu Azure Storage (Pokud se ještě neudělalo) a přiřazení oprávnění rozhraní Azure API pro FHIR k účtu úložiště
3. Výběr účtu úložiště v Azure API pro FHIR jako export účtu úložiště

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Povolení spravované identity v rozhraní API Azure pro FHIR

Prvním krokem při konfiguraci rozhraní API Azure pro FHIR pro export je povolení spravované identity systému v rámci služby. Všechny spravované identity v Azure si můžete přečíst [tady](../../active-directory/managed-identities-azure-resources/overview.md).

Provedete to tak, že přejdete do Azure API pro službu FHIR a vyberete okno identita. Když změníte stav na zapnuto, povolí se spravovaná identita v rozhraní Azure API pro službu FHIR.

![Povolit spravovanou identitu](media/export-data/fhir-mi-enabled.png)

Nyní můžeme přejít na další krok a vytvořit účet úložiště a přiřadit mu oprávnění k naší službě.

## <a name="adding-permission-to-storage-account"></a>Přidává se oprávnění k účtu úložiště.

Dalším krokem exportu je přiřazení oprávnění pro službu Azure API pro službu FHIR k zápisu do účtu úložiště.

Po vytvoření účtu úložiště přejděte na okno Access Control (IAM) v účtu úložiště a vyberte přidat přiřazení rolí.

![Exportovat přiřazení role](media/export-data/fhir-export-role-assignment.png)

Tady pak přidáte přispěvatele dat objektů BLOB úložiště rolí k našemu názvu služby.

![Přidat roli](media/export-data/fhir-export-role-add.png)

Nyní jsme připraveni k dalšímu kroku, kde můžeme vybrat účet úložiště v Azure API pro FHIR jako výchozí účet úložiště pro $export.

## <a name="selecting-the-storage-account-for-export"></a>Výběr účtu úložiště pro $export

Posledním krokem je přiřazení účtu Azure Storage, který Azure API for FHIR použije k exportu dat do. Provedete to tak, že přejdete do okna integrace v Azure API pro službu FHIR v Azure Portal a vyberete účet úložiště.

![Úložiště exportu FHIR](media/export-data/fhir-export-storage.png)

Poté, co jsme připraveni k exportu dat pomocí příkazu $export.

>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)
