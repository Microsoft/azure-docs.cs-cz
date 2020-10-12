---
title: Připojení dat Azure AD Identity Protection k Azure Sentinel
description: Naučte se streamovat protokoly a výstrahy z Azure AD Identity Protection do Azure Sentinel a zobrazovat řídicí panely, vytvářet vlastní výstrahy a vylepšit šetření.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564471"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Připojení dat z Azure Active Directory (Azure AD) Identity Protection

Můžete streamovat protokoly z [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) do Azure Sentinel a streamovat výstrahy do Azure Sentinel a zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Azure Active Directory Identity Protection poskytuje konsolidované zobrazení v rizikových uživatelích, detekci rizik a ohrožení zabezpečení, s možností okamžitě opravit riziko a nastavit zásady pro automatické napravení budoucích událostí. Služba je postavená na zkušenostech Microsoftu s ochranou identit spotřebitelů a díky signálům z více než 13 000 000 000 přihlášení denně získá obrovský přesnost. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít [předplatné Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- Musíte mít uživatele s oprávněními globálního správce nebo správce zabezpečení.


## <a name="connect-to-azure-ad-identity-protection"></a>Připojení k Azure AD Identity Protection

Pokud máte předplatné Azure AD Premium P2, zahrnuje Azure AD Identity Protection. Pokud [jsou nějaké zásady povolené](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) a generují výstrahy, můžou se data výstrah do Azure Sentinel snadno streamovat.

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure AD Identity Protection** .

1. Kliknutím na **připojit** spustíte streamování Azure AD Identity Protection událostí do Azure Sentinel.

1. Pokud chcete pro Azure AD Identity Protection výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.

Pokud chcete konektor otestovat, můžete [simulovat detekce](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) a vygenerovat ukázkové výstrahy, které budou streamovat do služby Azure Sentinel.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure AD Identity Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
