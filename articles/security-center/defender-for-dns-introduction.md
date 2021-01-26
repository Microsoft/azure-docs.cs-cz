---
title: Azure Defender pro DNS – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro DNS.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 957e39f7629337182c3e19a1a514c42883666301
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797000"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Seznámení s Azure Defenderem pro DNS

[Azure DNS](../dns/dns-overview.md) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí Microsoft Azure infrastruktury. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure Defender pro DNS poskytuje další vrstvu ochrany pro cloudové prostředky pomocí:

- Průběžné monitorování všech dotazů DNS z vašich prostředků Azure
- spuštění pokročilé analýzy zabezpečení, které vás upozorní na podezřelou aktivitu

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Stanov|**Azure Defender pro DNS** se fakturuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Jaké jsou výhody Azure Defenderu pro DNS?

Azure Defender pro službu DNS chrání před problémy, včetně:

- Exfiltrace dat z vašich prostředků Azure pomocí tunelování DNS
- Komunikace malwaru s C&serverem C
- Komunikace se škodlivými doménami jako phishing a kryptografická dolování
- Útoky DNS – komunikace se škodlivými Překladači DNS 

Úplný seznam výstrah poskytovaných službou Azure Defender pro službu DNS najdete na stránce s [referenčními údaji o výstrahách](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Závislosti

Azure Defender pro DNS nepoužívá žádné agenty. 

Pokud chcete chránit vrstvu DNS, povolte pro každé z vašich předplatných službu Azure Defender pro DNS, jak je popsáno v tématu [Povolení Azure Defenderu](security-center-pricing.md#enable-azure-defender).


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro DNS. Související materiály najdete v následujícím článku: 

- Výstrahy zabezpečení mohou být generovány Security Center nebo obdrženy Security Center z různých produktů zabezpečení. Pokud chcete všechny tyto výstrahy exportovat do Azure Sentinel, jakýchkoli SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Povolení Azure Defenderu](security-center-pricing.md#enable-azure-defender)