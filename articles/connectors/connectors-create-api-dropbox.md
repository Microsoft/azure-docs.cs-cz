---
title: Připojení k Dropboxu
description: Automatizace úloh a pracovních postupů, které nahrávají a spravují soubory v Dropboxu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665747"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Nahrávání a správa souborů v Dropboxu pomocí Azure Logic Apps

Pomocí konektoru Dropbox a Azure Logic Apps můžete vytvářet automatizované pracovní postupy, které nahrávají a spravují soubory ve vašem účtu Dropbox. 

Tento článek ukazuje, jak se připojit k Dropbox z aplikace logiky a pak přidat Dropbox **Při vytvoření souboru** aktivační události a Dropbox **Získat obsah souboru pomocí** akce cesta.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Dropbox [účet](https://www.dropbox.com/), který si můžete zaregistrovat zdarma. Přihlašovací údaje vašeho účtu jsou nezbytné pro vytvoření připojení mezi aplikací logiky a účtem Dropbox.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). V tomto příkladu potřebujete prázdnou aplikaci logiky.

## <a name="add-trigger"></a>Přidání triggeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Pod vyhledávacím polem zvolte **Vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Při vytvoření souboru**

   ![Vybrat aktivační událost Dropboxu](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropbox a autorizujte přístup k datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro aktivační událost. 

   V tomto příkladu vyberte složku, do které chcete sledovat vytváření souborů. Chcete-li procházet složky, zvolte ikonu složky vedle pole **Složka.**

## <a name="add-action"></a>Přidání akce

Nyní přidejte akci, která získá obsah z libovolného nového souboru.

1. Pod aktivační událostí zvolte **Další krok**. 

1. Pod vyhledávacím polem zvolte **Vše**. Do vyhledávacího pole zadejte jako filtr "Dropbox".
Ze seznamu akcí vyberte tuto akci: **Získání obsahu souboru pomocí cesty**

1. Pokud jste ještě neautorizovali Azure Logic Apps pro přístup k Dropboxu, autorizujte přístup teď.

1. Chcete-li přejít na cestu k souboru, kterou chcete použít, zvolte vedle pole **Cesta k souboru** tlačítko Tři tečky (**...**). 

   Můžete také klepnout do pole **Cesta k souboru** a ze seznamu dynamického obsahu vybrat **Cestu k souboru**, jejíž hodnota je k dispozici jako výstup z aktivační události, kterou jste přidali v předchozí části.

1. Až budete hotovi, uložte aplikaci logiky.

1. Chcete-li spustit aplikaci logiky, vytvořte nový soubor v Dropboxu.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](/connectors/dropbox/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
