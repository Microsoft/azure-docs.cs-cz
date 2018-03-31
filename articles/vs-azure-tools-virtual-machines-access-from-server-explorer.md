---
title: Přístup k Azure virtuální počítače z Průzkumníka serveru | Microsoft Docs
description: Get přehled o tom, jak zobrazit vytvářet a spravovat virtuální počítače Azure (VM) v Průzkumníku serveru v sadě Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: bbb52b07994ca796683bbcfe5a77ebb78df3bade
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Přístup k Azure virtuální počítače z Průzkumníka serveru

Pokud máte virtuální počítače hostované v Azure, můžete k dispozici v Průzkumníku serveru. Musíte nejprve se přihlásit k vašemu předplatnému Azure Chcete-li zobrazit mobilní služby. Pokud chcete přihlásit, otevřete místní nabídky pro Azure uzel v Průzkumníku serveru a zvolte **připojit k Microsoft Azure**.

1. V Průzkumníku cloudu vyberte virtuální počítač a potom vyberte klávesy F4 zobrazíte její vlastnosti – okno.

    Následující tabulka uvádí, které vlastnosti jsou k dispozici, ale jsou všechny jen pro čtení. Chcete-li změnit, použijte [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Vlastnost | Popis |
   | --- | --- |
   | Název DNS |Adresa URL s Internetovou adresu virtuálního počítače. |
   | Prostředí |Pro virtuální počítač hodnota této vlastnosti je vždy produkční. |
   | Název |Název virtuálního počítače. |
   | Velikost |Velikost virtuálního počítače, které odráží množství paměti a místa na disku, je k dispozici. Další informace najdete v tématu [velikostí virtuálních počítačů](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Status |Hodnoty zahrnují spouštění, spuštěno, zastavit, zastaveno a načítání stavu. Pokud se zobrazí stav načítání, aktuální stav neznámý. Hodnoty pro tuto vlastnost se liší od hodnoty, které jsou použity na [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | ID předplatného |ID předplatného pro váš účet Azure. Tyto informace můžete zobrazit na [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) zobrazením vlastností pro předplatné. |
2. Zvolte do uzlu koncového bodu a následně zobrazit **vlastnosti** okno.
3. Následující tabulka popisuje dostupné vlastnosti koncových bodů, ale jsou jen pro čtení. Chcete-li přidat nebo upravit koncové body pro virtuální počítač, použijte [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Vlastnost | Popis |
   | --- | --- |
   | Název |Identifikátor pro koncový bod. |
   | Privátní Port |Port pro přístup k síti interní do vaší aplikace. |
   | Protocol (Protokol) |Protokol, který používá přenosové vrstvy pro tento koncový bod, TCP nebo UDP. |
   | Veřejný port |Port, který se používá pro veřejný přístup k vaší aplikaci. |
