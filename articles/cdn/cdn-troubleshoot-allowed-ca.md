---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS v Azure CDN
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte ho vytvořit pomocí povolené certifikační autority (CA).
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259966"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS v Azure CDN

Pokud [povolíte funkci HTTPS pomocí vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) pro vlastní doménu Azure Content DELIVERY Network (CDN), musíte splnit konkrétní požadavky na certifikát. **Azure CDN Standard z profilu Microsoft** vyžaduje certifikát od jednoho z schválených certifikačních AUTORIT (CA) v následujícím seznamu. Pokud se použije certifikát z neschválené certifikační autority nebo certifikát podepsaný svým držitelem, žádost se odmítne. **Azure CDN Standard od Verizon** a **Azure CDN Premium od profilů Verizon** akceptují libovolný platný certifikát od jakékoli platné certifikační autority.

> [!NOTE]
> Možnost použití vlastního certifikátu pro povolení funkce HTTPS vlastní *domény není k dispozici pro* **Azure CDN Standard z profilů Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

