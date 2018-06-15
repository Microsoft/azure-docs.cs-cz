---
title: Spravovat úložiště Azure pomocí Azure Automation.
description: Další informace o používání služby Azure Automation ke správě úložiště Azure ve velkém měřítku.
services: storage, automation
documentationcenter: ''
author: jodoglevy
manager: eamono
editor: ''
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873797"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Správa úložiště Azure pomocí Azure Automation.
Tento průvodce vás seznámí s služba Azure Automation a jak může sloužit ke zjednodušení správy úložiště Azure BLOB, tabulek a front.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Služby Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušenou správu cloudu pomocí Automatizace procesu. Pomocí Azure Automation, dlouhotrvajících, ruční, problematických a často se opakujících úloh je možné automatizovat zvýšit spolehlivost a efektivitu a zkrátit čas na hodnotu pro vaši organizaci.

Azure Automation nabízí modul provádění vysoce spolehlivé a vysoce dostupné pracovního postupu, který rozšiřuje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesů může být spuštěna ručně, 3. stran systémy, nebo v naplánovaných intervalech tak, aby úlohy dojít přesně v případě potřeby.

Nižší provozní režie a uvolněte IT / DevOps zaměstnanci a zaměřit se na práci, kterou přidá obchodní value přesunutím vašeho cloudu spuštění úloh správy se automaticky automatizace Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Jak Azure Automation pomoci spravovat úložiště Azure?
Úložiště Azure lze spravovat ve službě Azure Automation pomocí rutin prostředí PowerShell, které jsou k dispozici v [prostředí Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Služby Azure Automation má tyto rutiny prostředí PowerShell úložiště k dispozici předinstalované, aby mohli provést všech objektů blob, tabulky a fronty úloh správy v rámci služby. Může také párovat tyto rutiny ve službě Azure Automation pomocí rutin pro jinými službami Azure, na automatizují komplexní úlohy v služeb Azure a systémech 3. stran.

[Galerie runbooků automatizace Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) obsahuje celou řadu produktu team a komunita runbooky tak, aby okamžitě začít s automatizací správy Azure Storage, ostatní služby Azure a systémech 3. stran. Galerie runbooků patří:

* [Odebrání úložiště objektů BLOB Azure, které jsou některé dní starý pomocí služby Automation](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Objekt Blob stažení ze služby Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Zálohovat všechny disky pro jeden virtuální počítač Azure nebo pro všechny virtuální počítače v cloudové službě](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Azure Automation a jak může sloužit ke správě objektů BLOB, tabulek a front Azure Storage, postupujte podle následujících odkazech na další informace o Azure Automation.

Zobrazit kurz Azure Automation [vytvoření nebo import runbooku ve službě Azure Automation](../../automation/automation-creating-importing-runbook.md).

