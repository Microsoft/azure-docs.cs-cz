---
title: Připojení dat Azure AD Identity Protection ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Azure AD Identity Protection dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 5dae8c4da46c750f69057f33d593f5bb7396a99e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882241"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Připojení dat z Azure AD Identity Protection



Můžete streamovat protokoly z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do Azure Sentinel a streamovat výstrahy do Azure Sentinel a zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Azure Active Directory Identity Protection poskytuje konsolidované zobrazení v rizikových uživatelích, detekci rizik a ohrožení zabezpečení, s možností okamžitě opravit riziko a nastavit zásady pro automatické napravení budoucích událostí. Služba je postavená na zkušenostech Microsoftu s ochranou identit spotřebitelů a díky signálům z více než 13 000 000 000 přihlášení denně získá obrovský přesnost. 


## <a name="prerequisites"></a>Předpoklady

- Musíte mít [licenci Azure Active Directory Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/) .
- Uživatel s oprávněními globálního správce nebo správce zabezpečení


## <a name="connect-to-azure-ad-identity-protection"></a>Připojení k Azure AD Identity Protection

Pokud už máte Azure AD Identity Protection, ujistěte se, že je [ve vaší síti povolená](../active-directory/identity-protection/overview-identity-protection.md).
Pokud je Azure AD Identity Protection nasazená a načítá data, můžou se data výstrah snadno streamovat do služby Azure Sentinel.


1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure AD Identity Protection** .

2. Kliknutím na **připojit** spustíte streamování Azure AD Identity Protection událostí do Azure Sentinel.


6. Pokud chcete pro Azure AD Identity Protection výstrahy použít příslušné schéma v Log Analytics, vyhledejte **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure AD Identity Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
