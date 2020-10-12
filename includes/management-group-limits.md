---
title: zahrnout soubor
description: zahrnout soubor
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748746"
---
| Prostředek | Omezení |
| --- | --- |
| Skupiny pro správu na tenanta Azure AD | 10 000 |
| Předplatná na skupinu pro správu | Počet. |
| Úrovně hierarchie skupiny pro správu | Kořenová úroveň plus 6 úrovní<sup>1</sup> |
| Přímá nadřazená skupina pro správu na jednu skupinu pro správu | Jednu |
| [Nasazení na úrovni skupiny pro správu](../articles/azure-resource-manager/templates/deploy-to-management-group.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Úrovně 6 neobsahují úroveň předplatného.

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Chcete-li odstranit nasazení na úrovni skupiny pro správu, použijte [příkaz Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) nebo [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
