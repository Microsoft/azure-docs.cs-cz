---
title: Povolená certifikační autorita pro povolení vlastního protokolu HTTPS v Azure CDN
description: Pokud používáte vlastní certifikát k povolení protokolu HTTPS ve vlastní doméně, musíte k jeho vytvoření použít povolenou certifikační autoritu.If you are use own certificate to enable HTTPS on a own domain, you must use a allowed certificate authority (CA) to create it.
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
ms.openlocfilehash: 81d2209c4b76db685e5a8d2625c84469d5c3dc43
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985843"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Povolené certifikační autority pro povolení vlastního protokolu HTTPS v Azure CDN

Při povolení funkce HTTPS musíte splnit konkrétní požadavky na certifikát [pomocí vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) pro vlastní doménu sítě pro doručování obsahu Azure (CDN). Azure **CDN Standard od** společnosti Microsoft vyžaduje certifikát od jednoho ze schválených certifikačních úřadů (CA) v následujícím seznamu. Pokud je certifikát od neschválenécertifikační autority nebo certifikát podepsaný svým držitelem použit, je žádost odmítnuta. **Profily Azure CDN Standard od Verizonu** a **Azure CDN Premium od společnosti Verizon** přijímají jakýkoli platný certifikát od libovolné platné certifikační autority.

> [!NOTE]
> Možnost použití vlastního certifikátu k povolení funkce HTTPS vlastní domény *není* k dispozici pro Azure CDN Standard z profilů **Akamai.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

