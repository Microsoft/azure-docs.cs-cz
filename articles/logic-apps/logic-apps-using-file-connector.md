---
title: Připojení k místním systémům souborů
description: Automatizace úloh a pracovních postupů, které se připojují k místním souborovým systémům pomocí konektoru systému souborů, prostřednictvím místní brány dat v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: ab17137f162b893b54942d870b07a36f87d1b71d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115073"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Připojení k místním systémům souborů pomocí Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru systému souborů můžete vytvářet automatizované úlohy a pracovní postupy, které vytvářejí a spravují soubory v místní sdílené složce, například:

- Vytvářejte, přiřazujte, připojujte, aktualizujte a odstraňujte soubory.
- Seznam souborů ve složkách nebo kořenových složkách.
- Získejte obsah souboru a metadata.

Tento článek ukazuje, jak se můžete připojit k místnímu systému souborů, jak je popsáno v tomto příkladu scénáře: zkopírujte soubor, který je odeslán do Dropboxu do sdílené složky a pak odešlete e-mail. Aplikace logiky používají místní bránu dat , které se bezpečně připojují a přistupují k místním [systémům.](../logic-apps/logic-apps-gateway-connection.md) Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Technické informace specifické pro konektor naleznete v [odkazu na konektor souborového systému](/connectors/filesystem/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Než budete moci připojit aplikace logiky k místním systémům, jako je server systému souborů, musíte [nainstalovat a nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete určit použití instalace brány při vytváření připojení systému souborů z aplikace logiky.

* Dropbox [účet](https://www.dropbox.com/), který si můžete zaregistrovat zdarma. Přihlašovací údaje vašeho účtu jsou nezbytné pro vytvoření připojení mezi aplikací logiky a účtem Dropbox.

* Přístup k počítači se systémem souborů, který chcete použít. Pokud například nainstalujete bránu dat do stejného počítače jako systém souborů, budete potřebovat pověření účtu pro tento počítač.

* E-mailový účet od poskytovatele, který je podporovaný Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tato aplikace logiky používá účet Office 365 Outlook. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). V tomto příkladu potřebujete prázdnou aplikaci logiky.

## <a name="add-trigger"></a>Přidání triggeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Do vyhledávacího pole zadejte jako filtr "Dropbox". Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Při vytvoření souboru**

   ![Vybrat aktivační událost Dropboxu](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropbox a autorizujte přístup k datům Dropboxu pro Azure Logic Apps.

1. Zadejte požadované informace pro aktivační událost.

   ![Aktivační událost dropboxu](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Přidání akcí

1. Pod aktivační událostí zvolte **Další krok**. Do vyhledávacího pole zadejte jako filtr "systém souborů". Ze seznamu akcí vyberte tuto akci: **Vytvořit soubor**

   ![Najít konektor systému souborů](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Pokud ještě nemáte připojení k systému souborů, budete vyzváni k vytvoření připojení.

   ![Vytvoření připojení](media/logic-apps-using-file-connector/file-system-connection.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Název připojení** | Ano | <*název připojení*> | Název, který chcete pro připojení |
   | **Kořenová složka** | Ano | <*název kořenové složky*> | Kořenová složka systému souborů, například pokud jste nainstalovali místní bránu dat, jako je místní složka v počítači, kde je nainstalována místní brána dat, nebo složka pro sdílenou síťovou složku, ke které má počítač přístup. <p>Příklad: `\\PublicShare\\DropboxFiles` <p>Kořenová složka je hlavní nadřazená složka, která se používá pro relativní cesty pro všechny akce související se souborem. |
   | **Typ ověřování** | Ne | <*typ auth*> | Typ ověřování, které používá systém souborů: **Windows** |
   | **Username** | Ano | <*domain*>\\<*uživatelské jméno* domény> | Uživatelské jméno počítače, ve kterém máte systém souborů |
   | **Heslo** | Ano | <*vaše heslo*> | Heslo pro počítač, ve kterém máte systém souborů |
   | **Brána** | Ano | <*nainstalovaný název brány*> | Název dříve nainstalované brány |
   |||||

1. Jakmile budete hotoví, vyberte **Vytvořit**.

   Logic Apps konfiguruje a testuje vaše připojení a ujistěte se, že připojení funguje správně. Pokud je připojení nastaveno správně, zobrazí se možnosti dříve vybrané akce.

1. V akci **Vytvořit soubor** zadejte podrobnosti pro kopírování souborů z Dropboxu do kořenové složky ve sdílené složce místního souboru. Chcete-li přidat výstupy z předchozích kroků, klepněte do polí a při zobrazení seznamu dynamického obsahu vyberte z dostupných polí.

   ![Vytvořit akci souboru](media/logic-apps-using-file-connector/create-file-filled.png)

1. Nyní přidejte akci aplikace Outlook, která odešle e-mail, aby příslušní uživatelé věděli o novém souboru. Zadejte příjemce, titul a tělo e-mailu. Pro testování můžete použít vlastní e-mailovou adresu.

   ![Akce odeslat e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Uložte svou aplikaci logiky. Otestujte aplikaci nahráním souboru do Dropboxu.

   Aplikace logiky by měla zkopírovat soubor do místní sdílené složky a odeslat příjemcům e-mail o zkopírovaném souboru.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/fileconnector/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak se [připojit k místním datům](../logic-apps/logic-apps-gateway-connection.md) 
* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
