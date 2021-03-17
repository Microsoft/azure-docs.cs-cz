---
title: Posílání a přijímání zpráv AS2 pro B2B
description: Zprávy Exchange AS2 pro scénáře integrace B2B Enterprise pomocí Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856666"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv AS2 pro podnikovou integraci B2B v Azure Logic Apps s využitím rozšíření Enterprise Integration Pack

> [!IMPORTANT]
> Původní konektor AS2 je zastaralý, takže pokud nepotřebujete sledovat možnosti, použijte místo toho konektor **AS2 (v2)** . S výjimkou sledování poskytuje konektor v2 stejné možnosti jako původní verze, je nativní pro modul runtime Logic Apps a má významná vylepšení výkonu v oblasti velikost zpráv, propustnosti a latence. Konektor v2 také nevyžaduje, abyste vytvořili připojení k účtu pro integraci. Místo toho, jak je popsáno v části požadavky, nezapomeňte propojit účet pro integraci do aplikace logiky, ve které plánujete konektor používat.

Pokud chcete pracovat se AS2 zprávami v Azure Logic Apps, můžete použít konektor AS2, který poskytuje triggery a akce, které podporují a spravují komunikaci AS2 (verze 1,2). Chcete-li například vytvořit zabezpečení a spolehlivost při odesílání zpráv, můžete použít tyto akce:

* [ **AS2 zakódovat** ](#encode) pro poskytování šifrování, digitálního podepisování a potvrzení prostřednictvím oznámení o vyřazení zpráv (MDN), které vám pomůžou zajistit Neodmítnutí. Tato akce například používá hlavičky AS2/HTTP a při konfiguraci provádí tyto úlohy:

  * Podepíše odchozí zprávy.
  * Šifruje odchozí zprávy.
  * Zkomprimuje zprávu.
  * Přenese název souboru do hlavičky MIME.

* [ **AS2 dekódovat** akci](#decode) pro poskytování dešifrování, digitálního podepisování a potvrzování prostřednictvím oznámení o zařazování zpráv (MDN). Tato akce například provede tyto úlohy:

  * Zpracuje hlavičky AS2/HTTP.
  * Sjednotí přijaté MDNs s původními odchozími zprávami.
  * Aktualizuje a koreluje záznamy v databázi bez odmítnutí.
  * Zapisuje záznamy pro vytváření sestav o stavu AS2.
  * Vytvoří výstup obsahu v datové části jako kódovaný v kódování Base64.
  * Určuje, zda jsou vyžadovány MDNs. Na základě smlouvy AS2 určuje, jestli má být MDNs synchronní nebo asynchronní.
  * Generuje synchronní nebo asynchronní MDNs na základě smlouvy AS2.
  * Nastaví korelační tokeny a vlastnosti v MDNs.

  Tato akce provádí tyto úlohy také při konfiguraci:

  * Ověří podpis.
  * Dešifruje zprávy.
  * Dekomprimuje zprávu.
  * Zkontroluje a zakáže duplicity ID zpráv.

Tento článek ukazuje, jak přidat akce kódování a dekódování AS2 do existující aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete použít konektor AS2, a Trigger, který spouští pracovní postup vaší aplikace logiky. Konektor AS2 poskytuje pouze akce, nikoli triggery. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a propojený s aplikací logiky, ve které plánujete použít konektor AS2. Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* Aspoň dva [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jste už definovali v účtu pro integraci pomocí kvalifikátoru identity AS2.

* Než budete moct použít konektor AS2, musíte vytvořit AS2 [smlouvu](../logic-apps/logic-apps-enterprise-integration-agreements.md) mezi vašimi obchodními partnery a uložit tuto smlouvu na účet pro integraci.

* Pokud používáte [Azure Key Vault](../key-vault/general/overview.md) pro správu certifikátů, ověřte, že klíče trezoru umožňují operace **šifrování** a **dešifrování** . V opačném případě se akce kódování a dekódování nezdaří.

  V Azure Portal ve vašem trezoru klíčů vyhledejte klíč, zkontrolujte **povolené operace**klíče a ověřte, že jsou vybrané operace **šifrování** a **dešifrování** . Příklad:

  ![Kontrolovat operace klíče trezoru](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kódování zpráv AS2

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**. Do vyhledávacího pole zadejte "AS2 encode" a ujistěte se, že jste vybrali akci AS2 (v2): **kódování AS2**

   ![Vyberte AS2 kódování.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Nyní zadejte informace pro tyto vlastnosti:

   | Vlastnost | Popis |
   |----------|-------------|
   | **Zpráva ke kódování** | Datová část zprávy |
   | **AS2 od** | Identifikátor odesilatele zprávy, jak Určuje smlouva AS2 |
   | **AS2 na** | Identifikátor příjemce zprávy, jak Určuje smlouva AS2 |
   |||

   Například:

   ![Vlastnosti kódování zprávy](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Pokud při posílání podepsaných nebo šifrovaných zpráv dochází k potížím, zvažte možnost vyzkoušet si různé formáty SHA256 algoritmu. Specifikace AS2 neposkytuje žádné informace o formátech SHA256, takže každý zprostředkovatel používá vlastní implementaci nebo formát.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekódování zpráv AS2

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V Návrháři přidejte novou akci do aplikace logiky.

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**. Do vyhledávacího pole zadejte "AS2 Decode" a ujistěte se, že jste vybrali akci AS2 (v2): **AS2 Decode**

   ![Vyberte AS2 dekódování.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Pro **zprávu pro zakódování** a vlastnosti **záhlaví zprávy** vyberte tyto hodnoty z předchozích výstupů triggeru nebo akce.

   Předpokládejme například, že vaše aplikace logiky přijímá zprávy prostřednictvím triggeru žádosti. Z této aktivační události můžete vybrat výstupy.

   ![Výběr textu a záhlaví z výstupů žádosti](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Ukázka

Pokud se chcete pokusit nasadit plně funkční aplikaci logiky a ukázkový scénář AS2, přečtěte si článek [Šablona a scénář AS2 Logic App](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/as2/). 

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá [omezení zpráv B2B pro ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
