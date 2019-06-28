---
title: Povolené certifikačních autorit pro povolení vlastní protokol HTTPS ve službě Azure CDN | Dokumentace Microsoftu
description: Pokud používáte vlastní certifikát pro povolení HTTPS pro vlastní doménu, musíte použít povolené certifikační autority (CA) k jeho vytvoření.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 8ce141fbf3d767159d16495bff5a93d791224c17
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331887"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Pro povolení vlastní protokol HTTPS ve službě Azure CDN povolená certifikační autority

Pro vlastní doménu Azure Content Delivery Network (CDN) na **Azure CDN Standard od společnosti Microsoft** koncový bod, když jste [povolení funkce HTTPS s použitím vlastní certifikát](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), je nutné použít povolený certifikační autorita (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

> [!NOTE]
> Možnost používat vlastní certifikát pro povolení HTTPS pro vlastní je k dispozici pouze s **Azure CDN Standard od společnosti Microsoft** profily. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

