---
title: Odesílání a přijímání zpráv AS2 pro B2B
description: Výměna zpráv AS2 pro scénáře podnikové integrace B2B pomocí aplikací Azure Logic Apps s podnikovou integrační sadou
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650554"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv AS2 pro podnikovou integraci B2B v aplikacích Azure Logic Apps s podnikovou integrací

> [!IMPORTANT]
> Původní konektor AS2 je zastaralá, takže se ujistěte, že místo toho používáte konektor **AS2 (v2).** Tato verze poskytuje stejné funkce jako původní verze, je nativní pro prostředí Logic Apps a poskytuje významné zlepšení výkonu, pokud jde o propustnost a velikost zprávy. Nativní konektor v2 také nevyžaduje vytvoření připojení k účtu integrace. Místo toho, jak je popsáno v požadavcích, ujistěte se, že propojíte účet integrace s aplikací logiky, kde plánujete použít konektor.

Pro práci se zprávami AS2 v Azure Logic Apps můžete použít konektor AS2, který poskytuje aktivační události a akce pro správu komunikace AS2. Chcete-li například zjistit zabezpečení a spolehlivost při přenosu zpráv, můžete použít tyto akce:

* [ **Akce kódování AS2** ](#encode) pro poskytování šifrování, digitálního podepisování a potvrzení prostřednictvím oznámení o dispozičním zabezpečení zpráv (MDN), která pomáhají podporovat neodvolatelnost. Tato akce například použije hlavičky AS2/HTTP a při konfiguraci provede tyto úlohy:

  * Podepisuje odchozí zprávy.
  * Šifruje odchozí zprávy.
  * Zkomprimuje zprávu.
  * Přenáší název souboru v hlavičce MIME.

* [ **AKCE DEKódování AS2** ](#decode) pro poskytování dešifrování, digitálního podepisování a potvrzení prostřednictvím oznámení dispozice zprávy (MDN). Tato akce například provádí tyto úkoly:

  * Zpracovává hlavičky AS2/HTTP.
  * Odsouhlasí přijaté adresy MDN s původními odchozími zprávami.
  * Aktualizuje a koreluje záznamy v databázi bez odmítnutí.
  * Zapisuje záznamy pro hlášení stavu AS2.
  * Výstupy užitečné části obsah jako base64 kódované.
  * Určuje, zda jsou vyžadovány mdn. Na základě smlouvy AS2 určuje, zda mdns by měly být synchronní nebo asynchronní.
  * Generuje synchronní nebo asynchronní MDNs na základě smlouvy AS2.
  * Nastaví tokeny korelace a vlastnosti na MDNs.

  Tato akce také provádí tyto úkoly při konfiguraci:

  * Ověří podpis.
  * Dešifruje zprávy.
  * Dekomprimuje zprávu.
  * Zkontrolujte a zakažte duplikáty ID zprávy.

Tento článek ukazuje, jak přidat akce kódování AS2 a dekódování do existující aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, odkud chcete použít konektor AS2 a aktivační událost, která spustí pracovní postup aplikace logiky. Konektor AS2 poskytuje pouze akce, nikoli aktivační události. Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který je přidružený k vašemu předplatnému Azure a propojený s aplikací logiky, kde plánujete použít konektor AS2. Vaše aplikace logiky i účet integrace musí existovat ve stejném umístění nebo v oblasti Azure.

* Alespoň dva [obchodní partneři,](../logic-apps/logic-apps-enterprise-integration-partners.md) které jste již definovali ve svém integračním účtu pomocí kvalifikátoru identitas 2.

* Než budete moci použít konektor AS2, musíte vytvořit [smlouvu](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 mezi svými obchodními partnery a uložit tuto smlouvu do svého integračního účtu.

* Pokud používáte [Azure Key Vault](../key-vault/key-vault-overview.md) pro správu certifikátů, zkontrolujte, zda klíče trezoru umožňují operace **šifrování** a **dešifrování.** V opačném případě se nezdaří akce kódování a dekódování.

  Na webu Azure Portal přejděte ke klíči v trezoru klíčů, zkontrolujte **povolené operace**klíče a zkontrolujte, zda jsou vybrané operace **Šifrování** a **Dešifrování,** například:

  ![Kontrola operací klíčů trezoru](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kódování zpráv AS2

1. Pokud jste tak ještě neučinili, otevřete na [webu Azure Portal](https://portal.azure.com)aplikaci logiky v Návrháři aplikací logiky.

1. V návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **Vše**. Do vyhledávacího pole zadejte "as2 encode" a ujistěte se, že jste vybrali akci AS2 (v2): **Zakódovat as2**

   ![Vyberte "KÓDOVÁNÍ AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Nyní zadejte informace o těchto vlastnostech:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Zpráva ke kódování** | Datová část zprávy |
   | **AS2 od** | Identifikátor odesílatele zprávy určený ve vaší smlouvě AS2 |
   | **AS2 až** | Identifikátor příjemce zprávy, jak je uvedeno ve vaší dohodě AS2 |
   |||

   Například:

   ![Vlastnosti kódování zpráv](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Pokud při odesílání podepsaných nebo šifrovaných zpráv dochází k problémům, zvažte možnost vyzkoušet různé formáty algoritmů SHA256. Specifikace AS2 neposkytuje žádné informace o formátech SHA256, takže každý zprostředkovatel používá vlastní implementaci nebo formát.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekódování zpráv AS2

1. Pokud jste tak ještě neučinili, otevřete na [webu Azure Portal](https://portal.azure.com)aplikaci logiky v Návrháři aplikací logiky.

1. V návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **Vše**. Do vyhledávacího pole zadejte "as2 decode" a ujistěte se, že jste vybrali akci AS2 (v2): **Dekódování AS2**

   ![Vyberte "DEkódování AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Pro **message ke kódování** a **message headers** vlastnosti, vyberte tyto hodnoty z předchozích výstupů aktivační události nebo akce.

   Předpokládejme například, že aplikace logiky přijímá zprávy prostřednictvím aktivační události Požadavku. Můžete vybrat výstupy z této aktivační události.

   ![Vybrat text a záhlaví z výstupů požadavku](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Ukázka

Chcete-li zkusit nasazení plně funkční aplikace logiky a ukázkový scénář AS2, podívejte se na [šablonu a scénář aplikace logiky AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/as2/). 

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor původní verze s popiskem ISE používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
