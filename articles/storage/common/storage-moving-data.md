---
title: Přesun velkých objemů dat do a z cloudového úložiště v Azure | Dokumentace Microsoftu
description: Přehled různých metod pro přesun dat do a z Azure Storage.
services: storage
author: JarrettRenshaw
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.component: common
ms.openlocfilehash: 81d7b5cf03e56ecc54db71b09af335d6cb794806
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525531"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Přesunutí dat z Azure Storage a do Azure Storage
Pokud chcete pro přesun místních dat do služby Azure Storage (nebo naopak), existuje široká škála způsobů, jak to provést. Si přístup, který vám bude nejlépe vyhovovat bude záviset na vaší situaci. Tento článek poskytuje stručný přehled různých scénářů a příslušné nabídky pro každé z nich.

## <a name="building-applications"></a>Vytváření aplikací
Pokud už vytváříte aplikace, vývoj s využitím rozhraní REST API nebo jednu z našich mnoho klientské knihovny je skvělým řešením pro přesun dat do a z Azure Storage.

Azure Storage poskytuje množství knihoven klienta pro rozhraní .NET, iOS, Java, Android, univerzální platforma Windows (UPW), Xamarin, C++, Node.JS, PHP, Ruby a Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Zobrazit [Začínáme s Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) Další informace.

Kromě toho nabízíme taky [knihovna pro přesun dat úložiště Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) tedy knihovny určená pro vysoce výkonné kopírování dat do a z Azure. Naše knihovna pro přesun dat najdete [dokumentaci](https://github.com/Azure/azure-storage-net-data-movement) Další informace. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rychlé zobrazení dat a interakce s nimi
Pokud chcete snadný způsob, jak zobrazit vaše data služby Azure Storage přitom má také možnost nahrávat a stahovat data, zvažte použití Azure Storage Explorer.

Projděte si náš seznam [průzkumníky úložiště Azure](../storage-explorers.md) Další informace.

## <a name="system-administration"></a>Správa systému
Pokud vyžaduje nebo jsou lépe seznámíte s nástrojem příkazového řádku (např. Správci systému), jsou zde několik možností, které byste měli zvážit:

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený pro vysoce výkonné kopírování dat do a ze služby Azure Storage. Můžete také zkopírovat data v rámci účtu úložiště nebo mezi jiný účet úložiště. AzCopy je k dispozici na [Windows](storage-use-azcopy.md) a na [Linux](storage-use-azcopy-linux.md).

Zobrazit [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) nebo [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md) Další informace.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell je modul, který nabízí rutiny pro správu služeb v Azure. Je to prostředí příkazového řádku založené na úlohách a skriptovací jazyk určený speciálně pro správu systému.

Zobrazit [pomocí Azure Powershellu s Azure Storage](storage-powershell-guide-full.md) Další informace.

### <a name="azure-cli"></a>Azure CLI
Rozhraní příkazového řádku Azure nabízí sadu open source příkazů pro různé platformy pro práci se službami Azure. Azure CLI je k dispozici ve Windows, os x a Linuxu.

Zobrazit [pomocí Azure CLI s Azure Storage](../storage-azure-cli.md) Další informace.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Přesun velkých objemů dat s pomalou síť
Jednou z největších problémů, které jsou přidružené k přesunu velkých objemů dat je doba přenosu. Pokud chcete načíst data do a z úložiště Azure bez starostí o nákladech sítě nebo psaní kódu, Azure Import/Export je vhodné řešení.

Zobrazit [Azure Import/Export](../storage-import-export-service.md) Další informace.

## <a name="backing-up-your-data"></a>Zálohování dat
Pokud potřebujete pouze k zálohování dat do služby Azure Storage, Azure Backup je způsob, jak přejít. Toto je výkonné řešení pro zálohování místních dat a virtuální počítače Azure.

Zobrazit [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) Další informace.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Přístup k vaší data dostupná místně i z cloudu
Pokud budete potřebovat řešení, pro přístup k vaší data dostupná místně i z cloudu, měli zvážit, použití Azure řešení hybridního cloudového úložiště, StorSimple. Toto řešení se skládá z fyzického zařízení StorSimple, jestli inteligentně úložiště často používá data na disky SSD, občas používají data na HDD a neaktivní/zálohování nebo archivaci dat ve službě Azure Storage.

Zobrazit [StorSimple](../../storsimple/storsimple-overview.md) Další informace.

## <a name="recovering-your-data"></a>Obnovení dat
Pokud máte místní úlohy a aplikace, budete potřebovat řešení, které umožňuje firmě dál běžet v případě havárie. Azure Site Recovery zpracovává replikace, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Replikovaná data jsou uložena ve službě Azure Storage umožňuje eliminovat potřebu sekundárního datacentra.

Zobrazit [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) Další informace.
### <a name="moving-data-faq"></a>Přesun dat – nejčastější dotazy:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Můžu migrovat virtuální pevné disky z jedné oblasti do druhého bez kopírování?
Jediný způsob, jak zkopírovat virtuální pevné disky oblasti se má kopírovat data mezi účty úložiště v jednotlivých oblastech. To můžete pomocí nástroje AZCopy. Podívejte se na přenos dat pomocí nástroje příkazového řádku Azcopy Další informace. Pro opravdu velké objemy dat můžete také Azure Import/Export. Zobrazit [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) Další informace.
