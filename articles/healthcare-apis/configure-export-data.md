---
title: Konfigurace nastavení exportu v rozhraní API Azure pro FHIR
description: Tento článek popisuje, jak nakonfigurovat nastavení exportu v rozhraní Azure API pro FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871803"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Nakonfigurujte nastavení exportu a exportujte data do účtu úložiště.

Azure API pro FHIR podporuje příkaz $export, který umožňuje exportovat data z Azure API pro účet FHIR do účtu úložiště.

Při exportu v rozhraní Azure API pro FHIR je potřeba provést čtyři kroky:

1. Povolení spravované identity v rozhraní Azure API pro službu FHIR
2. Vytvoření účtu Azure Storage (Pokud se ještě neudělalo) a přiřazení oprávnění rozhraní Azure API pro FHIR k účtu úložiště
3. Výběr účtu úložiště v Azure API pro FHIR jako export účtu úložiště
4. Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Povolení spravované identity v rozhraní API Azure pro FHIR

Prvním krokem při konfiguraci rozhraní API Azure pro FHIR pro export je povolení spravované identity systému v rámci služby. Všechny spravované identity v Azure si můžete přečíst [tady](../active-directory/managed-identities-azure-resources/overview.md).

Provedete to tak, že přejdete do Azure API pro službu FHIR a vyberete okno identita. Když změníte stav na zapnuto, povolí se spravovaná identita v rozhraní Azure API pro službu FHIR.

![Povolit spravovanou identitu](media/export-data/fhir-mi-enabled.png)

Nyní můžeme přejít na další krok a vytvořit účet úložiště a přiřadit mu oprávnění k naší službě.

## <a name="adding-permission-to-storage-account"></a>Přidává se oprávnění k účtu úložiště.

Dalším krokem exportu je přiřazení oprávnění pro službu Azure API pro službu FHIR k zápisu do účtu úložiště.

Po vytvoření účtu úložiště přejděte na okno Access Control (IAM) v účtu úložiště a vyberte přidat přiřazení rolí.

![Povolit spravovanou identitu](media/export-data/fhir-export-role-assignment.png)

Tady pak přidáte přispěvatele dat objektů BLOB úložiště rolí k našemu názvu služby.

![Povolit spravovanou identitu](media/export-data/fhir-export-role-add.png)

Nyní jsme připraveni k dalšímu kroku, kde můžeme vybrat účet úložiště v Azure API pro FHIR jako výchozí účet úložiště pro $export.

## <a name="selecting-the-storage-account-for-export"></a>Výběr účtu úložiště pro $export

Posledním krokem před vyvoláním $export příkazu je přiřazení účtu služby Azure Storage, který Azure API pro FHIR použije k exportu dat do. Provedete to tak, že přejdete do okna integrace v Azure API pro službu FHIR v Azure Portal a vyberete účet úložiště. 

![Povolit spravovanou identitu](media/export-data/fhir-export-storage.png)

Poté, co jsme připraveni k exportu dat pomocí příkazu $export.

## <a name="exporting-the-data-using-export-command"></a>Export dat pomocí příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export teď můžeme použít příkaz $export a vyexportovat data ze služby do zadaného účtu úložiště. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke specifikaci $export na adrese[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Všimněte si, že v současné době Azure API pro FHIR podporuje pouze export na úrovni systému, jak je definováno ve specifikaci exportu v [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . V současnosti také nepodporujeme parametry dotazů s $export.

>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)