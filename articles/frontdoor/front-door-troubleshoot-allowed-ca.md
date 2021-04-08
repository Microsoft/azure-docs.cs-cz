---
title: Povolená certifikační autorita pro povolení vlastního HTTPS na frontách Azure
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně služby Azure front-dveří, musíte k jeho vytvoření použít povolenou certifikační autoritu (CA).
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613675"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Povolené certifikační autority pro povolení vlastního HTTPS na frontách Azure

Když [povolíte funkci HTTPS pomocí vlastního certifikátu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) pro vlastní doménu služby Azure front dveří. K vytvoření certifikátu TLS/SSL potřebujete povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
