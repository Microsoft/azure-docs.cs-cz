---
title: Upgrade na účet úložiště pro obecné účely v2 – Azure Storage | Dokumentace Microsoftu
description: Upgradovat na účty úložiště pro obecné účely v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 8a0b823a12178df56417b05de28c2125ec155829
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740814"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgradovat na účet úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporuje nejnovější funkce služby Azure Storage a zapracovali všechny funkce pro obecné účely v1 a účty Blob storage. Účty pro obecné účely v2 se doporučuje pro většinu scénářů úložiště. Účty pro obecné účely v2 doručování nejnižší podle sazby za gigabajt kapacity ceny pro Azure Storage, jakož i průmysl konkurenceschopných cen za transakce.

Upgrade na účet úložiště pro obecné účely verze 2 z pro obecné účely v1 a účty úložiště Blob je jednoduché. Můžete upgradovat pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure. Upgrade účtu je nevratná a může vést k účtování poplatků.

## <a name="upgrade-using-the-azure-portal"></a>Upgrade s využitím webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V **nastavení** klikněte na tlačítko **konfigurace**.
4. V části **Druh účtu** klikněte na **Upgradovat**.
5. V části **Potvrdit upgrade** zadejte název svého účtu. 
6. Klikněte na tlačítko **upgradovat** v dolní části okna.

## <a name="upgrade-with-powershell"></a>Upgrade pomocí PowerShellu

Pokud chcete upgradovat účet pro obecné účely v1 na účet pro obecné účely verze 2 pomocí Powershellu, nejprve aktualizujte PowerShell, které chcete používat nejnovější verzi **AzureRm.Storage** modulu. Informace o instalaci PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

Pak zavolejte následující příkaz pro upgrade účet, kde nahradíte název vaší skupiny prostředků a účet úložiště:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Upgrade pomocí Azure CLI

Pokud chcete upgradovat účet pro obecné účely v1 na účet pro obecné účely verze 2 pomocí Azure CLI, nejprve nainstalujte nejnovější verzi Azure CLI. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Pak zavolejte následující příkaz pro upgrade účet, kde nahradíte název vaší skupiny prostředků a účet úložiště:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Zadat vrstvu přístupu k datům objektu blob

Účty pro obecné účely v2 podporují všechny služby Azure storage a datové objekty, ale jsou k dispozici pouze pro objekty BLOB bloku v úložišti objektů Blob úrovně přístupu. Při upgradu na účet úložiště pro obecné účely v2, můžete určit úroveň přístupu pro data objektů blob. 

Vrstva přístupu umožňují zvolit cenově nejvýhodnější úložiště založené na vaše postupy očekávané využití. Objekty BLOB bloku můžou být uložené v horké, studené a archivní úrovně. Další informace o úrovních přístupu najdete v části [Azure Blob storage: horká, studená a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

Ve výchozím nastavení v horká vrstva přístupu je vytvořen nový účet úložiště a účet úložiště pro obecné účely v1 je upgradovat na horká vrstva přístupu. Pokud zkoumáte jaké úroveň přístupu pro vaše data po upgradu, zvažte možnost váš scénář. Existují dva běžné uživatelské scénáře pro migraci na účet pro obecné účely verze 2:

* Máte stávající účet úložiště pro obecné účely v1 a chcete vyhodnotit přechod na účet úložiště pro obecné účely verze 2 s vhodnou úrovní úložiště pro data objektů blob.
* Rozhodli jste se použít účet úložiště pro obecné účely verze 2 nebo již máte a chcete vyhodnotit, jestli si vybrat horkou nebo studenou úroveň pro data objektů blob.

V obou případech je hlavní prioritou odhad nákladů na ukládání, přístup k a provozování na datech uložených v účtu úložiště pro obecné účely v2 a jejich porovnání s aktuálními náklady.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Odhadnout náklady za současného využití

Pokud chcete zjistit přibližnou cenu za ukládání a přístup k datům objektu blob v účtu úložiště pro obecné účely v2 v konkrétní úroveň, vyhodnotit svůj aktuální vzor používání nebo přibližný stanovili. Celkově vzato potřebujete vědět:

* Objekt Blob spotřebu úložiště, v gigabajtech, včetně:
    - Kolik dat se v účtu úložiště ukládá?
    - Jak se mění objem dat měsíčně? Nahrazují nová data neustále stará data?
* Vzor primární přístupový objekt Blob úložiště dat, včetně:
    - Kolik dat se čte a zapisuje do účtu úložiště? 
    - Počet operací čtení a zápisu operace, ke kterým došlo u dat v účtu storage?

Při rozhodování o nejlepší úroveň přístupu pro vaše potřeby, může být užitečné, chcete-li určit, jakou kapacitu se aktuálně používá data objektů blob a jak tato data používá. 

Pokud chcete shromažďovat data o využití pro váš účet úložiště, před migrací, můžete sledovat, účtu úložiště pomocí [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor provádí protokolování a poskytuje data metriky pro služby Azure, včetně služby Azure Storage. 

Pokud chcete monitorovat spotřebu dat pro objekty BLOB v účtu úložiště, povolte kapacitní metriky ve službě Azure Monitor. Kapacitní metriky zaznamenat data o tom, jak velké úložiště na každý den používají objekty BLOB ve vašem účtu. Kapacitní metriky je možné pro odhad nákladů na ukládání dat v účtu úložiště. Informace o tom, jaké je kapacita úložiště objektů Blob ceny pro každý typ účtu, najdete v článku [ceny za objekty blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pokud chcete monitorovat vzory přístupu k datům pro úložiště objektů Blob, povolte metriku transakcí ve službě Azure Monitor. Můžete filtrovat podle různých operací služby Azure Storage odhadnout, jak často se nazývá každý. Další informace naleznete v tématu jak různé typy transakcí se počítají pro bloku a doplňovacích objektů blob pro každý typ účtu, [ceny za objekty blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Další informace o shromažďování metrik ze služby Azure Monitor, naleznete v tématu [metrik Azure Storage ve službě Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Další postup

- [vytvořit účet úložiště](storage-quickstart-create-account.md)
- [Správa účtů služby Azure storage](storage-account-manage.md)