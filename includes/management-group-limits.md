---
title: zahrnout soubor
description: zahrnout soubor
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800118"
---
| Prostředek | Omezení |
| --- | --- |
| Skupiny pro správu na tenanta Azure AD | 10 000 |
| Předplatná na skupinu pro správu | Počet. |
| Úrovně hierarchie skupiny pro správu | Kořenová úroveň plus 6 úrovní<sup>1</sup> |
| Přímá nadřazená skupina pro správu na jednu skupinu pro správu | Jednu |
| [Nasazení na úrovni skupiny pro správu](../articles/azure-resource-manager/templates/deploy-to-management-group.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Úrovně 6 neobsahují úroveň předplatného.

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Chcete-li odstranit nasazení na úrovni skupiny pro správu, použijte [příkaz Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) nebo [AZ Deployment mg Delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
