---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS na dveřích Azure Front Door
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte k jeho vytvoření použít povolenou certifikační autoritu.If you are use own certificate to enable HTTPS on a own domain, you must use a allowed certificate authority (CA) to create it.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874666"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS na azure front door

Pro vlastní doménu Azure Front Door při [povolení funkce HTTPS pomocí vlastního certifikátu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)musíte k vytvoření certifikátu TLS/SSL použít povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, bude vaše žádost zamítnuta.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
