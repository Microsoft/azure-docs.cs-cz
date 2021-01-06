---
title: Připojení účtu Azure dosah 
description: Připojte účet Azure dosah k pracovnímu prostoru synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918733"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Rychlý Start: připojení účtu Azure dosah k pracovnímu prostoru synapse 

> [!IMPORTANT]
> Integrace mezi Azure synapse Analytics a Azure dosah je v současnosti ve verzi Preview. Pokud vás zajímá dosah Azure v synapse, připojte se prosím k prodejnímu zástupci Microsoftu.

V tomto rychlém startu zaregistrujete účet Azure dosah do pracovního prostoru synapse. Toto připojení umožňuje vyhledat prostředky Azure dosah a pracovat s nimi prostřednictvím možností synapse. 

V synapse můžete provádět následující úlohy: 
- Pomocí vyhledávacího pole v horní části můžete najít dosah assety na základě klíčových slov. 
- Pochopení dat na základě metadat, řádků a poznámek 
- Připojte tato data k pracovnímu prostoru pomocí propojených služeb nebo datových sad Integration. 
- Analýza těchto datových sad pomocí synapse Apache Spark, synapse SQL a toku dat 

## <a name="prerequisites"></a>Požadavky 
- [Účet Azure dosah](../../purview/create-catalog-portal.md) 
- [Pracovní prostor synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Přihlášení k pracovnímu prostoru synapse 

Přejdete do https://web.azuresynapse.net svého pracovního prostoru a přihlásíte se k němu. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Oprávnění k připojení účtu Azure dosah 

- K připojení účtu Azure dosah k pracovnímu prostoru synapse potřebujete roli **Přispěvatel** v pracovním prostoru Synapse z Azure Portal IAM a potřebujete přístup k tomuto účtu Azure dosah.

## <a name="connect-an-azure-purview-account"></a>Připojení účtu Azure dosah  

- V pracovním prostoru synapse si Projděte **Správa**  ->  **Azure dosah**. Vyberte **připojit k účtu dosah**. 
- Můžete si vybrat **z předplatného Azure** nebo **zadat ručně**. **Z předplatného Azure** můžete vybrat účet, ke kterému máte přístup. 
- Po připojení byste měli být schopni zobrazit název účtu dosah na kartě **dosah účtu Azure**. 
- K hledání dat můžete použít panel hledání v horním středu pracovního prostoru synapse. 

## <a name="nextsteps"></a>Další kroky 

[Registrace a kontrola prostředků Azure synapse v Azure dosah](../../purview/register-scan-azure-synapse-analytics.md)

[Zjišťování, připojení a zkoumání dat v synapse pomocí Azure dosah](how-to-discover-connect-analyze-azure-purview.md)   