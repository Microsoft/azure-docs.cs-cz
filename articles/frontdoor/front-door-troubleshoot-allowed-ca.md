---
title: Povolená certifikační autorita pro povolení vlastního HTTPS na frontách Azure
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte ho vytvořit pomocí povolené certifikační autority (CA).
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399119"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Povolené certifikační autority pro povolení vlastního HTTPS na frontách Azure

U vlastní domény služby Azure front-dveří můžete při [Povolení funkce HTTPS pomocí vlastního certifikátu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)použít pro vytvoření certifikátu TLS/SSL povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
