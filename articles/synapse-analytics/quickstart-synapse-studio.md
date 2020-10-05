---
title: 'Rychlý Start: použití synapse studia'
description: V tomto rychlém startu uvidíte a naučíte se, jak snadné je dotazování různých typů souborů pomocí synapse studia.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: a80a87cf595ff8f47d1f14d50cd3af0a1519b694
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260389"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Rychlý Start: použití synapse Studio (Preview)

V tomto rychlém startu se dozvíte, jak zadávat dotazy na soubory pomocí synapse studia.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Předpoklady

[Vytvořte pracovní prostor Azure synapse a přidružený účet úložiště](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Spuštění funkce Synapse Studio

V pracovním prostoru Azure synapse v Azure Portal klikněte na **Spustit synapse Studio**.

![Spuštění funkce Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Alternativně můžete spustit synapse Studio kliknutím na [Azure synapse Analytics](https://web.azuresynapse.net) a zadáním příslušného tenanta, předplatného a hodnot pracovního prostoru.

## <a name="browse-storage-accounts"></a>Procházet účty úložiště

Po otevření synapse studia přejděte na **data** a potom rozbalte **účty úložiště** , abyste viděli účet úložiště v pracovním prostoru.

![Procházet soubory v úložišti](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Můžete vytvářet nové složky a nahrávat soubory pomocí odkazů na panelu nástrojů k uspořádání souborů.

## <a name="query-files-on-storage-account"></a>Dotazování souborů na účet úložiště

> [!IMPORTANT]
> Aby `Storage Blob Reader` bylo možné zadávat dotazy na soubory, musíte být členem role v základním úložišti. Přečtěte si, jak [přiřadit oprávnění RBAC pro nástroj **pro čtení dat objektů BLOB úložiště** nebo **přispěvatele úložiště** v Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role).

1. Nahrajte některé `PARQUET` soubory.
2. Vyberte jeden nebo více souborů a pak vytvořte nový skript SQL nebo Poznámkový blok Spark pro zobrazení obsahu souborů. Pokud chcete vytvořit Poznámkový blok, budete muset vytvořit [fond Apache Spark v pracovních prostorech synapse](quickstart-create-apache-spark-pool-studio.md).

   ![Dotazování souborů na úložiště](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Spuštěním vygenerovaného dotazu nebo poznámkového bloku zobrazíte obsah souboru.

   ![Zobrazit obsah souboru](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Můžete změnit dotaz na filtrování a řazení výsledků. Vyhledá funkce jazyka, které jsou k dispozici v [přehledu funkcí](sql/overview-features.md)SQL na vyžádání v SQL.

## <a name="next-steps"></a>Další kroky

- Umožněte uživatelům Azure AD dotazování souborů [přiřazením **čtecího modulu dat objektů BLOB úložiště** nebo **přispěvatele dat objektů BLOB úložiště** na Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role)
- [Dotazování souborů na Azure Storage pomocí SQL na vyžádání](sql/on-demand-workspace-overview.md)
- [Vytvoření fondu úloh Apache Sparku pomocí webu Azure Portal](quickstart-create-apache-spark-pool-portal.md)
- [Vytvoření sestavy Power BI u souborů uložených v Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
