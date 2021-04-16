---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531624"
---
* **[Veřejná IPv4 adresa](../articles/virtual-network/public-ip-addresses.md#standard)Standard SKU**, pokud váš klient používá rozhraní API verze 2021-01-01-Preview nebo novější. Při nastavování virtuální sítě pro externí nebo interní přístup se vyžaduje prostředek veřejné IP adresy. V interní virtuální síti se veřejná IP adresa používá jenom pro operace správy. Přečtěte si další informace o [IP adresách API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * IP adresa musí být ve stejné oblasti a předplatném jako instance API Management a virtuální síť.

  * Hodnota IP adresy je přiřazena virtuální veřejné IPv4 adrese instance API Management v této oblasti. 

  * Při změně z vnější na interní virtuální síť (nebo naopak), změně podsítí v síti nebo při aktualizaci zón dostupnosti pro instanci API Management, je nutné nakonfigurovat jinou veřejnou IP adresu. 

  > [!IMPORTANT]
  > V současné době používá Azure Portal rozhraní API verze 2021-01-01 Preview při vytváření nebo aktualizaci instance API Management. Tuto verzi rozhraní API můžete zadat pomocí šablony Azure Resource Manager nebo REST API API Management. Rozhraní příkazového řádku Azure a Azure PowerShell aktuálně podporují rozhraní API verze 2020-12-01.
