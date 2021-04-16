---
title: Vytvoření uživatelem definovaného bodu obnovení pro vyhrazený fond SQL
description: Naučte se používat Azure Portal k vytvoření bodu obnovení definovaného uživatelem pro vyhrazený fond SQL ve službě Azure synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567923"
---
# <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

V tomto článku se naučíte, jak vytvořit nový bod obnovení definovaný uživatelem pro vyhrazený fond SQL ve službě Azure synapse Analytics pomocí Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Vytvoření bodů obnovení definovaných uživatelem pomocí Azure Portal

Uživatelem definované body obnovení lze také vytvořit prostřednictvím Azure Portal.

1. Přihlaste se ke svému účtu [Azure Portal](https://portal.azure.com/) .

2. Přejděte do vyhrazeného fondu SQL, pro který chcete vytvořit bod obnovení.

3. V levém podokně vyberte **Přehled** a vyberte **+ nový bod obnovení**. Pokud není vybráno tlačítko nový bod obnovení, ujistěte se, že vyhrazený fond SQL není pozastaven.

    ![Nový bod obnovení](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Zadejte název pro uživatelem definovaný bod obnovení a klikněte na **použít**. Uživatelem definované body obnovení mají výchozí dobu uchování sedm dní.

    ![Název bodu obnovení](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího vyhrazeného fondu SQL](restore-sql-pool.md)

