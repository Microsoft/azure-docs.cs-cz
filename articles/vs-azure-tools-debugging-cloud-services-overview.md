---
title: Možnosti pro ladění Azure cloud services | Dokumentace Microsoftu
description: Ladění cloudové služby Azure
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: e4ca0f710257530a05acdec2b240ae2870d4fa80
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969304"
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Informace o různých způsobech ladění cloudové služby Azure
Tento článek obsahuje odkazy na různé způsoby ladění cloudové služby Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Ladění cloudové služby Azure v sadě Visual Studio
Můžete ušetřit čas a peníze pomocí webu Azure compute emulátoru k ladění cloudové služby na místním počítači. Ladění služby místně, než ho nasadíte, můžete zlepšit výkon a spolehlivost bez nutnosti platit za výpočetní čas. Nicméně některé může dojít k chybám pouze v případě, že provozujete cloudovou službu v Azure. Povolením vzdáleného ladění při publikování služby a potom připojování ladicího programu k instanci role můžete ladit chyby, ke kterým dochází pouze v případě, že provozujete cloudovou službu v Azure. Další informace najdete v tématu [ladění cloudové služby na místním počítači](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Pomocí IntelliTrace 
Pokud používáte Visual Studio Enterprise pro zápis role cílových rozhraní .NET Framework 4.5, můžete povolit IntelliTrace v době nasazení cloudové služby Azure ze sady Visual Studio. IntelliTrace poskytuje protokol, který vám pomůže s Visual Studio pro ladění vaší aplikace, jako kdyby byly spuštěné v Azure. Další informace najdete v tématu [ladění publikované cloudové služby pomocí nástroje IntelliTrace a sady Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Vzdálené ladění 
Můžete povolit vzdálené ladění cloudových služeb v době, kdy nasadit cloudovou službu v sadě Visual Studio. Pokud budete chtít povolit vzdálené ladění pro nasazení, nainstaluje se na virtuálních počítačích, na kterých běží instance každé role vzdálené ladění. Tyto služby – například `msvsmon.exe` – nebudou mít vliv na výkon nebo způsobit dodatečné náklady. Další informace najdete v tématu [ladění cloudové služby v Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Další postup
- [Ladění Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) – další podrobnosti o tom, k ladění cloudových služeb Azure.