---
title: zahrnout soubor
description: zahrnout soubor
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678212"
---
### <a name="set-up-pagination"></a>Nastavení stránkování

Pro některé konektory a jejich akce, které načíst více položek můžete překročit výsledky konektoru výchozí velikost stránky. V takovém případě akce vrátí pouze první stránky s výsledky. Například výchozí velikost stránky pro **systému SQL Server - Get řádky** akce je 2048, ale může lišit podle dalších nastavení. Ujistěte se, získáte všechny záznamy, zapněte **stránkování** nastavení pro tuto akci. Toto nastavení má svou aplikaci logiky požádejte konektor pro zbývající záznamy, ale po dokončení akce vrátit všechny výsledky jako do jedné zprávy. 

Zde jsou některé konektory, kde můžete zapnout stránkování pro určité akce: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Tady je příklad pro **získat řádky** akce:

1. Chcete-li zjistit, jestli akce podporuje stránkování, otevřete akce **nastavení**. 

   ![Na akce otevřete "Nastavení"](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Pokud akce podporuje stránkování, změňte **stránkování** nastavení z **vypnout** k **na**. Abyste měli jistotu, že akce vrátí minimální sady výsledků, zadejte hodnotu pro **Limit**.

   ![Zadejte, zda akci vrátit minimální počet výsledků](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Až budete připraveni, zvolte **provádí**.