---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717121"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure premium storage: návrh pro vysoký výkon

Tento článek obsahuje pokyny pro vytváření vysoce výkonné aplikace využívající Azure Premium Storage. Můžete použít pokyny uvedené v tomto dokumentu v kombinaci s osvědčené postupy z hlediska výkonu pro technologie, které používá vaše aplikace. Pro ilustraci, pokyny, jsme pomocí SQL serveru běžícího na Premium Storage jako příklad v tomto dokumentu.

Když jsme situacích výkonu pro vrstvy úložiště v tomto článku, je potřeba optimalizovat aplikační vrstvu. Například pokud hostujete Sharepointovou farmu na Azure Premium Storage, můžete použít SQL Server příklady v tomto článku optimalizovat databázového serveru. Kromě toho Optimalizujte webový server farmy služby SharePoint a aplikační server většina výkon.

V tomto článku se vám pomohou zodpovědět následující běžné otázky týkající se optimalizace výkonu aplikace na Azure Premium Storage

* Postupy: měření výkonu vaší aplikace?  
* Proč se zobrazuje očekávaný vysoký výkon?  
* Faktory, které ovlivňují výkon vašich aplikací ve službě Storage úrovně Premium?  
* Jak tyto faktory mají vliv na výkon vaší aplikace ve službě Storage úrovně Premium?  
* Optimalizace můžete pro vstupně-výstupních operací, šířky pásma a latencí?  

Nabízíme tyto pokyny konkrétně pro Premium Storage, protože úlohy běžící ve službě Storage úrovně Premium jsou vysoce citlivé na výkon. Uvádíme příklady, kde je to vhodné. Můžete také použít některé z těchto pokynů pro aplikace běžící na virtuálních počítačích IaaS s disky Storage úrovně Standard.