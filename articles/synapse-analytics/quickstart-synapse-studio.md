---
title: Použití Synapse Studio (náhled)
description: V tomto rychlém startu uvidíte a dozvíte se, jak snadné je dotazovat se na různé typy souborů pomocí Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423863"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Úvodní příručka: Použití Synapse Studia (náhled)

V tomto rychlém startu se dozvíte, jak dotazovat soubory pomocí Synapse Studio.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky

[Vytvořte pracovní prostor Azure Synapse a přidružený účet úložiště](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Spuštění Synapse Studio

V pracovním prostoru Azure Synapse na webu Azure Portal klikněte na **Spustit Synapse Studio**.

![Spuštění Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Synapse Studio můžete také spustit tak, že kliknete na [Azure Synapse Analytics](https://web.azuresynapse.net) a poskytnete příslušné hodnoty klienta, předplatného a pracovního prostoru.

## <a name="browse-storage-accounts"></a>Procházet účty úložiště

Po otevření Synapse Studia přejděte na **Data** a potom **rozbalte účty úložiště** a zobrazte účet úložiště v pracovním prostoru.

![Procházení souborů v úložišti](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Můžete vytvářet nové složky a nahrávat soubory pomocí odkazů v panelu nástrojů k uspořádání souborů.

## <a name="query-files-on-storage-account"></a>Dotazovat se na soubory v účtu úložiště

> [!IMPORTANT]
> Abyste mohli dotazovat na `Storage Blob Reader` soubory, musíte být členem role v podkladovém úložišti. Zjistěte, jak [přiřadit oprávnění RBAC **pro čtení dat objektů blob úložiště** nebo datový přispěvatel **e-blob** úložiště ve službě Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Nahrajte `PARQUET` nějaké soubory.
2. Vyberte jeden nebo více souborů a pak vytvořte nový skript SQL nebo poznámkový blok Spark, abyste viděli obsah souborů. Pokud chcete vytvořit poznámkový blok, budete muset vytvořit [fond Apache Spark v pracovním prostoru](spark/apache-spark-notebook-create-spark-use-sql.md).

   ![Soubory dotazů v úložišti](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Spusťte generovaný dotaz nebo poznámkový blok a zostřikujte obsah souboru:

   ![Zobrazení obsahu souboru](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Dotaz můžete změnit tak, aby filtrovat a řadit výsledky. Vyhledejte funkce jazyka, které jsou k dispozici v sql na vyžádání v [přehledu funkcí SQL](sql/overview-features.md).

## <a name="next-steps"></a>Další kroky

- Povolení uživatelům Azure AD k dotazování na soubory [přiřazením oprávnění RBAC **datového zařízení k blob úložiště** nebo **rbac přispěvatele objektů blob úložiště** v úložišti Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Dotazovat se na soubory v Úložišti Azure pomocí SQL Na vyžádání](sql/on-demand-workspace-overview.md)
- [Vytvoření fondu Apache Spark](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Vytvoření sestavy Power BI u souborů uložených ve službě Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
