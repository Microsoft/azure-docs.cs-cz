---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS
titleSuffix: Azure Content Delivery Network
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte ho vytvořit pomocí povolené certifikační autority (CA).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573394"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS

Pokud [povolíte funkci HTTPS pomocí vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) pro vlastní doménu Azure CDN (Content Delivery Network), vyžadují se konkrétní požadavky na certifikát. 

* **Azure CDN Standard z profilu Microsoft** vyžaduje certifikát od jednoho z schválených certifikačních AUTORIT (CA) v následujícím seznamu. Pokud se použije certifikát z neschválené certifikační autority nebo certifikát podepsaný svým držitelem, žádost se odmítne. 

* **Azure CDN Standard od Verizon** a **Azure CDN Premium od profilů Verizon** akceptují libovolný platný certifikát od jakékoli platné certifikační autority. Profily Verizon nepodporují certifikáty podepsané svým držitelem.

> [!NOTE]
> Možnost použití vlastního certifikátu pro povolení funkce HTTPS vlastní *domény není k dispozici pro* **Azure CDN Standard z profilů Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

