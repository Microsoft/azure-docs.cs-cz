---
title: Připojení dat Azure AD Identity Protection k Azure Sentinel Preview | Microsoft Docs
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129261"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Připojení dat z Azure AD Identity Protection

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete streamovat protokoly z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do Azure Sentinel a streamovat výstrahy do Azure Sentinel a zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Azure Active Directory Identity Protection poskytuje konsolidované zobrazení v rizikových uživatelích, detekci rizik a ohrožení zabezpečení, s možností okamžitě opravit riziko a nastavit zásady pro automatické napravení budoucích událostí. Služba je postavená na zkušenostech Microsoftu s ochranou identit spotřebitelů a díky signálům z více než 13 000 000 000 přihlášení denně získá obrovský přesnost. 


## <a name="prerequisites"></a>Požadavky

- Musíte mít [licenci Azure Active Directory Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/) .
- Uživatel s oprávněními globálního správce nebo správce zabezpečení


## <a name="connect-to-azure-ad-identity-protection"></a>Připojení k Azure AD Identity Protection

Pokud už máte Azure AD Identity Protection, ujistěte se, že je [ve vaší síti povolená](../active-directory/identity-protection/enable.md).
Pokud je Azure AD Identity Protection nasazená a načítá data, můžou se data výstrah snadno streamovat do služby Azure Sentinel.


1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure AD Identity Protection** .

2. Kliknutím na **připojit** spustíte streamování Azure AD Identity Protection událostí do Azure Sentinel.


6. Pokud chcete pro Azure AD Identity Protection výstrahy použít příslušné schéma v Log Analytics, vyhledejte **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit Azure AD Identity Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
