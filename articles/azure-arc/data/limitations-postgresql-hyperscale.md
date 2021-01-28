---
title: Omezení PostgreSQL škálování s povoleným rozšířením Azure ARC
description: Omezení PostgreSQL škálování s povoleným rozšířením Azure ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 01/21/2021
ms.topic: how-to
ms.openlocfilehash: ecf3c1f0d553c6521178fa5e1f67fc1cf95de73e
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987057"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Omezení PostgreSQL škálování s povoleným rozšířením Azure ARC

Tento článek popisuje omezení PostgreSQL škálování s povoleným rozšířením Azure ARC. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Zálohování a obnovení

- Obnovení k určitému bodu v čase (například obnovení do konkrétního data a času) do stejné skupiny serverů není podporováno. Při obnovení určitého bodu v čase je nutné provést obnovení na jiné skupině serverů, kterou jste nasadili před obnovením. Po obnovení do nové skupiny serverů můžete skupinu serverů původu odstranit.
- Obnovení celého obsahu zálohy (na rozdíl od obnovení do konkrétního bodu v čase) na stejnou skupinu serverů je podporováno pro PostgreSQL verze 12. PostgreSQL verze 11 není podporována z důvodu omezení PostgreSQL modulu s časovými osami. Chcete-li obnovit celý obsah zálohy skupiny serverů PostgreSQL verze 11, je nutné ji obnovit do jiné skupiny serverů.


## <a name="databases"></a>Databáze

Hostování více než jedné databáze ve skupině serverů není podporováno.


## <a name="security"></a>Zabezpečení

Správa uživatelů a rolí není podporovaná. Prozatím pokračujte používáním standardního uživatele Postgres.

## <a name="roles-and-responsibilities"></a>Role a povinnosti

Role a zodpovědnosti mezi společností Microsoft a jejími zákazníky se liší mezi službami Azure PaaS (platforma jako služba) a hybridem Azure (například s povoleným PostgreSQLm škálováním Azure ARC). 

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

Následující tabulka shrnuje odpovědi na nejčastější dotazy týkající se rolí a zodpovědností podpory.

| Otázka                      | Platforma jako služba (PaaS) Azure | Hybridní služby Azure ARC |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Kdo poskytuje infrastrukturu?  | Microsoft                          | Zákazník                  |
| Kdo software poskytuje? *       | Microsoft                          | Microsoft                 |
| Kdo operace provádí? | Microsoft                          | Zákazník                  |
| Poskytuje Microsoft SLA?      | Yes                                | No                        |
| Kdo má za SLA? | Microsoft                          | Zákazník                  |

\* Služby Azure

__Proč společnost Microsoft neposkytuje SLA v Azure ARC Hybrid Services?__ Protože společnost Microsoft nevlastní infrastrukturu a nepracuje ji. Zákazníci.

## <a name="next-steps"></a>Další kroky

- **Vyzkoušejte** si to. Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure. 

- **Vytvořte si vlastní.** Pomocí těchto kroků vytvořte vlastní cluster Kubernetes: 
   1. [Instalace klientských nástrojů](install-client-tools.md)
   2. [Vytvoření kontroleru dat ARC Azure](create-data-controller.md)
   3. [Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Další informace o datových službách s podporou ARC Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Přečtěte si o ARC Azure](https://aka.ms/azurearc)
