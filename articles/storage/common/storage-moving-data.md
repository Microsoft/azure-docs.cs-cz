---
title: Přesunutí velké objemy dat do nebo ze cloudové úložiště v Azure | Microsoft Docs
description: Přehled různé metody pro přesun dat do a z Azure Storage.
services: storage
documentationcenter: ''
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: 980e4675c2d2e88716a3133abb027988aecd538f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984591"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Přesunutí dat z Azure Storage a do Azure Storage
Pokud chcete přesunout místní data do služby Azure Storage (nebo naopak), existuje mnoho různých způsobů, jak to udělat. Přístup, který nejlépe vyhovuje bude záviset na váš scénář. Tento článek vám poskytne rychlý přehled o různých scénářů a příslušné nabídky pro každé z nich.

## <a name="building-applications"></a>Tvorba aplikací
Pokud vytváříte aplikace, vývoj rozhraní REST API nebo jeden z našich mnoho klientské knihovny je skvělým způsobem, jak přesun dat do a ze služby Azure Storage.

Azure Storage poskytuje množství knihoven klienta pro rozhraní .NET, iOS, Java, Android, univerzální platformu Windows (UWP), Xamarin, C++, Node.JS, PHP, Ruby a Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

V tématu [Začínáme s Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) Další informace.

Kromě toho mají k dispozici [knihovna pro přesun dat úložiště Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) tedy knihovny určená pro vysoce výkonné kopírování dat do a z Azure. Naleznete v našem knihovna pro přesun dat [dokumentace](https://github.com/Azure/azure-storage-net-data-movement) Další informace. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rychlé zobrazení dat a interakce s nimi
Pokud chcete snadný způsob, jak zobrazit data Azure Storage přitom také má schopnost nahrávání a stahování dat, pak zvažte použití Azure Storage Explorer.

Podívejte se na naše seznam [Průzkumníci úložiště Azure](../storage-explorers.md) Další informace.

## <a name="system-administration"></a>Správa systému
Pokud vyžadují nebo jsou pohodlnější pomocí nástroje příkazového řádku (např. Správci systému), jsou zde je třeba zvážit několik možností:

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený pro vysoce výkonné kopírování dat do a z Azure Storage. Můžete také zkopírovat data v rámci účtu úložiště nebo mezi jiným účtům úložiště. AzCopy je k dispozici na [Windows](storage-use-azcopy.md) a na [Linux](storage-use-azcopy-linux.md).

V tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) nebo [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md) Další informace.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell je modul, který nabízí rutiny pro správu služeb v Azure. Je to prostředí příkazového řádku založené na úlohách a skriptovací jazyk určený speciálně pro správu systému.

V tématu [použití Azure Powershellu s Azure Storage](storage-powershell-guide-full.md) Další informace.

### <a name="azure-cli"></a>Azure CLI
Rozhraní příkazového řádku Azure poskytuje sadu softwaru open source, příkazy a platformy pro práci se službami Azure. Rozhraní příkazového řádku Azure je dostupná v systému Windows, na OSX a Linux.

V tématu [použití Azure CLI s Azure Storage](../storage-azure-cli.md) Další informace.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Přesunutí velkých objemů dat s pomalou síť
Jednou z největších problémů, které jsou přidružené k přesunu velkých objemů dat je dobu přenosu. Pokud chcete získat data z úložiště Azure bez starostí o náklady na sítě nebo psaní kódu, Azure Import/Export je vhodné řešení.

V tématu [Azure Import/Export](../storage-import-export-service.md) Další informace.

## <a name="backing-up-your-data"></a>Zálohování dat
Pokud potřebujete jednoduše zálohování dat do služby Azure Storage, Azure Backup je způsob, jak přejděte. Toto je výkonné řešení pro zálohování místních dat. a virtuálních počítačích Azure.

V tématu [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) Další informace.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Přístup k vaší data místně i z cloudu
Pokud potřebujete řešení pro přístup k vaší data místně i z cloudu, pak měli byste zvážit použití Azure hybridní řešení cloudového úložiště, StorSimple. Toto řešení se skládá z fyzického zařízení StorSimple, že inteligentně úložiště často používá data na jednotkách SSD, občas používají data na HDD a neaktivní nebo zálohování nebo archivaci dat v úložišti Azure.

V tématu [StorSimple](../../storsimple/storsimple-overview.md) Další informace.

## <a name="recovering-your-data"></a>Obnovení dat
Pokud máte místní úlohy a aplikace, budete potřebovat řešení, které umožní váš podnik dál běžet v případě havárie. Azure Site Recovery zpracovává replikace, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Replikovaná data jsou uložena ve službě Azure Storage, umožňuje eliminují nutnost použití sekundárního datového centra na místě.

V tématu [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) Další informace.
### <a name="moving-data-faq"></a>Přesun dat – nejčastější dotazy:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Můžete migrovat virtuální pevné disky z jedné oblasti do druhého bez kopírování?
Jediný způsob, jak zkopírovat virtuální pevné disky mezi oblast je kopírovat data mezi účty úložiště na každou oblast. To můžete pomocí nástroje AZCopy. V části přenosu dat pomocí nástroje příkazového řádku Azcopy Další informace. Pro velmi velké objemy dat můžete také Azure Import/Export. V tématu [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) Další informace.
