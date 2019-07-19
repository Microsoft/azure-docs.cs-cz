---
title: Připojit Cloud App Security dat ke službě Azure Sentinel Preview | Microsoft Docs
description: Přečtěte si, jak připojit Cloud App Security dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881086"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat z Microsoft Cloud App Security 

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí jediného kliknutí můžete streamovat protokoly z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinel. Toto připojení umožňuje streamovat výstrahy z Cloud App Security do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení

## <a name="connect-to-cloud-app-security"></a>Připojení k Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud je Cloud App Security nasazená a ingestují vaše data, můžou se data výstrah do Azure Sentinel snadno streamovat.


1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Cloud App Security** .

2. Klikněte na **Připojit**.

3. Pokud chcete pro Cloud App Security výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit Microsoft Cloud App Security ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
