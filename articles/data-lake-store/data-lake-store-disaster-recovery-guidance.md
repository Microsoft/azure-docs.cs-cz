---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966053"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 poskytuje místně redundantní úložiště (LRS). Proto data ve vašem účtu Data Lake Storage Gen1 je odolná vůči přechodným selháním hardwaru v rámci datového centra prostřednictvím automatizovaných replik. To zajišťuje odolnost a vysokou dostupnost, splnění Data Lake Storage Gen1 SLA. Tento článek obsahuje pokyny, jak dále chránit data před výpadky v celé oblasti nebo náhodným odstraněním.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii
Je důležité, aby si každý zákazník připravil vlastní plán zotavení po havárii. Přečtěte si informace v tomto článku k vytvoření plánu zotavení po havárii. Tady jsou některé prostředky, které vám můžou s vytvořením vlastního plánu pomoct.

* [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Osvědčené postupy
Doporučujeme zkopírovat důležitá data do jiného účtu Data Lake Storage Gen1 v jiné oblasti s frekvencí zarovnanou s potřebami vašeho plánu zotavení po havárii. Pro kopírování dat existují nejrůznější způsoby včetně [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellu](data-lake-store-get-started-powershell.md) nebo [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Pokud dojde k místnímu výpadku, můžete získat přístup k datům v oblasti, kde byla data zkopírována. Řídicí panel [stavu služby Azure](https://azure.microsoft.com/status/) můžete sledovat a určit stav služby Azure po celém světě.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Pokyny pro obnovení v případě poškození nebo nechtěného odstranění dat
Zatímco Data Lake Storage Gen1 poskytuje odolnost proti chybám dat prostřednictvím automatizovaných replik, to nebrání vaší aplikaci (nebo vývojáři/uživatelé) z poškození dat nebo jejich náhodné odstranění.

### <a name="best-practices"></a>Osvědčené postupy
Chcete-li zabránit nechtěnému odstranění, doporučujeme nejprve nastavit správné zásady přístupu pro váš účet Data Lake Storage Gen1.  To zahrnuje použití [uzamčení prostředků Azure](../azure-resource-manager/management/lock-resources.md) k uzamčení důležitých prostředků a také použití řízení přístupu na úrovni účtu a souboru pomocí dostupných [funkcí zabezpečení Data Lake Storage Gen1](data-lake-store-security-overview.md). Doporučujeme také, abyste běžně vytvářeli kopie důležitých dat pomocí [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShellnebo](data-lake-store-get-started-powershell.md) [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) v jiném účtu, složce, složce nebo předplatném Azure úložiště datového jezera.  Kopie pak můžete využít k obnovení poškozených nebo odstraněných dat. Azure Data Factory je užitečná služba pro opakované vytváření a nasazování kanálů k přesunu dat.

Organizace mohou také povolit [diagnostické protokolování](data-lake-store-diagnostic-logs.md) pro svůj účet Data Lake Storage Gen1 ke shromažďování stop auditu přístupu k datům, které poskytují informace o tom, kdo mohl odstranit nebo aktualizovat soubor.

## <a name="next-steps"></a>Další kroky
* [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)

