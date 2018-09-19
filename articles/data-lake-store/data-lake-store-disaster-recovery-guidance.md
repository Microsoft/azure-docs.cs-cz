---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pokyny k zotavení po havárii pro Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 44c1dc3e3f6c2c9af52a6e9c9320d4a8ba63b4d0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127104"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Pokyny pro zotavení po havárii pro data ve službě Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 poskytuje místně redundantní úložiště (LRS). Proto je datům v účtu Data Lake Storage Gen1 odolná vůči krátkodobému selhání hardwaru v datacentru díky automatizovaným replikám. Tím se zajistí odolnost a vysoká dostupnost splňující podmínky smlouva SLA pro Data Lake Storage Gen1. Tento článek obsahuje pokyny o tom, jak rozšířit ochranu vašich dat z výjimečných výjimečnými výpadky celé oblasti nebo nechtěným odstraněním.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Přečtěte si informace v tomto článku k sestavení plánu zotavení po havárii. Tady jsou některé prostředky, které vám můžou s vytvořením vlastního plánu pomoct.

* [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Osvědčené postupy
Doporučujeme kopírovat důležitá data do jiného účtu Data Lake Storage Gen1 v jiné oblasti s frekvencí odpovídá potřebám vašeho plánu zotavení po havárii. Pro kopírování dat existují nejrůznější způsoby včetně [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellu](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Pokud dojde k oblastnímu výpadku, můžete pak přístup k datům v oblasti, kam se zkopíroval data. Můžete monitorovat [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/) k určení stavu služby Azure po celém světě.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Pokyny pro obnovení v případě poškození nebo nechtěného odstranění dat
Zatímco služba Data Lake Storage Gen1 poskytuje odolnost dat prostřednictvím automatizovaných replik, přesto aplikace (nebo vývojářům/uživatelům) poškození nebo nechtěném odstranění.

### <a name="best-practices"></a>Osvědčené postupy
Abyste zabránili nechtěnému odstranění, doporučujeme nejdříve nastavit správné zásady přístupu k účtu Data Lake Storage Gen1.  To zahrnuje použití [ámků prostředků Azure](../azure-resource-manager/resource-group-lock-resources.md) k uzamčení důležitých prostředků a použití souborů a řízení přístupu na úrovni pomocí dostupných [funkcí zabezpečení Data Lake Storage Gen1](data-lake-store-security-overview.md). Doporučujeme také pravidelně vytvářet kopie důležitých dat pomocí [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [prostředí Azure PowerShell](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) v jiném Gen1 úložiště Data Lake účet, složku nebo předplatné Azure.  Kopie pak můžete využít k obnovení poškozených nebo odstraněných dat. Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Organizace také můžou povolit [protokolování diagnostiky](data-lake-store-diagnostic-logs.md) ke svému účtu Data Lake Storage Gen1 ke shromažďování záznamů pro audit přístupu k data, která poskytuje informace o kdo může odstranit nebo aktualizovat soubor.

## <a name="next-steps"></a>Další postup
* [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)

