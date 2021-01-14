---
title: Podpora pro 32 operační systémy ve virtuálních počítačích Azure | Microsoft Docs
description: Informace o operačních systémech, které jsou podporované na virtuálních počítačích Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 1b801bac28b131ff43c47024649bfe0986f768c7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202891"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Podpora 32bitových operačních systémů ve virtuálních počítačích Azure

Microsoft Azure teď umožňuje uživatelům do Azure přenést do svých 32 operačních systémů Windows. Podporované jsou jenom specializované virtuální pevné disky a v Azure nebudou fungovat generalizované image. Vzhledem k tomu, že některé z těchto operačních systémů již dosáhly smlouvy o podpoře na konci životního cyklu, společnost Microsoft nenabízí další podporu pro tyto operační systémy. Podpora se také nenabízí pro operační systémy založené na systému Linux nebo pro distribuci na platformě BSD (Berkeley Software Distribution), které běží na Microsoft Azurem virtuálním počítači (VM).

> [!NOTE]
> Platforma Azure má omezení adresního prostoru paměti na virtuálních počítačích, na kterých běží operační systémy 32, kde je k dispozici jenom 1 GB paměti pro virtuální počítač a zbytek paměti pro virtuální počítač se zobrazí jako rezervovaný v rámci virtuálního počítače hosta. Jedná se o známý problém a v současné době ještě nemáte k dispozici žádnou opravu. Doporučujeme přesunout se na 64bitovou verzi operačního systému.
> 

## <a name="more-information"></a>Další informace

Další informace o operačních systémech, které jsou podporované na virtuálních počítačích Azure, najdete v následujících článcích znalostní báze Microsoft Knowledge Base:

* [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Podpora Linuxu a open source technologií v Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Reference

* [Další informace o bezplatných rozšířených aktualizacích zabezpečení pro Windows Server 2008/R2 v Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Přečtěte si další informace o podpoře systému Windows Server 2008 SP2 32 – specializované image v Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Další informace o podpoře migrace imagí Windows serveru 2008 do Azure pomocí Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Další informace o podporovaných operačních systémech rozšíření Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Další informace o používání Windows serveru 2003 na Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a Stack Overflow fórech](https://azure.microsoft.com/support/forums/).

Případně můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
