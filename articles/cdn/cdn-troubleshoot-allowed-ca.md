---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS v Azure CDN
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte ho vytvořit pomocí povolené certifikační autority (CA).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083109"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS v Azure CDN

U vlastní domény služby Azure Content Delivery Network (CDN) na **Azure CDN Standard od** koncového bodu Microsoftu můžete při [Povolení funkce HTTPS pomocí vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)použít pro vytvoření certifikátu SSL povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

> [!NOTE]
> Možnost použití vlastního certifikátu pro povolení vlastního protokolu HTTPS je k dispozici pouze u **Azure CDN Standard z profilů společnosti Microsoft** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

