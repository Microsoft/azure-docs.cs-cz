---
title: zahrnout soubor
description: zahrnout soubor
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334785"
---
| Prostředek | Omezení |
| --- | --- |
| Skupiny pro správu na adresář | 10 000 |
| Předplatná na skupinu pro správu | Počet. |
| Úrovně hierarchie skupiny pro správu | Kořenová úroveň plus 6 úrovní<sup>1</sup> |
| Přímá nadřazená skupina pro správu na jednu skupinu pro správu | Jeden |
| [Nasazení na úrovni skupiny pro správu](../articles/azure-resource-manager/templates/deploy-to-management-group.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Úrovně 6 neobsahují úroveň předplatného.

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Chcete-li odstranit nasazení na úrovni skupiny pro správu, použijte [příkaz Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) nebo [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
