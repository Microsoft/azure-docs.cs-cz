---
title: Místo naslouchacího procesu VNN pro skupiny dostupnosti na SQL Server virtuálních počítačích použijte naslouchací proces distribuovaných síťových názvů (DNN).
description: Zpráva pro přesměrování zákazníků pomocí naslouchacího procesu DNN místo naslouchacího procesu VNN
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167984"
---
> [!NOTE]
> Zákazníci, kteří SQL Server 2019 CU8 a novější ve Windows 2016 a novějším, můžou místo toho nahradit tradiční naslouchací proces VNN a Azure Load Balancer pomocí [naslouchacího procesu DNN (Distributed Network Name)](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md) . Přeskočte zbývající kroky v tomto článku, které vytvoří naslouchací proces a nástroj pro vyrovnávání zatížení.
