---
title: Připojení k systémům souborů místně
description: Automatizujte úlohy a pracovní postupy, které se připojují k místním systémům souborů pomocí konektoru systému souborů prostřednictvím místní brány dat v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 65b6b1f783dbabc9ad2e1a4bf79008240d1b2726
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659913"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Připojení k místním systémům souborů pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru systému souborů můžete vytvářet automatizované úlohy a pracovní postupy, které vytvářejí a spravují soubory v místní sdílené složce, například:

- Vytváření, získávání, přidávání, aktualizace a odstraňování souborů.
- Vypíše soubory ve složkách nebo kořenových složkách.
- Získá obsah souboru a metadata.

V tomto článku se dozvíte, jak se připojit k místnímu systému souborů, jak je popsáno v tomto ukázkovém scénáři: zkopírování souboru nahraného do Dropboxu do sdílené složky a odeslání e-mailu. K bezpečnému připojení k místním systémům využívají Logic Apps místní [bránu dat](../logic-apps/logic-apps-gateway-connection.md). Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Technické informace specifické pro konektor najdete v referenčních informacích o [konektoru systému souborů](/connectors/filesystem/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Než budete moct připojit Logic Apps k místním systémům, jako je třeba váš server systému souborů, musíte [nainstalovat a nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete nastavit, aby při vytváření připojení k systému souborů z aplikace logiky používali instalaci brány.

* [Účet Dropboxu](https://www.dropbox.com/), který si můžete zaregistrovat zdarma. Přihlašovací údaje k účtu jsou nezbytné pro vytvoření připojení mezi vaší aplikací logiky a vaším účtem Dropbox.

* Přístup k počítači, který má systém souborů, který chcete použít. Pokud například nainstalujete bránu dat na stejný počítač jako systém souborů, budete potřebovat přihlašovací údaje účtu pro tento počítač.

* E-mailový účet od poskytovatele, který podporuje Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/). Tato aplikace logiky používá pracovní nebo školní účet. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) V tomto příkladu potřebujete prázdnou aplikaci logiky.

## <a name="add-trigger"></a>Přidání triggeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Do vyhledávacího pole zadejte jako filtr "Dropbox". V seznamu triggery vyberte tuto aktivační událost: **když se vytvoří soubor** .

   ![Výběr triggeru Dropboxu](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropboxu a udělte přístup k datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro aktivační událost.

   ![Trigger Dropboxu](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Přidat akce

1. V části Trigger klikněte na **Další krok**. Do vyhledávacího pole zadejte jako filtr "systém souborů". V seznamu akce vyberte tuto akci: **vytvořit soubor** .

   ![Najít konektor systému souborů](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Pokud ještě nemáte připojení k systému souborů, budete vyzváni k vytvoření připojení.

   ![Vytvoření připojení](media/logic-apps-using-file-connector/file-system-connection.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Název připojení** | Yes | <*název připojení*> | Název, který chcete pro připojení |
   | **Kořenová složka** | Yes | <*název kořenové složky*> | Kořenová složka pro systém souborů, například pokud jste nainstalovali místní bránu dat, jako je například místní složka v počítači, kde je nainstalována místní brána dat, nebo složka sdílené síťové složky, ke které má počítač přístup. <p>Příklad: `\\PublicShare\\DropboxFiles` <p>Kořenová složka je hlavní nadřazená složka, která se používá pro relativní cesty ke všem akcím, které se týkají souborů. |
   | **Typ ověřování** | No | <*typ ověřování*> | Typ ověřování, který používá systém souborů: **Windows** |
   | **Uživatelské jméno** | Yes | <*domain* > \\ Doména < *uživatelské jméno*> <p>-nebo- <p><*místní* > \\ počítač < *uživatelské jméno*> | Uživatelské jméno počítače, kde máte složku systému souborů. <p>Pokud je složka systému souborů ve stejném počítači jako místní brána dat, můžete použít <*local-computer* > \\ < *uživatelské jméno* místního počítače>. |
   | **Heslo** | Yes | <*Vaše heslo*> | Heslo pro počítač, na kterém máte systém souborů |
   | **brány** | Yes | <*instalace – brána – název*> | Název dříve nainstalované brány |
   |||||

1. Jakmile budete hotoví, vyberte **Vytvořit**.

   Logic Apps nakonfiguruje a otestuje připojení a zajistí správné fungování připojení. Pokud je připojení správně nastavené, zobrazí se možnosti pro akci, kterou jste předtím vybrali.

1. V akci **vytvořit soubor** zadejte podrobnosti o kopírování souborů z Dropboxu do kořenové složky ve vaší místní sdílené složce. Chcete-li přidat výstupy z předchozích kroků, klikněte do polí a při zobrazení seznamu dynamického obsahu vyberte z polí k dispozici.

   ![Akce vytvoření souboru](media/logic-apps-using-file-connector/create-file-filled.png)

1. Teď přidejte akci Outlooku, která pošle e-mail, aby příslušné uživatele znali o novém souboru. Zadejte příjemce, název a text e-mailu. Pro účely testování můžete použít vlastní e-mailovou adresu.

   ![Akce Odeslat e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Uložte aplikaci logiky. Otestujte svoji aplikaci nahráním souboru do Dropboxu.

   Vaše aplikace logiky by měla zkopírovat soubor do místní sdílené složky a odeslat příjemcům e-mail o zkopírovaném souboru.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/fileconnector/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak se [připojit k místním datům](../logic-apps/logic-apps-gateway-connection.md) 
* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
