---
title: Připojení k systémům soubor místně - Azure Logic Apps | Microsoft Docs
description: Připojit k místní systémy souborů z pracovních aplikace logiky prostřednictvím místní brána dat a konektor systému souborů
keywords: systémy souborů, místní
services: logic-apps
author: derek1ee
manager: jeconnoc
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 019b5fcd218ddd471c5f02d0332b8f5b5bf0edb3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300816"
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Připojit k místní systémy souborů z aplikace logiky s konektorem systému souborů

Spravovat data a bezpečný přístup k místním prostředkům, můžete použít aplikace logiky bránu dat na místě. Tento článek ukazuje, jak můžete připojit k systému souborů na místní prostřednictvím této základní příklad scénáře: Zkopírujte soubor, který nahrání dat do sdílené složky a potom odeslat e-mail.

## <a name="prerequisites"></a>Požadavky

* Stáhněte si nejnovější [místní brána dat](https://www.microsoft.com/download/details.aspx?id=53127).

* Nainstalujte a nastavte si nejnovější místní brána data gateway, verze 1.15.6150.1 nebo vyšší. Pokyny najdete v tématu [připojit ke zdrojům dat místně](http://aka.ms/logicapps-gateway). Než budete pokračovat v těchto krocích, je nutné nainstalovat bránu na místní počítač.

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Přidání aktivační události a akcí pro připojení k systému souborů

1. Vytvoření prázdné aplikace logiky Přidání této aktivační události jako první krok: **Dropboxu - Pokud dojde k vytvoření souboru** 

2. V části aktivační událost, zvolte **+ další krok** > **přidat akci**. 

3. Do vyhledávacího pole zadejte "systém souborů" jako filtr. Když se zobrazí všechny akce pro konektor systému souborů, vyberte **systém souborů – vytvoření souboru** akce. 

   ![Vyhledejte soubor konektoru](media/logic-apps-using-file-connector/search-file-connector.png)

4. Pokud již nemáte připojení k systému souborů, se zobrazí výzva k vytvoření připojení. 

5. Vyberte **připojit prostřednictvím místní brána dat**. Jakmile se zobrazí vlastnosti připojení, nastavte připojení uvedených v tabulce.

   ![Konfigurace připojení](media/logic-apps-using-file-connector/create-file.png)

   | Nastavení | Popis |
   | ------- | ----------- |
   | **Kořenová složka** | Zadejte kořenová složka pro systém souborů. Můžete zadat do místní složky v počítači, kde je nainstalován bránu dat na místě, nebo složce může být sdílená síťová složka má počítač přístup. <p>**Tip:** kořenové složky je hlavní nadřazené složky, která se používá k relativní cesty pro všechny akce související s souboru. | 
   | **Typ ověřování** | Typ ověřování, který je používán systém souborů | 
   | **Uživatelské jméno** | Zadejte svoje uživatelské jméno {*domény*\\*uživatelské jméno*} pro bránu dříve nainstalované. | 
   | **Heslo** | Zadejte heslo pro bránu dříve nainstalované. | 
   | **Brána** | Vyberte dříve nainstalované brány. | 
   ||| 

6. Po zadání všech podrobností o připojení, zvolte **vytvořit**. 

   Služba Logic Apps nakonfiguruje a otestuje připojení, a ujistěte se, že připojení funguje správně. 
   Pokud je připojení správně nastavena, zobrazí se možnosti pro akci, kterou jste dříve vybrali. 
   Konektor systému souborů je nyní připravena k použití.

7. Nastavit **vytvořit soubor** akce pro kopírování souborů z Dropbox do kořenové složky pro místní pro sdílení souborů.

   ![Vytvoření souboru akce](media/logic-apps-using-file-connector/create-file-filled.png)

8. Po této akci pro kopírování souboru přidejte Outlook akci, která odešle e-mail, aby příslušné uživatele vědět o nový soubor. Zadejte příjemce, název a text e-mailu. 

   V **dynamický obsah** seznamu, můžete data výstupy z konektoru nástroje souboru, můžete přidat další podrobnosti k e-mailu.

   ![Odesílání e-mailu akce](media/logic-apps-using-file-connector/send-email.png)

9. Uložte svou aplikaci logiky. Testování aplikace s tím, že nahrajete soubor na Dropbox. Soubor by měl získat zkopírován do místní sdílené složky a měli byste obdržet e-mailu o operaci.

Blahopřejeme, nyní máte pracovní aplikace logiky, která může připojit k systému souborů na místě. 

Zkuste zkoumat další funkce, které nabízí konektor, například:

- Vytvořit soubor
- Zobrazit seznam souborů ve složce
- Připojit soubor
- Odstranit soubor
- Získat obsah souboru
- Získat obsah souboru pomocí cesty
- Načíst metadata souboru
- Získat metadata souboru pomocí cesty
- Zobrazit seznam souborů v kořenové složce
- Aktualizovat soubor

## <a name="view-the-swagger"></a>Zobrazení swagger

Najdete v článku [swagger podrobnosti](/connectors/fileconnector/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* K vylepšení Azure Logic Apps a konektory, hlasovat o nebo odeslání nápadů na [Azure Logic Apps User Voice lokality](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Připojení k místním datům](../logic-apps/logic-apps-gateway-connection.md) 
* [Monitorování aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Integrace Enterprise pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
