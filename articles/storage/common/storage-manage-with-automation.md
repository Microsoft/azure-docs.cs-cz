---
title: Správa služby Azure Storage pomocí Azure Automation
description: Další informace o používání služby Azure Automation ke správě služby Azure Storage ve velkém měřítku.
services: storage, automation
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/10/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: 0cf8a1ee90757b9a89e7659282b2fd52fa436dea
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245068"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Správa služby Azure Storage pomocí Azure Automation
Tato příručka vás seznámí s služby Azure Automation a jak ho lze zjednodušit správu objektů BLOB, tabulek a front Azure Storage.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesu. Pomocí Azure Automation, dlouhotrvajících, ruční, problematických a často opakovaných úloh je možné automatizovat zvyšuje spolehlivost a efektivitu a zkrácení doby uvedení na hodnotu pro vaši organizaci.

Azure Automation poskytuje spolehlivé a vysoce dostupného prováděcího modulu, která se škáluje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesy můžete být spuštěna ručně, systémy třetích stran nebo v naplánovaných intervalech tak, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT / pracovníci DevOps a zaměřte se na práci, která vytváří obchodní hodnota přesunutím úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Jak Azure Automation pomáhá spravovat Azure Storage?
Úložiště Azure můžete spravovat ve službě Azure Automation prostřednictvím rutin prostředí PowerShell, které jsou k dispozici v [prostředí Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Tak, aby provedením všech objektů blob, tabulky a fronty úloh správy ve službě Azure Automation obsahuje tyto rutiny prostředí PowerShell úložiště k dispozici hned po spuštění. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémech třetích stran.

[v galerii runbooků Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) obsahuje produkt týmu a komunity sady runbook, chcete-li začít s automatizací správy Azure Storage, dalších služeb Azure a systémech třetích stran. Galerie runbooků patří:

* [Odebrat objekty BLOB Azure Storage, které jsou některé dní staré pomocí služby Automation](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Stažení objektu Blob ze služby Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Zálohovat všechny disky pro jeden virtuální počítač Azure nebo pro všechny virtuální počítače v cloudové službě](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby Azure Automation a jak jej lze spravovat objekty BLOB, tabulky a fronty Azure Storage, použijte tyto odkazy na další informace o službě Azure Automation.

Projděte si kurz služby Azure Automation [vytvoření nebo import runbooku ve službě Azure Automation](../../automation/automation-creating-importing-runbook.md).

