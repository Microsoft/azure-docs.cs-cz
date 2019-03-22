---
title: Připojte se k Dropboxu – Azure Logic Apps
description: Odesílat a spravovat soubory pomocí rozhraní REST API Dropboxu a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314412"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Odesílat a spravovat soubory v Dropboxu pomocí Azure Logic Apps

Konektor Dropbox a Azure Logic Apps můžete vytvářet automatizované pracovní postupy, které odesílat a spravovat soubory v účtu Dropbox. 

Tento článek popisuje, jak se připojit k Dropboxu vaší aplikace logiky a pak přidejte Dropboxu **při vytvoření souboru** aktivační události nebo Dropbox **získat obsah souboru pomocí cesty** akce.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* A [účtu Dropbox](https://www.dropbox.com/), které můžete se zaregistrovat zadarmo. Svoje přihlašovací údaje jsou nezbytné pro vytvoření připojení mezi vaší aplikace logiky a účtu Dropbox.

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). V tomto příkladu je třeba prázdné aplikace logiky.

## <a name="add-trigger"></a>Přidat trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Pod vyhledávacím polem vyberte **všechny**. Do vyhledávacího pole zadejte jako filtr "dropboxu".
Ze seznamu triggerů vyberte tento trigger: **Když se vytvoří soubor**

   ![Vyberte trigger Dropboxu](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropbox a autorizaci přístupu k vašim datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro trigger. 

   V tomto příkladu vyberte složku, ve které chcete sledovat vytváření souborů. Procházení složek, zvolte ikonu složky vedle **složky** pole.

## <a name="add-action"></a>Přidat akci

Teď přidejte akci, která získá obsah z libovolného nového souboru.

1. Pod triggerem zvolte **další krok**. 

1. Pod vyhledávacím polem vyberte **všechny**. Do vyhledávacího pole zadejte jako filtr "dropboxu".
Ze seznamu akcí vyberte tuto akci: **Získat obsah souboru pomocí cesty**

1. Pokud již nejsou oprávnění Azure Logic Apps pro přístup k Dropboxu, autorizujete přístup k nyní.

1. Přejděte k souboru na cestě, kterou chcete použít, vedle položky **cesta k souboru** vyberte symbol tří teček (**...** ) tlačítko. 

   Můžete také kliknout na uvnitř **cesta k souboru** a ze seznamu dynamického obsahu vyberte **cesta k souboru**, jehož hodnota je k dispozici jako výstup z aktivační události, které jste přidali v předchozí části.

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

1. Pokud chcete aktivovat svou aplikaci logiky, vytvořte nový soubor v Dropboxu.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v konektoru OpenAPI (dříve Swagger) souboru, najdete v článku [konektoru referenční stránce](/connectors/dropbox/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
