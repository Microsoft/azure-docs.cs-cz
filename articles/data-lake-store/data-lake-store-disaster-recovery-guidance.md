---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Store | Microsoft Docs
description: Pokyny k zotavení po havárii pro Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9a2705a2372ea4f2fbea1e27bf675679dfda28f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Pokyny pro zotavení po havárii pro dat v Data Lake Store

Azure Data Lake Store poskytuje místně redundantní úložiště (LRS). Data ve vašem účtu Azure Data Lake Store je proto odolné vůči selhání hardwaru v rámci oblasti prostřednictvím automatizované repliky. Tím se zajišťuje odolnost a vysoká dostupnost splňující podmínky smlouvy SLA pro Azure Data Lake Store. Tento článek obsahuje pokyny o tom, jak další data chránit před výjimečných celou oblast výpadků nebo náhodného odstranění.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Přečtěte si informace v tomto článku k vytvoření vašeho plánu zotavení po havárii. Tady jsou některé prostředky, které vám můžou s vytvořením vlastního plánu pomoct.

* [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Osvědčené postupy
Doporučujeme kopírovat důležitá data do jiného účtu Data Lake Store v jiné oblasti tak často, jak odpovídá potřebám vašeho plánu zotavení po havárii. Pro kopírování dat existují nejrůznější způsoby včetně [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellu](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Pokud dojde k výpadku oblasti, máte pak k datům přístup v oblasti, kam se data zkopírovala. Stav služeb Azure na celém světě můžete sledovat prostřednictvím [řídicího panelu stavu Azure](https://azure.microsoft.com/status/).

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Pokyny pro obnovení v případě poškození nebo nechtěného odstranění dat
I když Azure Data Lake Store zajišťuje odolnost dat prostřednictvím automatizovaných replik, nemůže vaší aplikaci (nebo vývojářům/uživatelům) zabránit v poškození nebo nechtěném odstranění dat.

### <a name="best-practices"></a>Osvědčené postupy
Aby se zabránilo nechtěnému odstranění, doporučujeme nejdříve nastavit správné zásady přístupu k účtu Data Lake Store.  To zahrnuje použití [ámků prostředků Azure](../azure-resource-manager/resource-group-lock-resources.md) k uzamčení důležitých prostředků a také použití řízení přístupu na úrovni souborů a rolí pomocí dostupných [funkcí zabezpečení služby Data Lake Store](data-lake-store-security-overview.md). Doporučujeme také pravidelně vytvářet kopie důležitých dat pomocí [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellu](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) v jiném účtu Data Lake Store, složce nebo předplatném Azure.  Kopie pak můžete využít k obnovení poškozených nebo odstraněných dat. Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Organizace také můžou povolit [protokolování diagnostiky](data-lake-store-diagnostic-logs.md) pro účet Azure Data Lake Store ke shromažďování záznamů pro audit přístupu k datům. Ty poskytují informace o tom, kdo mohl soubor odstranit nebo aktualizovat.

## <a name="next-steps"></a>Další postup
* [Začínáme s Azure Data Lake Storem](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)

