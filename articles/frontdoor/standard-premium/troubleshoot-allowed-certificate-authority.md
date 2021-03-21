---
title: Povolené certifikační autority pro Azure přední dveře Standard/Premium (Preview)
description: V tomto článku jsou uvedené všechny certifikační autority, které jsou povolené při vytváření vlastního certifikátu.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434862"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Povolené certifikační autority pro Azure přední dveře Standard/Premium (Preview)

Pokud povolíte funkci HTTPS pomocí vlastního certifikátu pro vlastní doménu Azure front-dveří Standard/Premium. K vytvoření certifikátu TLS/SSL potřebujete povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
