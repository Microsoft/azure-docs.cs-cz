---
title: Připojení účtu Azure Purview 
description: Připojte účet Azure dosah k pracovnímu prostoru synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 55f0d2e8df36cc11f26c5ff6259ebe2215aaffc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880538"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Rychlý Start: připojení účtu Azure dosah k pracovnímu prostoru synapse 


V tomto rychlém startu zaregistrujete účet Azure dosah do pracovního prostoru synapse. Toto připojení umožňuje vyhledat prostředky Azure dosah a pracovat s nimi prostřednictvím možností synapse. 

V synapse můžete provádět následující úlohy: 
- Pomocí vyhledávacího pole v horní části můžete najít dosah assety na základě klíčových slov. 
- Pochopení dat na základě metadat, řádků a poznámek 
- Připojte tato data k pracovnímu prostoru pomocí propojených služeb nebo datových sad Integration. 
- Analýza těchto datových sad pomocí synapse Apache Spark, synapse SQL a toku dat 

## <a name="prerequisites"></a>Předpoklady 
- [Účet Azure dosah](../../purview/create-catalog-portal.md) 
- [Pracovní prostor synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Přihlášení k pracovnímu prostoru synapse 

Přejdete do  [https://web.azuresynapse.net](https://web.azuresynapse.net) svého pracovního prostoru a přihlásíte se k němu. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Oprávnění k připojení účtu Azure dosah 

- K připojení účtu Azure dosah k pracovnímu prostoru synapse potřebujete roli **Přispěvatel** v pracovním prostoru Synapse z Azure Portal IAM a potřebujete přístup k tomuto účtu Azure dosah. Další podrobnosti najdete v tématu věnovaném [oprávněním Azure dosah](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Připojení účtu Azure Purview  

- V pracovním prostoru synapse si Projděte **Správa**  ->  **Azure dosah**. Vyberte **připojit k účtu dosah**. 
- Můžete si vybrat **z předplatného Azure** nebo **zadat ručně**. **Z předplatného Azure** můžete vybrat účet, ke kterému máte přístup. 
- Po připojení byste měli být schopni zobrazit název účtu dosah na kartě **dosah účtu Azure**. 
- K hledání dat můžete použít panel hledání v horním středu pracovního prostoru synapse. 

## <a name="nextsteps"></a>Další kroky 

[Registrace a kontrola prostředků Azure synapse v Azure dosah](../../purview/register-scan-azure-synapse-analytics.md)

[Zjišťování, připojování a zkoumání dat ve službě Synapse s využitím Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
