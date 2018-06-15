---
title: Úvod do IP toku ověřit v sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje přehled IP sledovací proces sítě ověřte tok funkce
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181595"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Úvod do IP toku ověřit v sledovací proces sítě Azure

Tok IP ověřte kontroluje, zda paket povolený nebo zakázaný do nebo z virtuálního počítače. Informace se skládá z směr, protokol, místní IP, vzdálené IP, místního portu a vzdáleného portu. Pokud paketu je zakázané skupiny zabezpečení, je vrácen název pravidla, které odepřen paketu. Při lze zvolit všechny zdrojové i cílové adresy IP, IP tok ověření pomáhá správcům rychle diagnostikovat problémy s připojením z nebo na Internet a z nebo na místním prostředí.

Tok IP ověřte cílem síťové rozhraní virtuálního počítače. Tok přenosů dat je pak ověřit podle nakonfigurovaného nastavení do nebo z rozhraní sítě. Tok IP ověření je užitečné v potvrzení, pokud pravidla v skupinu zabezpečení sítě blokuje příchozí nebo odchozí provoz nebo z virtuálního počítače.

Instance sledovací proces sítě musí být vytvořen ve všech oblastech, které chcete spustit IP tok ověření. Sledovací proces sítě je služba, místní a může být spustili jenom s prostředky ve stejné oblasti. Instance, používá nemá vliv na výsledky IP tok ověření podle postupu spojeného s síťový adaptér je stále vrátit.

![1][1]

## <a name="next-steps"></a>Další postup

Najdete v následujícím článku se dozvíte, pokud je paket povolený nebo zakázaný pro konkrétní virtuální počítač prostřednictvím portálu. [Zkontrolovat, pokud provoz je povolené na virtuálním počítači s IP tok ověření pomocí portálu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












