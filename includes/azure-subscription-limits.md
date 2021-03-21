---
title: zahrnout soubor
description: zahrnout soubor
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562190"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| vCPU na [předplatné](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10 000 |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |200 |200 |
| [Účty úložiště](../articles/storage/common/storage-account-create.md) na předplatné<sup>2</sup> |100 |100 |
| [Cloudové služby](../articles/cloud-services/cloud-services-choose-me.md) na předplatné |20 |200 |
| [Místní sítě](/previous-versions/azure/reference/jj157100(v=azure.100)) na předplatné |10 |500 |
| Servery DNS na předplatné |9 |100 |
| Rezervované IP adresy na předplatné |20 |100 |
| [Skupiny vztahů](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) na předplatné |256 |256 |
| Délka názvu předplatného (ve znacích) | 64 | 64 |

<sup>1</sup> Velmi malé instance se počítají jako jeden vCPU k vCPU limitu, i když se používá částečný procesor.

<sup>2</sup> . Limit účtu úložiště zahrnuje účty úložiště úrovně Standard i Premium.