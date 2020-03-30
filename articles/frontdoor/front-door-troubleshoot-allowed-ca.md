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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471519"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS na azure front door

Pro vlastní doménu Azure Front Door při [povolení funkce HTTPS pomocí vlastního certifikátu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), musíte použít povolenou certifikační autoritu (CA) k vytvoření certifikátu SSL. V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, bude vaše žádost zamítnuta.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
