---
title: Zprávy AS2 pro podnikovou integraci B2B – Azure Logic Apps
description: Výměna zpráv AS2 v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729392"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv AS2 pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack

Pro práci s zprávy AS2 v Azure Logic Apps, můžete použít konektor AS2, který poskytuje triggery a akce pro správu komunikace AS2. Například k vytvoření, zabezpečení a spolehlivost při přenosu zprávy, můžete použít tyto akce:

* [**Zakódovat do zprávy AS2** akce](#encode) pro poskytování, digitální podepisování a šifrování potvrzení zprávy oznámení pomocí zprávy dispozice (MDN), které bylo možné podporovat nepopiratelnosti. Například tato akce platí hlavičky AS2/HTTP a provede tyto úlohy při konfiguraci:

  * Příznaky odchozích zpráv.
  * Šifruje odchozí zprávy.
  * Komprimuje zprávy.
  * Název souboru v hlavičce MIME přenáší.

* [**Dekódovat zprávu AS2** akce](#decode) pro poskytování dešifrování, digitálnímu podepisování a potvrzování prostřednictvím zprávy dispozice upozornění (zprávy MDN.). Například tato akce provede tyto úlohy: 

  * Zpracovává hlavičky AS2/HTTP.
  * Sloučí přijatý něho s původní odchozích zpráv.
  * Aktualizace a koreluje záznamy v databázi – popírání odpovědnosti.
  * Zapíše záznamy pro generování sestav o stavu AS2.
  * Datová část obsahu výstupy jako s kódováním base64.
  * Určuje, zda jsou vyžadovány něho. Podle AS2 smlouvy, určuje, zda něho by měl být synchronní nebo asynchronní.
  * Generuje synchronní nebo asynchronní něho na základě smlouvy AS2.
  * Nastaví vlastnosti a tokenů korelace na něho.

  Tato akce také provádí tyto úlohy při konfiguraci:

  * Ověří podpis.
  * Dešifruje zprávy.
  * Dekomprimuje zprávy. 
  * Zkontrolujte a zakázat duplicity ID zpráv.

Tento článek popisuje, jak přidat AS2 kódování a dekódování akce do existující aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky ve které chcete použít konektoru AS2 a aktivační události, která spustí pracovní postup aplikace logiky. Konektor AS2 poskytuje jenom akce, triggery nejsou. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a propojený na aplikaci logiky, jež k používání konektoru AS2. Obě vaše logiku aplikace a účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* Alespoň dva [obchodními partnery](../logic-apps/logic-apps-enterprise-integration-partners.md) , že už máte definované ve vašem účtu integrace pomocí AS2 kvalifikátor identity.

* Před použitím konektoru AS2, je nutné vytvořit AS2 [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md) mezi vašimi obchodními partnery a úložiště tuto smlouvu v účtu integrace.

* Pokud používáte [Azure Key Vault](../key-vault/key-vault-overview.md) pro certifikát správy, zkontrolujte, že váš trezor klíčů povolit **šifrovat** a **dešifrovat** operace. V opačném případě kódování a dekódování akce nezdaří.

  Na webu Azure Portal, přejděte k trezoru klíčů, zobrazit klíč trezoru **povolené operace**a ujistěte se, že **šifrovat** a **dešifrovat** operace jsou vybrány.

  ![Zkontrolujte operací trezoru klíčů](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kódování zpráv AS2

1. Pokud jste tak dosud neučinili, v [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky. 

1. V části **zvolte akci** a vyhledávání vyberte **všechny**. Do vyhledávacího pole zadejte "kódování as2" a vyberte tuto akci: **Zakódovat do zprávy AS2**.

   ![Vyberte "Kódovat do zprávy AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Pokud nemáte stávající připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení, vyberte účet pro integraci, kterou chcete připojit a zvolte **vytvořit**.

   ![Vytvoření připojení k účtu pro integraci](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Nyní obsahují informace pro tyto vlastnosti:

   | Vlastnost | Popis |
   |----------|-------------|
   | **AS2-od** | Identifikátor odesílatele zprávy podle vaší smlouvy AS2 |
   | **AS2-do** | Identifikátor příjemce zprávy podle vaší smlouvy AS2 |
   | **Text** | Datovou část zprávy |
   |||

   Příklad:

   ![Kódování vlastnosti zprávy](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekódování zprávy AS2

1. Pokud jste tak dosud neučinili, v [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky. 

1. V části **zvolte akci** a vyhledávání vyberte **všechny**. Do vyhledávacího pole zadejte "dekódování as2" a vyberte tuto akci: **Dekódovat zprávu AS2**

   ![Vyberte "Dekódování AS2 zprávu"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Pokud nemáte stávající připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení, vyberte účet pro integraci, kterou chcete připojit a zvolte **vytvořit**.

   ![Vytvoření připojení k účtu pro integraci](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Pro **tělo** a **záhlaví**, vyberte tyto hodnoty z předchozí výstupů triggeru nebo akce.

   Předpokládejme například, že aplikace logiky přijme zprávy prostřednictvím triggeru požadavku. Výstupy můžete vybrat z této aktivační události.

   ![Vyberte záhlaví a text z výstupů žádosti](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Ukázka

Vyzkoušejte si nasazení scénář plně funkční logiku aplikace a ukázky AS2, najdete v článku [AS2 šablony aplikace logiky a scénář](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v konektoru OpenAPI (dříve Swagger) souboru, najdete v článku [konektoru referenční stránce](/connectors/as2/).

## <a name="next-steps"></a>Další postup

Další informace o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
