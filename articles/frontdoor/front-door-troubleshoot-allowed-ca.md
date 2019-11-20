---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS ve službě Azure front-dveří
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte ho vytvořit pomocí povolené certifikační autority (CA).
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
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184586"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS ve službě Azure front-dveří

U vlastní domény služby front-dveří Azure, když [povolíte funkci protokolu HTTPS pomocí vlastního certifikátu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), musíte k vytvoření certifikátu SSL použít povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
