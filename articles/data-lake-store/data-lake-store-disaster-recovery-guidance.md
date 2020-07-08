---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Storage Gen1 | Microsoft Docs
description: Pokyny pro zotavení po havárii pro Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75966053"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 poskytuje místně redundantní úložiště (LRS). Data v Data Lake Storage Gen1m účtu jsou proto odolná proti přechodným chybám hardwaru v rámci datového centra prostřednictvím automatizovaných replik. Tím se zajistí odolnost a vysoká dostupnost, která splňuje Data Lake Storage Gen1 SLA. Tento článek poskytuje pokyny, jak dále chránit vaše data před vzácnými výpadky nebo neúmyslnými odstraněními v rámci oblastí.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Přečtěte si informace v tomto článku a sestavte si plán zotavení po havárii. Tady jsou některé prostředky, které vám můžou s vytvořením vlastního plánu pomoct.

* [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Osvědčené postupy
Doporučujeme zkopírovat kritická data do jiného Data Lake Storage Gen1 účtu v jiné oblasti s frekvencí zarovnaným na požadavky vašeho plánu zotavení po havárii. Pro kopírování dat existují nejrůznější způsoby včetně [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellu](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Pokud dojde k oblastnímu výpadku, můžete k datům přistupovat v oblasti, kde byla data zkopírována. Pokud chcete zjistit stav služby Azure po celém světě, můžete monitorovat [Azure Service Health řídicí panel](https://azure.microsoft.com/status/) .

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Pokyny pro obnovení v případě poškození nebo nechtěného odstranění dat
I když Data Lake Storage Gen1 zajišťuje odolnost dat prostřednictvím automatizovaných replik, nebrání vaší aplikaci (nebo vývojářům a uživatelům) v poškození dat nebo jejich nechtěnému odstranění.

### <a name="best-practices"></a>Osvědčené postupy
Abyste zabránili nechtěnému odstranění, doporučujeme, abyste nejdřív nastavili správné zásady přístupu pro svůj účet Data Lake Storage Gen1.  To zahrnuje použití [zámků prostředků Azure](../azure-resource-manager/management/lock-resources.md) k uzamčení důležitých prostředků a také k použití zabezpečení a řízení přístupu na úrovni souborů pomocí dostupných [funkcí zabezpečení Data Lake Storage Gen1](data-lake-store-security-overview.md). Doporučujeme také pravidelně vytvářet kopie důležitých dat pomocí [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) v jiném Data Lake Storage Gen1m účtu, složce nebo předplatném Azure.  Kopie pak můžete využít k obnovení poškozených nebo odstraněných dat. Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Organizace také můžou povolit [protokolování diagnostiky](data-lake-store-diagnostic-logs.md) pro svůj Data Lake Storage Gen1 účet ke shromáždění informací o auditu pro přístup k datům, které poskytují informace o tom, kdo mohl soubor odstranit nebo aktualizovat.

## <a name="next-steps"></a>Další kroky
* [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)

